---
title: 使用kubeadm在CentOS7上安装Kubernetes集群
toc: true
date: 2016-09-27 10:52:03
tags: 
    - Docker
    - K8s
---

Kubernetes1.4版本提供kubeadm命令进行简化k8s集群的安装，只要使用2个简单命令就可以完成安装。 安装kubernetes以后，使用`kubeadm init`启动master，使用`kubeadm joins`把node添加到集群里。下面是根据官方博客[Installing Kubernetes on Linux with kubeadm](http://kubernetes.io/docs/getting-started-guides/kubeadm/)练习的记录。

#### 使用vagrant创建两个centos7
在k8s-centos7-cluster文件夹下创建Vagrantfile文件。Vagrantfile配置如下：
``` ruby 
# -*- mode: ruby -*-
# vi: set ft=ruby :

boxes = [
  { :name => :master,:ip => '192.168.1.20',:forward => 80,:cpus => 1,:mem => 1024},
  { :name => :node1,:ip => '192.168.1.21',:forward => 80,:cpus => 1,:mem => 1024},
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
            #config.vm.network "forwarded_port", guest: 80, host: 8080
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

在Vagrantfile文件目录下，创建两个CentOS7系统，一个作为master，一个作为node
```bash
➜  k8s-centos7-cluster vagrant up
Bringing machine 'master' up with 'virtualbox' provider...
Bringing machine 'node1' up with 'virtualbox' provider...
...
```

Ok，下面我们开始在CentOS7中安装k8s集群

#### 安装kubelet和kuebadm

在所有的机子上都必须安装docker，kubelet，kubectl，kubeadm，无论是master还是node。并且使用root权限进行安装。

```bash
➜  k8s-centos7-cluster vagrant ssh master #输入密码登录master
[vagrant@master ~]$ 
[vagrant@master ~]$ sudo su -
```

登录master，切换到root用户，然后执行下面的命令:

```bahs
# cat <<EOF > /etc/yum.repos.d/k8s.repo
[kubelet]
name=kubelet
baseurl=http://files.rm-rf.ca/rpms/kubelet/
enabled=1
gpgcheck=0
EOF
# yum install docker kubelet kubeadm kubectl kubernetes-cni
# systemctl enable docker && systemctl start docker
# systemctl enable kubelet && systemctl start kubelet
```

等待下载后安装。
安装完成后，可以使用`systemctl status` 查看安装好的组件服务状态。

```bash
[root@master ~]# systemctl status docker
● docker.service - Docker Application Container Engine
   Loaded: loaded (/usr/lib/systemd/system/docker.service; enabled; vendor preset: disabled)
   Active: active (running) since Tue 2016-09-27 04:57:44 UTC; 41s ago
```

#### 初始化master

在master上运行控制组件，包含etcd（集群的数据库），API server（kubectl 客户端沟通用）。这些组件都在pod中通过kubelet启动运行。
初始化master，选择上面实现安装过kubelet和kubeadm的主机，然后运行下面的命令：

```bash
[root@master ~]# kubeadm init --use-kubernetes-version v1.4.0-beta.11
```

运行后，会下载安装集群用的数据库和控制组件，需要等待一些时间，输出内容如下：

```bash
<master/tokens> generated token: "88958f.2068ff49c1675f8c"
<master/pki> created keys and certificates in "/etc/kubernetes/pki"
<util/kubeconfig> created "/etc/kubernetes/kubelet.conf"
<util/kubeconfig> created "/etc/kubernetes/admin.conf"
<master/apiclient> created API client configuration
<master/apiclient> created API client, waiting for the control plane to become ready
```

未完待续。。。








