# 次时代Java编程（一）：Java里的协程

[Java](http://www.open-open.com/lib/tag/Java)   2016-05-19 09:47:52 发布

作者：刘小溪，Maxleap的高级开发工程师，喜欢倒腾一些有意思的技术框架，对新的技术以及语言非常有兴趣，以前在shopex担任架构师，目前在Maxleap负责基础架构以及服务框架这块技术，同时也会对Vert.x的社区提供一些开源上的支持。

责编：钱曙光，关注架构和算法领域，寻求报道或者投稿请发邮件qianshg@csdn.net，另有「CSDN 高级架构师群」，内有诸多知名互联网公司的大牛架构师，欢迎架构师加微信qshuguang2008申请入群，备注姓名+公司+职位。

摘抄自[博客](http://www.open-open.com/lib/view/open1468892872350.html)

### 什么是协程（coroutine）

这东西其实有很多名词，比如有的人喜欢称为纤程（Fiber），或者绿色线程（GreenThread）。其实最直观的解释可以定义为线程的线程。有点拗口，但本质上就是这样。

我们先回忆一下线程的定义，操作系统产生一个进程，进程再产生若干个线程 并行 的处理逻辑，线程的切换由操作系统负责调度。传统语言C++ Java等线程其实与操作系统线程是1:1的关系，每个线程都有自己的Stack，Java在64位系统默认Stack大小是1024KB，所以指望一个进程开启上万个线程是不现实的。但是实际上我们也不会这么干，因为起这么多线程并不能充分的利用CPU，大部分线程处于等待状态，CPU也没有这么核让线程使用。所以一般线程数目都是CPU的核数。

传统的J2EE系统都是基于每个请求占用一个线程去完成完整的业务逻辑（包括事务）。所以系统的吞吐能力取决于每个线程的操作耗时。如果遇到很耗时的I/O行为，则整个系统的吞吐立刻下降，比如JDBC是同步阻塞的，这也是为什么很多人都说数据库是瓶颈的原因。这里的耗时其实是让CPU一直在等待I/O返回，说白了线程根本没有利用CPU去做运算，而是处于空转状态。暴殄天物啊。另外过多的线程，也会带来更多的ContextSwitch开销。

Java的JDK里有封装很好的ThreadPool，可以用来管理大量的线程生命周期，但是本质上还是不能很好的解决线程数量的问题，以及线程空转占用CPU资源的问题。

先阶段行业里的比较流行的解决方案之一就是单线程加上异步回调。其代表派是 node.js 以及Java里的新秀 Vert.x 。他们的核心思想是一样的，遇到需要进行I/O操作的地方，就直接让出CPU资源，然后注册一个回调函数，其他逻辑则继续往下走，I/O结束后带着结果向事件队列里插入执行结果，然后由事件调度器调度回调函数，传入结果。这时候执行的地方可能就不是你原来的代码区块了，具体表现在代码层面上，你会发现你的局部变量全部丢失，毕竟相关的栈已经被覆盖了，所以为了保存之前的栈上数据，你要么选择带着一起放入回调函数里，要么就不停的嵌套，从而引起反人类的Callback hell。

因此相关的Promise，CompletableFuture等技术都是为解决相关的问题而产生的。但是本质上还是不能解决业务逻辑的割裂。

说了这么多，终于可以提一下协程了，协程的本质上其实还是和上面的方法一样，只不过他的核心点在于调度那块由他来负责解决，遇到阻塞操作，立刻yield掉，并且记录当前栈上的数据，阻塞完后立刻再找一个线程恢复栈并把阻塞的结果放到这个线程上去跑，这样看上去好像跟写同步代码没有任何差别，这整个流程可以称为 coroutine ，而跑在由coroutine负责调度的线程称为 Fiber 。比如Golang里的 go 关键字其实就是负责开启一个 Fiber ，让 func 逻辑跑在上面。而这一切都是发生的用户态上，没有发生在内核态上，也就是说没有ContextSwitch上的开销。

既然我们的标题叫Java里的协程，自然我们会讨论JVM上的实现，JVM上早期有 kilim 以及现在比较成熟的 Quasar 。而本文章会全部基于 Quasar ,因为 kilim 已经很久不更新了。

### 简单的例子，用Java写出Golang的味道

上面已经说明了什么是 Fiber(协程调度线程) ，什么是 coroutine（针对阻塞代码的类同步执行流程） 。这里尝试通过 Quasar 来实现类似于golang的 coroutine 以及 channel 。这里假设各位已经大致了解golang。

为了对比，这里先用golang实现一个对于10以内自然数分别求平方的例子，当然了可以直接单线程for循环就完事了，但是为了凸显coroutine的高逼格，我们还是要稍微复杂化一点的。

```java
func counter(out chan<- int) {
  for x := 0; x < 10; x++ {
    out <- x
  }
  close(out)
}

func squarer(out chan<- int, in <-chan int) {
  for v := range in {
    out <- v * v
  }
  close(out)
}

func printer(in <-chan int) {
  for v := range in {
    fmt.Println(v)
  }
}

func main() {
  //定义两个int类型的channel
  naturals := make(chan int)
  squares := make(chan int)

  //产生两个Fiber,用go关键字
  go counter(naturals)
  go squarer(squares, naturals)
  //获取计算结果
  printer(squares)
}
```

上面的例子，有点类似生产消费者模式，通过channel两解耦两边的数据共享。大家可以将channel理解为Java里的 SynchronousQueue 。那传统的基于线程模型的Java实现方式，想必大家都知道怎么做，这里就不啰嗦了，我直接上 Quasar 版的，几乎可以原封不动的copy golang的代码。

```java
public class Example {

  private static void printer(Channel<Integer> in) throws SuspendExecution,  InterruptedException {
    Integer v;
    while ((v = in.receive()) != null) {
      System.out.println(v);
    }
  }

  public static void main(String[] args) throws ExecutionException, InterruptedException, SuspendExecution {
    //定义两个Channel
    Channel<Integer> naturals = Channels.newChannel(-1);
    Channel<Integer> squares = Channels.newChannel(-1);

    //运行两个Fiber实现.
    new Fiber(() -> { for (int i = 0; i < 10; i++) naturals.send(i); naturals.close(); }).start(); 
    new Fiber(() -> { Integer v; while ((v = naturals.receive()) != null) squares.send(v * v); squares.close(); }).start(); 
    printer(squares); 
  } 
}
```

看起来Java似乎要啰嗦一点，没办法这是Java的风格，而且毕竟不是语言上支持coroutine，是通过第三方的库。到后面我会考虑用其他JVM上的语言去实现，这样会显得更精简一点。

说到这里各位肯定对Fiber很好奇了。也许你会表示怀疑Fiber是不是如上面所描述的那样，下面我们尝试用Quasar建立一百万个Fiber，看看内存占用多少，我先尝试了创建百万个 Thread 。

```java
for (int i = 0; i < 1000000; i++) {
  new Thread(() -> { try { Thread.sleep(10000); } catch (InterruptedException e) { e.printStackTrace(); } }).start(); 
} 
```

很不幸，直接报 Exception in thread "main" java.lang.OutOfMemoryError: unable to create new native thread ，这是情理之中的。下面是通过 Quasar 建立百万个 Fiber 。

```java
public static void main(String[] args) throws ExecutionException, InterruptedException, SuspendExecution {
  int FiberNumber = 1_000_000;  //注意这里的下划线不是格式错误，这个是一个可以用来分割数字的占位符，没有具体的作用
  CountDownLatch latch = new CountDownLatch(1);
  AtomicInteger counter = new AtomicInteger(0);

  for (int i = 0; i < FiberNumber; i++) {
    new Fiber(() -> { counter.incrementAndGet(); if (counter.get() == FiberNumber) { System.out.println("done"); } Strand.sleep(1000000); }).start(); } latch.await(); } 
```

我这里加了latch，阻止程序跑完就关闭， Strand.sleep 其实跟 Thread.sleep 一样，只是这里针对的是 Fiber 。

最终控制台是可以输出 done 的，说明程序已经创建了百万个Fiber，设置Sleep是为了让 Fiber 一直运行，从而方便计算内存占用。官方宣称一个空闲的 Fiber 大约占用 400Byte ，那这里应该是占用 400MB 堆内存，但是这里通过 jmap -heap pid 显示大约占用了 1000MB ，也就是说一个 Fiber 占用1KB。

### Quasar是怎么实现Fiber的

其实Quasar实现的coroutine的方式与Golang很像，只不过一个是框架级别实现，一个是语言内置机制而已。

如果你熟悉了Golang的调度机制，那理解Quasar的调度机制就会简单很多，因为两者是差不多的。

Quasar里的Fiber其实是一个continuation，他可以被Quasar定义的scheduler调度，一个continuation记录着运行实例的状态，而且会被随时中断，并且也会随后在他被中断的地方恢复。Quasar其实是通过修改bytecode来达到这个目的，所以运行Quasar程序的时候，你需要先通过java-agent在运行时修改你的代码，当然也可以在编译期间这么干。golang的内置了自己的调度器，Quasar则默认使用 ForkJoinPool 这个JDK7以后才有的，具有 work-stealing 功能的线程池来当调度器。work-stealing非常重要，因为你不清楚哪个Fiber会先执行完，而work-stealing可以动态的从其他的等等队列偷一个context过来，这样可以最大化使用CPU资源。

那这里你会问了，Quasar怎么知道修改哪些字节码呢，其实也很简单，Quasar会通过java-agent在运行时扫描哪些方法是可以中断的，同时会在方法被调用前和调度后的方法内插入一些 continuation 逻辑，如果你在方法上定义了 @Suspendable 注解，那Quasar会对调用该注解的方法做类似下面的事情。

这里假设你在方法 f 上定义了 @Suspendable ，同时去调用了有同样注解的方法 g ，那么所有调用 f 的方法会插入一些字节码，这些字节码的逻辑就是记录当前Fiber栈上的状态，以便在未来可以动态的恢复。(Fiber类似线程也有自己的栈)。在 suspendable方法链内 Fiber的父类会调用 Fiber.park ，这样会抛出 SuspendExecution 异常，从而来停止 线程 的运行，好让Quasar的调度器执行调度。这里的 SuspendExecution 会被Fiber自己捕获，业务层面上不应该捕获到。如果Fiber被唤醒了(调度器层面会去调用 Fiber.unpark )，那么 f 会在被中断的地方重新被调用(这里Fiber会知道自己在哪里被中断)，同时会把 g 的调用结果( g 会return结果)插入到 f 的恢复点，这样看上去就好像 g 的return是 f 的 local variables 了，从而避免了callback嵌套。

上面啰嗦了一大堆，其实简单点讲就是，想办法让运行中的线程栈停下来，好让Quasar的调度器介入。JVM线程中断的条件只有两个，一个是抛异常，另外一个就是return。这里Quasar就是通过抛异常的方式来达到的，所以你会看到我上面的代码会抛出 SuspendExecution 。但是如果你真捕获到这个异常，那就说明有问题了，所以一般会这么写。

写那么多感觉就一句话，就是通过抛异常的方式将代码从当前的运行线程中切出，交由当前的框架进行执行与调度。

```java
@Suspendable
public int f() {
  try {
    // do some stuff
    return g() * 2;
  } catch(SuspendExecution s) {
    //这里不应该捕获到异常.
    throw new AssertionError(s);
  }
}
```

### 与Golang性能对比

在github上无意中发现一个有趣的benchmark，大致是测试各种语言在生成百万actor/Fiber的开销 [skynet](https://github.com/atemerev/skynet) 。

大致的逻辑是先生成10个Fiber，每个Fiber再生成10个Fiber，直到生成1百万个Fiber，然后每个Fiber做加法累积计算，并把结果发到channel里，这样一直递归到根Fiber。后将最终结果发到channel。如果逻辑没有错的话结果应该是499999500000。我们搞个Quasar版的，来测试一下性能。

所有的测试都是基于我的Macbook Pro Retina 2013later。Quasar-0.7.5:JDK8，JDK 1.8.0_91，Golang 1.6

```java
public class Skynet {

  private static final int RUNS = 4;
  private static final int BUFFER = 1000; // = 0 unbufferd, > 0 buffered ; < 0 unlimited

  static void skynet(Channel<Long> c, long num, int size, int div) throws SuspendExecution, InterruptedException {
    if (size == 1) {
      c.send(num);
      return;
    }

    Channel<Long> rc = newChannel(BUFFER);
    long sum = 0L;
    for (int i = 0; i < div; i++) {
      long subNum = num + i * (size / div);
      new Fiber(() -> skynet(rc, subNum, size / div, div)).start();
    }
    for (int i = 0; i < div; i++)
      sum += rc.receive();
    c.send(sum);
  }

  public static void main(String[] args) throws Exception {
    //这里跑4次，是为了让JVM预热好做优化，所以我们以最后一个结果为准。
    for (int i = 0; i < RUNS; i++) {
      long start = System.nanoTime();

      Channel<Long> c = newChannel(BUFFER);
      new Fiber(() -> skynet(c, 0, 1_000_000, 10)).start();
      long result = c.receive();

      long elapsed = (System.nanoTime() - start) / 1_000_000;
      System.out.println((i + 1) + ": " + result + " (" + elapsed + " ms)");
    }
  }
}
```

golang的代码我就不贴了，大家可以从github上拿到，我这里直接贴出结果。

| platform | time  |
| -------- | ----- |
| Golang   | 261ms |
| Quasar   | 612ms |

从Skynet测试中可以看出，Quasar的性能对比Golang还是有差距的，但是不应该达到两倍多吧，经过向Quasar作者求证才得知这个测试并没有测试出实际性能，只是测试调度开销而已。

因为skynet方法内部几乎没有做任何事情，只是简单的做了一个加法然后进一步的递归生成新的Fiber而已，相当于只是测试了Quasar生成并调度百万Fiber所需要的时间而已。而Java里的加法操作开销远比生成Fiber的开销要低，因此感觉整体性能不如golang(golang的coroutine是语言级别的)。

实际上我们在实际项目中生成的Fiber中不可能只做一下简单的加法就退出，至少要花费1ms做一些简单的事情吧，(Quasar里Fiber的调度差不多在us级别)，所以我们考虑在skynet里加一些比较耗时的操作，比如随机生成1000个整数并对其进行排序，这样Fiber里算是有了相应的性能开销，与调度的开销相比，调度的开销就可以忽略不计了。(大家可以把调度开销想象成不定积分的常数)。

下面我分别为两种语言了加了数组排序逻辑，并插在响应的Fiber里。

```java
public class Skynet {

  private static Random random = new Random();
  private static final int NUMBER_COUNT = 1000;
  private static final int RUNS = 4;
  private static final int BUFFER = 1000; // = 0 unbufferd, > 0 buffered ; < 0 unlimited

  private static void numberSort() {
    int[] nums = new int[NUMBER_COUNT];
    for (int i = 0; i < NUMBER_COUNT; i++)
      nums[i] = random.nextInt(NUMBER_COUNT);
    Arrays.sort(nums);
  }

  static void skynet(Channel<Long> c, long num, int size, int div) throws SuspendExecution, InterruptedException {
    if (size == 1) {
      c.send(num);
      return;
    }
    //加入排序逻辑
    numberSort();
    Channel<Long> rc = newChannel(BUFFER);
    long sum = 0L;
    for (int i = 0; i < div; i++) {
      long subNum = num + i * (size / div);
      new Fiber(() -> skynet(rc, subNum, size / div, div)).start();
    }
    for (int i = 0; i < div; i++)
      sum += rc.receive();
    c.send(sum);
  }

  public static void main(String[] args) throws Exception {
    for (int i = 0; i < RUNS; i++) {
      long start = System.nanoTime();

      Channel<Long> c = newChannel(BUFFER);
      new Fiber(() -> skynet(c, 0, 1_000_000, 10)).start();
      long result = c.receive();

      long elapsed = (System.nanoTime() - start) / 1_000_000;
      System.out.println((i + 1) + ": " + result + " (" + elapsed + " ms)");
    }
  }
}
const (
    numberCount = 1000
    loopCount   = 1000000
)

//排序函数
func numberSort() {
    nums := make([]int, numberCount)
    for i := 0; i < numberCount; i++ {
        nums[i] = rand.Intn(numberCount)
    }
    sort.Ints(nums)
}

func skynet(c chan int, num int, size int, div int) {
    if size == 1 {
        c <- num
        return
    }
  //加了排序逻辑
    numberSort()
    rc := make(chan int)
    var sum int
    for i := 0; i < div; i++ {
        subNum := num + i*(size/div)
        go skynet(rc, subNum, size/div, div)
    }
    for i := 0; i < div; i++ {
        sum += <-rc
    }
    c <- sum
}

func main() {
    c := make(chan int)
    start := time.Now()
    go skynet(c, 0, loopCount, 10)
    result := <-c
    took := time.Since(start)
    fmt.Printf("Result: %d in %d ms.\n", result, took.Nanoseconds()/1e6)
}
```

| platform | time    |
| -------- | ------- |
| Golang   | 23615ms |
| Quasar   | 15448ms |

最后再进行一次测试，发现Java的性能优势体现出来了。几乎是golang的1.5倍，这也许是JVM/JDK经过多年优化的优势。因为加了业务逻辑后，对比的就是各种库以及编译器对语言的优化了，协程调度开销几乎可以忽略不计。

### 为什么协程在Java里一直那么小众

其实早在JDK1的时代，Java的线程被称为 GreenThread ，那个时候就已经有了Fiber，但是当时不能与操作系统实现N:M绑定，所以放弃了。现在Quasar凭借 ForkJoinPool 这个成熟的线程调度库。另外，如果你希望你的代码能够跑在Fiber里面，需要一个很大的前提条件，那就是你所有的库，必须是异步无阻塞的，也就说必须类似于node.js上的库，所有的逻辑都是异步回调，而自Java里基本上所有的库都是同步阻塞的，很少见到异步无阻塞的。而且得益于J2EE，以及Java上的三大框架(SSH)洗脑，大部分Java程序员都已经习惯了基于线程，线性的完成一个业务逻辑，很难让他们接受一种将逻辑割裂的异步编程模型。

但是随着异步无阻塞这股风气起来，以及相关的 coroutine 语言Golang大力推广，人们越来越知道如何更好的榨干CPU性能(让CPU避免不必要的等待，减少上下文切换)，阻塞的行为基本发生在I/O上，如果能有一个库能把所有的I/O行为都包装成异步阻塞的话，那么Quasar就会有用武之地，JVM上公认的是异步网络通信库是Netty，通过Netty基本解决了网络I/O问题，另外还有一个是文件I/O，而这个JDK7提供的NIO2就可以满足，通过 AsynchronousFileChannel 即可。剩下的就是如何将他们封装成更友好的API了。目前能达到生产级别的这种异步工具库，JVM上只有 Vert.x3 ，封装了Netty4，封装了 AsynchronousFileChannel ，而且Vert.x官方也出了一个相对应的封装了 Quasar 的库 vertx-sync 。

Quasar目前是由一家商业公司Parallel Universe控制着，且有自己的一套体系，包括Quasar-actor，Quasar-galaxy等各个模块，但是Quasar-core是开源的，此外Quasar自己也通过Fiber封装了很多的第三方库，目前全都在comsat这个项目里。随便找一个项目看看，你会发现其实通过Quasar的Fiber去封装第三方的同步库还是很简单的。

### 写在最后

异步无阻塞的编码方式其实有很多种实现，比如node.js的提倡的Promise，对应到Java8的就是CompletableFuture。

另外事件响应式也算是一个比较流行的做法，比如ReactiveX系列，RxJava、Rxjs、RxSwift等。我个人觉得RxJava是一个非常好的函数式响应实现(JDK9会有对应的JDK实现)，但是我们不能要求所有的程序员一眼就提炼出业务里的functor，monad(这些能力需要长期浸淫在函数式编程思想里)，反而RxJava特别适合用在前端与用户交互的部分，因为用户的点击滑动行为是一个个真实的事件流，这也是为什么RxJava在Android端非常火的原因，而后端基本上都是通过Rest请求过来，每一个请求其实已经限定了业务范围，不会再有复杂的事件逻辑，所以基本上RxJava在Vert.x这端只是做了一堆的flatmap，再加上微服务化，所有的业务逻辑都已经做了最小的边界，所以顺序的同步的编码方式更适合写业务逻辑的后端程序员。

所以这里Golang开了个好头，但是Golang也有其自身的限制，比如不支持泛型，当然这个仁者见仁智者见智了，包的依赖管理比较弱，此外Golang没有线程池的概念，如果coroutine里的逻辑发生了阻塞，那么整个程序会hang死。而这点Vert.x提供了一个Worker Pool的概念，可以将需要耗时执行的逻辑包到线程池里面，执行完后异步返回给EventLoop线程。