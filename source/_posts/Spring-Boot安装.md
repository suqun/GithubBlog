---
title: Spring Boot安装
date: 2016-02-16 22:54:54
tags: 
    - SpringBoot
    - Spring
---

### JDK

Spring Boot可以跟典型的Java开发工具一块使用或安装为一个命令行工具。不管怎样，你将需要安装Java SDK v1.6 或更高版本。在开始之前，你需要检查下当前安装的Java版本：

```bash
java -version
```

注：尽管Spring Boot兼容Java 1.6，如果可能的话，你应该考虑使用Java最新版本。

### maven

Spring Boot兼容Apache Maven 3.2或更高版本,OSX系统下使用brew安装

```bash
brew install maven
```

<!-- more -->

Spring Boot依赖的groupId为org.springframework.boot。通常你的Maven POM文件需要继承spring-boot-starter-parent，然后声明一个或多个“Starter POMs”依赖。Spring Boot也提供了一个用于创建可执行jars的Maven插件。

典型的pom.xml文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>myproject</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <!-- Inherit defaults from Spring Boot -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.3.0.BUILD-SNAPSHOT</version>
    </parent>

    <!-- Add typical dependencies for a web application -->
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>

    <!-- Package as an executable jar -->
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
    
    <!-- Add Spring repositories -->
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

### Spring Boot CLI

如果你的环境是Mac，并使用Homebrew，想要安装Spring Boot CLI只需如下操作：

```bash
brew tap pivotal/tap
brew install springboot
```

### 测试

下面是一个相当简单的web应用，你可以用它测试你的安装是否成功。创建一个名叫app.groovy的文件：

```java
@RestController
class ThisWillActuallyRun {

    @RequestMapping("/")
    String home() {
        "Hello World!"
    }

}
```

然后简单地从一个shell中运行它：

```bash
spring run app.groovy
```

注：当你首次运行该应用时将会花费一点时间，因为需要下载依赖。后续运行将会快很多。

在你最喜欢的浏览器中打开localhost:8080，然后你应该看到以下输出：

```
Hello World!
```

原文：[Spring Boot参考指南](http://course.tianmaying.com/spring-boot-reference/lesson/lesson-10#0)
