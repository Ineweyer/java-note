# Jetty 源码剖析系列 (1) - 总览 | 芋道源码 —— 纯源码解析博客

摘要: 原创出处 http://ericcenblog.com/2017/05/27/jettyyuan-ma-pou-xi/ 「Eric Cen」欢迎转载，保留摘要，谢谢！

- [Jetty Architecture](http://www.iocoder.cn/Jetty/EricCen/intro/)
- [启动一个内嵌的 Jetty 服务器](http://www.iocoder.cn/Jetty/EricCen/intro/)

[Jetty](http://www.eclipse.org/jetty/) 是 Eclipse Foundation 出的一个轻量级 Web 服务器和 Servlet 容器。[Spark](http://sparkjava.com/) 使用了 Jetty 作为它的内嵌服务器。我一直都对 Web 服务器有着迷之兴趣，前前后后去读过好几个 Web 服务器的源码，有代码量太大设计模式复杂而半途而废的，如 Tomcat，有因为代码量小而简单而读完的，如 NanoHTTPD。相比之下，Jetty 大小适中，而且也够成熟，之前在工作中写的一个项目就是用了 Jetty Runner 来实现了一个 RESTful Service。这段时间重新去学习了一遍 Java NIO，所以这次就趁机把 Jetty 的源码好好撸一遍。

Jetty Version： 9.4.5

Let's hit on the road!

# Jetty Architecture

在开始剖析代码之前，我们先看一下 Jetty 的整体架构，Jetty 的官方文档对 Jetty 架构有很详细的介绍 ([点这里](http://www.eclipse.org/jetty/documentation/current/architecture.html))。

![img](http://ericcenblog.com/content/images/2017/05/Capture-38.JPG)

如文档所述：



The Jetty Server is the plumbing between a collection of 'Connector's that accept connections and a collection of 'Handler's that service requests from the connections and produce responses, with threads from a thread pool doing the work.

Connector 负责接收网络请求，Handler 负责解析请求并产生响应，通过线程池 ThreadPool 来执行任务，而 Connector，Handler，ThreadPool 这三个组件都是依附在 Server 中。

# 启动一个内嵌的 Jetty 服务器

本文开头提到过 Spark 就是用了 Jetty 做为它内嵌的服务器，从上文所看到 Jetty 是由 Server，Connector，Handler，ThreadPool 这几个组件组成，那我们来看一下如何启动一个内嵌的 Jetty 服务器。

![img](http://ericcenblog.com/content/images/2017/05/Capture-44.JPG)

是的，上面的代码就已经是启动了一个内嵌的 Jetty 服务器！(当然，有部分代码我已经省略)。仔细看一下，`Server`，`Connector`，`Handler`，都有了，那`ThreadPool`呢？我们看一下 Server 类的构造函数：

![img](http://ericcenblog.com/content/images/2017/05/Capture-42.JPG)

![img](http://ericcenblog.com/content/images/2017/05/Capture-43.JPG)

我们可以看到线程池是在`Server`的构造函数里面创建出来了，默认是一个`QueuedThreadPool`，基于队列的线程池。 实现一个线程池是一个不错的 Java 面试题，我们就先看一下 Jetty 是如何实现的。我们先看一下`QueuedThreadPool`的层级关系:

![img](http://ericcenblog.com/content/images/2017/05/Capture-45.JPG)

再看`QueuedThreadPool`的构造函数：

![img](http://ericcenblog.com/content/images/2017/05/Capture-46.JPG)

我们可以看到`QueuedThreadPool`是把`Runnable`的 job 放在一个`org.eclipse.jetty.util.BlockingArrayQueue`里,`org.eclipse.jetty.util.BlockingArrayQueue`是 Jetty 自己实现的基于数组的阻塞队列。

![img](http://ericcenblog.com/content/images/2017/05/Capture-47.JPG)

从前文QueuedThreadPool的层级关系图看到QueuedThreadPool实现了LifeCycle接口，这个LifeCycle接口在 Jetty 里面是一个非常重要概念，Server，Connector，Handler，和QueuedThreadPool都是实现LifeCycle接口。这些组件都会通过调用start()方法来启动，而start()方法最终又会调用doStar()方法。我们先来看QueuedThreadPool这个线程池是怎样启动的：

![img](http://ericcenblog.com/content/images/2017/05/Capture-48.JPG)

它先把_threadsStarted这个Atomic的变量设成 0，然后启动_minThreads个线程：

![img](http://ericcenblog.com/content/images/2017/05/Capture-49.JPG)

![img](http://ericcenblog.com/content/images/2017/05/Capture-50.JPG)

可以看到，实际上它是启动了_minThreads个_runnable 线程，而这个_runnable线程做的是什么的？

![img](http://ericcenblog.com/content/images/2017/05/Capture-51.JPG)

我们可以看到这个_runnable线程是去_jobs也就是BlockingArrayQueue这个队列里面取出一个Runnable的job, 然后执行这个 job：

![img](http://ericcenblog.com/content/images/2017/05/Capture-52.JPG)

![img](http://ericcenblog.com/content/images/2017/05/Capture-53.JPG)



To Be Continue......