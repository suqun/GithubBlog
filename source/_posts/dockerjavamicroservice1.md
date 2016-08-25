---
title: 基于Docker的java微服务(一) 部署Chris Richardson的转账案例
toc: true
date: 2016-08-24 19:23:15
tags: 
    - DOCKER
    - SPRING BOOT 
    - CQRS
    - Event-Sourcing
---

### 概述

本文主要参考[使用DCHQ自动部署和管理基于Docker的云/虚拟化环境Java微服务](http://www.infoq.com/cn/articles/Automate-Docker-Cloud-Java-Microservice-Deployment-with-DCHQ)

最近在学习微服务,前两周了解基于Spring Cloud的微服务框架,这两天开始看看关于Docker的微服务。

Spring Cloud整合了Netflix开源的Eureka,Hystrix,Ribbon,Feign,ZUUL等,
是一个基于Spring Boot实现的云应用开发工具，它为基于JVM的云应用开发中的配置管理、服务发现、断路器、智能路由、微代理、控制总线、全局锁、决策竞选、
分布式会话和集群状态管理等操作提供了一种简单的开发方式([参考DIDI](http://blog.didispace.com/springcloud1/))。

那么Docker的微服务是什么样的呢。有空的同学可以看看Spring Cloud和Docker的比较
[Netflix OSS、Spring Cloud还是Kubernetes?](http://www.infoq.com/cn/articles/netflix-oss-spring-cloud-kubernetes?utm_campaign=rightbar_v2&utm_source=infoq&utm_medium=articles_link&utm_content=link_text)。
简单的说,就是基于Docker的调度器Kubernetes可以帮忙把大家从服务发现、负载均衡、容错等功能中解放出来,更专注于业务逻辑开发。

Kubernetes是个什么鬼?要了解它,我们得先了解下,我们开发好的项目是怎么在Docker上部署应用的,多个Docker容器又是如何管理的。
这篇文章是Chris Richardson针对事件溯源、CQRS和Docker所创建的转账[案例](https://github.com/cer/event-sourcing-examples)。

案例主要功能如下:

- 基于一个初始的余额，创建新账户
- 查询某个账户，得到其余额
- 从一个账户到另一个账户进行转账

我们就用这个例子,来了解下整个开发部署流程(**仅仅是了解**)。案例的具体业务逻辑介绍请移步[event-sourcing-examples](https://github.com/cer/event-sourcing-examples)

### 获取Event Store凭证

架构使用事件驱动的方式来确保数据的一致性,这里面使用的是Event Store。

在使用之前,需要获取Event Store凭证。
进入[Event Store](http://eventuate.io/)官网,注册个账号,过几个小时一般就会收到来自[chris](chris@chrisrichardson.net)的邮件。
邮件中有EVENTUATE_API_KEY_ID和EVENTUATE_API_KEY_SECRET,这个在后面的yml模板里会用到。

### gradle构建

从[event-sourcing-examples](https://github.com/cer/event-sourcing-examples)clone项目到本地。
 
直接下载下来的例子，部署测试的时候，会报几个错误，需要对代码做部分修改

- 修改 xx-xx-side-service模块中build.gradle文件,添加如下内容
```gradle
jar {
    manifest {
        attributes 'Main-Class': 'net.chrisrichardson.eventstore.javaexamples.banking.web.main.XxxxSideServiceMain'
    }
}
```
添加这个解决部署时遇到的找不到manifest错误

- xx-xx-side-service主方法添加注解@SpringBootApplication

主方法没有@SpringBootApplication这个注解，是无法启动spring boot滴。

修改后，使用gradle的assemble命令构建，构建成功后，模块的/build/libs会生成jar包。

### docker-compose
gradle构建完毕后，我们要把service模块的jar包放到一个docker镜像中，然后启动这个docker。
这里使用了docker-compose来生成启动镜像。

docker-compose的安装及介绍，请移步：[Docker Compose 项目](https://yeasy.gitbooks.io/docker_practice/content/compose/)

#### docker-compose.yml
docker-compose管理调度docker容器默认是根据docker-compose.yml模板进行的。这个模板里定义了生成镜像部署镜像的一些步骤。

本案例的yml如下：

```yml
accountscommandside:
  image: openjdk:8u92-jdk-alpine
  working_dir: /app
  volumes:
    - ./accounts-command-side-service/build/libs:/app
  command: java -jar /app/accounts-command-side-service.jar
  ports:
    - "8080:8080"
  environment:
    EVENTUATE_API_KEY_ID: 5NJSVTRJ6UTYVL8U4RN8TKDRM
    EVENTUATE_API_KEY_SECRET: fiAKWYEEj7EVxNi6yKXF8WDcVLbYA8Cu5RnFFKjwVOw

transactionscommandside:
  image: openjdk:8u92-jdk-alpine
  working_dir: /app
  volumes:
    - ./transactions-command-side-service/build/libs:/app
  command: java -jar /app/transactions-command-side-service.jar
  ports:
    - "8082:8080"
  environment:
    EVENTUATE_API_KEY_ID: 5NJSVTRJ6UTYVL8U4RN8TKDRM
    EVENTUATE_API_KEY_SECRET: fiAKWYEEj7EVxNi6yKXF8WDcVLbYA8Cu5RnFFKjwVOw


accountsqueryside:
  image: openjdk:8u92-jdk-alpine
  working_dir: /app
  volumes:
    - ./accounts-query-side-service/build/libs:/app
  command: java -jar /app/accounts-query-side-service.jar
  ports:
    - "8081:8080"
  links:
    - mongodb
  environment:
    EVENTUATE_API_KEY_ID: 5NJSVTRJ6UTYVL8U4RN8TKDRM
    EVENTUATE_API_KEY_SECRET: fiAKWYEEj7EVxNi6yKXF8WDcVLbYA8Cu5RnFFKjwVOw
    SPRING_DATA_MONGODB_URI: mongodb://mongodb/mydb

mongodb:
  image: mongo:3.2.9
  hostname: mongodb
  command: mongod --smallfiles
  ports:
    - "27017:27017"
```

这里面定义了4个容器内容，分别是accountscommandside，transactionscommandside，accountsqueryside，mongodb

- image：指定为镜像名称或镜像 ID。如果镜像在本地不存在，Compose 将会尝试拉取这个镜像
- volumes：卷挂载路径设置，这里是将容器的/app路径挂载到宿主机/build/libs 路径上
- command：覆盖容器启动后默认执行的命令，这里是默认直接启动spring boot项目
- ports：暴露端口信息，格式如下
    - 宿主：容器 （HOST:CONTAINER）
    - 容器（宿主会随机选择端口）
- links：链接到其它服务中的容器。使用服务名称（同时作为别名）或服务名称：服务别名 （SERVICE:ALIAS）格式都可以。 
    - db
    - db:database
    - redis
- environment：设置环境变量

替换成你自己的EVENTUATE_API_KEY_ID和EVENTUATE_API_KEY_SECRET，否则部署后运行测试，会报401未授权错误。

#### docker-compse up

设置好yml模板以后，使用docker-compse up来启动这4个容器

```bash
➜  java-spring git:(master) ✗ docker-compose up
```

再开个shell，看下启动的四个容器的状态

```bash
➜  java-spring git:(master) ✗ docker-compose ps
                Name                              Command               State            Ports
--------------------------------------------------------------------------------------------------------
javaspring_accountscommandside_1       java -jar /app/accounts-co ...   Up      0.0.0.0:8080->8080/tcp
javaspring_accountsqueryside_1         java -jar /app/accounts-qu ...   Up      0.0.0.0:8081->8080/tcp
javaspring_mongodb_1                   /entrypoint.sh mongod --sm ...   Up      0.0.0.0:27017->27017/tcp
javaspring_transactionscommandside_1   java -jar /app/transaction ...   Up      0.0.0.0:8082->8080/tcp
```
四个状态都是up，好了，访问服务测试下。

### 测试 

先创建2个账户，每个都初始500美元
```bash
➜  java-spring git:(master) ✗ curl -X POST -H "Content-Type: application/json" -H "Cache-Control: no-cache" -d '{"initialBalance": 500}' "http://localhost:8080/accounts"
{"accountId":"00000156bfc1c044-0242ac1100460000"}%                                                                            
➜  java-spring git:(master) ✗ curl -X POST -H "Content-Type: application/json" -H "Cache-Control: no-cache" -d '{"initialBalance": 500}' "http://localhost:8080/accounts"
{"accountId":"00000156bfc1da88-0242ac1100960000"}%                                                                                                       
```

根据账户ID查询
```bash
➜  java-spring git:(master) ✗ curl -X GET "http://localhost:8081/accounts/00000156bfc1c044-0242ac1100460000"
{"accountId":"00000156bfc1c044-0242ac1100460000","balance":50000}%                                                        
➜  java-spring git:(master) ✗ curl -X GET "http://localhost:8081/accounts/00000156bfc1da88-0242ac1100960000"
{"accountId":"00000156bfc1da88-0242ac1100960000","balance":50000}%
```

可以看到每个账户里都有50000美分。试试转账，然后再查询
```bash
➜  java-spring git:(master) ✗ curl -X POST -H "Content-Type: application/json" -d '{"fromAccountId" : "00000156bfc1c044-0242ac1100460000", "toAccountId" : "00000156bfc1da88-0242ac1100960000", "amount" : 500}' "http://localhost:8082/transfers"
{"moneyTransferId":"00000156bfc75387-0242ac1100ac0000"}%                                                                                                                                                    
➜  java-spring git:(master) ✗ curl -X GET "http://localhost:8081/accounts/00000156bfc1da88-0242ac1100960000"
{"accountId":"00000156bfc1da88-0242ac1100960000","balance":50000}%                                                                                                                                          
➜  java-spring git:(master) ✗ curl -X GET "http://localhost:8081/accounts/00000156bfc1c044-0242ac1100460000"
{"accountId":"00000156bfc1c044-0242ac1100460000","balance":0}%                                                                                                                                              
➜  java-spring git:(master) ✗ curl -X GET "http://localhost:8081/accounts/00000156bfc1da88-0242ac1100960000"
{"accountId":"00000156bfc1da88-0242ac1100960000","balance":100000}%
```

可以看到中间有个状态是不对的，这个基于事件驱动的，还没有自己看，应该是有延迟，后来再查询就是准确的了。事件驱动的后面会专门看看再整理篇文章。

### 结语

本篇主要描述了如何使用docker-compose构建基于docker的java微服务框架。后续会对里面的知识点做些详细的学习。



