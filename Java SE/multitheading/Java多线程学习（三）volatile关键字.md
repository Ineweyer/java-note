# Java多线程学习（三）volatile关键字



 版权声明：本文为博主原创文章，未经博主允许不得转载。	https://blog.csdn.net/qq_34337272/article/details/79680771



转载请备注地址：<https://blog.csdn.net/qq_34337272/article/details/79680693>

**系列文章传送门：** 
[Java多线程学习（一）Java多线程入门](https://blog.csdn.net/qq_34337272/article/details/79640870)

[Java多线程学习（二）synchronized关键字（1）](https://blog.csdn.net/qq_34337272/article/details/79655194)

[Java多线程学习（二）synchronized关键字（2）](https://blog.csdn.net/qq_34337272/article/details/79670775)

[Java多线程学习（三）volatile关键字](https://blog.csdn.net/qq_34337272/article/details/79680771)

[Java多线程学习（四）等待/通知（wait/notify）机制](https://blog.csdn.net/qq_34337272/article/details/79690279)

**系列文章将被优先更新于微信公众号“Java面试通关手册”，欢迎广大Java程序员和爱好技术的人员关注。**

**本节思维导图：**

![volatile关键字](https://ws1.sinaimg.cn/large/006rNwoDgy1fpov5xqf97j30rc09674f.jpg)

思维导图源文件+思维导图软件关注微信公众号：**“Java面试通关手册”**回复关键字：**“Java多线程”**免费领取。

### 一 简介

#### **先来看看维基百科对“volatile关键字”的定义：**

在程序设计中，尤其是在C语言、C++、C#和Java语言中，使用volatile关键字声明的变量或对象通常具有与优化、多线程相关的特殊属性。通常，volatile关键字用来阻止（伪）编译器认为的无法“被代码本身”改变的代码（变量/对象）进行优化。如在C语言中，volatile关键字可以用来提醒编译器它后面所定义的变量随时有可能改变，因此编译后的程序每次需要存储或读取这个变量的时候，都会直接从变量地址中读取数。如果没有volatile关键字，则编译器可能优化读取和存储，可能暂时使用寄存器中的值，如果这个变量由别的程序更新了的话，将出现不一致的现象。据

在C环境中，volatile关键字的真实定义和适用范围经常被误解。虽然C++、C#和Java都保留了C中的volatile关键字，但在这些编程语言中volatile的用法和语义却大相径庭。

#### **Java中的“volatile关键字”关键字：**

在 JDK1.2 之前，Java的内存模型实现总是从**主存**（即共享内存）读取变量，是不需要进行特别的注意的。而在当前的 Java 内存模型下，线程可以把变量保存**本地内存**（比如机器的寄存器）中，而不是直接在主存中进行读写。这就可能造成一个线程在主存中修改了一个变量的值，而另外一个线程还继续使用它在寄存器中的变量值的拷贝，造成**数据的不一致**。 
![数据的不一致](https://ws1.sinaimg.cn/large/006rNwoDgy1fpo4w1256pj307l04m3yf.jpg) 
要解决这个问题，就需要把变量声明为 **volatile**，这就指示 JVM，这个变量是不稳定的，每次使用它都到主存中进行读取。 
![volatile关键字的可见性](https://ws1.sinaimg.cn/large/006rNwoDgy1fpo4w2fiv9j30d606mmx5.jpg)

### 二 volatile关键字的可见性

**volatile 修饰的成员变量**在每次被线程访问时，都强迫**从主存（共享内存）中重读该成员变量的值**。而且，当成员变量发生变化时，**强迫线程将变化值回写到主存（共享内存）**。这样在任何时刻，**两个不同的线程总是看到某个成员变量的同一个值**，这样也就保证了同步数据的**可见性**。

RunThread.java

```java
 private boolean isRunning = true;
 int m;
    public boolean isRunning() {
        return isRunning;
    }
    public void setRunning(boolean isRunning) {
        this.isRunning = isRunning;
    }
    @Override
    public void run() {
        System.out.println("进入run了");
        while (isRunning == true) {
            int a=2;
            int b=3;
            int c=a+b;
            m=c;
        }
        System.out.println(m);
        System.out.println("线程被停止了！");
    }
}
```

Run.java

```java
public class Run {
    public static void main(String[] args) throws InterruptedException {
        RunThread thread = new RunThread();

        thread.start();
        Thread.sleep(1000);
        thread.setRunning(false);

        System.out.println("已经赋值为false");
    }
}
```

运行结果： 
![死循环](https://user-gold-cdn.xitu.io/2018/3/24/162576feda44ccf6?w=722&h=218&f=jpeg&s=13245)

RunThread类中的isRunning变量没有加上volatile关键字时，运行以上代码会出现死循环，这是因为isRunning变量虽然被修改但是没有被写到主存中，这也就导致该线程在本地内存中的值一直为true，这样就导致了死循环的产生。

解决办法也很简单：isRunning变量前加上volatile关键字即可。 
![isRunning变量前加上volatile关键字](https://user-gold-cdn.xitu.io/2018/3/24/1625749cb6172840?w=568&h=60&f=jpeg&s=28225) 
这样运行就不会出现死循环了。 
加上volatile关键字后的运行结果： 
![加上volatile关键字后的运行结果](https://user-gold-cdn.xitu.io/2018/3/24/16257706f546dc54?w=418&h=116&f=jpeg&s=7860)

**你是不是以为到这就完了？**

**不存在的！！！**(这里还有一点需要强调，下面的内容一定要看，不然你在用volatile关键字时会很迷糊，因为书籍几乎都没有提这个问题)

假如你把while循环代码里加上任意一个输出语句或者sleep方法你会发现死循环也会停止，不管isRunning变量是否被加上了上volatile关键字。

加上输出语句：

```java
    while (isRunning == true) {
            int a=2;
            int b=3;
            int c=a+b;
            m=c;
            System.out.println(m);
        }
```

加上sleep方法：

```java
        while (isRunning == true) {
            int a=2;
            int b=3;
            int c=a+b;
            m=c;
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                // TODO Auto-generated catch block
                e.printStackTrace();
            }
        }
```

**这是为什么呢？**

因为：**JVM会尽力保证内存的可见性**，即便这个变量没有加同步关键字。换句话说，只要CPU有时间，JVM会尽力去保证变量值的更新。这种与volatile关键字的不同在于，volatile关键字会强制的保证线程的可见性。而不加这个关键字，JVM也会尽力去保证可见性，但是如果CPU一直有其他的事情在处理，它也没办法。最开始的代码，一直处于死循环中，CPU处于一直占用的状态，这个时候CPU没有时间，JVM也不能强制要求CPU分点时间去取最新的变量值。而加了输出或者sleep语句之后，CPU就有可能有时间去保证内存的可见性，于是while循环可以被终止。

### 三 volatile关键字能保证原子性吗？

《Java并发编程艺术》这本书上说保证但是在自增操作（非原子操作）上不保证，《Java多线程编程核心艺术》这本书说不保证。

我个人更倾向于这种说法：volatile无法保证对变量原子性的。我个人感觉《Java并发编程艺术》这本书上说volatile关键字保证原子性吗但是在自增操作（非原子操作）上不保证这种说法是有问题的。只是个人看法，希望不要被喷。可以看下面测试代码：

MyThread.java

```java
public class MyThread extends Thread {
    volatile public static int count;

    private static void addCount() {
        for (int i = 0; i < 100; i++) {
            count=i;
        }
        System.out.println("count=" + count);

    }
    @Override
    public void run() {
        addCount();
    }
}
```

Run.java

```java
public class Run {
    public static void main(String[] args) {
        MyThread[] mythreadArray = new MyThread[100];
        for (int i = 0; i < 100; i++) {
            mythreadArray[i] = new MyThread();
        }

        for (int i = 0; i < 100; i++) {
            mythreadArray[i].start();
        }
    }

}
```

运行结果：

上面的“count=i;”是一个原子操作，但是运行结果大部分都是正确结果99，但是也有部分不是99的结果。 **个人观点，显然这个也不是原子操作，其中包含一个取值操作和赋值操作，分两步进行不是原子操作**
![运行结果](https://user-gold-cdn.xitu.io/2018/3/24/16257997d1c7c065?w=162&h=511&f=jpeg&s=101761) 
**解决办法**：

使用synchronized关键字加锁。（这只是一种方法，Lock和AtomicInteger原子类都可以，因为之前学过synchronized关键字，所以我们使用synchronized关键字的方法）

修改MyThread.java如下：

```java
public class MyThread extends Thread {
    public static int count;

    synchronized private static void addCount() {
        for (int i = 0; i < 100; i++) {
            count=i;
        }
        System.out.println("count=" + count);
    }
    @Override
    public void run() {
        addCount();
    }
}
```

这样运行输出的count就都为99了，所以要保证数据的**原子性**还是要使用synchronized关键字。

### 四 synchronized关键字和volatile关键字比较

- **volatile关键字**是线程同步的**轻量级实现**，所以**volatile性能肯定比synchronized关键字要好**。但是**volatile关键字只能用于变量而synchronized关键字可以修饰方法以及代码块**。synchronized关键字在JavaSE1.6之后进行了主要包括为了减少获得锁和释放锁带来的性能消耗而引入的偏向锁和轻量级锁以及其它各种优化之后执行效率有了显著提升，**实际开发中使用synchronized关键字还是更多一些**。
- **多线程访问volatile关键字不会发生阻塞，而synchronized关键字可能会发生阻塞**
- **volatile关键字能保证数据的可见性，但不能保证数据的原子性。synchronized关键字两者都能保证。**
- **volatile关键字用于解决变量在多个线程之间的可见性，而ynchronized关键字解决的是多个线程之间访问资源的同步性。**

**参考：** 
《Java多线程编程核心技术》

《Java并发编程的艺术》

极客学院Java并发编程wiki: <http://wiki.jikexueyuan.com/project/java-concurrency/volatile1.html>

如果你觉得博主的文章不错，欢迎转发点赞。你能从中学到知识就是我最大的幸运。

> 欢迎关注我的微信公众号:”**Java面试通关手册**“（一个有温度的微信公众号，无广告，单纯技术分享，期待与你共同进步~~~坚持原创，分享美文，分享各种Java学习资源。你想关注便关注，公众号只是我记录文字和生活的地方，无所谓利益，请不要一棒子打死一群做“自媒体”的人。)