---
title: Java NIO 教程
date: 2016-06-25 13:24:25
tags: Java NIO
toc: true
---

原文地址[Java Reflection](http://tutorials.jenkov.com/java-reflection/classes.html)

Java NIO(New IO) 是java(从java1.4开始) IO API的一个选择,可以代替Java标准IO和Java网络编程API。对于标准的IO来说,Java NIO提供了不同的处理IO的方式。

#### Channels and Buffers

在标准的IO API中,使用字节流和字符流。在NIO中需要用到channels和buffers。数据总是从channel获取读到buffer中,从buffer中获取写入channel。

#### Non-blocking IO

Java NIO 可以非阻塞式的处理IO。比如,一个线程将channel的数据读到buffer。在读的过程中,线程可以做其他事情。一旦数据读取完毕放到buffer中,线程在继续处理。写数据也是一样的操作。

#### Selectors

Java NIO 有个 『selectors』的概念,一个selector就是一个对象,通过事件(比如:连接打开,数据到达等等)监控多个channels。这样,一个单独的线程就可以监控多个channel的数据。

这些都是如何工作的,本系列的下一章 [the Java NIO overview]()会详细描述。