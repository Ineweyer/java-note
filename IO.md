## 1. 阻塞、非阻塞、同步、异步之间的关系

1. 阻塞和非阻塞

阻塞和非阻塞关注的是**程序在等待调用结果（消息，返回值）时的状态**

阻塞调用是指调用结果返回之前，当前线程会被挂起。调用线程得到结果之后才返回。非阻塞调用是指在不能立刻得到结果之前，该调用不会阻塞当前线程。

**个人最简单粗暴的理解：阻塞，陷入到当前的调用中无法自拔，非阻塞，试探性质，已经告诉你要干啥了，你自己去干，我要做什么是我自己的事，当然一般情况我需要实现逻辑来监督调用是否完成**

2. 同步和异步

同步和异步关注的**消息通信机制**，所谓同步就是在发出一个“调用”时，在没有得到结果之前，该”调用“就不返回。但是一旦调用返回，就得到了返回值了。

而异步则相反，**“调用”在发出之后，这个调用就直接返回了，没有返回结果**。换句话说，当一个异步过程调用发出后，调用者不会立即得到结果。而是在“调用”发出后，“被调用者”通过状态，通知来通知调用者，或通过回调函数处理这个调用。

**个人最简单粗暴理解：同步，就是赖上你了，反正怎么滴你也需要给我知道调用的结果，异步，调用之后，被调用者自己挑时间方式来告诉自己结果如何，当然自己得实现如何接收其通知**

## 2. BIO、NIO、AIO总结

**同步与异步**

- **同步：** 同步就是发起一个调用后，被调用者未处理完请求之前，调用不返回。
- **异步：** 异步就是发起一个调用后，立刻得到被调用者的回应表示已接收到请求，但是被调用者并没有返回结果，此时我们可以处理其他的请求，被调用者通常依靠事件，回调等机制来通知调用者其返回结果。

同步和异步的区别最大在于异步的话调用者不需要等待处理结果，被调用者会通过回调等机制来通知调用者其返回结果。

**阻塞和非阻塞**

- **阻塞：** 阻塞就是发起一个请求，调用者一直等待请求结果返回，也就是当前线程会被挂起，无法从事其他任务，只有当条件就绪才能继续。
- **非阻塞：** 非阻塞就是发起一个请求，调用者不用一直等着结果返回，可以先去干其他事情。

### 2.1 BIO（Blocking I/O）

#### 2.1.1 传统BIO

通信模型如下图所示，采用一请求一应答的方式。

![ä¼ ç"BIOéä¿¡æ¨¡åå¾](https://camo.githubusercontent.com/5ef6de9824ae82bb0c403522a647953d1193a362/68747470733a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f322e706e67)

#### 2.1.2 伪异步IO

当有新的客户端接入时，将客户端的Socket封装成一个Task投递到线程池中处理。可以通过设置消息队列的大小和最大线程数，因此资源的占用是可控的。

![ä¼ªå¼æ­¥IOæ¨¡åå¾](https://camo.githubusercontent.com/04b258a50ca7f9762f43d64e70f4489440bae4eb/68747470733a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f332e706e67)

在活动连接数不是特别高的情况下，这种模型还是不错，线程池可以缓冲处理不了的连接或请求。但是针对十万甚至是百万级连接的时候，传统的BIO模型是无能为力的。

### 2.2 NIO（new I/O）

NIO是一种非阻塞的I/O模型，面向缓冲，基于通道的I/O操作方法。支持阻塞和非阻塞两种模式。

阻塞模式适用于低负载、低开发的应用程序。

非阻塞模式适用于高负载、高并发的应用。

#### 2.2.1 特点

1. NIO流支持非阻塞的，非阻塞情况下发起IO请求后不用等待IO完成就可以继续做其他的事情
2. NIO是面向Buffer，直接将数据读到流中进行操作
3. Channel通道，NIO通过通道进行读和写，通道是双向的可以读也可以写，也因为有Buffer，通道可以异步地读写。
4. Selector(选择器)，可以使用单线程处理多个通道，因此只需要较少的线程来处理这些通道。

#### 2.2.2 核心组件

- Channel（通道）：
- Buffer（缓冲区）：
- Selector（选择器）：

#### 2.2.3 原生NIO的问题

- JDK 的 NIO 底层由 epoll 实现，该实现饱受诟病的**空轮询 bug 会导致 cpu 飙升 100%**
- 项目庞大之后，自行实现的 NIO 很容易出现各类 bug，**维护成本较高**，上面这一坨代码我都不能保证没有 bug

于是乎，大家更喜欢使用Netty

#### 2.2.4 Buffer

作用：用于和NIO Channel交互，从Channel中读取数据到buffers里，从Buffer把数据写入到Channels。**Buffer其实就是一块内存区域。**

利用Buffer读写数据，通常遵循四个步骤：

1. 把数据写入buffer
2. 调用flip
3. 从Buffer中读取数据
4. 调用buffer.clear()或者buffer.compact()清空buffer，clear会清空所有数据，compact只会清空已经读取的数据。

Buffer中 具有三个属性需要掌握，分别是：

- **capacity容量**，在读写模式都是代表容量
- **position位置**，在写模式下代表下一个可写位置，在读模式下代表数据起始位置，
- **limit限制**，在写模式下代表可以写的最大位置，在Read模式下代表数据的结束位置

buffer常见方法：

| 方法                             | 介绍                                                         |
| -------------------------------- | ------------------------------------------------------------ |
| abstract Object array()          | 返回支持此缓冲区的数组 （可选操作）                          |
| abstract int arrayOffset()       | 返回该缓冲区的缓冲区的第一个元素的在数组中的偏移量 （可选操作） |
| int capacity()                   | 返回此缓冲区的容量                                           |
| Buffer **clear()**               | 清除此缓存区。将position = 0;limit = capacity;mark = -1;     |
| **Buffer flip()**                | flip()方法可以吧Buffer从写模式切换到读模式。调用flip方法会把position归零，并设置limit为之前的position的值。 也就是说，现在position代表的是读取位置，limit标示的是已写入的数据位置。 |
| abstract boolean hasArray()      | 告诉这个缓冲区是否由可访问的数组支持                         |
| boolean hasRemaining()           | return position < limit，返回是否还有未读内容                |
| abstract boolean isDirect()      | 判断个缓冲区是否为 direct                                    |
| abstract boolean isReadOnly()    | 判断告知这个缓冲区是否是只读的                               |
| int limit()                      | 返回此缓冲区的限制                                           |
| Buffer position(int newPosition) | 设置这个缓冲区的位置                                         |
| int remaining()                  | return limit - position; 返回limit和position之间相对位置差   |
| Buffer rewind()                  | 把position设为0，mark设为-1，不改变limit的值                 |
| Buffer mark()                    | 将此缓冲区的标记设置在其位置                                 |

#### 2.2.5 Channel

通常来说，所有的IO都是从Channel通道开始的

- 从通道进行数据**读取**，创建一个缓冲区，然后请求通道**读取数据**
- 从通道进行数据**写入**，创建一个缓冲区，**填充数据**，并要求通道写入数据

**与流的区别：**

- 通道可以读也可以写，流一般来说是单向的
- 通道可以异步读写
- 通道总是基于缓冲区的buffer来读写

**NIO几个Channel的实现**

- FileChannel：用于文件数据的读写
- DatagramChannel：用于UDP的数据的读写
- SocketChannel：用于TCP数据读写，一般是客户端实现
- ServerSocketChannel：允许允许我们监听TCP连接请求，每个请求会创建一个SocketChannel，一般是服务器端实现

**FileChannel的一般使用规则：**

1. 开启FileChannel，使用之前必须打开，但是没有办法直接打开，需要依附于InputStream、OutputStream、RandomAccessFile获取FileChannel
2. 从FileChannel读取数据/写入数据
3. 关闭FileChannel

**SocketChannel和ServerSocketChannel一般使用规则：**

**客户端**

>  1.通过SocketChannel连接到远程服务器
>  2.创建读数据/写数据缓冲区对象来读取服务端数据或向服务端发送数据
>  3.关闭SocketChannel

**服务端**

> 1. 通过ServerSocketChannel 绑定ip地址和端口号
>
> 2. 通过ServerSocketChannelImpl的accept()方法创建一个SocketChannel对象用户从客户端读/写数据
>
> 3. 创建读数据/写数据缓冲区对象来读取客户端数据或向客户端发送数据
>
> 4. 关闭SocketChannel和ServerSocketChannel

**DatagramChannel一般使用规则**

> 1. 获取DataGramChannel，
> 2. 接收/发送消息

**Scatter/Gather**

本地矢量I/O操作，当请求一个Scatter/Gather操作时，该请求会被翻译为适当的本地调用来直接填充或者抽取缓冲区，减少或者避免缓冲区拷贝和系统调用。

**Scatter/Gather 功能是通过通道实现而不是buffer**

- 从一个Channel读取的信息分散到N个缓冲区中（Buffer）
- Gather将N个Buffer里面内容按照顺序发送到Channel

即每次的读写都是针对N个Buffer数组。

FileChannel可以通过transferFrom和transferTo方法和其他的Channel进行互换操作

#### 2.2.6 Selector

一般称为选择器，也可以翻译为多路复用器。

好处：使用更少的线程就可以处理通道，相比较于多线程，避免了线程上下文切换大开销

channel必须是非阻塞的，所以FileChannel不能切换为非阻塞

**使用方法**

> 1. Selector的创建
> 2. 注册Channel到Selector

```java
Selector selector = Selector.open();
channel.configureBlocking(false);
// 第二个参数为监听的事件，主要有Connect、Accept、Read、Write，其常量表示为SelectionKey.OP_Connect...
SelectionKey key = channel.register(selector, Selectionkey.OP_READ);
```

SelectionKey键表示一个特定的通道对象和一个特定的选择器对象之间的注册关系。

Selector中维护三种类型SelectionKey集合：已注册的键的集合、已选择的键的集合、已取消键的集合

通过select方法获取从上一次调用select后准备就绪的通道个数，当其不为零时可以通过调用selectedKeys方法获取已经选择键集合

选择器执行选择过程中，系统底层会依次询问每个通道是否就绪，可能会造成调用线程进入阻塞状态，通过下面方法唤醒在select中阻塞的线程：

- wakeup方法，让阻塞状态的select方法立刻返回
- close方法，任何一个在选择过程中被阻塞的线程都被唤醒

服务器端代码模板：

```java
ServerSocketChannel ssc = ServerSocketChannel.open();
ssc.socket().bind(new InetSocketAddress("localhost", 8080));
Selector selector = Selector.open();
ssc.register(selector, SelectionKey.OP_ACCEPT);
while(true) {
    int readyNum = selector.select();
    if(readyNum == 0) {
        continue;
    }
    Set<SelectionKey> selectsKeys = seletor.selectedKeys();
    Iterator<SelectionKey> it = selectedKeys.iterator();
    while(it.hasNext()) {
        SelectionKey key = it.next();
        if(key.isAcceptable()) {
           //接受连接 
        }
        else if(key.isReadable()) {
            //通道可读
        }
        else if(key.isWritable()) {
            //通道可写
        }
        it.remove();
    }
}
```

#### 2.2.7 内存映射(NIO新特性)

为了提高文件的读写速度而设计，让你能够创建和修改大到无法读入内存的文件。通过直接将文件的一段映射到进程的私有地址空间中，而不需要OS内核缓冲区，加快了读取速度。

内存映射主要用到：MappedByteBuffer和FileChannel。通过channel的map方法建立内存映射，即将两者绑在一起。

内存映射的优点：

1. 用户进程将文件数据视为内存，不需要发出read或write系统调用
2. 当用户进程触摸映射的内存空间时，将自动生成页面错误，以从磁盘引入文件数据，如果用户修改映射的内存空间，受影响的页面自动标记为脏，并随后刷新到磁盘更新文件。
3. 操作系统的虚拟内存子系统将执行页面的职能缓存，根据系统负载自动管理内存
4. 数据始终是页面对齐的，不需要缓冲区复制
5. 可以映射非常大的文件，而不消耗大量内存来复制数据。

#### 2.2.8 字符及编码(NIO新特性)

大部分操作系统在I/O文件存储方面仍然是以字节为导向的，所以无论使用何种编码，Unicode或其他编码，在字节序列或字符集编码之间仍然需要进行转化。java.nio.charset包中组成的类满足这个需求。

一种编码的方式：

```java
Charset charset = Charset.forName("GBK");
ByteBuffer bb = charset.encode("test");
bb.get()； //可以每次拿到一个字符
```

NIO中提供两个类：CharsetEncoder、CharsetDecoder两个实现编码转化方案。

#### 2.2.9 多路复用IO模型(NIO新特性)

会有一个线程不断地去轮询多个socket的状态，只有当socket真正有读写事件时，才真正调用实际的IO读写操作。NIO背后的基石是Reactor设计模式，其中包含角色有：

- Reactor 将I/O事件发派给对应的Handler
- Acceptor 处理客户端连接请求
- Handlers 执行非阻塞读/写

#### 2.2.10 文件时锁定(NIO新特性)

NIO的文件通道在读写数据的时候主要使用了阻塞模式，它不能支持非阻塞模式的读写，而且FileChannel的对象是不能够直接实例化的，只能依附于打开的文件对象，并且仅能通过getChannel方法返回一个Channel对象去连接桶一个文件，也就是针对同一个文件进行读写操作。

解决了很多java应用程序和非java应用程序之间共享数据的问题。

文件锁的api如下所示：

```java
// 如果请求的锁定范围是有效的，阻塞直至获取锁
 public final FileLock lock()  
// 尝试获取锁非阻塞，立刻返回结果  
 public final FileLock tryLock()  

// 第一个参数：要锁定区域的起始位置  
// 第二个参数：要锁定区域的尺寸,  不一定需要限制在文件区域内可以超出
// 第三个参数：true为共享锁，false为独占锁  
 public abstract FileLock lock (long position, long size, boolean shared)  
 public abstract FileLock tryLock (long position, long size, boolean shared) 
```

#### 2.2.11 AsychronousFileChannel异步文件通道

使用demo：

```java
AsynchronousFileChannel fileChannel =
    AsynchronousFileChannel.open(path, StandardOpenOption.READ);

// 读取
// 方式1
Future<Integer> operation = fileChannel.read(buffer, 0); //立即返回
while(!operation.isDone());  //等待读取完成
//方式2
fileChannel.read(buffer, position, buffer, new CompletionHandler<Integer, ByteBuffer>(){...});

//写
//方式1
Future<Integer> operation = fileChannel.write(buffer, position);
buffer.clear();
while(!operation.isDone());
//方式2
fileChannel.write(buffer, position, buffer, new CompletionHandler<Integer, ByteBuffer>(){...});
```



### 2.3 AIO（Asynchronous I/O）

AIO 也就是 NIO 2。在 Java 7 中引入了 NIO 的改进版 NIO 2,它是异步非阻塞的IO模型。异步 IO 是基于事件和回调机制实现的，也就是应用操作之后会直接返回，不会堵塞在那里，当后台处理完成，操作系统会通知相应的线程进行后续的操作。

AIO 是异步IO的缩写，虽然 NIO 在网络操作中，提供了非阻塞的方法，但是 NIO 的 IO 行为还是同步的。对于 NIO 来说，我们的业务线程是在 IO 操作准备好时，得到通知，接着就由这个线程自行进行 IO 操作，IO操作本身是同步的。（除了 AIO 其他的 IO 类型都是同步的，这一点可以从底层IO线程模型解释，推荐一篇文章：[《漫话：如何给女朋友解释什么是Linux的五种IO模型？》](https://mp.weixin.qq.com/s?__biz=Mzg3MjA4MTExMw==&mid=2247484746&idx=1&sn=c0a7f9129d780786cabfcac0a8aa6bb7&source=41#wechat_redirect) ）

## 3. IO

### 3.1 基本IO分类

**IO流的分类：**

- 按照流的流向分，可以分为输入流和输出流；
- 按照操作单元划分，可以划分为字节流和字符流；
- 按照流的角色划分为节点流和处理流。

1. 按照操作方式分类结构图

节点流：流的一端直接接的是目标设备

处理流：程序通过一个间接流去调用节点流类，已达到更加灵活方便读写各种数据类型，间接流都是处理流。

![ææä½æ¹å¼åç±"ç"æå¾ï¼](https://camo.githubusercontent.com/50f105c85f6b42d643d46e1ac7bb0f855b92cd9d/68747470733a2f2f757365722d676f6c642d63646e2e786974752e696f2f323031382f352f31362f313633363764346664316365316234363f773d37323026683d3130383026663d6a70656726733d3639353232)

2. 按照操作对象分类结构图

![ææä½å¯¹è±¡åç±"ç"æå¾](https://camo.githubusercontent.com/8957efacdf1cd4eac15d844da8353a7f77a3c863/68747470733a2f2f757365722d676f6c642d63646e2e786974752e696f2f323031382f352f31362f313633363764363733623065323638643f773d37323026683d35333526663d6a70656726733d3436303831)

IO流原理分析：Java IO涉及40多个类。但是都是从下面个四个抽象类派生来的：

- **InputStream/Reader**: 所有的输入流的基类，前者是字节输入流，后者是字符输入流。
- **OutputStream/Writer**: 所有输出流的基类，前者是字节输出流，后者是字符输出流。

### 3.2 Path 和 Files

针对Path：

- 创建一个Path，通过工具类Paths的get方法创建
- File和Path之间的转换，File和URI之间的转换，就是使用toPath、toFile、toURI进行转换
- 获取Path的相关信息，
- 移除Path中的冗余项，normalize、toRealPath（融合了toAbsolutePath和normalize）

Files：

- 创建文件，createFile、createDirectory（不会创建多层文件夹）、createDirectories(会创建多层文件夹)
- 删除文件，delete(path)
- 复制文件，copy(sourcePath, destinationPath)
- 获取文件属性
- 遍历一个文件夹，newDirectoryStream(dir)
- 遍历整个文件夹，walkFileTree(startingDir, new FindJavaVisitor(result)); //第二个参数为遍历接口

## 4. 关于多线线程的一些理解

### 4.1 各个Thread之间的关系（独立，各自的异常不会影响对方）

![img](https://cyc2018.github.io/CS-Notes/pics/96706658-b3f8-4f32-8eb3-dcb7fc8d5381.jpg)

各个线程在创建完成之后就分道扬镳了，一个线程的死亡不会影响到其他线程（Deamon线程会随着被守护线程而消亡）。所以一个线程死亡之前把屁股擦干净。

### 4.2 Interrupt总结

调用线程的**interrupt方法仅能立即打断那些在阻塞状态或者挂起的线程对象（通过抛出异常方式立即处理）**，但是对于正在正常运行的代码块，仅能接受到通知，如果不显示地查看自己的状态（调用线程对象的isInterrupted方法查看是否已经被打断）根本不知道自己已经被打断。这也从另外一个侧面说明一个问题，最好不要使用从外部打断的方式来结束一个正在运行的线程，最好在线程内部自己主动查看标志判断自己是否需要退出。

- 当一个线程被打断之后就不能再次进行线程的调度，即调用sleep、wait、yield等代码块会抛出异常