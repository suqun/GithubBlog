---
title: Java NIO 概述
date: 2016-06-25 13:27:34
tags: Java NIO
---

Java NIO 包含下面三个核心组件:

- Channels
- Buffers
- Selectors
    
Java NIO 还有很多其他的类和组件,Channel,Buffer,Selector是核心的API。其他的组件,诸如Pipe、FileLock仅仅只是这3个核心组组件的实际应用。

#### Channels  and Buffers

典型的,NIO中的所有IO都是起始于Channel。Channel有点像流。可以将Channel中数据读到Buffer里,也可以将Buffer里的数据写入Channel中。说明如下:

![Java NIO: Channels read data into Buffers, and Buffers write data into Channels](http://tutorials.jenkov.com/images/java-nio/overview-channels-buffers.png)
**Java NIO: Channels read data into Buffers, and Buffers write data into Channels**

Java NIO中主要的Channel实现类有：

- FileChannel
- DatagramChannel
- SocketChannel
- ServerSocketChannel

可以看出,这些channels覆盖了UDP+TCP的网络IO,以及文件IO。

Java NIO中主要的Buffer实现类有:

- ByteBuffer
- CharBuffer
- DoubleBuffer
- FloatBuffer
- IntBuffer
- LongBuffer
- ShortBuffer

这些实现类也包含了通过IO发送数据所需要的基本的数据类型：byte、short、int、long、float、double、Char型。

Java NIO也有 MappedByteBuffer类型，用于表示内存映射文件。

#### Selectors

一个Selectors允许一个单线程同时处理多个Channel。如果应用中有很多打开的连接（Channels）这么做是很方便的，但是每个连接的流量都很低。比如，聊天服务器。

下面是一个Selector处理3个Channel的说明：

![Java NIO: A Thread uses a Selector to handle 3 Channel's](http://tutorials.jenkov.com/images/java-nio/overview-selectors.png)
**Java NIO: A Thread uses a Selector to handle 3 Channel's**

先将Channels注册到Selector中，然后调用他的select()方法。这个方法会阻塞直到有注册的channel相应的事件触发。一旦这个方法返回，线程就可以处理这个事件。比如正在打开的连接，获取到数据的事件等等。














