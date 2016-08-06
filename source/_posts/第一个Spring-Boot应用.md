---
title: 第一个Spring Boot应用
date: 2016-02-17 22:32:06
tags:
    - SPRING BOOT
toc: true
---

原文地址：[天码营-SpringBoot参考指南](http://course.tianmaying.com/spring-boot-reference/lesson/lesson-11#0) 本文只是学习笔记

### 准备
让我们使用Java开发一个简单的"Hello World!" web应用，来强调下Spring Boot的一些关键特性。我们将使用Maven构建该项目，因为大多数IDEs都支持它。

注：[spring.io](spring.io)网站包含很多使用Spring Boot的"入门"指南。如果你正在找特定问题的解决方案，可以先去那瞅瞅。

在开始前，你需要打开一个终端，检查是否安装可用的Java版本和Maven：

```bash
➜  ~ java -version
java version "1.8.0_65"
Java(TM) SE Runtime Environment (build 1.8.0_65-b17)
Java HotSpot(TM) 64-Bit Server VM (build 25.65-b01, mixed mode)
➜  ~ mvn -v
Apache Maven 3.3.9 (bb52d8502b132ec0a5a3f4c09453c07478323dc5; 2015-11-11T00:41:47+08:00)
Maven home: /usr/local/Cellar/maven/3.3.9/libexec
Java version: 1.8.0_65, vendor: Oracle Corporation
Java home: /Library/Java/JavaVirtualMachines/jdk1.8.0_65.jdk/Contents/Home/jre
Default locale: zh_CN, platform encoding: UTF-8
OS name: "mac os x", version: "10.11", arch: "x86_64", family: "mac"
```

<!-- more -->

### POM

需要以创建一个Maven pom.xml文件作为开始。该pom.xml是用来构建项目的处方。打开你最喜欢的文本编辑器，然后添加以下内容：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>myproject</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.3.2.BUILD-SNAPSHOT</version>
    </parent>

    <!-- Additional lines to be added here... -->

    <!-- (you don't need this if you are using a .RELEASE version) -->
    <repositories>
        <repository>
            <id>spring-snapshots</id>
            <url>http://repo.spring.io/snapshot</url>
            <snapshots><enabled>true</enabled></snapshots>
        </repository>
        <repository>
            <id>spring-milestones</id>
            <url>http://repo.spring.io/milestone</url>
        </repository>
    </repositories>
    <pluginRepositories>
        <pluginRepository>
            <id>spring-snapshots</id>
            <url>http://repo.spring.io/snapshot</url>
        </pluginRepository>
        <pluginRepository>
            <id>spring-milestones</id>
            <url>http://repo.spring.io/milestone</url>
        </pluginRepository>
    </pluginRepositories>
</project>
```

我的pom文件在springboot文件夹下的，添加内容后可以通过运行mvn package测试它，第一次会很慢，要下载依赖包

```bash
➜  springboot mvn dependency:tree

[INFO] --- maven-dependency-plugin:2.10:tree (default-cli) @ myproject ---
[INFO] com.example:myproject:jar:0.0.1-SNAPSHOT

```

### web依赖

开发一个web应用，我们将添加一个spring-boot-starter-web依赖,编辑pom.xml添加spring-boot-starter-web依赖

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```

再次运行mvn dependency:tree，查看包依赖情况

```bash
[INFO] com.example:myproject:jar:0.0.1-SNAPSHOT
[INFO] \- org.springframework.boot:spring-boot-starter-web:jar:1.3.2.BUILD-SNAPSHOT:compile
[INFO]    +- org.springframework.boot:spring-boot-starter:jar:1.3.2.BUILD-SNAPSHOT:compile
[INFO]    |  +- org.springframework.boot:spring-boot:jar:1.3.2.BUILD-SNAPSHOT:compile
[INFO]    |  +- org.springframework.boot:spring-boot-autoconfigure:jar:1.3.2.BUILD-SNAPSHOT:compile
[INFO]    |  +- org.springframework.boot:spring-boot-starter-logging:jar:1.3.2.BUILD-SNAPSHOT:compile
[INFO]    |  |  +- ch.qos.logback:logback-classic:jar:1.1.3:compile
[INFO]    |  |  |  +- ch.qos.logback:logback-core:jar:1.1.3:compile
[INFO]    |  |  |  \- org.slf4j:slf4j-api:jar:1.7.13:compile
[INFO]    |  |  +- org.slf4j:jcl-over-slf4j:jar:1.7.13:compile
[INFO]    |  |  +- org.slf4j:jul-to-slf4j:jar:1.7.13:compile
[INFO]    |  |  \- org.slf4j:log4j-over-slf4j:jar:1.7.13:compile
[INFO]    |  +- org.springframework:spring-core:jar:4.2.4.RELEASE:compile
[INFO]    |  \- org.yaml:snakeyaml:jar:1.16:runtime
[INFO]    +- org.springframework.boot:spring-boot-starter-tomcat:jar:1.3.2.BUILD-SNAPSHOT:compile
[INFO]    |  +- org.apache.tomcat.embed:tomcat-embed-core:jar:8.0.30:compile
[INFO]    |  +- org.apache.tomcat.embed:tomcat-embed-el:jar:8.0.30:compile
[INFO]    |  +- org.apache.tomcat.embed:tomcat-embed-logging-juli:jar:8.0.30:compile
[INFO]    |  \- org.apache.tomcat.embed:tomcat-embed-websocket:jar:8.0.30:compile
[INFO]    +- org.springframework.boot:spring-boot-starter-validation:jar:1.3.2.BUILD-SNAPSHOT:compile
[INFO]    |  \- org.hibernate:hibernate-validator:jar:5.2.2.Final:compile
[INFO]    |     +- javax.validation:validation-api:jar:1.1.0.Final:compile
[INFO]    |     +- org.jboss.logging:jboss-logging:jar:3.3.0.Final:compile
[INFO]    |     \- com.fasterxml:classmate:jar:1.1.0:compile
[INFO]    +- com.fasterxml.jackson.core:jackson-databind:jar:2.6.5:compile
[INFO]    |  +- com.fasterxml.jackson.core:jackson-annotations:jar:2.6.5:compile
[INFO]    |  \- com.fasterxml.jackson.core:jackson-core:jar:2.6.5:compile
[INFO]    +- org.springframework:spring-web:jar:4.2.4.RELEASE:compile
[INFO]    |  +- org.springframework:spring-aop:jar:4.2.4.RELEASE:compile
[INFO]    |  |  \- aopalliance:aopalliance:jar:1.0:compile
[INFO]    |  +- org.springframework:spring-beans:jar:4.2.4.RELEASE:compile
[INFO]    |  \- org.springframework:spring-context:jar:4.2.4.RELEASE:compile
[INFO]    \- org.springframework:spring-webmvc:jar:4.2.4.RELEASE:compile
[INFO]       \- org.springframework:spring-expression:jar:4.2.4.RELEASE:compile
[INFO] ------------------------------------------------------------------------
```

### 代码

创建一个单独的Java文件。Maven默认会编译src/main/java下的源码，所以你需要创建那样的文件结构，然后添加一个名为src/main/java/Example.java的文件

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@EnableAutoConfiguration
public class Example {

    @RequestMapping("/")
    String home(){
        return "Hello World!";
    }

    public static void main(String[] args) {
        SpringApplication.run(Example.class,args);
    }
}
```

### @RestController

It’s a very common use case to have Controllers implement a REST API, thus serving only JSON, XML or custom MediaType content. For convenience, instead of annotating all your @RequestMapping methods with @ResponseBody, you can annotate your Controller Class with @RestController.

@RestController is a stereotype annotation that combines @ResponseBody and @Controller. More than that, it gives more meaning to your Controller and also may carry additional semantics in future releases of the framework.

### @EnableAutoConfiguration

这个注解告诉Spring Boot根据添加的jar依赖猜测你想如何配置Spring。由于spring-boot-starter-web添加了Tomcat和Spring MVC，所以auto-configuration将假定你正在开发一个web应用并相应地对Spring进行设置。

很多Spring Boot开发者总是使用@Configuration，@EnableAutoConfiguration和@ComponentScan注解他们的main类。由于这些注解被如此频繁地一块使用，Spring Boot提供一个方便的@SpringBootApplication选择。

该@SpringBootApplication注解等价于以默认属性使用@Configuration，@EnableAutoConfiguration和@ComponentScan。

```java
package com.example.myproject;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication // same as @Configuration @EnableAutoConfiguration @ComponentScan
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

}
```

### mian

这只是一个标准的方法，它遵循Java对于一个应用程序入口点的约定。我们的main方法通过调用run，将业务委托给了Spring Boot的SpringApplication类。SpringApplication将引导我们的应用，启动Spring，相应地启动被自动配置的Tomcat web服务器。我们需要将Example.class作为参数传递给run方法来告诉SpringApplication谁是主要的Spring组件。为了暴露任何的命令行参数，args数组也会被传递过去

### 运行

到此我们的应用应该可以工作了。由于使用了spring-boot-starter-parent POM，这样我们就有了一个非常有用的run目标，我们可以用它启动程序。在项目根目录下输入mvn spring-boot:run来启动应用：

```bash
➜  springboot mvn spring-boot:run

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::  (v1.3.2.BUILD-SNAPSHOT)

2016-02-20 18:57:49.387  INFO 2800 --- [           main] Example                                  : Starting Example on larrydeMacBook-Pro.local with PID 2800 (/Users/larry/IdeaProjects/java/springboot/target/classes started by larry in /Users/larry/IdeaProjects/java/springboot)

2016-02-20 18:57:50.116  INFO 2800 --- [           main] s.b.c.e.t.TomcatEmbeddedServletContainer : Tomcat initialized with port(s): 8080 (http)
2016-02-20 18:57:50.124  INFO 2800 --- [           main] o.apache.catalina.core.StandardService   : Starting service Tomcat
2016-02-20 18:57:50.125  INFO 2800 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet Engine: Apache Tomcat/8.0.30
2016-02-20 18:57:50.298  INFO 2800 --- [ost-startStop-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2016-02-20 18:57:50.298  INFO 2800 --- [ost-startStop-1] o.s.web.context.ContextLoader            : Root WebApplicationContext: initialization 

AnnotationMBeanExporter        : Registering beans for JMX exposure on startup
2016-02-20 18:57:50.887  INFO 2800 --- [           main] s.b.c.e.t.TomcatEmbeddedServletContainer : Tomcat started on port(s): 8080 (http)
2016-02-20 18:57:50.891  INFO 2800 --- [           main] Example                                  : Started Example in 2.096 seconds (JVM running for 4.109)
```

浏览器打开localhost:8080

```
Hello World!
```

### 可执行jar

为了创建可执行的jar，需要将spring-boot-maven-plugin添加到我们的pom.xml中。在dependencies节点下插入以下内容：

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

保存你的pom.xml，然后从命令行运行mvn package：

```bash
➜  springboot mvn package  

[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 38.328 s
[INFO] Finished at: 2016-02-20T19:06:06+08:00
[INFO] Final Memory: 24M/319M
[INFO] ------------------------------------------------------------------------
```

使用`jar tvf` 查看jar包内部结构

```bash
➜  springboot jar tvf target/myproject-0.0.1-SNAPSHOT.jar
     0 Sat Feb 20 19:06:06 CST 2016 META-INF/
   411 Sat Feb 20 19:06:06 CST 2016 META-INF/MANIFEST.MF
   909 Sat Feb 20 18:55:32 CST 2016 Example.class
     0 Sat Feb 20 19:06:06 CST 2016 META-INF/maven/
     0 Sat Feb 20 19:06:06 CST 2016 META-INF/maven/com.example/
     0 Sat Feb 20 19:06:06 CST 2016 META-INF/maven/com.example/myproject/
  1871 Sat Feb 20 19:04:56 CST 2016 META-INF/maven/com.example/myproject/pom.xml
   114 Sat Feb 20 19:06:06 CST 2016 META-INF/maven/com.example/myproject/pom.properties

```

使用`java -jar`运行jar包

```bash
➜  springboot java -jar target/myproject-0.0.1-SNAPSHOT.jar

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::  (v1.3.2.BUILD-SNAPSHOT)

2016-02-20 19:07:17.774  INFO 2849 --- [nio-8080-exec-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring FrameworkServlet 'dispatcherServlet'
2016-02-20 19:07:17.774  INFO 2849 --- [nio-8080-exec-1] o.s.web.servlet.DispatcherServlet        : FrameworkServlet 'dispatcherServlet': initialization started
2016-02-20 19:07:17.785  INFO 2849 --- [nio-8080-exec-1] o.s.web.servlet.DispatcherServlet        : FrameworkServlet 'dispatcherServlet': initialization completed in 11 ms

```

使用`ctrl-c`退出程序
