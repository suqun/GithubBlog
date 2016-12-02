---
title: Java NIO Selector
toc: true
date: 2016-11-16 22:27:08
tags: Java NIO
---

原文：[http://tutorials.jenkov.com/java-nio/selectors.html](http://tutorials.jenkov.com/java-nio/selectors.html)

`Selector`是Java NIO中用来检查一个或多个NIO通道的，决定哪个通道做好准备进行读写的组件。这样，一个单线程就可以管理多个通道，以便管理多个网络连接。

## 为何使用Selector?

使用单线程处理多通道的好处就是可以使用更少的线程处理多个通道。实际上可以使用只用一个线程处理多个通道。在操作系统中，线程切换开销很大。每个线程都会占用一些资源（内存）。因此，线程越少越好。

但是，当前操作系统和CPU多任务处理上已经非常好，多线程的开销已经变得很小了。如果一个CPU有多个内核，不使用多任务可能是在浪费CPU能力。不管怎么说，关于那种设计的讨论应该放在另一篇不同的文章中。在这里，只要知道使用Selector能够处理多个通道就足够了。

使用一个Selector处理3个channel的图解如下：

![
Java NIO: A Thread uses a Selector to handle 3 Channel's](http://7xpk5e.com1.z0.glb.clouddn.com/JavaNIOSelectors.png)

## Selector的创建

调用`Selector.open()`方法创建一个selector。像这样：

```java
Selector selector = Selector.open();
```

## Channel注册到Selector上

为了结合Selector使用Channel，首先要将Channel注册到Selector上。通过方法`SelectableChannel.register()`实现:

```java
channel.configureBlocking(false);

SelectionKey key = channel.register(selector, SelectionKey.OP_READ);
```

结合Selector使用时，Channel必须是非阻塞师的。这就意味着，你不能把FileChannel和Selector结合使用，因为FileChannel不能切换到非阻塞模式。Socket Channel确可以很好的结合Selector使用。

`register()`方法的第二个参数需要注意下。这是个有趣的设置，意思是在通过Selector监听Channel时刚兴趣的事件。可以监听到以下四种事件：

- Connect
- Accept
- Read
- Write

一个channel触发了事件就是意味着该事件已就绪。因此，channel连接服务成功就是`Connect`就绪。服务socke channel准备接受进入的连接就是`Accept`就绪。服务socket channel已经准备好了可以读取的数据就是`Read`就绪。channel准备好可以写入数据就是`Write`就绪。

这四种事件用SelectionKey的常量表示： 

- SelectionKey.OP_CONNECT
- SelectionKey.OP_ACCEPT
- SelectionKey.OP_READ
- SelectionKey.OP_WRITE

如果对多个事件感兴趣，那么可以用“位或”操作符将常量连接起来，像这样：

```java
int interestSet = SelectionKey.OP_READ | SelectionKey.OP_WRITE;    
```

下面还会继续提到interest集合。

## SelectionKey

通过前面示例可以看到，调用`register()`方法向selector上注册channel时返回`SelectionKey`对象。这个`SelectionKey`对象中包含很多有趣的属性。

- interest集合
- ready 集合
- Channel
- Selector
- 附加对象（可选）

下面会描述这些属性。

### Interest集合

就像向Selector注册通道一节中所描述的，interest集合是你所选择的感兴趣的事件集合。通过SelectionKey可以读写interest集合。 

```java
int interestSet = selectionKey.interestOps();

boolean isInterestedInAccept  = interestSet & SelectionKey.OP_ACCEPT;
boolean isInterestedInConnect = interestSet & SelectionKey.OP_CONNECT;
boolean isInterestedInRead    = interestSet & SelectionKey.OP_READ;
boolean isInterestedInWrite   = interestSet & SelectionKey.OP_WRITE; 
```

可以看到，使用『位与』操作interest集合和给定的的SelectionKey常量，可以确定某个确定的世界是否在interest集合中。

### Ready集合

ready集合是channel已经准备就绪的channel集合。在一次`selection`以后，可以先获得ready集合。至于`selecton`，会再下面的章节解释。可以这样获取ready集合：

```java
int readySet = selectionKey.readyOps();
```

可以用像检测interest集合那样的方法，来检测channel中什么事件或操作已经就绪。但是，也可以使用以下四个方法，它们都会返回一个布尔类型：

```java
selectionKey.isAcceptable();
selectionKey.isConnectable();
selectionKey.isReadable();
selectionKey.isWritable();
```

### Channel + Selector

Accessing the channel + selector from the SelectionKey is trivial. Here is how it's done:
从SelectionKey中获得channel和selector很简单，像这样就好:

```java
Channel  channel  = selectionKey.channel();
Selector selector = selectionKey.selector();   
```

### 附加对象

可以将一个对象附加到SelectionKey上。这是个识别给定的channel的简便方法，还可以附加更多信息上去。比如，附加个与channel一起使用的buffer，或者聚合更多数据的对象。例如：

```java
selectionKey.attach(theObject);
Object attachedObj = selectionKey.attachment();
```

也可以在注册时附加对象，像这样： 

```java
SelectionKey key = channel.register(selector, SelectionKey.OP_READ, theObject);
```

## 通过Selector选择Channel

一旦向Selector注册了一个或多个channel，就可以调用任一`select()`方法。这些方法返回那些注册时感兴趣事件（connect，accept，read 或者 write）的channel。
也就是说，如果感兴趣的channel已对读数据做好准备，那么在调用`select()`方法以后，就会返回对读就绪的channel。

select方法有以下几种:

1. `int select()`
2. `int select(long timeout)`
3. `int selectNow()`

`select()` 阻塞直到至少一个channel已经对监听事件做好准备。

`select(long timeout)` 和`select()`一样，除了最长会阻塞timeout毫秒(参数)。

`selectNow()` 不会阻塞，无论channel有没有准备好都会直接返回。（没有准备好的直接返回0）

select()方法返回的int值表示有多少通道已经就绪。亦即，自上次调用select()方法后有多少通道变成就绪状态。如果调用select()方法，因为有一个通道变成就绪状态，返回了1，若再次调用select()方法，如果另一个通道就绪了，它会再次返回1。如果对第一个就绪的channel没有做任何操作，现在就有两个就绪的通道，但在每次select()方法调用之间，只有一个通道就绪了。

### selectedKeys() 

调用select()方法后，一旦其返回值表明一个或多个channel就绪，就可以通过`selectedKeys()`方法访问『selected key set』（已选择键集）中的就绪channel。

```java
Set<SelectionKey> selectedKeys = selector.selectedKeys();  
```

调用`Channel.register()`向selector注册channel以后返回`SelectionKey`对象。这个对象就代表了注册到selector的channel。可以通过SelectionKey对象的`electedKeySet()`方法获得这些对象。
 
遍历已选择的键集获得就绪的channel： 

```java
Set<SelectionKey> selectedKeys = selector.selectedKeys();

Iterator<SelectionKey> keyIterator = selectedKeys.iterator();

while(keyIterator.hasNext()) {
    
    SelectionKey key = keyIterator.next();

    if(key.isAcceptable()) {
        // a connection was accepted by a ServerSocketChannel.

    } else if (key.isConnectable()) {
        // a connection was established with a remote server.

    } else if (key.isReadable()) {
        // a channel is ready for reading

    } else if (key.isWritable()) {
        // a channel is ready for writing
    }

    keyIterator.remove();
}
```

这个循环遍历已选择键集中的每个键，并检测各个键所对应的通道的就绪事件。

注意在每次遍历后调用`keyIterator.remove()`方法。Selector不会从已选择键集中自动删除SelectionKey的实例。在处理完channel后必须调用此方法。下次channel会准备好，Selector将其重新添加到已选择的键集中。 （原文：Notice the keyIterator.remove() call at the end of each iteration. The Selector does not remove the SelectionKey instances from the selected key set itself. You have to do this, when you are done processing the channel. The next time the channel becomes "ready" the Selector will add it to the selected key set again.）

调用`SelectionKey.channel()`方法会返回需要处理的channel。比如ServerSocketChannel或者SocketChannel等。


### wakeUp()方法

某个线程调用select()方法以后会被阻塞，即使没有就绪的channel，也可以使其从select()方法返回。只要让其它线程在第一个线程调用select()方法的那个对象上调用Selector.wakeup()方法即可。阻塞在select()方法上的线程会立马返回。

如果有其它线程调用了wakeup()方法，但当前没有线程阻塞在select()方法上，下个调用select()方法的线程会立即“醒来（wake up）”。

### close()方法

用完Selector后调用其close()方法会关闭该Selector，且使注册到该Selector上的所有SelectionKey实例无效。通道本身并不会关闭。

## 完整的Selector示例

下面是一个完整的selector例子，open,register,监听等

```java
Selector selector = Selector.open();

channel.configureBlocking(false);

SelectionKey key = channel.register(selector, SelectionKey.OP_READ);


while(true) {

  int readyChannels = selector.select();

  if(readyChannels == 0) continue;


  Set<SelectionKey> selectedKeys = selector.selectedKeys();

  Iterator<SelectionKey> keyIterator = selectedKeys.iterator();

  while(keyIterator.hasNext()) {

    SelectionKey key = keyIterator.next();

    if(key.isAcceptable()) {
        // a connection was accepted by a ServerSocketChannel.

    } else if (key.isConnectable()) {
        // a connection was established with a remote server.

    } else if (key.isReadable()) {
        // a channel is ready for reading

    } else if (key.isWritable()) {
        // a channel is ready for writing
    }

    keyIterator.remove();
  }
}
```


