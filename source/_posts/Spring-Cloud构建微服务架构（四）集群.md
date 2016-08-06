---
title: Spring Cloud构建微服务架构（四）集群
date: 2016-08-05 10:07:59
tags: 
    - SPRING BOOT
    - SPRING CLOUD
    - NETFLIX
    - EUREKA
    - RIBBON
toc: true
---

前面三篇都分享自[程序猿DD](http://blog.didispace.com/)的博客,暂时(2016年08月05日)还没有更新关于Eureka集群的博客。
这里参考了[木木彬](https://segmentfault.com/blog/mumubin)的[Spring Cloud实战(二)-Spring Cloud Eureka](https://segmentfault.com/a/1190000006149891)博客内容。
对集群配置简单记录下,方便以后查阅。同时也期待`程序猿DD`更新更多更精彩的博客。

本文代码基于[程序猿DD / SpringBoot-Learning / Chapter9-1-3](https://git.oschina.net/didispace/SpringBoot-Learning/tree/master/Chapter9-1-3?dir=1&filepath=Chapter9-1-3&oid=cc93af44af30b42320041332790d071ed72a2450&sha=4329c564d673b6cf7a53893ad9770abb7a67b328)进行集群配置。

Spring Cloud 官方文档上对集群配置介绍的非常简单，对Eureka Server进行[Peer Awareness](http://cloud.spring.io/spring-cloud-static/docs/1.0.x/spring-cloud.html#_peer_awareness)配置，这样多个服务端就可以关联到一起。好了，下面看看具体的配置。

#### hosts修改

在hosts（路径：/etc/hosts）文件中添加

```
  127.0.0.1       eureka-primary
  127.0.0.1       eureka-secondary
  127.0.0.1       eureka-tertiary
```

#### 服务端配置

先注释掉application.properties中的配置，添加application.yml，在yml添加多个profiles,和instanceId，此时Eureka Server 同时也是个Eureka Client,需要设置eureka.client.serviceUrl.defaultZone,值是另外两个（这就是官网所说的`Peer Awareness`）:

```yml
---
spring:
  application:
    name: eureka-server-clustered
  profiles: primary
server:
  port: 1111
eureka:
  instance:
    hostname: eureka-primary
  client:
    registerWithEureka: true
    fetchRegistry: true
    serviceUrl:
      defaultZone: http://eureka-secondary:1112/eureka/,http://eureka-tertiary:1113/eureka/
---
spring:
  application:
    name: eureka-server-clustered
  profiles: secondary
server:
  port: 1112
eureka:
  instance:
    hostname: eureka-secondary
  client:
    registerWithEureka: true
    fetchRegistry: true
    serviceUrl:
      defaultZone: http://eureka-secondary:1111/eureka/,http://eureka-tertiary:1113/eureka/
---
spring:
  application:
    name: eureka-server-clustered
  profiles: tertiary
server:
  port: 1113
eureka:
  instance:
    hostname: eureka-tertiary
  client:
    registerWithEureka: true
    fetchRegistry: true
    serviceUrl:
      defaultZone: http://eureka-secondary:1111/eureka/,http://eureka-tertiary:1112/eureka/
```

#### 服务端启动

配置完成，要分别启动3个Server，分别执行下面的命令即可：

```bash
➜  eureka-server git:(master) ✗ mvn clean && mvn install
➜  eureka-server git:(master) ✗ cd target
➜  target git:(master) ✗ java -Dspring.profiles.active=primary -jar eureka-server-0.0.1-SNAPSHOT.jar
```

```bash
➜  target git:(master) ✗ java -Dspring.profiles.active=secondary -jar eureka-server-0.0.1-SNAPSHOT.jar
```

```bash
➜  target git:(master) ✗ java -Dspring.profiles.active=tertiary -jar eureka-server-0.0.1-SNAPSHOT.jar
```

我们访问其中一个服务地址http://localhost:1111/ 可以看到如下内容，说明服务启动成功：

![Eureka Server](http://7xpk5e.com1.z0.glb.clouddn.com/eureka-server-1.png)

#### 客户端配置

服务端已准备就绪，客户端如何注册到多个服务地址呢？其实在服务端配置defaultZone时，指定多个地址，就告诉我们客户端也这么指定就可以啦。

修改compute-service的application.properties中的defaultZone值

```
#指定微服务的名称后续在调用的时候只需要使用该名称就可以进行服务的访问
spring.application.name=compute-service
#应用端口
server.port=2222
#指定服务注册中心的位置
#eureka.client.serviceUrl.defaultZone=http://localhost:1111/eureka/
eureka.client.serviceUrl.defaultZone=http://eureka-primary:1111/eureka/,http://eureka-secondary:1112/eureka/,http://eureka-tertiary:1113/eureka/
```

#### 启动客户端

客户端默认端口是2222，我们启动2个客户端，另一个端口用2223好了。

```bash
➜  compute-service git:(master) ✗ mvn clean && mvn insatll
➜  compute-service git:(master) ✗ cd target
➜  target git:(master) ✗ java -jar compute-service-0.0.1-SNAPSHOT.jar
```

```bash
➜  target git:(master) ✗ java -DServer.port=2223 -jar compute-service-0.0.1-SNAPSHOT.jar
```

重新查看下http://localhost:1111/

![client starting](http://7xpk5e.com1.z0.glb.clouddn.com/eureka-server-2.png)

2个客户端启动成功了。

#### 测试

启动消费者eureka-ribbon成功后，简单测试下

```bash
➜  ~ curl localhost:3333/add
30%
```

一个客户端也打出了日志

```bash
2016-08-05 11:18:23.554  INFO 5127 --- [nio-2223-exec-1] com.ow.wises.web.ComputeController       : /add, host:192.168.1.145, service_id:compute-service, result:30
```

好了，就先这样了。

