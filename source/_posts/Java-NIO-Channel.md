---
title: Java NIO Channel
date: 2016-06-26 21:04:43
tags: Java NIO
toc: true
---
Java NIO 的Channels有些像流，但是也有一些区别：

- 可以向一个Channel即读又写。流是典型的单向的（写或者读）
- Channels的读写是异步的
- Channels总是读数据到Buffer中，或者将Buffer中的数据写入Channel

上面提到的，从channel中读取数据至buffer中，将buffer中的数据写入channel中：

![Java NIO: Channels read data into Buffers, and Buffers write data into Channels](http://tutorials.jenkov.com/images/java-nio/overview-channels-buffers.png)
**Java NIO: Channels read data into Buffers, and Buffers write data into Channels**

#### Channel Implementations

在Java NIO中有一些比较重要的channel实现类：

- FileChannel
- DatagramChannel
- SocketChannel
- ServerSocketChannel

`FileChannel`从文件中读取数据。
`DatagramChannel`通过UDP读写网络中的数据。
`SocketChannel`通过TCP读写网络中的数据。
`ServerSocketChannel`可以监听新进来的TCP连接，像Web服务器那样，对每一个新进来的连接都会创建一个SocketChannel。

#### Basic Channel Example 

下面是一个使用`FileChannel`读数据到Buffer中的例子：

```java
public class ChannelExample {
    public static void main(String[] args) throws IOException {
        RandomAccessFile accessFile = new RandomAccessFile("nio-data.txt","rw");
        FileChannel fileChannel = accessFile.getChannel();

        ByteBuffer buffer = ByteBuffer.allocate(48);

        int bytesRead = fileChannel.read(buffer);

        while (bytesRead != -1) {
            System.out.println("Read " + bytesRead);
            buffer.flip();
            if (buffer.hasRemaining()) {
                System.out.print((char) buffer.get());
            }
            buffer.clear();
            bytesRead = fileChannel.read(buffer);
        }
        accessFile.close();
    }
}
```

注意 buf.flip() 的调用，首先读取数据到Buffer，然后反转Buffer，接着再从Buffer中读取数据。下一节会深入讲解Buffer的更多细节。




