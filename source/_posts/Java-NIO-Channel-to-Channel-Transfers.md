---
title: Java NIO Channel之间的数据传输
toc: true
date: 2016-11-10 11:05:11
tags: Java NIO
---

原文：[http://tutorials.jenkov.com/java-nio/channel-to-channel-transfers.html](http://tutorials.jenkov.com/java-nio/channel-to-channel-transfers.html)

在Java NIO 中channel之间可以直接相互传输。比如一个FileChannel类型的channel，FileChannel类提供`transferTo()`和`transferFrom()`两个方法做这个事情。

## transferFrom()

`FileChannel.transferFrom()`方法将一个channel的数据传入FileChannel。

代码🌰：

```java
RandomAccessFile fromFile = new RandomAccessFile("fromFile.txt", "rw");
FileChannel      fromChannel = fromFile.getChannel();

RandomAccessFile toFile = new RandomAccessFile("toFile.txt", "rw");
FileChannel      toChannel = toFile.getChannel();

long position = 0;
long count    = fromChannel.size();

toChannel.transferFrom(fromChannel, position, count);
```

position参数确定目标文件的传输初始位置，count参数确定传输的最大数量。channel中的字节数若是少于count，就读取全部字节。
另外，SocketChannel传输的是其内部此时此处的就绪的数据（SocketChannel后续可能还会有更多的可用数据）。因此，从SocketChannel传输数据时，有可能不能把全部的请求数据（不足count的数据）都传入FileChannel中。

## transferTo()

`transferTo()`方法将FileChannel数据传入其他的channel中。

代码🌰：

```java
RandomAccessFile fromFile = new RandomAccessFile("fromFile.txt", "rw");
FileChannel      fromChannel = fromFile.getChannel();

RandomAccessFile toFile = new RandomAccessFile("toFile.txt", "rw");
FileChannel      toChannel = toFile.getChannel();

long position = 0;
long count    = fromChannel.size();

fromChannel.transferTo(position, count, toChannel);
```

和上面的例子很相似。区别在于调用方法的FileChannel对象不一样。

关于SocketChannel的问题在transferTo()方法中同样存在。SocketChannel会一直传输数据直到目标buffer被填满。


