---
title: 基于Docker的java微服务(二) CentOS7部署Kubernetes集群
toc: true
date: 2016-09-07 19:23:15
tags: 
    - Docker
    - K8s
---

本文主要参考美团云的[在CentOS7上部署Kubernetes集群](https://mos.meituan.com/library/37/how-to-setup-k8s-cluster-on-CentOS7/)

### 概述

Kubernetes(k8s)是Google开源的大规模容器集群管理系统, 本文将基于CentOS7自带的Kubernetes组件、分布式键值存储系统etcd以及Flannel实现的overlay网络来搭建一个简单的k8s集群。

### vagrant安装多台CentOS7

使用vagrant创建多个CentOS7虚拟机用于集群部署。vagrant的安装非常简单,网上一大堆教程，可以参考[这里](http://www.tuicool.com/articles/miE7vm6)。我使用的是1.8.5版本。同时还需要下载CentOS的box文件,
由于box文件都在国外的网站上下载速度缓慢,可以从我的云盘上下载[CentOS-7-x86_64-Minimal-1511.box](https://pan.baidu.com/s/1jI6T4EE)。
有了box文件后,执行以下命令添加box

```bash
    vagrant add box CentOS-7-x86_64-Minimal-1511.box
```

查看vagrant已添加的box

```bash
    vagrant box list
```

创建单台CentOS很简单，需要下面的步骤

```bash
    mkidr centos7
    cd centos7
    vagrant init
    vagrant up
```

vagrant init会初始化一个Vagrantfile的文件，CentOS的配置都是这个文件设定的。这里给出创建多台centos的Vagrantfile配置。

```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

boxes = [
  { :name => :master,:ip => '192.168.253.7',:cpus => 1,:mem => 1024},
  { :name => :worker1,:ip => '192.168.253.8',:cpus => 1,:mem => 1024},
  { :name => :worker2,:ip => '192.168.253.9',:cpus => 1,:mem => 1024},
  ]

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
    boxes.each do |opts|
        config.vm.define opts[:name] do |config|
            config.vm.box       = "centos7"
            config.vm.boot_timeout = 360
            config.ssh.username = "vagrant"
            config.ssh.password = "vagrant"
            config.vm.synced_folder ".", "/vagrant", disabled:true
            config.vm.network  "public_network", ip: opts[:ip]
            config.vm.hostname = "%s.vagrant" % opts[:name].to_s
            config.vm.provider "virtualbox" do |vb|
                vb.customize ["modifyvm", :id, "--cpus", opts[:cpus] ] if opts[:cpus]
                vb.customize ["modifyvm", :id, "--memory", opts[:mem] ] if opts[:mem]
            end
            #config.vm.provision "shell", inline: $update_script
            #config.vm.provision "shell", path: opts[:provision] if opts[:provision]
       end
    end
end
```

在这个文件的目录下执行`vagrant up`就可以创建3台虚拟机。我们把其中master作为k8s的Master节点，worker1，worker2作为k8s的Node节点来创建k8s集群。

### 环境准备

| master        | worker1       | worker2      | 
| ------------- |:-------------:|:------------:|
| 192.168.253.7 | 192.168.253.8 |192.168.253.9 |
| kubernetes    | ntpd          |ntpd          |
| etcd          | flannel       |flannel       |
| ntpd          | kubernetes    |kubernetes    |

Master节点禁用CentOS7自带防火墙，安装kubernetes、etcd、ntpd等软件。

```bash
sudo systemctl stop firewalld && sudo systemctl disable firewalld
sudo yum install -y kubernetes etcd ntp.x86_64
```

Node节点同样禁用CentOS7自带防火墙，安装kubernetes、flannel、ntpd等软件。

```bash
sudo systemctl stop firewalld && sudo systemctl disable firewalld
sudo yum install -y ntp.x86_64 flannel kubernetes
```

### Master配置

修改etcd配置`/etc/etcd/etcd.conf`

```bash
ETCD_LISTEN_CLIENT_URLS="http://0.0.0.0:2379"
```

修改kubernetes全局配置`/etc/kubernetes/config`

```bash
KUBE_LOG_LEVEL="--v=2"
```

修改kubernetes apiserver的配置`/etc/kubernetes/apiserver`

```bash
KUBE_API_ADDRESS="--address=0.0.0.0"
KUBE_API_PORT="--port=8080"
KUBELET_PORT="--kubelet_port=10250"
```

启动master的ntpd、etcd、kube-apiserver、kube-scheduler、kube-controller-manager服务，设置为开机启动，并查看启动后的状态。如果每一个服务都启动成功，那么sudo systemctl status $SRV显示的信息则包含Active: active (running)

```bash
#!/bin/bash
for SRV in ntpd etcd kube-apiserver kube-scheduler kube-controller-manager;
do
    sudo systemctl start $SRV
    sudo systemctl enable $SRV
    sudo systemctl status $SRV
done
```

修改etcd配置，在设定Node中flannel所使用的子网范围为172.17.1.0~172.17.254.0（每一个Node节点都有一个独立的flannel子网）

```bash
etcdctl mk /coreos.com/network/config '{"Network":"172.17.0.0/16", "SubnetMin": "172.17.1.0", "SubnetMax": "172.17.254.0"}'
```

### Node配置

修改Node节点上flannel的配置`/etc/sysconfig/`flanneld，设定etcd的相关信息，其中192.168.253.7为Master的IP地址。

```bash
FLANNEL_ETCD="http://192.168.253.7:2379"
```

修改Node节点kubernetes的全局配置文件`/etc/kubernetes/config`

```bash
KUBE_LOG_LEVEL="--v=2"
KUBE_MASTER="--master=http://192.168.253.7:8080"
```

修改Node节点上kubernetes kubelet的配置`/etc/kubernetes/kubelet`

```bash
KUBELET_ADDRESS="--address=0.0.0.0"
KUBELET_PORT="--port=10250"
KUBELET_API_SERVER="--api_servers=http://192.168.253.7:8080"
KUBELET_HOSTNAME=""
```

启动Node节点上的相关服务
```bash
#!/bin/bash
for SRV in ntpd flanneld docker kube-proxy kubelet;
do
    sudo systemctl start $SRV
    sudo systemctl enable $SRV
    sudo systemctl status $SRV
done
```

类似Master节点，如果每一个服务都启动成功，那么sudo systemctl status $SRV显示的信息则包含Active: active (running)。

配置步骤都是参考[美团云在CentOS7上部署Kubernetes集群](https://mos.meituan.com/library/37/how-to-setup-k8s-cluster-on-CentOS7/)。
不巧的是，我在这一步上花了1周的时间。。。。Node节点上的flanneld和docker服务死活起不起来。
通过`systemctl status flanneld`，发现一直报错`Failed to retrieve network config`。

**解决方法**：

修改Node节点上的flanneld配置 `/etc/sysconfig/flanneld `

```bash
FLANNEL_ETCD_KEY="/coreos.com/network"
```

然后重新启动flanneld和docker服务即可。

### 测试

在Master上查看节点信息

```bash
[vagrant@master ~]$ kubectl get nodes
NAME              STATUS    AGE
worker1.vagrant   Ready     4m
worker2.vagrant   Ready     10s
```

在Master节点查看flannel子网分配情况

```bash
[vagrant@master ~]$ etcdctl ls /coreos.com/network/subnets
/coreos.com/network/subnets/172.17.29.0-24
```

### Guestbook部署到k8s中

[Guestbook example](https://github.com/kubernetes/kubernetes/blob/release-1.2/examples/guestbook/README.md)

部署的过程中遇到2个问题

1. 通过`kubectl create -f xxx.yaml`创建pod显示成功，但是通过 `kubectl get pod`命令确查询不到任何pod信息。解决方案可以参考：[ kubenetes无法创建pod/创建RC时无法自动创建pod的问题](http://www.voidcn.com/blog/jinzhencs/article/p-5975011.html)

2. 解决了上面的问题后，可以get到pod信息，但是node节点通过查询docker images并未发现任何镜像，原因是因为国内的网络问题，无法下载到镜像所致。可以手动pull
镜像：`registry.access.redhat.com/rhel7/pod-infrastructure`以及你所需的其他镜像。

### 总结

这里的集群部署参考的是美团云分享的。都只是练手用的。

k8s部署还可以更简单，一键部署。使用vagrant+coreOs，安装完虚拟机后，Master节点c1会自动下在k8s所需的环境，奈何大陆的程序员比较苦逼，有墙的存在。在翻墙的情况下可以尝试一键部署k8s集群。详情请移步[要翻才能看的到](https://coreos.com/kubernetes/docs/latest/kubernetes-on-vagrant.html)。
