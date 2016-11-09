---
title: Java NIO Scatter / Gather
toc: true
date: 2016-11-09 23:09:19
tags: Java NIO
---

原文：[http://tutorials.jenkov.com/java-nio/scatter-gather.html](http://tutorials.jenkov.com/java-nio/scatter-gather.html)
 
Java NIO 从一开始就内嵌了scatter/gather的支持。scatter/gather是从channel中读取写入的操作概念。

**scatter**：从channel中将数据读到多个buffers中的操作。也就是说，channel的分散器将channel中的数据分散到多个buffers。

**gather**：将多个buffers中的数据写入一个channel中的操作。也就是说，channel的收集器，将多个buffers中的数据收集到channel中。

scatter/gatter经常用于需要将传输的数据分开处理的场合。比如，一个信息包含head和body，你可能会将消息体和消息头分散到不同的buffer中，这样你可以方便的处理消息头和消息体。

# Scattering Reads
 
Scattering Reads，将单个channel中的数据读到多个buffers中，下面是原理图示： 

![java-nio-scatter-read](http://7xpk5e.com1.z0.glb.clouddn.com/java-nio-scatter-read.png)

代码🌰：

```java
ByteBuffer header = ByteBuffer.allocate(128);
ByteBuffer body   = ByteBuffer.allocate(1024);

ByteBuffer[] bufferArray = { header, body };
channel.read(bufferArray);
```

注意buffer首先被插入到数组，然后再将数组作为channel.read() 的输入参数。read()方法按照buffer在数组中的顺序将从channel中读取的数据写入到buffer，当一个buffer被写满后，channel紧接着向另一个buffer中写。

Scattering Reads在移动下一个buffer前，必须填满当前的buffer，这也意味着它不适用于动态大小消息。换句话说，如果存在消息头和消息体，消息头必须完成填充（例如填满128byte），Scattering Reads才能正常工作。

# Gathering Writes

Gathering Writes：将多个buffers中的数据写入单个channel，下面是原理图示：

![Gathering Write](http://7xpk5e.com1.z0.glb.clouddn.com/java-nio-gather.png)

代码🌰：
```java
ByteBuffer header = ByteBuffer.allocate(128);
ByteBuffer body   = ByteBuffer.allocate(1024);

//write data into buffers
ByteBuffer[] bufferArray = { header, body };

channel.write(bufferArray);
```

The array of buffers are passed into the write() method, which writes the buffers数组是write()方法的入参，write()方法会按照buffer在数组中的顺序，将数据写入到channel，注意只有position和limit之间的数据才会被写入。因此，如果一个buffer的容量为128byte，但是仅仅包含58byte的数据，那么这58byte的数据将被写入到channel中。因此与Scattering Reads相反，Gathering Writes能较好的处理动态消息。
