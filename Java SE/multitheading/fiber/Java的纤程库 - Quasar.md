# Java的纤程库 - Quasar

2016年07月21日 10:18:45 [Joker_Ye](https://me.csdn.net/hj7jay) 阅读数：14120 标签： [java](http://so.csdn.net/so/search/s.do?q=java&t=blog)[纤程库](http://so.csdn.net/so/search/s.do?q=%E7%BA%A4%E7%A8%8B%E5%BA%93&t=blog)[线程池](http://so.csdn.net/so/search/s.do?q=%E7%BA%BF%E7%A8%8B%E6%B1%A0&t=blog)[优化](http://so.csdn.net/so/search/s.do?q=%E4%BC%98%E5%8C%96&t=blog)[学习](http://so.csdn.net/so/search/s.do?q=%E5%AD%A6%E4%B9%A0&t=blog)



版权声明：本文为博主原创文章，未经博主允许不得转载。	https://blog.csdn.net/hj7jay/article/details/51980038

最近遇到的一个问题大概是微服务架构中经常会遇到的一个问题：

服务 A 是我们开发的系统，它的业务需要调用 B 、 C 、 D 等多个服务，这些服务是通过http的访问提供的。 问题是 B 、 C 、 D 这些服务都是第三方提供的，不能保证它们的响应时间，快的话十几毫秒，慢的话甚至1秒多，所以这些服务的Latency比较长。幸运地是这些服务都是集群部署的，容错率和并发支持都比较高，所以不担心它们的并发性能，唯一不爽的就是就是它们的Latency太高了。

![img](http://static.open-open.com/lib/uploadImg/20160719/20160719094616_36.png)

系统A会从Client接收Request, 每个Request的处理都需要多次调用B、C、D的服务，所以完成一个Request可能需要1到2秒的时间。为了让A能更好地支持并发数，系统中使用线程池处理这些Request。当然这是一个非常简化的模型，实际的业务处理比较复杂。

可以预见，因为系统B、C、D的延迟，导致整个业务处理都很慢，即使使用线程池，但是每个线程还是会阻塞在B、C、D的调用上，导致I/O阻塞了这些线程， CPU利用率相对来说不是那么高。

当然在测试的时候使用的是B、C、D的模拟器，没有预想到它们的响应是那么慢，因此测试数据的结果还不错，吞吐率还可以，但是在实际环境中问题就暴露出来了。

## 概述

最开始线程池设置的是200,然后用HttpUrlConnection作为http client发送请求到B、C、D。当然HttpUrlConnection也有一些坑，比如 [Persistent Connections](http://docs.oracle.com/javase/7/docs/technotes/guides/net/http-keepalive.html) 、 [Caveats of HttpURLConnection](http://techblog.bozho.net/caveats-of-httpurlconnection/) ,跳出坑后性能依然不行。

通过测试，如果B、C、D等服务延迟接近0毫秒，则HttpUrlConnection的吞吐率(线程池的大小为200)能到40000 requests/秒,但是随着第三方服务的响应时间变慢，它的吞吐率急剧下降，B、C、D的服务的延迟为100毫秒的时候，则HttpUrlConnection的吞吐率降到1800 requests/秒，而B、C、D的服务的延迟为100毫秒（这个数字一定写错了，但是是多少就不知道了）的时候HttpUrlConnection的吞吐率降到550 requests／秒。

增加 http.maxConnections 系统属性并不能显著增加吞吐率。

如果增加调用HttpUrlConnection的线程池的大小，比如增加到2000，性能会好一些，但是B、C、D的服务的延迟为500毫秒的时候，吞吐率为3800 requests/秒，延迟为1秒的时候，吞吐率为1900 requests/秒。

虽然线程池的增大能带来性能的提升，但是线程池也不能无限制的增大，因为每个线程都会占用一定的资源，而且随着线程的增多，线程之间的切换也更加的频繁，对CPU等资源也是一种浪费。

切换成其它http的实现，比如netty，也无法带来更好的性能。系统A 严重依赖Http协议，而Http协议又是一个 blocking 协议，只有等到Response返回才可以发送下一个请求(虽然有些http server支持http pipelining,但是我们无法保证/控制第三方的B、C、D支持这个特性)。

下面列出了一些常用的http client:

- JDK’s [URLConnection](http://docs.oracle.com/javase/8/docs/api/java/net/URLConnection.html) uses traditional thread-blocking I/O.
- [Apache HTTP Client](https://hc.apache.org/httpcomponents-client-ga/index.html) uses traditional thread-blocking I/O with thread-pools.
- [Apache Async HTTP Client](https://hc.apache.org/httpcomponents-asyncclient-4.1.x/index.html) uses NIO.
- [Jersey](https://jersey.java.net/documentation/latest/async.html#d0e10447) is a ReST client/server framework; the client API can use several HTTP client backends including URLConnection and Apache HTTP Client.
- [OkHttp](http://square.github.io/okhttp/) uses traditional thread-blocking I/O with thread-pools.
- [Retrofit](http://square.github.io/retrofit/) turns your HTTP API into a Java interface and can use several HTTP client backends including Apache HTTP Client.
- [Grizzly](https://grizzly.java.net/) is network framework with low-level HTTP support; it was using NIO but it switched to AIO .
- [Netty](http://netty.io/) is a network framework with HTTP support (low-level), multi-transport, includes NIO and native (the latter uses epoll on Linux).
- [Jetty Async HTTP Client](http://www.eclipse.org/jetty/documentation/current/http-client.html) uses NIO.
- [Async HTTP Client](https://github.com/AsyncHttpClient/async-http-client) wraps either Netty, Grizzly or JDK’s HTTP support.
- [clj-http](https://github.com/dakrone/clj-http) wraps the Apache HTTP Client.
- [http-kit](https://dzone.com/articles/www.http-kit.org/client.html) is an async subset of clj-http implemented partially in Java directly on top of NIO.
- [http async client](https://github.com/cch1/http.async.client) wraps the Async HTTP Client for Java.

这个列表摘自 [High-Concurrency HTTP Clients on the JVM](https://dzone.com/articles/high-concurrency-http-clients-on-the-jvm) ,不止于此，这篇文章重点介绍基于java纤程库quasar的实现的http client库，并比较了性能。我们待会再说。

回到我前面所说的系统，如何能更好的提供性能？有一种方案是借助其它语言的优势，比如Go，让Go来代理完成和B、C、D的请求，系统A通过一个TCP连接与Go程序交流。第三方服务B、C、D的Response结果可以异步地返回给系统A。

![img](http://static.open-open.com/lib/uploadImg/20160719/20160719094616_838.png)

Go的优势在于可以实现request-per-goroutine,整个系统中可以有成千上万个goroutine。 goroutine是轻量级的，而且在I/O阻塞的时候可以不占用线程，这让Go可以轻松地处理上万个链接，即使I/O阻塞也没问题。Go和Java之间的通讯协议可以通过Protobuffer来实现，而且它们之间只保留一个TCP连接即可。

当然这种架构的修改带来系统稳定性的降低，服务A和服务B、C、D之间的通讯增加了复杂性。同时，因为是异步方式，服务A的业务也要实现异步方式，否则200个线程依然等待Response的话，还是一个阻塞的架构。

通过测试，这种架构可以带来稳定的吞吐率。 不管服务B、C、D的延迟有多久，A的吞吐率能维持15000 requests/秒。当然Go到B、C、D的并发连接数也有限制，我把最大值调高到20000。

这种曲折的方案的最大的两个弊病就是架构的复杂性以及对原有系统需要进行大的重构。 高复杂性带来的是系统的稳定性的降低，包括部署、维护、网络状况、系统资源等。同时系统要改成异步模型，因为系统业务线程发送Request后不能等待Go返回Response,它需要从Client接收更多的Request,而收到Response之后它才继续执行剩下的业务，只有这样才不会阻塞，进而提到系统的吞吐率。

将系统A改成异步，然后使用HttpUrlConnection线程池行不行？HttpUrlConnection线程池还是导致和B、C、D通讯的吞吐率下降，但是Go这种方案和B、C、D通讯的吞吐率可以维持一个较高的水平。

考虑到Go的优势，那么能不能在Java中使用类似Go的这种goroutine模型呢？那就是本文要介绍的Java纤程库: [Quasar](http://docs.paralleluniverse.co/quasar/)

## quasar初步

Java官方并没有纤程库。但是伟大的社区提供了一个优秀的库，它就是Quasar。

创始人是 [Ron Pressler和Dafna Pressler](http://www.paralleluniverse.co/about/) ,由Y Combinator孵化。

Quasar is a library that provides high-performance lightweight threads, Go-like channels, Erlang-like actors, and other asynchronous(异步) programming tools for Java and Kotlin.

Quasar提供了高性能轻量级的线程，提供了类似Go的channel，Erlang风格的actor，以及其它的异步编程的工具，可以用在Java和Kotlin编程语言中。Scala目前的支持还不完善，我想如果这个公司能快速的发展壮大，或者被一些大公司收购的话，对Scala的支持才能提上日程。

你需要把下面的包加入到你的依赖中：

- Core (必须) co.paralleluniverse:quasar-core:0.7.5[:jdk8] (对于 JDK 8，需要增加jdk8 classifier)
- Actor co.paralleluniverse:quasar-actors:0.7.5
- Clustering co.paralleluniverse:quasar-galaxy:0.7.5
- Reactive Stream co.paralleluniverse:quasar-reactive-streams:0.7.5
- Kotlin co.paralleluniverse:quasar-kotlin:0.7.5

Quasar fiber依赖java instrumentation修改你的代码，可以在运行时通过java Agent实现，也可以在编译时使用ant task实现。

通过java agent很简单，在程序启动的时候将下面的指令加入到命令行：

```java
-javaagent:path-to-quasar-jar.jar
```

对于maven来说，你可以使用插件 [maven-dependency-plugin](http://maven.apache.org/plugins/maven-dependency-plugin/) ,它会为你的每个依赖设置一个属性，以便在其它地方引用，我们主要想使用 ${co.paralleluniverse:quasar-core:jar} :

```xml
<plugin>
	<artifactId>maven-dependency-plugin</artifactId>	
	<version>2.5.1</version>
	<executions>
		<execution>
			<id>getClasspathFilenames</id>
			<goals>
				<goal>properties</goal>
			</goals>
		</execution>
	</executions>
</plugin>

```

然后你可以配置 exec-maven-plugin 或者 maven-surefire-plugin 加上agent参数，在执行maven任务的时候久可以使用Quasar了。

官方提供了一个 [Quasar Maven archetype](https://github.com/puniverse/quasar-mvn-archetype) ,你可以通过下面的命令生成一个quasar应用原型：

```java
git clone https://github.com/puniverse/quasar-mvn-archetype
cdquasar-mvn-archetype
mvn install
cd..
mvn archetype:generate -DarchetypeGroupId=co.paralleluniverse -DarchetypeArtifactId=quasar-mvn-archetype -DarchetypeVersion=0.7.4-DgroupId=testgrp -DartifactId=testprj
cd testprj
mvn test
mvn clean compile dependency:properties exec:exec
```

如果你使用gradle，可以看一下gradle项目模版： [Quasar Gradle template project](https://github.com/puniverse/quasar-gradle-template) 。

最容易使用Quasar的方案就是使用Java Agent,它可以在运行时instrument程序。如果你想编译的时候就使用AOT instrumentation(Ahead-of-Time)，可以使用Ant任务 co.paralleluniverse.fibers.instrument.InstrumentationTask ，它包含在quasar-core.jar中。

Quasar最主要的贡献就是提供了轻量级线程的实现，叫做fiber(纤程)。Fiber的功能和使用类似Thread, API接口也类似，所以使用起来没有违和感，但是它们不是被操作系统管理的，它们是由一个或者多个ForkJoinPool调度。一个idle fiber只占用400K内存，切换的时候占用更少的CPU，你的应用中可以有上百万的fiber，显然Thread做不到这一点。这一点和Go的goroutine类似。Fiber并不意味着它可以在所有的场景中都可以替换Thread。

**Fiber 和Thead如何选择**

- **经常等待型选fiber**：当fiber的代码经常会被等待其它fiber阻塞的时候，就应该使用fiber。

- **计算密集型选thead**：对于那些需要CPU长时间计算的代码，很少遇到阻塞的时候，就应该首选thread

以上两条是选择fiber还是thread的判断条件，主要还是看任务是I/O blocking相关还是CPU相关。幸运地是，fiber API使用和thread使用类似，所以代码略微修改久就可以兼容。

Fiber特别适合替换哪些异步回调的代码。使用 FiberAsync 异步回调很简单，而且性能很好，扩展性也更高。

类似Thread, fiber也是用Fiber类表示：

```java
new Fiber<V>() {
	@Override
	protected V run() throws SuspendExecution, InterruptedException {
		// your code
 	}
}.start();
```

与Thread类似，但也有些不同。Fiber可以有一个返回值,类型为泛型V，也可以为空Void。 run 也可以抛出异常 InterruptedException 。

你可以传递 SuspendableRunnable 或 SuspendableCallable 给Fiber的构造函数:

```java
new Fiber<Void>(new SuspendableRunnable() {
	publicvoidrun()throwsSuspendExecution, InterruptedException {
		// your code
 	}
}).start();
```

甚至你可以调用Fiber的join方法等待它完成，调用 get 方法得到它的结果。

Fiber继承Strand类。Strand类代表一个Fiber或者Thread，提供了一些底层的方法。

逃逸的Fiber(Runaway Fiber)是指那些陷入循环而没有block、或者block fiber本身运行的线程的Fiber。偶尔有逃逸的fiber没有问题，但是太频繁会导致性能的下降，因为需要调度器的线程可能都忙于逃逸fiber了。Quasar会监控这些逃逸fiber,你可以通过JMX监控。如果你不想监控，可以设置系统属性 co.paralleluniverse.fibers.detectRunawayFibers 为 false 。

fiber中的 ThreadLocal 是fiber local的。 InheritableThreadLocal 继承父fiber的值。

Fiber、SuspendableRunnable 、SuspendableCallable 的 run 方法会抛出SuspendExecution异常。但这并不是真正意义的异常，而是fiber内部工作的机制，通过这个异常暂停因block而需要暂停的fiber。

任何在Fiber中运行的方法，需要声明这个异常(或者标记@Suspendable),都被称为suspendable method。

反射调用通常都被认为是suspendable, Java8 lambda 也被认为是suspendable。不应该将类构造函数或类初始化器标记为suspendable。

synchronized 语句块或者方法会阻塞操作系统线程，所以它们不应该标记为suspendable。Blocking线程调用默认也不被quasar允许。但是这两种情况都可以被quasar处理，你需要在Quasar java agent中分别加上 m 和 b 参数，或者ant任务中加上 allowMonitors 和 allowBlocking 属性。

## quasar原理

Quasar最初fork自 [Continuations Library](http://www.matthiasmann.de/content/view/24/26/) 。

如果你了解其它语言的coroutine, 比如Lua,你久比较容易理解quasar的fiber了。 Fiber实质上是 continuation ， continuation可以捕获一个计算的状态，可以暂停当前的计算，等隔一段时间可以继续执行。Quasar通过instrument修改suspendable方法。Quasar的调度器使用 ForkJoinPool 调度这些fiber。

Fiber调度器FiberScheduler是一个高效的、work-stealing、多线程的调度器。

默认的调度器是 FiberForkJoinScheduler ,但是你可以使用自己的线程池去调度，请参考 FiberExecutorScheduler 。

当一个类被加载时，Quasar的instrumentation模块 (使用 Java agent时) 搜索suspendable 方法。每一个suspendable 方法 f 通过下面的方式 instrument:

它搜索对其它suspendable方法的调用。对suspendable方法 g 的调用，一些代码会在这个调用 g 的前后被插入，它们会保存和恢复fiber栈本地变量的状态，记录这个暂停点。在这个“suspendable function chain”的最后，我们会发现对 Fiber.park 的调用。 park 暂停这个fiber，扔出 SuspendExecution异常。

当 g block的时候，SuspendExecution异常会被Fiber捕获。 当Fiber被唤醒(使用unpark), 方法 f 会被调用， 执行记录显示它被block在g的调用上，所以程序会立即跳到 f 调用 g 的那一行,然后调用它。最终我们会到达暂停点，然后继续执行。当 g 返回时， f 中插入的代码会恢复f的本地变量。

过程听起来很复杂，但是它只会带来3% ~ 5%的性能的损失。

下面看一个简单的例子, 方法m2声明抛出SuspendExecution异常，方法m1调用m2和m3,所以也声明抛出这个异常，最后这个异常会被Fiber所捕获：

```java
public class Helloworld{
 
    static void m1() throws SuspendExecution, InterruptedException {
        String m = "m1";

        System.out.println("m1 begin");
        m = m2();
        m = m3();
        System.out.println("m1 end");
        System.out.println(m);

    }

    static String m2() throws SuspendExecution, InterruptedException {
        return"m2";
    }

    static String m3() throws SuspendExecution, InterruptedException {
        return"m3";
    }

    static public void main(String[] args) throws ExecutionException, InterruptedException {
        newFiber<Void>("Caller",newSuspendableRunnable() {

        @Override
        public void run() throws SuspendExecution, InterruptedException {
            m1();
        }
        }).start();
    }
}
```

反编译这段代码 (一般的反编译软件如jd-gui不能把这段代码反编译java文件，[Procyon](https://bitbucket.org/mstrobel/procyon/wiki/Java%20Decompiler) 虽然能反编译，但是感觉反编译有错。所以我们还是看字节码（基于栈的机器指令）吧)：

```java
@Instrumented(suspendableCallSites={16,17}, methodStart=13, methodEnd=21, methodOptimized=false)
static void m1()
throws SuspendExecution, InterruptedException
 {
// Byte code:
// 0: aconst_null
// 1: astore_3
// 2: invokestatic 88 co/paralleluniverse/fibers/Stack:getStack ()Lco/paralleluniverse/fibers/Stack;
// 5: dup
// 6: astore_1
// 7: ifnull +42 -> 49
// 10: aload_1
// 11: iconst_1
// 12: istore_2
// 13: invokevirtual 92 co/paralleluniverse/fibers/Stack:nextMethodEntry ()I
// 16: tableswitch default:+24->40, 1:+64->80, 2:+95->111
// 40: aload_1
// 41: invokevirtual 96 co/paralleluniverse/fibers/Stack:isFirstInStackOrPushed ()Z
// 44: ifne +5 -> 49
// 47: aconst_null
// 48: astore_1
// 49: iconst_0
// 50: istore_2
// 51: ldc 2
// 53: astore_0
// 54: getstatic 3 java/lang/System:out Ljava/io/PrintStream;
// 57: ldc 4
// 59: invokevirtual 5 java/io/PrintStream:println (Ljava/lang/String;)V
// 62: aload_1
// 63: ifnull +26 -> 89
// 66: aload_1
// 67: iconst_1
// 68: iconst_1
// 69: invokevirtual 100 co/paralleluniverse/fibers/Stack:pushMethod (II)V
// 72: aload_0
// 73: aload_1
// 74: iconst_0
// 75: invokestatic 104 co/paralleluniverse/fibers/Stack:push (Ljava/lang/Object;Lco/paralleluniverse/fibers/Stack;I)V
// 78: iconst_0
// 79: istore_2
// 80: aload_1
// 81: iconst_0
// 82: invokevirtual 108 co/paralleluniverse/fibers/Stack:getObject (I)Ljava/lang/Object;
// 85: checkcast 110 java/lang/String
// 88: astore_0
// 89: invokestatic 6 com/colobu/fiber/Helloworld:m2 ()Ljava/lang/String;
// 92: astore_0
// 93: aload_1
// 94: ifnull +26 -> 120
// 97: aload_1
// 98: iconst_2
// 99: iconst_1
// 100: invokevirtual 100 co/paralleluniverse/fibers/Stack:pushMethod (II)V
// 103: aload_0
// 104: aload_1
// 105: iconst_0
// 106: invokestatic 104 co/paralleluniverse/fibers/Stack:push (Ljava/lang/Object;Lco/paralleluniverse/fibers/Stack;I)V
// 109: iconst_0
// 110: istore_2
// 111: aload_1
// 112: iconst_0
// 113: invokevirtual 108 co/paralleluniverse/fibers/Stack:getObject (I)Ljava/lang/Object;
// 116: checkcast 110 java/lang/String
// 119: astore_0
// 120: invokestatic 7 com/colobu/fiber/Helloworld:m3 ()Ljava/lang/String;
// 123: astore_0
// 124: getstatic 3 java/lang/System:out Ljava/io/PrintStream;
// 127: ldc 8
// 129: invokevirtual 5 java/io/PrintStream:println (Ljava/lang/String;)V
// 132: getstatic 3 java/lang/System:out Ljava/io/PrintStream;
// 135: aload_0
// 136: invokevirtual 5 java/io/PrintStream:println (Ljava/lang/String;)V
// 139: aload_1
// 140: ifnull +7 -> 147
// 143: aload_1
// 144: invokevirtual 113 co/paralleluniverse/fibers/Stack:popMethod ()V
// 147: return
// 148: aload_1
// 149: ifnull +7 -> 156
// 152: aload_1
// 153: invokevirtual 113 co/paralleluniverse/fibers/Stack:popMethod ()V
// 156: athrow
// Line number table:
// Java source line #13 -> byte code offset #51
// Java source line #15 -> byte code offset #54
// Java source line #16 -> byte code offset #62
// Java source line #17 -> byte code offset #93
// Java source line #18 -> byte code offset #124
// Java source line #19 -> byte code offset #132
// Java source line #21 -> byte code offset #139
// Local variable table:
// start length slot name signature
// 53 83 0 m String
// 6 147 1 localStack co.paralleluniverse.fibers.Stack
// 12 99 2 i int
// 1 1 3 localObject Object
// 156 1 4 localSuspendExecution SuspendExecution
// Exception table:
// from to target type
// 49 148 148 finally
// 49 148 156 co/paralleluniverse/fibers/SuspendExecution
// 49 148 156 co/paralleluniverse/fibers/RuntimeSuspendExecution
 }
```

这段反编译的代码显示了方法m被instrument后的样子，虽然我们不能很清楚的看到代码执行的样子，但是也可以大概地看到它实际在方法的最开始加入了此方法的栈信息的检查（＃0 ～ ＃49，如果是第一次运行这个方法，则直接运行，然后在一些暂停点上加上一些栈压入的处理，并且可以在下次执行的时候直接跳到上次的暂停点上。

官方的工程师关于Quasar的instrument操作如下：

- Fully analyze the bytecode to find all the calls into suspendable methods. A method that (potentially) calls into other suspendable methods is itself considered suspendable, transitively.
- Inject minimal bytecode in suspendable methods (and only them) that will manage an user-mode stack, in the following places:
  - At the beginning we’ll check if we’re resuming the fiber and only in this case we’ll jump into the relevant bytecode index.
  - Before a call into another suspendable method we’ll push a snapshot of the current activation frame, including the resume bytecode index; we can do it because we know the structure statically from the analysis phase.
  - After a call into another suspendable method we’ll pop the top activation frame and, if resumed, we’ll restore it in the current fiber.

我并没有更深入的去了解Quasar的实现细节以及调度算法，有兴趣的读者可以翻翻它的代码。如果你有更深入的剖析，请留下相关的地址，以便我加到参考文档中。

曾经， 陆陆续续也有一些Java coroutine的实现( [coroutine-libraries](http://stackoverflow.com/questions/2846428/available-coroutine-libraries-in-java) ), 但是目前来说最好的应该还是Quasar。

Oracle会实现一个官方的纤程库吗？目前来说没有看到这方面的计划，而且从Java的开发进度上来看，这个特性可能是遥遥无期的，所以目前还只能借助社区的力量，从第三方库如Quasar中寻找解决方案。

更多的Quasar知识，比如Channel、Actor、Reactive Stream 的使用可以参考官方的文档，官方也提供了多个 [例子](http://docs.paralleluniverse.co/quasar/#examples) 。

## Comsat介绍

Comsat又是什么？

Comsat还是Parallel Universe提供的集成Quasar的一套开源库，可以提供web或者企业级的技术，如HTTP服务和数据库访问。

Comsat并不是一套web框架。它并不提供新的API,只是为现有的技术如Servlet、JAX－RS、JDBC等提供Quasar fiber的集成。

它包含非常多的库，比如Spring、ApacheHttpClient、OkHttp、Undertow、Netty、Kafka等。

## 性能对比

刘小溪在CSDN上写了一篇关于Quasar的文章： [次时代Java编程（一）：Java里的协程](http://www.open-open.com/lib/view/open1468892872350.html) ，写的挺好，建议读者读一读。

个人感觉可以直接使用当前协程技术将client进行重写，使原本在多个线程中实现的业务在协程中进行实现，提高了并发度。

它参考 [Skynet](https://github.com/atemerev/skynet) 的测试写了代码进行对比，这个测试是并发执行整数的累加：

测试结果是Golang花了261毫秒，Quasar花了612毫秒。其实结果还不错，但是文中指出这个测试没有发挥Quasar的性能。因为quasar的性能主要在于阻塞代码的调度上。

虽然文中加入了排序的功能，显示Java要比Golang要好，但是我觉得这又陷入了另外一种错误的比较， Java的排序算法使用TimSort，排序效果相当好，Go的排序效果显然比不上Java的实现，所以最后的测试主要测试排序算法上。 真正要体现Quasar的性能还是测试在有阻塞的情况下fiber的调度性能。

HttpClient

话题扯的越来越远了，拉回来。我最初的目的是要解决的是在第三方服务响应慢的情况下提高系统 A 的吞吐率。最初A是使用200个线程处理业务逻辑，调用第三方服务。因为线程总是被第三方服务阻塞，所以系统A的吞吐率总是很低。

虽然使用Go可以解决这个问题，但是对于系统A的改造比较大，还增加了系统的复杂性。

这正是Quasar fiber适合的场景，如果一个Fiber被阻塞，它可以暂时放弃线程，以便线程可以用来执行其它的Fiber。虽然整个集成系统的吞吐率依然很低，这是无法避免的，但是系统的吞吐率确很高。

Comsat提供了Apache Http Client的实现： FiberHttpClientBuilder ：

```java
final CloseableHttpClient client = FiberHttpClientBuilder.
				create(2).// use 2 io threads
					setMaxConnPerRoute(concurrencyLevel).
						setMaxConnTotal(concurrencyLevel).build();
```

然后在Fiber中久可以调用:

```java
String response = client.execute(newHttpGet("http://localhost:8080"), BASIC_RESPONSE_HANDLER);
```

你也可以使用异步的HttpClient:

```java
final CloseableHttpAsyncClient client = FiberCloseableHttpAsyncClient.wrap(HttpAsyncClients.
	custom().
		setMaxConnPerRoute(concurrencyLevel).
			setMaxConnTotal(concurrencyLevel).
				build());
client.start();
```

Comsat还提供了Jersey Http Client: AsyncClientBuilder.newClient() 。

甚至提供了 Retrofit 、 OkHttp 的实现。

经过测试，虽然随着系统B、C、D的响应时间的拉长，吞吐率有所降低，但是在latency为100毫秒的时候吞吐率依然能达到9900 requests/秒，可以满足我们的需求，而我们的代码改动的比较小。