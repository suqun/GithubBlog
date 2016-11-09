---
title: Java NIO Buffer
date: 2016-06-28 22:10:31
tags: Java NIO
toc: true
---

原文：[http://tutorials.jenkov.com/java-nio/buffers.html](http://tutorials.jenkov.com/java-nio/buffers.html)

Java NIO Buffers是和NIO的Channels交互使用的。你知道的，数据是从Channel中读到Buffer里，数据从Buffer里写入到Channel中。 

Buffer本质上是可以读写数据的内存块。这个内存块被NIO的Buffer对象包裹，然后提供很多方法以便能够简单的操作这个内存块。

#### Buffer基础用法

使用Buffer读写数据基本上就4步：

1. 数据写入Buffer
2. 调用 `buffer.flip()`
3. 从Buffer中读出数据
4. 调用`buffer.clear()`或者`buffer.compact()`方法

当你将数据写入buffer，buffer会一直留意你已经写了多少数据。一旦你需要读数据，你必须调用`flip()`方法将buffer从写模式切换到读模式中。进入读模式后，buffer允许你读取其中被写入的数据。

一旦你读完了所有的数据，你需要清空buffer，以备buffer可以继续被写入。这么做有两种方式：调用`clear()`方法或者调用`compact()`方法。`clear()`方法会清空buffer中的所有数据。`compact()`方法只会清楚掉你已经读过的数据。那些没读的数据会移到buffer的起始处，然后数据会接着这些未读数据后面继续写入。

**栗子**

下面是Buffer用法的🌰，用到的write，flip，read，clear等操作会作注释说明。

```java
public class JavaNIOBuffer {
    public static void main(String[] args) throws IOException {
        String path = Thread.currentThread().getContextClassLoader().getResource("").getPath();
        RandomAccessFile aFile = new RandomAccessFile(path+"/nio-data.txt", "rw");
        FileChannel inChannel = aFile.getChannel();

        //create buffer with capacity of 48 bytes
        ByteBuffer buf = ByteBuffer.allocate(48);

        int bytesRead = inChannel.read(buf); //read into buffer.
        while (bytesRead != -1) {

            buf.flip();  //make buffer ready for read

            while(buf.hasRemaining()){
                System.out.print((char) buf.get()); // read 1 byte at a time
            }

            buf.clear(); //make buffer ready for writing
            bytesRead = inChannel.read(buf);
        }
        aFile.close();
    }
}
```

#### Buffer Capacity, Position and Limit

buffer本质上就是一个你写入数据，然后读取数据的内存块。这个内存块被NIO Buffer对象包裹后，提供了一系列简单操作内存块的方法。

Buffer有3个你需要了解的属性：

- capacity
- position
- imit

position和limit的含义取决于Buffer处在读模式还是写模式。capacity含义一直都是一样的，和Buffer模式无关。

这是一个关于capacity，position和limit的说明，后面会对其进行说明。

![buffers-modes](http://tutorials.jenkov.com/images/java-nio/buffers-modes.png)

##### Capacity

Buffer作为一个内存块是有固定的大小值，称之为『capacity』。你只能向Buffer中写入byte，long，char等类型数据。一旦Buffer满了，在向其写入数据之前你需要清空它（读取数据或者清空数据）

##### Position

向Buffer中写数据是从一个确定的position开始，初始的position是0。当byte，long等数据写入Buffer，position会向前移动到下一个可供插入数据的单元。position最大值为capacity-1。

从Buffer中读数据也是从一个给定的位置开始。当你将Buffer从写模式切换到读模式后，position的值重置为0。从Buffer中读数据就是从position读，读取后，position会向前移动到下一个可供读取的单元。

##### Limit

写模式中，limit就是写入buffer的数据量。Limit等于buffer的capacity。

当切换到读模式后，limit就是buffer中可以读取的数据量。就是说，切换到读模式时，limit就是设置为写模式中position的值。也就是说，buffer中写入的数据都可以读取到。

#### Buffer的类型

伴随Java NIO的Buffer类型有:

- ByteBuffer
- MappedByteBuffer
- CharBuffer
- DoubleBuffer
- FloatBuffer
- IntBuffer
- LongBuffer
- ShortBuffer

可以看到，这些Buffer类型表示了不同的数据类型。换句话说，就是可以通过char，short，int，long，float 或 double类型来操作缓冲区中的字节。

MappedByteBuffer有点特殊，在它的专门章节中再描述。

#### Buffer的分配

在获取到Buffer对象后首先要去分配。每个Buffer类都有个`allocate()`方法。下面的例子是ByteBuffer分配capacity为48字节的例子：

```java
ByteBuffer buf = ByteBuffer.allocate(48);
```

Here is an example allocating a CharBuffer with space for 1024 characters:
下面是CharBuffer分配1024个字符的例子:
```java
CharBuffer buf = CharBuffer.allocate(1024);
```

#### 向Buffer中写入数据

向Buffer中写入数据有两种方式：

- 从Channel中获取数据写入Buffer
- 通过buffer的`put()`方法写入数据

从Channel中获取数据写入buffer的例子：
```java
int bytesRead = inChannel.read(buf);//read into buffer.
```

使用`put()`方法写入数据的例子：

```java
buf.put(127);
```

有不同的版本的`put()`方法，允许你使用不同的方式写入数据。比如，在特定的位置写入，写入字节或数组。查看JavaDoc获取buffer具体的实现。

#### flip()

`flip()`方法用来切换Buffer的读和写模式。调用`flip()`，设置position为0，设置limit为原来的position值。就是说，position现在用来标记读的位置，Limit用来标记可以读多少。

#### 从Buffer中读数据

从Buffer中读取数据有两种方式：

- 将buffer中的数据读入到channel中
- 调用buffer自带的`get()`方法直接读
 
举个🌰：将Buffer中的数据读到channel中

```java
//read from buffer into channel.
int bytesWritten = inChannel.write(buf);
```

举个🌰：使用`get()`方法读取Buffer
```java
byte aByte = buf.get();
```

有不同的版本的`get()`方法，允许你使用不同的方式写入数据。比如，在特定的位置写入，写入字节或数组。查看JavaDoc获取buffer具体的实现。

#### rewind()

`Buffer.rewind()`重置position为0，这样就可以重新读buffer的数据。limit不受影响，始终可以标记buffer中可读的数据量。

#### clear() and compact()

从Buffer中读完数据以后要做好Buffer写的准备。可以调用`clear()`方法或者`compact()`方法。

调用`clear()`方法会重置position的值为0，Limit的值为capacity。意思是，Buffer已经清空。Buffer中的数据并没有清楚掉。只是告诉你从Buffer的哪个位置可以写入数据。

如果存在没有读取的数据，调用`clear()`方法后，该数据会被标记为'遗忘的'，因为这些数据再也没有什么标记其实被读过的还是没被读过的。

如果你必须先向Buffer中写入数据，然后还想读那些没有被读过的数据。需要调用`compact()`方法代替`clear()`方法。

`compact()`方法把所有没有读过的数据复制到Buffer的起始处。然后设置position的值为味道数据后面的值。Limit仍然设置为capacity。这样，Buffer就做好写的准备，而不必覆盖掉未读的数据。

#### mark() and reset()

使用`Buffer.mark()`方法可以标记一个指定的position。这样再之后调用`Buffer.reset()`方法重置position到标记的地方。

举个🌰：
```java
buffer.mark();
//call buffer.get() a couple of times, e.g. during parsing.
buffer.reset();  //set position back to mark.    
```

#### equals() and compareTo()

比较两个buffers仍然可以使用`equals()`和`compareTo()`方法。

##### equals()

两个buffers相等，那么： 
- 有相同的类型（byte、char、int等）。
- Buffer中剩余的byte、char等的个数相等。
- Buffer中所有剩余的byte、char等都相同。

就是说：equals只是比较Buffer的一部分，不是每一个在它里面的元素都比较。实际上，它只比较Buffer中的剩余元素。

##### compareTo()

compareTo()方法比较两个Buffer的剩余元素(byte、char等)， 如果满足下列条件，则认为一个Buffer“小于”另一个Buffer：

- 第一个不相等的元素小于另一个Buffer中对应的元素 。
- 所有元素都相等，但第一个Buffer比另一个先耗尽(第一个Buffer的元素个数比另一个少)。
