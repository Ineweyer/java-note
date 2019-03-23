# Java多线程学习（四）等待/通知（wait/notify）机制

 版权声明：本文为博主原创文章，未经博主允许不得转载。	https://blog.csdn.net/qq_34337272/article/details/79690279

转载请备注地址：<https://blog.csdn.net/qq_34337272/article/details/79690279> 
**系列文章传送门:**

[Java多线程学习（一）Java多线程入门](https://blog.csdn.net/qq_34337272/article/details/79640870)

[Java多线程学习（二）synchronized关键字（1）](https://blog.csdn.net/qq_34337272/article/details/79655194)

[Java多线程学习（二）synchronized关键字（2）](https://blog.csdn.net/qq_34337272/article/details/79670775)

[Java多线程学习（三）volatile关键字](https://blog.csdn.net/qq_34337272/article/details/79680771)

[Java多线程学习（四）等待/通知（wait/notify）机制](https://blog.csdn.net/qq_34337272/article/details/79690279)

[Java多线程学习（五）线程间通信知识点补充](https://blog.csdn.net/qq_34337272/article/details/79694226)

**系列文章将被优先更新于微信公众号“Java面试通关手册”，欢迎广大Java程序员和爱好技术的人员关注。**

**本节思维导图：** 
![本节思维导图](https://user-gold-cdn.xitu.io/2018/3/25/1625d2a9188ec021?w=1254&h=452&f=jpeg&s=229471)

思维导图源文件+思维导图软件关注[微信公众号](https://www.baidu.com/s?wd=%E5%BE%AE%E4%BF%A1%E5%85%AC%E4%BC%97%E5%8F%B7&tn=24004469_oem_dg)：**“Java面试通关手册”** 回复关键字：**“Java多线程”** 免费领取。

## 一 等待/通知机制介绍

### 1.1 不使用等待/通知机制

当两个线程之间存在生产和消费者关系，也就是说第一个线程（生产者）做相应的操作然后第二个线程（消费者）感知到了变化又进行相应的操作。比如像下面的whie语句一样，假设这个value值就是第一个线程操作的结果，doSomething()是第二个线程要做的事，当满足条件value=desire后才执行doSomething()。

但是这里有个问题就是：第二个语句不停过通过轮询机制来检测判断条件是否成立。如果轮询时间的间隔太小会浪费CPU资源，轮询时间的间隔太大，就可能取不到自己想要的数据。所以这里就需要我们今天讲到的等待/通知（wait/notify）机制来解决这两个矛盾。

```java
    while(value=desire){
        doSomething();
    }123
```

### 1.2 什么是等待/通知机制？

**通俗来讲：**

等待/通知机制在我们生活中比比皆是，一个形象的例子就是厨师和服务员之间就存在等待/通知机制。 
1. 厨师做完一道菜的时间是不确定的，所以菜到服务员手中的时间是不确定的； 
2. 服务员就需要去“等待（wait）”； 
3. 厨师把菜做完之后，按一下铃，这里的按铃就是“通知（nofity）”； 
4. 服务员听到铃声之后就知道菜做好了，他可以去端菜了。

**用专业术语讲：**

等待/通知机制，是指一个线程A调用了对象O的wait()方法进入等待状态，而另一个线程B调用了对象O的notify()/notifyAll()方法，线程A收到通知后退出等待队列，进入可运行状态，进而执行后续操作。上诉两个线程通过对象O来完成交互，而对象上的**wait()方法**和**notify()/notifyAll()方法**的关系就如同开关信号一样，用来完成等待方和通知方之间的交互工作。

### 1.3 等待/通知机制的相关方法

| 方法名称        | 描述                                                         |
| --------------- | ------------------------------------------------------------ |
| notify()        | 随机唤醒等待队列中等待同一共享资源的 **“一个线程”**，并使该线程退出等待队列，进入可运行状态，也就是**notify()方法仅通知“一个线程”** |
| notifyAll()     | 使所有正在等待队列中等待同一共享资源的 **“全部线程”** 退出等待队列，进入可运行状态。此时，优先级最高的那个线程最先执行，但也有可能是随机执行，这取决于JVM虚拟机的实现 |
| wait()          | 使调用该方法的线程释放共享资源锁，然后从运行状态退出，进入等待队列，直到被再次唤醒 |
| wait(long)      | 超时等待一段时间，这里的参数时间是毫秒，也就是等待长达n毫秒，如果没有通知就超时返回 |
| wait(long，int) | 对于超时时间更细力度的控制，可以达到纳秒                     |

## 二 等待/通知机制的实现

### 2.1 我的第一个等待/通知机制程序

MyList.java

```java
public class MyList {
    private static List<String> list = new ArrayList<String>();

    public static void add() {
        list.add("anyString");
    }

    public static int size() {
        return list.size();
    }

}
```

ThreadA.java

```java
public class ThreadA extends Thread {

    private Object lock;

    public ThreadA(Object lock) {
        super();
        this.lock = lock;
    }

    @Override
    public void run() {
        try {
            synchronized (lock) {
                if (MyList.size() != 5) {
                    System.out.println("wait begin "
                            + System.currentTimeMillis());
                    lock.wait();
                    System.out.println("wait end  "
                            + System.currentTimeMillis());
                }
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

}
```

ThreadB.java

```java
public class ThreadB extends Thread {
    private Object lock;

    public ThreadB(Object lock) {
        super();
        this.lock = lock;
    }

    @Override
    public void run() {
        try {
            synchronized (lock) {
                for (int i = 0; i < 10; i++) {
                    MyList.add();
                    if (MyList.size() == 5) {
                        lock.notify();
                        System.out.println("已发出通知！");
                    }
                    System.out.println("添加了" + (i + 1) + "个元素!");
                    Thread.sleep(1000);
                }
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

}
```

Run.java

```java
public class Run {

    public static void main(String[] args) {

        try {
            Object lock = new Object();

            ThreadA a = new ThreadA(lock);
            a.start();

            Thread.sleep(50);

            ThreadB b = new ThreadB(lock);
            b.start();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

    }

}123456789101112131415161718192021
```

运行结果： 
![运行结果](https://user-gold-cdn.xitu.io/2018/3/25/1625c574b1426b6b?w=334&h=317&f=jpeg&s=66304) 
从运行结果:”wait end 1521967322359”最后输出可以看出，notify()执行后并不会立即释放锁。下面我们会补充介绍这个知识点。

**synchronized关键字可以将任何一个Object对象作为同步对象来看待，而Java为每个Object都实现了等待/通知（wait/notify）机制的相关方法，它们必须用在synchronized关键字同步的Object的临界区内**。通过调用wait()方法可以使处于临界区内的线程进入等待状态，同时释放被同步对象的锁。而notify()方法可以唤醒一个因调用wait操作而处于阻塞状态中的线程，使其进入就绪状态。被重新唤醒的线程会视图重新获得临界区的控制权也就是锁，并继续执行wait方法之后的代码。如果发出notify操作时没有处于阻塞状态中的线程，那么该命令会被忽略。

如果我们这里不通过等待/通知（wait/notify）机制实现，而是使用如下的while循环实现的话，我们上面也讲过会有很大的弊端。

```java
 while(MyList.size() == 5){
        doSomething();
    }
```

### 2.2线程的基本状态

上面几章的学习中我们已经掌握了与线程有关的大部分API，这些API可以改变线程对象的状态。如下图所示： 

1. **新建(new)**：新创建了一个线程对象。 
2. **可运行(runnable)**：线程对象创建后，其他线程(比如main线程）调用了该对象的start()方法。该状态的线程位于可运行线程池中，等待被线程调度选中，获 取cpu的使用权。 
3. **运行(running)**：可运行状态(runnable)的线程获得了cpu时间片（timeslice），执行程序代码。 
4. **阻塞(block)**：阻塞状态是指线程因为某种原因放弃了cpu使用权，也即让出了cpu timeslice，暂时停止运行。直到线程进入可运行(runnable)状态，才有 机会再次获得cpu timeslice转到运行(running)状态。阻塞的情况分三种：
-  (一). **等待阻塞**：运行(running)的线程执行o.wait()方法，JVM会把该线程放 入等待队列(waitting queue)中。

- (二). **同步阻塞**：运行(running)的线程在获取对象的同步锁时，若该同步锁 被别的线程占用，则JVM会把该线程放入锁池(lock pool)中。

-  (三). **其他阻塞**: 运行(running)的线程执行Thread.sleep(long ms)或t.join()方法，或者发出了I/O请求时，JVM会把该线程置为阻塞状态。当sleep()状态超时join()等待线程终止或者超时、或者I/O处理完毕时，线程重新转入可运行(runnable)状态。

5. **死亡(dead)**：线程run()、main()方法执行结束，或者因异常退出了run()方法，则该线程结束生命周期。死亡的线程不可再次复生。

**备注：** 
**可以用早起坐地铁来比喻这个过程：**

还没起床：sleeping

起床收拾好了，随时可以坐地铁出发：Runnable

等地铁来：Waiting

地铁来了，但要排队上地铁：I/O阻塞

上了地铁，发现暂时没座位：synchronized阻塞

地铁上找到座位：Running

到达目的地：Dead

### 2.3 notify()锁不释放

**当方法wait()被执行后，锁自动被释放，但执行完notify()方法后，锁不会自动释放。必须执行完notify()方法所在的synchronized代码块后才释放。**

下面我们通过代码验证一下：

（完整代码：<https://github.com/Snailclimb/threadDemo/tree/master/src/wait_notifyHoldLock>）

带wait方法的synchronized代码块

```java
            synchronized (lock) {
                System.out.println("begin wait() ThreadName="
                        + Thread.currentThread().getName());
                lock.wait();
                System.out.println("  end wait() ThreadName="
                        + Thread.currentThread().getName());
            }
```

带notify方法的synchronized代码块

```java
            synchronized (lock) {
                System.out.println("begin notify() ThreadName="
                        + Thread.currentThread().getName() + " time="
                        + System.currentTimeMillis());
                lock.notify();
                Thread.sleep(5000);
                System.out.println("  end notify() ThreadName="
                        + Thread.currentThread().getName() + " time="
                        + System.currentTimeMillis());
            }
```

如果有三个同一个对象实例的线程a,b,c,a线程执行带wait方法的synchronized代码块然后bb线程执行带notify方法的synchronized代码块紧接着c执行带notify方法的synchronized代码块。

运行效果如下： 
![运行效果](https://user-gold-cdn.xitu.io/2018/3/25/1625ce0786da8306?w=682&h=169&f=jpeg&s=163101) 
这也验证了我们刚开始的结论：必须执行完notify()方法所在的synchronized代码块后才释放。

### 2.4 当interrupt方法遇到wait方法

当线程呈wait状态时，对线程对象调用interrupt方法会出现InterruptedException异常。

Service.java

```java
public class Service {
    public void testMethod(Object lock) {
        try {
            synchronized (lock) {
                System.out.println("begin wait()");
                lock.wait();
                System.out.println("  end wait()");
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
            System.out.println("出现异常了，因为呈wait状态的线程被interrupt了！");
        }
    }
}
```

ThreadA.java

```java
public class ThreadA extends Thread {

    private Object lock;

    public ThreadA(Object lock) {
        super();
        this.lock = lock;
    }

    @Override
    public void run() {
        Service service = new Service();
        service.testMethod(lock);
    }

}
```

Test.java

```java
public class Test {

    public static void main(String[] args) {

        try {
            Object lock = new Object();

            ThreadA a = new ThreadA(lock);
            a.start();

            Thread.sleep(5000);

            a.interrupt();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

    }

}
```

并且由于之前的notify通知需要执行完当前的代码块才能交出对象的共享锁，所以即使在interrupt之前调用notify通知等待的线程，同样还是会报InterruptedException。一种简单的方法是对notify语句使用synchronized修饰。

```java
public class Test {

    public static void main(String[] args) {

        try {
            Object lock = new Object();

            ThreadA a = new ThreadA(lock);
            a.start();

			Thread.sleep(1000);

            //这种直接写没有办法把wait线程捞出来,同样会报InterruptedException
            // lock.notify();
            
            //这种写法能够将wait的线程唤醒，同时将锁释放使被唤醒的线程能够从wait状态出来
			synchronized (lock) {
				lock.notify();
			}
			
			Thread.sleep(1000);
			
            a.interrupt();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

    }

}
```



运行结果： 
![运行结果](https://user-gold-cdn.xitu.io/2018/3/25/1625d13890ce09de?w=926&h=178&f=jpeg&s=157966)

参考：

[《Java多线程编程核心技术》](https://www.baidu.com/s?wd=%E3%80%8AJava%E5%A4%9A%E7%BA%BF%E7%A8%8B%E7%BC%96%E7%A8%8B%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E3%80%8B&tn=24004469_oem_dg)

[《Java并发编程的艺术》](https://www.baidu.com/s?wd=%E3%80%8AJava%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B%E7%9A%84%E8%89%BA%E6%9C%AF%E3%80%8B&tn=24004469_oem_dg)

如果你觉得博主的文章不错，欢迎转发点赞。你能从中学到知识就是我最大的幸运。

欢迎关注我的[微信公众号](https://www.baidu.com/s?wd=%E5%BE%AE%E4%BF%A1%E5%85%AC%E4%BC%97%E5%8F%B7&tn=24004469_oem_dg)：**“Java面试通关手册”**（分享各种Java学习资源，面试题，以及[企业级](https://www.baidu.com/s?wd=%E4%BC%81%E4%B8%9A%E7%BA%A7&tn=24004469_oem_dg)Java实战项目回复关键字免费领取）。另外我创建了一个Java学习交流群（群号：**174594747**），欢迎大家加入一起学习，这里更有面试，学习视频等资源的分享。