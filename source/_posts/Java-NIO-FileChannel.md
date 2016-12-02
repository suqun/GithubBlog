---
title: Java NIO FileChannel
toc: true
date: 2016-11-22 00:14:33
tags: Java NIO
---

原文：[http://tutorials.jenkov.com/java-nio/file-channel.html]{http://tutorials.jenkov.com/java-nio/file-channel.html}

Java NIO中的FileChannel是一个连接文件的channel，可以使用它从文件中读取数据或向文件中写入数据。Java NIO的FileChannel是NIO的一个选择相对标准的Java IO API来说。
 
FileChannel可以设置成非阻塞模式，但是仍然会按照阻塞模式运行。

## 打开FileChannel
在使用FileChannel时需要先打开它，但是不能直接打开。需要借助InputStream,OutputStream或者是RandomAccessFile。

举个🌰： 

```java
RandomAccessFile aFile     = new RandomAccessFile("data/nio-data.txt", "rw");
FileChannel      inChannel = aFile.getChannel();
```

## 从FileChannel读数据

从FileChannel读数据可以调用`read()`方法。

举个🌰：

```java
ByteBuffer buf = ByteBuffer.allocate(48);

int bytesRead = inChannel.read(buf);
```

首先给Buffer分配字节， FileChannel中的数据就是读到Buffer中。

其次，再调用`FileChannel.read()`方法。从FileChannel中将数据读入buffer。read()的整型返回值告诉我们已经写入Buffer的字节量。如果返回`-1`,就是读到了文件的结尾。

## FileChannel写入数据

Writing data to a FileChannel is done using the FileChannel.write() method, which takes a Buffer as parameter. Here is an example:

```java
String newData = "New String to write to file..." + System.currentTimeMillis();

ByteBuffer buf = ByteBuffer.allocate(48);
buf.clear();
buf.put(newData.getBytes());

buf.flip();

while(buf.hasRemaining()) {
    channel.write(buf);
}
```

Notice how the FileChannel.write() method is called inside a while-loop. There is no guarantee of how many bytes the write() method writes to the FileChannel. Therefore we repeat the write() call until the Buffer has no further bytes to write.

## 关闭FileChannel

When you are done using a FileChannel you must close it. Here is how that is done:

```java
channel.close();    
```

## FileChannel Position

When reading or writing to a FileChannel you do so at a specific position. You can obtain the current position of the FileChannel object by calling the position() method.

You can also set the position of the FileChannel by calling the position(long pos) method.

Here are two examples:

```java
long pos channel.position();

channel.position(pos +123);
```

If you set the position after the end of the file, and try to read from the channel, you will get -1 - the end-of-file marker.

If you set the position after the end of the file, and write to the channel, the file will be expanded to fit the position and written data. This may result in a "file hole", where the physical file on the disk has gaps in the written data.

## FileChannel Size

The size() method of the FileChannel object returns the file size of the file the channel is connected to. Here is a simple example:

```java
long fileSize = channel.size();    
```

## FileChannel Truncate

You can truncate a file by calling the FileChannel.truncate() method. When you truncate a file, you cut it off at a given length. Here is an example:

```java
channel.truncate(1024);
```

This example truncates the file at 1024 bytes in length.

## FileChannel Force

The FileChannel.force() method flushes all unwritten data from the channel to the disk. An operating system may cache data in memory for performance reasons, so you are not guaranteed that data written to the channel is actually written to disk, until you call the force() method.

The force() method takes a boolean as parameter, telling whether the file meta data (permission etc.) should be flushed too.

Here is an example which flushes both data and meta data:

```java
channel.force(true);
```


