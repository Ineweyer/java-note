# 协程，高并发IO终极杀器(3)

[![海纳](https://pic1.zhimg.com/v2-4367659cbc867e1bf7d91e93087a5b6b_xs.jpg)](https://www.zhihu.com/people/hinus)

[海纳](https://www.zhihu.com/people/hinus)



这节课，我介绍一种在Java实现的开源协程库：Quasar，它的官方主页在这里：

[Quasar](http://www.paralleluniverse.co/quasar/)

。这个库实现了一种可以和Go语言中的Goroutine相对标的编程概念：Fiber。Fiber是一种真正的协程。

## Fiber的基本用法

我们来写一个与上节课，go语言中的协程相对应的例子，以此来学习Quasar的基本用法：

```java
public class CoroutineTest {
    public static void main(String[] args) {
        Fiber fiberA = new FiberTask("hello");
        Fiber fiberB = new FiberTask("world");
        fiberA.start();
        fiberB.start();
        try {
            fiberA.join();
            fiberB.join();
        } catch (ExecutionException e) {
            e.printStackTrace();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

    }
}

class FiberTask extends Fiber<Integer> {
    private String msg;

    public FiberTask(String msg) {
        this.msg = msg;
    }

    @Override
    protected Integer run() throws SuspendExecution, InterruptedException {
        for (int i = 0; i < 5; i++) {
            System.out.println(msg);
            Fiber.park(1000, TimeUnit.MILLISECONDS);
        }
        return 0;
    }
}
```

这个程序直接跑是跑不通的，一定要按照quasar的官方文档，使用gradle去执行，才会比较简单。具体的示例，看这里：[Quasar](http://link.zhihu.com/?target=http%3A//docs.paralleluniverse.co/quasar/)，里面详细介绍了如何使用maven和gradle来配置quasar。小密圈里有直接的例子，圈里的同学注意查看。

这个例子中，是通过继承自Fiber类创建了两个协程。协程内部，打印一次msg，然后就协程就会休眠1秒钟。这两个协程的运行结果如下：



![img](https://pic3.zhimg.com/80/v2-7a055cc3f9c9e6c3c671fe68c472f31e_hd.png)

具体的原理，和我们上节课介绍的Go语言的执行原理十分相似，这里就不再多做介绍了。



我们本节课的重点在于讲解为什么协程可以提高高并发IO情况下的效率。

## 使用协程提升IO效率

回忆一下，我们介绍的IO模型：[Java NIO(4): 阻塞与非阻塞 - 知乎专栏](https://zhuanlan.zhihu.com/p/27400943)，以及[Java NIO(6): Selector - 知乎专栏](https://zhuanlan.zhihu.com/p/27434028)。同步阻塞式的代码是多么简单清晰啊，而通过Selector实现的异步代码又是那么地难看，难以理解。

那我们回到问题的原点，阻塞式的代码的问题到底在哪里呢？回忆一下，原来是因为调用一个IO接口以后，整个线程就会阻塞住。当线程阻塞了，就不能再去处理其他的IO请求了。所以一个服务端能支持多少个客户端连接，往往取决于服务端所能创建的线程数量。而线程是很消耗内存资源的，在我的测试机上，也就只能创建几千个线程而已。

所以我们使用Selector进行改造，这条思路已经介绍过了。不再赘述。我们可不可以换个角度呢？我其实只是想让线程占用的内存资源降下来就行了，这不就是协程吗？协程是可以与线程一样地对执行单元进行抽象。但是协程相比线程十分轻量，内存使用大约只有线程的十分之一甚至是几十分之一。那么，我们就可以通过创建更多的协程来实现同步的写法啊。

好，看一个例子：

```java
public class CoroutineServer {
    public void run() throws IOException {
        ServerSocketChannel serverChannel = ServerSocketChannel.open();
        serverChannel.configureBlocking(false);
        serverChannel.socket().bind(new InetSocketAddress("127.0.0.1", 8000));

        Selector selector = Selector.open();
        serverChannel.register(selector, SelectionKey.OP_ACCEPT);

        while (true) {
            selector.select();
            Iterator ite = selector.selectedKeys().iterator();

            while (ite.hasNext()) {
                SelectionKey key = (SelectionKey)ite.next();

                if (key.isAcceptable()) {
                    ServerSocketChannel s = (ServerSocketChannel)key.channel();
                    SocketChannel clientSocket = s.accept();
                    System.out.println("Got a new Connection");

                    clientSocket.configureBlocking(false);

                    SelectionKey newKey = clientSocket.register(selector, SelectionKey.OP_WRITE);

                    FiberConnection client = new FiberConnection(clientSocket, newKey);
                    client.start();
                    newKey.attach(client);

                    System.out.println("client waiting");
                }
                else if (key.isReadable()) {
                    FiberConnection client = (FiberConnection) key.attachment();
                    client.onRead();
                }
                else if (key.isWritable()) {
                    FiberConnection client = (FiberConnection) key.attachment();
                    client.onWrite();
                }

                ite.remove();
            }
        }
    }

    public static void main( String[] args ) throws Exception
    {
        CoroutineServer server = new CoroutineServer();
        server.run();
    }
}

class FiberConnection extends Fiber<Integer> {
    private SocketChannel socketChannel;
    private SelectionKey key;
    private ByteBuffer recvBuffer;

    public FiberConnection(SocketChannel socketChannel, SelectionKey key) {
        this.socketChannel = socketChannel;
        this.key = key;
        this.recvBuffer = ByteBuffer.allocate(32);
    }

    @Override
    protected Integer run() throws SuspendExecution, InterruptedException {
        int a = getInteger();
        int b = getInteger();
        if (b == 0)
            b += 1;

        sendResult(a / b);

        return 0;
    }

    private int getInteger() throws  SuspendExecution {
        sendResult(0);

        Fiber.park();

        int res = 0;
        try {
            try {
                recvBuffer.clear();

                // read may fail even SelectionKey is readable
                // when read fails, the fiber should suspend, waiting for next
                // time the key is ready.
                int n = socketChannel.read(recvBuffer);
                while (n == 0) {
                    n = socketChannel.read(recvBuffer);
                }

                if (n == -1) {
                    close();
                    return 0;
                }

                System.out.println("received " + n + " bytes from client");
            } catch (IOException e) {
                e.printStackTrace();
            }

            recvBuffer.flip();
            res = getInt(recvBuffer);

            // when read ends, we are no longer interested in reading,
            // but in writing.
            key.interestOps(SelectionKey.OP_WRITE);
        } catch (Exception e) {
        }

        return res;
    }

    public void sendResult(int r) throws SuspendExecution {
        Fiber.park();
        try {
            try {
                recvBuffer.clear();
                recvBuffer.put(String.valueOf(r).getBytes());
                recvBuffer.flip();
                socketChannel.write(recvBuffer);

                key.interestOps(SelectionKey.OP_READ);
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        catch (Exception e) {
        }
    }

    public int getInt(ByteBuffer buf) {
        int r = 0;
        while (buf.hasRemaining()) {
            r *= 10;
            r += buf.get() - '0';
        }

        return r;
    }

    public void close() {
        try {
            socketChannel.close();
            key.cancel();
        }
        catch (IOException e){};
    }

    public void onRead() {
        this.unpark();
    }

    public void onWrite() {
        this.unpark();
    }
}
```

在这个例子中，FiberConnection就是这样的，对客户端连接进行抽象的协程。在这个协程的run方法里，我们再次见到了同步代码，先从网络上取得一个整数，再去取得一个整数，然后把商发回到客户端。这个例子与之前使用异步Callback模型的例子可以进行一下比较。显然，这个例子中的run方法简洁多了。

但是，我们要知道的一点是，协程的休眠和唤醒都是发生在用户态的，也就是说应用程序开发者要自己负责协程的休眠和唤醒。即，在向客户端发送请求以后（sendResult(0)），就会主动休眠(park)，一直到客户端把数据返回到服务端，再把协程唤醒(unpark)。

这个程序的基础仍然是Selector，也就是说我们只在socket上有数据的时候，才会去把协程唤醒，让协程去读取数据。

肯定有人会有疑问，看上去，协程并没有使得程序的复杂度下降嘛。但是，如果我告诉你，getInteger, sendResult这些方法如果已经在语言中封装好了呢？selector这些全都不可见了。你是不是就会觉得这个程序写起来就太爽了？

你可能已经猜到了，Go语言就是把这些东西封装好了。Java封装的Epoll，只是屏蔽了平台差异，并没有改变它的异步模型。但是Go语言却把所有的IO都封装好了，你调用一个read方法，阻塞的是协程，而不是线程，而Goroutine又可以大量创建。所以你看，编程的简单程序可以与Thread阻塞式代码相当，而性有却又和异步相当。所以协程是是绝对的高并发IO的大杀器。

因为quasar只是一个半成品，所以我可以拿它来演示如何进行改造。而Go经过完整的封装，已经不需要再去做这个工作了。所以上节课就没有演示它的原理。到这里，大家应该能理解Go语言的核心特性了。

好了。今天的课程就到这里了。

没有作业。