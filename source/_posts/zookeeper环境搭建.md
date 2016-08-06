---
title: zookeeper环境搭建
date: 2015-12-28 22:40:00
tags: 
    - ZOOKEEPER
toc: true
---

#### 集群模式安装
- 设置3台CentOS虚拟机192.168.1.105/106/107 网络模式桥接
- 安装zookeeper到opt目录中
    * cd /opt
    * wget zookeeperURL
    * tar xzvf zookeeper_3.**.gz
- 安装JDK环境

<!-- more -->

- 配置
    * cd zookeeper/conf/
    * cp zoo_sample.cfg zoo.cfg
    * vim zoo.cfg
        - tickTime=2000
        - dataDir=/var/zookeeper
        - clientPort=2181
        - 配置服务器 server.id=host:port:port
        - server.1=192.168.1.105:2888:3888
        - server.2=192.168.1.106:2888:3888
        - server.3=192.168.1.107:2888:3888
        - 保存退出
- 将zoo.cfg 负责到其他2台服务器上
    * scp zoo.cfg root@192.168.1.106:/opt/zookeeper/conf
    * scp zoo.cfg root@192.168.1.107:/opt/zookeeper/conf
- 在var目录下创建zookeeper目录
    * cd /var && mkdir zookeeper
- 在var/zookeeper目录下创建myid文件,内容为server.id的id值
    * vim myid
    * 1
    * wq!
    * 其他2台服务器响应创建写入2，3
- 启动zookeeper
    * cd /opt/zookeeper
    * ./zkServer.sh start
    * 测试连接 telnet 192.168.105 2181 (yum install telnet)
    * stat (显示当前服务器不能对外提供服务，需要其他其他2台)

#### 伪集群模式安装
1. 配置
    * cd zookeeper/conf/
    * cp zoo_sample.cfg zk1.cfg
    * vim zk1.cfg
        - tickTime=2000
        - dataDir=/var/zookeeper/zk1
        - clientPort=2181
        - server.1=192.168.1.105:2888:3888
        - server.2=192.168.1.105:2889:3889
        - server.3=192.168.1.105:2890:3890
    * cp zk1.cfg zk2.cfg
        - tickTime=2000
        - dataDir=/var/zookeeper/zk2
        - clientPort=2182
        - server.1=192.168.1.105:2888:3888
        - server.2=192.168.1.105:2889:3889
        - server.3=192.168.1.105:2890:3890
    * cp zk1.cfg zk3.cfg
        - tickTime=2000
        - dataDir=/var/zookeeper/zk3
        - clientPort=2183
        - server.1=192.168.1.105:2888:3888
        - server.2=192.168.1.105:2889:3889
        - server.3=192.168.1.105:2890:3890
    * 在var/zookeeper/zk1(zk2,zk3)目录下创建myid文件,内容为server.id的id值
        - vim myid
        - 1
        - wq!
        
2. 启动zookeeper
    * cd /opt/zookeeper
    * ./zkServer.sh start zk1.cfg
    * ./zkServer.sh start zk2.cfg
    * ./zkServer.sh start zk3.cfg
    * 查看节点状态
        - ./zkServer.sh status zk1.cfg
        - ./zkServer.sh status zk2.cfg 
        - ./zkServer.sh status zk3.cfg 
        
#### 单机模式安装
- 保留一台服务器，其他同集群安装配置
    - server.1=192.168.1.105:2888:3888
