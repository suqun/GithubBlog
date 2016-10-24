---
title: Java NIO Buffer
date: 2016-06-28 22:10:31
tags: Java NIO
toc: true
---

Java NIO Buffers是和NIO的Channels交互使用的。你知道的，数据是从Channel中读到Buffer里，数据从Buffer里写入到Channel中。 

Buffer本质上是可以读写数据的内存块。这个内存块被NIO的Buffer对象包裹，然后提供很多方法以便能够简单的操作这个内存块。

#### Basic Buffer Usage

使用Buffer读写数据基本上就4步：

1. 数据写入Buffer
2. 调用 buffer.flip()
3. 从Buffer中读出数据
4. 调用buffer.clear()或者buffer.compact()方法


