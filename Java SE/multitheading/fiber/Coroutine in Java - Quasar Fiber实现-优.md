# Coroutine in Java - Quasar Fiber实现-优

2016年08月31日 15:27:49

标签： [Coroutine](http://so.csdn.net/so/search/s.do?q=Coroutine&t=blog)[Quasar](http://so.csdn.net/so/search/s.do?q=Quasar&t=blog)[Fiber](http://so.csdn.net/so/search/s.do?q=Fiber&t=blog) 更多

个人分类： [并发处理](https://blog.csdn.net/mawming/article/category/3046717)

转自 https://segmentfault.com/a/1190000006079389?from=groupmessage&isappinstalled=0

说到协程(Coroutine)，很多人会想到go,lua,erlang等语言，其实JVM上也有蛮多的实现，如PicoThread,Kilim,Quasar等，本文主要介绍其中一种Coroutine实现 -- Quasar Fiber，Quasar Fiber相对来说流行度更好一些,如果之前没有接触过协程(用户级轻量级线程)，可以看下[What are fibers](http://zeroturnaround.com/rebellabs/what-are-fibers-and-why-you-should-care/)、[Coroutine](https://en.wikipedia.org/wiki/Coroutine)

Quasar是一个快速精炼的Java并发库，其特点是提供轻量线程，也就是**纤程模型**，Clojure 和 Kotlin也提供类似真正轻量模型，已经被证明是一种成熟的[**并发**](http://www.jdon.com/concurrency.html)范式，这种Actor模型是真正的Erlang的Actor模型。  Akka是一种号称Actor模型的应用框架，拥有丰富的API。 但是，Akka – 即使它的Java API –受到Scala非常严重的影响，对于Java开发者通常感到陌生，而Quasar 的actor则是完全Java所以让开发者比较熟悉，无论你是写Java还是Clojure 或 Kotlin.  Akka的API是基于回调(callback-based). Quasa提供了类似Erlang和Go语言的fiber纤程，因此真正无堵塞(Akka并无纤程概念，只是线程池而已)，Quasar的actor API非常简单，更兼容于Java代码，这些都是非常类似Erlang，而Akka导入大量陌生概念，比如由于缺乏简单的堵塞selective接受方式(这些 Erlang 和 Quasar都提供), Akka必须引入消息stashing. 其他概念如monadic future都与业务逻辑或Actor模型无关，但是带来复杂性。

那么为什么要使用协程？
协程可以用同步的编程方式达到或接近于纯异步的性能，而没有异步带来的Callback hell，虽然有很多机制或模式解决或解耦callback hell的问题, 但同步的编程方式更容易维护和理解(风格之争是另外一个话题了，有兴趣可以看下[akka跟fiber的比较](http://www.javacodegeeks.com/2015/05/quasar-and-akka-a-comparison.html))
相比于os thread，fiber不管在内存资源还是调度上都比前者轻量的多，相对于thread blocking, fiber blocking可以达到比前者大几个数量级的并发度，更有效的利用CPU资源(运行fiber的worker线程并没有block)
具体大家可以看下[Why and When use Fiber](https://medium.com/@markpapadakis/coroutines-and-fibers-why-and-when-5798f08464fd)

好像是个神奇的东西呢，咋实现的
相比于callback接口回调的异步框架，Coroutine这个暂停和恢复在没有JVM支持下，比较难以理解，是怎么做到的？有没有什么魔法？其实JVM中Coroutine的实现方式有很多([implementing-coroutines-in-java](http://stackoverflow.com/questions/2846664/implementing-coroutines-in-java))，Quasar Fiber则是通过字节码修改技术在编译或载入时织入必要的上下文保存/恢复代码，通过抛异常来暂停，恢复的时候根据保存的上下文(Continuation),恢复jvm的方法调用栈和局部变量,Quasar Fiber提供相应的Java类库来实现,对应用有一定的侵入性(很小)

Quasar Fiber 主要有 Instrument + Continuation + Scheduler几个部分组成

- **Instrument** 做一些代码的植入，如park前后上下文的保存/恢复等
- **Continuation** 保存方法调用的信息，如局部变量，引用等,用户态的stack,这个也是跟akka等基于固定callback接口的异步框架最大的区别
- **Scheduler** 调度器，负责将fiber分配到具体的os thread执行

下面具体介绍下Quasar Fiber的实现细节,最好先阅读下[quasar官方文档](http://docs.paralleluniverse.co/quasar/),不是很长

# Instrument

## Weaving 织入

quasar fiber的运行需要织入一些指令，用于调用栈的保存和恢复，quasar提供了三种方式进行织入(AOT、javaagent、ClassLoader)
quasar 会对我们的代码进行static call-site分析，在必要的地方织入用于保存和恢复调用栈的代码。
哪些方法需要instrument?

- 方法带有Suspendable 注解
- 方法带有SuspendExecution
- 方法为classpath下/META-INF/suspendables、/META-INF/suspendable-supers指定的类或接口,或子类
  符合上面条件的method,quasar会对其做call site分析,也许为了效率，quasar并没有对所有方法做call site分析

方法内哪些指令需要instrument(在其前后织入相关指令)?

- 调用方法带有Suspendable 注解
- 调用方法带有SuspendExecution
- 调用方法为classpath下/META-INF/suspendables、/META-INF/suspendable-supers指定的类或接口,或子类
  主要为了解决第三方库无法添加Suspendable注解的问题
- 通过反射调用的方法
- 动态方法调用 MethodHandle.invoke
- Java动态代理InvocationHandler.invoke
- Java 8 lambdas调用

> 注意，方法的instructions是编译期决定，所以这里调用的方法指的是编译期静态绑定的,这里比较容易犯错，我总结了如下两点
> 1.基于接口或基类编译的代码，如果实现类有可能suspend，那么需要在接口或基类中添加suspendable annotation或suspend异常
> 2.如果实现类确实会suspend，需要添加suspendable annotation或suspend异常,当然可以把所以实现类都声明成suspendable，如果方法里找不到suspend的调用，该方法将不被instrument,所以也没有overhead，尽管这个overhead非常微小

接下来我们简单看下quasar instrument都织入了哪些代码
![img](https://segmentfault.com/img/bVzFuf)

从上图可以看出，quasar instrument主要在park()前保存相关的局部变量和pc，再fiber恢复执行的时候通过pc(switch case跳转的程序计数器,非寄存器pc) jump到park()之后的代码并恢复局部变量，另外在方法调用前后还会push/pop相关的Contiuation

instrument还会对JUC(java.util.concurrent)中的Thread.park，替换成Fiber.park，这样park to thread就变成park to fiber，所以使用juc的代码，可以不用修改的跑在Fiber上
quasar在织入代码的同时，会对处理的类和方法加上Instrumented注解，以在运行期检查是否Instrumented,Instrumented注解包含了一个suspendableCallSites数组，用来存放方法体内suspendable call的line number
contiuations/stack详细请看contiuations章节

## QuasarInstrumentor

不管哪种织入方式，都是通过创建QuasarInstrumentor来处理Class的字节流
QuasarInstrumentor内部使用ASM来处理Class的字节流,通过SuspendableClassifier类来判断是否需要instrument
SuspendableClassifier有两个子类，分别为DefaultSuspendableClassifier和SimpleSuspendableClassifier
`DefaultSuspendableClassifier` 扫描classpath下SuspendableClassifier的实现，并且调用其接口判断是否需要instrument,也会调用SimpleSuspendableClassifier
`SimpleSuspendableClassifier` 通过/META-INF/suspendables、/META-INF/suspendable-supers判断

> Quasar-core.jar包中suspendable-supers包含java nio及juc lock/future等接口，因为这些接口无法改变签名，而quasar织入是在编译或载入时，无法知道具体实现类是否Suspendable,所以需显式指定

### Method Instrument实现细节

这里是整个Quasar Fiber是实现原理中最为关键的地方，也是大家疑问最多的地方,大家有兴趣可以看下源代码，大概1000多行的ASM操作,既可以巩固JVM知识又能深入原理理解Fiber,这里我不打算引入过多ASM的知识，主要从实现逻辑上进行介绍

InstrumentClass 继承ASM的ClassVisitor，对Suspendable的方法前后进行织入
InstrumentClass visitEnd中会创建InstrumentMethod,具体织入的指令在InstrumentMethod中处理
结合上面的instrument示例代码图，不妨先思考几个问题

- 怎么找到suspend call
- 怎么保存、恢复局部变量,栈帧等
- switch case跳转如何织入
- suspend call在try catch块中，如何处理
- 什么情况下在suspend call前后可以不织入也能正常运行

`1.怎么找到suspend call`
InstrumentMethod.callsSuspendables这个方法会遍历方法的instructions，
如果instruction是method invoke,则判断是否为suspend call(判断逻辑见上面章节)
如果instruction为suspend call，则把instrunction序号和source line number分别纪录到suspCallsBcis及suspCallsSourceLines这两个数组，供后面逻辑使用

`2.switch case跳转织入是如何实现的`
现在我们知道了怎么找到method中的suspend call，那如何把这些suspend calls拆分成instrument示例图中那样呢(switch case,pc...)
这个拆分过程在InstrumentMethod.collectCodeBlocks
根据上面计算的suspend call的数组，分配label数组，然后根据pc计数器(详细见后续章节)进行跳转label
label是JVM里用于jump类指令，如(GOTO,IFEQ,TABLESWITCH等)
quasar会把织入的上下文保存恢复指令及代码原始的指令生成到对应label

3.怎么保存、恢复局部变量,栈帧
```
- 在方法开始执行
1.调用Stack.nextMethodEntry，开启新的method frame
 
- 在方法结束执行
1.Stack.popMethod, 进行出栈
 
- 在调用Suspendable方法之前,增加以下逻辑
1.调用Stack.pushMethod 保存栈帧信息
2.依次调用Stack.put保存操作数栈数据
3.依次调用Stack.put保存局部变量
 
- 在Suspendable方法调用后
1.依次调用Stack.get恢复局部变量
2.依次调用Stack.get恢复操作数栈
  恢复局部变量和操作数栈的区别是前者在get后调用istore
```
因为Stack.put有3个参数，所以这里每个put其实是多条jvm指令
```
aload_x //如果是保存操作数栈，这条指令不需要，因为值已经在操作数栈了
aload_x //load Stack引用
iconst_x //load Stack idx
invokestatic  co/paralleluniverse/fibers/Stack:push (Ljava/lang/Object;Lco/paralleluniverse/fibers/Stack;I)V
```
```
/**
   Stack.put会根据不同类型进行处理，Object或Array保存到dataObject[]，其他保存到dataLong[]
**/
public static void push(long value, Stack s, int idx) 
public static void push(float value, Stack s, int idx)
public static void push(double value, Stack s, int idx) 
public static void push(Object value, Stack s, int idx)
public static void push(int value, Stack s, int idx) 

```
java编译期可知局部变量表和操作数栈个数，上面put或get依赖这些信息，Stack具体逻辑见后面章节

`4.什么情况下在suspend call前后可以不织入也能正常运行` 
这里其实是一个优化，就是如果method内部只有一个suspend call,且前后没有如下指令

- side effects,包括方法调用，属性设置
- 向前jump
- monitor enter/exit

那么，quasar并不会对其instrument，也就不需要collectCodeBlocks分析,因为不需要保存、恢复局部变量

`5.suspend call在try catch块中，如何处理`
如果suspend call在一个大的try catch中，而我们又需要在中间用switch case切分，似乎是个比较棘手的问题，
所以在织入代码前，需要对包含suspend call的try catch做切分，将suspend call单独包含在try catch当中,通过ASM MethodNode.tryCatchBlocks.add添加新try catch块,
quasar先获取MethodNode的tryCatchBlocks进行遍历，如果suspend call的指令序号在try catch块内，那么就需要切分,以便织入代码

# Fiber

下面介绍下Quasar Fiber中的提供给用户的类和接口
Strand(英文意思是线)是quasar里对Thread和Fiber统一的抽象，Fiber是Strand的用户级线程实现，Thread是Strand内核级线程的实现

![img](https://segmentfault.com/img/bVzFt1)

Fiber主要有几下几个功能

## new

```
@SuppressWarnings("LeakingThisInConstructor")



public Fiber(String name, FiberScheduler scheduler, int stackSize, SuspendableCallable<V> target)
```

| 属性      | 类型                   | 说明                                       |
| --------- | ---------------------- | ------------------------------------------ |
| name      | String                 | fiber名称                                  |
| scheduler | FiberScheduler         | 调度器，默认为FiberForkJoinScheduler       |
| stackSize | int                    | stack大小,默认32                           |
| target    | SuspendableCallable<V> | 具体业务代码,在SuspendableCallable.run()里 |

构造函数主要完成以下几件事情

- 设置state为State.NEW
- 初始化Stack(用于保存fiber调用栈信息,Continuations的具体实现)
- 校验target是否Instrumented
- 将当前fiber封装成一个可以由scheduler调度的task,默认为FiberForkJoinTask
- 保存Thread的inheritableThreadLocals和contextClassLoader到Fiber

## start

Fiber.start() 逻辑比较简单，如下

- 将fiber state切换到State.STARTED
- 调用task的submit，提交给scheduler运行
  这里默认的为FiberForkJoinScheduler，FiberForkJoinScheduler会提交到内部的ForkJoinPool，并hash到其中一个work queue

## exec

fiber scheduler的worker thread从work quere获取到task，并调用fiber.exec()
fiber.exec()主要步骤如下

- cancel timeout task
- 将Thread的threadlocals、inheritableThreadLocals、contextClassLoader分别与fiber的互换,实现了local to fiber而不是local to thread,这里需要特别注意
- 所以基于thread local和context classloader的代码基本上都能运行在fiber上
- state = State.RUNNING;
- 运行业务逻辑(方法fiber.run())
- state = State.TERMINATED;

Fiber暂停时如何处理
fiber task切换有两种方式，一种是fiber task正常结束, 一种是fiber task抛SuspendExecution

fiber.exec()里会catch SuspendExecution,并交出执行权限，具体步骤如下

- stack sp = 0; // fiber恢复执行需要从最开始的frame恢复
- 设置fiber状态 TIMED_WAITING/WAITING
- 恢复线程的Thread的threadlocals、inheritableThreadLocals、contextClassLoader

调用栈信息已经在park()之前保存到stack中(见instrument章节)，所以这里无需处理

## park

暂停当前fiber的执行，并交出执行权
fiber task状态: RUNNABLE -> PARKING -> PARKED

fiber状态: RUNNING -> WAITING

Fiber.park方法如下，只能在当前fiber调用

```
static boolean park(Object blocker, ParkAction postParkActions, long timeout, TimeUnit unit) throwsSuspendExecution
```

park主要逻辑如下

- 设置fiber状态
- 如果设置了timeout，则向FiberTimedScheduler新增ScheduledFutureTask，用于超时检查
- 设置fiber.postPark = postParkActions，用于上面exec方法捕获异常后执行
- 抛异常，移交执行权限, 后续逻辑见exec章节移交执行权限

## unpark

恢复fiber的执行
fiber task状态: PARKED -> RUNNABLE

fiber状态: WAITING -> RUNNING

unpark主要也是做两件事情，一是设置状态，二是把fiber task重新submit到scheduler

> 这里除了手工调用fiber的park,unpark来暂停和恢复fiber外，可以用FiberAsync类来将基于callback的异步调用封装成fiber blocking，基于fiber的第三方库comsat就是通过将bio替换成nio，然后再封装成FiberAsync来实现的,FiberAsync可参考[http://blog.paralleluniverse....](http://blog.paralleluniverse.co/2014/02/06/fibers-threads-strands/)

## 状态切换

fiber运行状态由两部分组成，一个是fiber本身的状态，一个是scheduler task的状态

fiber状态

| 状态          | 描述                               |
| ------------- | ---------------------------------- |
| NEW           | Strand created but not started     |
| STARTED       | Strand started but not yet running |
| RUNNING       | Strand is running                  |
| WAITING       | Strand is blocked                  |
| TIMED_WAITING | Strand is blocked with a timeout   |
| TERMINATED    | Strand has terminated              |

task状态,这里以默认的FiberForkJoinTask为例

| 状态     | 描述                                                         |
| -------- | ------------------------------------------------------------ |
| RUNNABLE | 可运行                                                       |
| LEASED   | unpark时状态是RUNNABLE，设置为LEASED，那就是说两个状态等价咯 |
| PARKED   | 停止                                                         |
| PARKING  | 停止中                                                       |

运行状态切换图

![img](https://segmentfault.com/img/bVzFuG)

# Continuation

Fiber/Coroutine = Continuation + scheduler可以看出，Continuation在Fiber中是至关重要的，他保存了fiber恢复执行时的必要数据，如pc,sp等

Quasar 中Continuation的实现为Stack类

## Stack

Stack类是quasar 对Fiber Continuation的实现类，该类由quasar instrument调用，以保存和恢复方法调用栈信息

| 属性       | 类型     | 说明                                                     |
| ---------- | -------- | -------------------------------------------------------- |
| sp         | int      | 代表当前操作的frame序号                                  |
| dataLong   | long[]   | holds primitives on stack as well as each method's entry |
| dataObject | Object[] | holds refs on stack，防止jvm gc回收方法局部对象          |

dataLong中每一个long，代表一个method frame,具体定义如下

- entry (PC) : 14 bits, 程序计数器，用于swich case跳转
- num slots : 16 bits, 当前method frame占用多少个slot
- prev method slots : 16 bits , 上一个method frame占用多少个slot，主要用于pop跳转

我简单画了一个stack例子，其中pc,slots,prev slots用逗号分隔,xxxxxx代表method frame额外的一些数据
下面idx和data分别代码dataLong的序号和内容

| idx  | data      | 说明                                                   |
| ---- | --------- | ------------------------------------------------------ |
| 5    | 0L        | 下一个frame的存储位置，sp指向该节点                    |
| 4    | xxxxxxx   | 方法2局部变量c                                         |
| 3    | xxxxxxx   | 方法2局部变量b                                         |
| 2    | 7 , 3 , 2 | 方法2,pc计数器为7,占用3个slot,上一个方法占用2个slots   |
| 1    | xxxxxxx   | 方法1局部变量a                                         |
| 0    | 1，2 , 0  | 方法1,pc计数器为1，占用2个slot，上一个方法占用0个slots |

quasar会在instrument阶段织入stack/continuation逻辑,具体如下

- 调用Suspendable方法之前，调用Stack.pushMethod
- 在Suspendable方法开始， 调用Stack.nextMethodEntry
- 在Suspendable方法结束， 调用Stack.popMethod

下面我们依次看下这几个方法的逻辑

```
Stack.pushMethod
```

- 保存当前pc
- 保存当前slots数量
- 将下一个frame设置成0L

```
Stack.nextMethodEntry
```

- 将sp移动到当前frame位置
- 将上一个freme的slots数量设置到当前frame的prev slots字段

```
Stack.popMethod
```

- 按照当前frame的prev slots进行出栈操作

# Scheduler

scheduler顾名思义，是执行fiber代码的地方，quasar里用ForkJoinPool做为默认scheduler的线程池，

ForkJoinPool的优势这里不再强调，我们主要关注下Quasar中如何使用ForkJoinPool来调度fiber task

![img](https://segmentfault.com/img/bVzFuT)

## FiberForkJoinScheduler

quasar里默认的fiber task scheduler,是JUC ForkJoinPool的wrapper类, ForkJoinPool具体细节参考[ForkJoinPool](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ForkJoinPool.html)
![img](https://segmentfault.com/img/bVzFu0)

```
//主要属性



private final ForkJoinPool fjPool;//具体执行task的线程池



private final FiberTimedScheduler timer;//监控fiber timeout的scheduler



private final Set<FiberWorkerThread> activeThreads;//保存fiber worker线程
```

## FiberForkJoinTask

wrapper了fiber的ForkJoinTask

```
//主要属性



private final ForkJoinPool fjPool;



private final Fiber<V> fiber;
```

![img](https://segmentfault.com/img/bVzFve)

## FiberTimedScheduler

quasar自实现的timeout scheduler，用于fiber timeout的处理
FiberTimedScheduler默认的work queue为SingleConsumerNonblockingProducerDelayQueue,这是一个多生产单消费的无锁队列，类似于Netty的MpscArrayQueue
实现逻辑也比较简单，从SingleConsumerNonblockingProducerDelayQueue内部的优先级队列取数据，如果超时了则调用fiber.unpark()

# monitor

可以通过JMX监控fiber的运行状态，work queue的堆积,fiber的数量，调度延迟等

# comsat

comsat在quasar fiber基础上提供了一些库，使得跟fiber的集成更加容易，比如与servlet、springboot、drapwizard集成

[https://github.com/puniverse/...](https://github.com/puniverse/comsat)

> COMSAT (or Comsat) is a set of open source libraries that integrate Quasar with various web or enterprise technologies (like HTTP services and database access). With Comsat, you can write web applications that are scalable and performing and, at the same time, are simple to code and maintain.
>
> Comsat is not a web framework. In fact, it does not add new APIs at all (with one exception, Web Actors, mentioned later). It provides implementation to popular (and often, standard) APIs like Servlet, JAX-RS, and JDBC, that can be used efficiently within Quasar fibers.

# 遇到的问题与解决

本人在应用中集成Fiber的时候遇到了不少问题，有些问题也反映了Quasar Fiber不是很完善，这里列出来供大家参考下

## Netty PoolByteBufAllocator 在 Fiber调用 导致Memory Leak

由于Quasar字节码的处理，ThreadLocal在fiber上调用，实际是"local to Fiber"，而不是"local to Thread", 如果要绕过Fiber取underlying的ThreadLocal，需要用TrueThreadLocal

Netty的PoolByteBufAllocator$PoolThreadLocalCache用到了ThreadLocal，如果运行在fiber上，每次PoolThreadLocalCache.get()都会返回新的PoolThreadCache对象(因为每个请求起一个新的fiber处理,非WebActor模式)

而在PoolThreadCache的构造函数里，会调用ThreadDeathWatcher.watch，把当前线程和PoolThreadLocalCache.get()返回的对象 add到全局ThreadDeathWatcher列表，以便相关线程停止的时候能释放内存池

但是对于fiber就会有问题了, PoolThreadLocalCache.get()不断的返回新的对象，然后add到ThreadDeathWatcher，而正在运行fiber的fiber-Fork/JoinPool的worker线程并不会终止，最终导致ThreadDeathWatcher里watcher列表越来越多,导致memory leak,100% full gc time

![img](https://segmentfault.com/img/bVzFG7)

问题总结：fiber上ThreadLocal返回的对象，逃逸到了全局对象里，而netty只会在真正的线程(os thread)终止时释放内存

解决办法: 不使用Netty的对象池,或则mock netty代码换成用TrueThreadLocal

## 启动的时候会有[quasar] WARNING: Can’t determine super class of xxx

Quasar这个告警只会在启动的时候出现，可以忽略，Quasar暂时没有开关可以swith off

Fabio: As for the first warning, this is the relevant code and it basically means Quasar’s instrumentor couldn’t load a class’ superclass. This can happen because the class is not present or, more likely, because the classloader where that instrumentation code is running doesn’t allow to access it. Adding to that the strange warning about the agent not being running, I think the latter is most probably the case.

If the application runs you can just ignore the warnings (they should be printed only at instrumentation time, so bootstrap/warming stage) or if you can share a minimal project setup I could help having a deeper look to figure out what’s happening exactly.

[https://groups.google.com/for...](https://groups.google.com/forum/#)!topic/comsat-user/fb_1vrAxfyI

## FJP worker运行时如果有疑似blocking,会有WARNING hogging the CPU or blocking a thread

you can disable the warning by setting a system property with "-Dco.paralleluniverse.fibers.detectRunawayFibers=false”

## 独立Tomcat + Quasar Agent FiberHttpServlet报NPE

[quasar] ERROR: Unable to instrument class co/paralleluniverse/fibers/servlet/FiberHttpServlet

From the full logs I see that my setup and your setup are different though: I’m using an embedded Tomcat while you’re running Tomcat as a standalone servlet container and using the agent for instrumentation. Unfortunately the agent doesn’t currently work with standalone Tomcat and you need to use the instrumenting loader.

官方推荐：独立Tomcat + QuasarWebAppClassLoader 或者 内嵌容器 ＋ Quasar Agent

## WARNING: Uninstrumented methods on the call stack (marked with **)

Quasar不能修改第三方库为@Suspend, 可以显式的把相关的方法放入META-INF/suspendables

独立Tomcat + QuasarWebAppClassLoader UnableToInstrumentException (harmless)

这是个Comsat的bug,但是无害，可以忽略

> UnableToInstrumentException: Unable to instrument co/paralleluniverse/fibers/Fiber#onResume()V because of catch for SuspendExecution
> [google group comsat issues 25](https://github.com/puniverse/comsat/issues/25)