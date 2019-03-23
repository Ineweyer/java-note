# Java多线程学习（六）Lock锁的使用

 版权声明：本文为博主原创文章，未经博主允许不得转载。	https://blog.csdn.net/qq_34337272/article/details/79714196

**我自己总结的Java学习的系统知识点以及面试问题，目前已经开源，会一直完善下去，欢迎建议和指导欢迎Star：**<https://github.com/Snailclimb/Java-Guide>

**系列文章传送门:**

[Java多线程学习（二）synchronized关键字（1）](https://blog.csdn.net/qq_34337272/article/details/79655194)

[Java多线程学习（二）synchronized关键字（2）](https://blog.csdn.net/qq_34337272/article/details/79670775)

[Java多线程学习（三）volatile关键字](https://blog.csdn.net/qq_34337272/article/details/79680771)

[Java多线程学习（四）等待/通知（wait/notify）机制](https://blog.csdn.net/qq_34337272/article/details/79690279)

[Java多线程学习（五）线程间通信知识点补充](https://blog.csdn.net/qq_34337272/article/details/79694226)

**系列文章将被优先更新于微信公众号“Java面试通关手册”，欢迎广大Java程序员和爱好技术的人员关注。**

**本节思维导图：** 
![本节思维导图](https://user-gold-cdn.xitu.io/2018/3/27/1626755a8e9a8774?w=1197&h=571&f=jpeg&s=258439)

思维导图源文件+思维导图软件关注微信公众号：**“Java面试通关手册”** 回复关键字：**“Java多线程”** 免费领取。

## 一 Lock接口

### 1.1 Lock接口简介

锁是用于通过多个线程控制对共享资源的访问的工具。通常，锁提供对共享资源的独占访问：一次只能有一个线程可以获取锁，并且对共享资源的所有访问都要求首先获取锁。 但是，一些锁可能允许并发访问共享资源，如ReadWriteLock的读写锁。

在Lock接口出现之前，Java程序是靠synchronized关键字实现锁功能的。JDK1.5之后并发包中新增了Lock接口以及相关实现类来实现锁功能。

虽然synchronized方法和语句的范围机制使得使用监视器锁更容易编程，并且有助于避免涉及锁的许多常见编程错误，但是有时您需要以更灵活的方式处理锁。例如，用于遍历并发访问的数据结构的一些算法需要使用**“手动”或“链锁定”**：您获取节点A的锁定，然后获取节点B，然后释放A并获取C，然后释放B并获得D等。在这种场景中synchronized关键字就不那么容易实现了，使用Lock接口容易很多。

**Lock接口的实现类：** 

- ReentrantLock ， 
- ReentrantReadWriteLock.ReadLock ， 
- ReentrantReadWriteLock.WriteLock

### 1.2 Lock的简单使用

```java
  Lock lock=new ReentrantLock()；
  //lock不要放在try语句中，不然会导致在获取锁失败时导致锁无法释放
  lock.lock();
   try{
    }finally{
    lock.unlock();
    }
```

因为Lock是接口所以使用时要结合它的实现类，另外在finall语句块中释放锁的目的是保证获取到锁之后，最终能够被释放。

**注意：** 最好不要把获取锁的过程写在try语句块中，因为如果在获取锁时发生了异常，异常抛出的同时也会导致锁无法被释放。

### 1.3 Lock接口的特性和常见方法

**Lock接口提供的synchronized关键字不具备的主要特性：**

| 特性               | 描述                                                         |
| ------------------ | ------------------------------------------------------------ |
| 尝试非阻塞地获取锁 | 当前线程尝试获取锁，如果这一时刻锁没有被其他线程获取到，则成功获取并持有锁 |
| 能被中断地获取锁   | 获取到锁的线程能够响应中断，当获取到锁的线程被中断时，中断异常将会被抛出，同时锁会被释放 |
| 超时获取锁         | 在指定的截止时间之前获取锁， 超过截止时间后仍旧无法获取则返回 |

**Lock接口基本的方法：**

| 方法名称                                  | 描述                                                         |
| ----------------------------------------- | ------------------------------------------------------------ |
| void lock()                               | 获得锁。如果锁不可用，则当前线程将被禁用以进行线程调度，并处于休眠状态，直到获取锁。 |
| void lockInterruptibly()                  | 获取锁，如果可用并立即返回。如果锁不可用，那么当前线程将被禁用进行线程调度，并且处于休眠状态，和lock()方法不同的是在锁的获取中可以中断当前线程（相应中断）。 |
| Condition newCondition()                  | 获取等待通知组件，该组件和当前的锁绑定，当前线程只有获得了锁，才能调用该组件的wait()方法，而调用后，当前线程将释放锁。 |
| boolean tryLock()                         | 只有在调用时才可以获得锁。如果可用，则获取锁定，并立即返回值为true；如果锁不可用，则此方法将立即返回值为false 。 |
| boolean tryLock(long time, TimeUnit unit) | 超时获取锁，当前线程在一下三种情况下会返回： 1. 当前线程在超时时间内获得了锁；2.当前线程在超时时间内被中断；3.超时时间结束，返回false. |
| void unlock()                             | 释放锁。                                                     |

## 二 Lock接口的实现类：ReentrantLock

ReentrantLock和synchronized关键字一样可以用来实现线程之间的同步互斥，但是在功能是比synchronized关键字更强大而且更灵活。ReentrantLock 获取到是一个排他锁，即只能由一个线程持有锁。

**ReentrantLock类常见方法：**

构造方法：

| 方法名称                    | 描述                                                       |
| --------------------------- | ---------------------------------------------------------- |
| ReentrantLock()             | 创建一个 ReentrantLock的实例。                             |
| ReentrantLock(boolean fair) | 创建一个特定锁类型（公平锁/非公平锁）的ReentrantLock的实例 |

ReentrantLock类常见方法(Lock接口已有方法这里没加上)：

| 方法名称                                                    | 描述                                                       |
| ----------------------------------------------------------- | ---------------------------------------------------------- |
| int getHoldCount()                                          | 查询当前线程保持此锁定的个数，也就是调用lock()方法的次数。 |
| protected Thread getOwner()                                 | 返回当前拥有此锁的线程，如果不拥有，则返回 null            |
| protected Collection getQueuedThreads()                     | 返回包含可能正在等待获取此锁的线程的集合                   |
| int getQueueLength()                                        | 返回等待获取此锁的线程数的估计。                           |
| protected Collection getWaitingThreads(Condition condition) | 返回包含可能在与此锁相关联的给定条件下等待的线程的集合。   |
| int getWaitQueueLength(Condition condition)                 | 返回与此锁相关联的给定条件等待的线程数的估计。             |
| boolean hasQueuedThread(Thread thread)                      | 查询给定线程是否等待获取此锁。                             |
| boolean hasQueuedThreads()                                  | 查询是否有线程正在等待获取此锁。                           |
| boolean hasWaiters(Condition condition)                     | 查询任何线程是否等待与此锁相关联的给定条件                 |
| boolean isFair()                                            | 如果此锁的公平设置为true，则返回 true 。                   |
| boolean isHeldByCurrentThread()                             | 查询此锁是否由当前线程持有。                               |
| boolean isLocked()                                          | 查询此锁是否由任何线程持有。                               |

### 2.1 第一个ReentrantLock程序

ReentrantLockTest.java

```java
public class ReentrantLockTest {

    public static void main(String[] args) {

        MyService service = new MyService();

        MyThread a1 = new MyThread(service);
        MyThread a2 = new MyThread(service);
        MyThread a3 = new MyThread(service);
        MyThread a4 = new MyThread(service);
        MyThread a5 = new MyThread(service);

        a1.start();
        a2.start();
        a3.start();
        a4.start();
        a5.start();

    }

    static public class MyService {

        private Lock lock = new ReentrantLock();

        public void testMethod() {
            lock.lock();
            try {
                for (int i = 0; i < 5; i++) {
                    System.out.println("ThreadName=" + Thread.currentThread().getName() + (" " + (i + 1)));
                }
            } finally {
                lock.unlock();
            }

        }

    }

    static public class MyThread extends Thread {

        private MyService service;

        public MyThread(MyService service) {
            super();
            this.service = service;
        }

        @Override
        public void run() {
            service.testMethod();
        }
    }
}
```

运行结果： 
![运行结果](https://user-gold-cdn.xitu.io/2018/3/27/162657bce509be87?w=278&h=503&f=jpeg&s=231060) 
从运行结果可以看出，当一个线程运行完毕后才把锁释放，其他线程才能执行，其他线程的执行顺序是不确定的。

### 2.2 Condition接口简介

我们通过之前的学习知道了：synchronized关键字与wait()和notify/notifyAll()方法相结合可以实现等待/通知机制，ReentrantLock类当然也可以实现，但是需要借助于Condition接口与newCondition() 方法。Condition是JDK1.5之后才有的，它具有很好的灵活性，比如可以实现多路通知功能也就是在一个Lock对象中可以创建多个Condition实例（即对象监视器），线程对象可以注册在指定的Condition中，从而可以有选择性的进行线程通知，在调度线程上更加灵活。

在使用notify/notifyAll()方法进行通知时，被通知的线程是有JVM选择的，使用ReentrantLock类结合Condition实例可以实现“选择性通知”，这个功能非常重要，而且是Condition接口默认提供的。

而synchronized关键字就相当于整个Lock对象中只有一个Condition实例，所有的线程都注册在它一个身上。如果执行notifyAll()方法的话就会通知所有处于等待状态的线程这样会造成很大的效率问题，而Condition实例的signalAll()方法 只会唤醒注册在该Condition实例中的所有等待线程

**Condition接口的常见方法：**

| 方法名称                                | 描述                                   |
| --------------------------------------- | -------------------------------------- |
| void await()                            | 相当于Object类的wait方法               |
| boolean await(long time, TimeUnit unit) | 相当于Object类的wait(long timeout)方法 |
| signal()                                | 相当于Object类的notify方法             |
| signalAll()                             | 相当于Object类的notifyAll方法          |

### 2.3 使用Condition实现等待/通知机制

\1. **使用单个Condition实例实现等待/通知机制：**

UseSingleConditionWaitNotify.java

```java
public class UseSingleConditionWaitNotify {

    public static void main(String[] args) throws InterruptedException {

        MyService service = new MyService();

        ThreadA a = new ThreadA(service);
        a.start();

        Thread.sleep(3000);

        service.signal();

    }

    static public class MyService {

        private Lock lock = new ReentrantLock();
        public Condition condition = lock.newCondition();

        public void await() {
            lock.lock();
            try {
                System.out.println(" await时间为" + System.currentTimeMillis());
                condition.await();
                System.out.println("这是condition.await()方法之后的语句，condition.signal()方法之后我才被执行");
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                lock.unlock();
            }
        }

        public void signal() throws InterruptedException {
            lock.lock();
            try {               
                System.out.println("signal时间为" + System.currentTimeMillis());
                condition.signal();
                // 即使已经通知释放锁，但是必须还是要等到这个代码块中的代码执行完成之后wait的线程才能继续执行下去，也就是说下面的代码同样是在wait的线程之前执行，不管这个部分有多么耗时
                Thread.sleep(3000);
                System.out.println("这是condition.signal()方法之后的语句");
            } finally {
                lock.unlock();
            }
        }
    }

    static public class ThreadA extends Thread {

        private MyService service;

        public ThreadA(MyService service) {
            super();
            this.service = service;
        }

        @Override
        public void run() {
            service.await();
        }
    }
}
```

运行结果： 
![运行结果](https://user-gold-cdn.xitu.io/2018/3/27/16265c438d4480a8?w=1005&h=114&f=jpeg&s=25742)
在使用wait/notify实现等待通知机制的时候我们知道必须执行完notify()方法所在的synchronized代码块后才释放锁。在这里也差不多，必须执行完signal所在的try语句块之后才释放锁，condition.await()后的语句才能被执行。

**注意：** 必须在condition.await()方法调用之前调用lock.lock()代码获得同步监视器，不然会报错。

2. **使用多个Condition实例实现等待/通知机制：**

UseMoreConditionWaitNotify.java

```java
public class UseMoreConditionWaitNotify {
    public static void main(String[] args) throws InterruptedException {

        MyserviceMoreCondition service = new MyserviceMoreCondition();

        ThreadA a = new ThreadA(service);
        a.setName("A");
        a.start();

        ThreadB b = new ThreadB(service);
        b.setName("B");
        b.start();

        Thread.sleep(3000);

        service.signalAll_A();

    }
    static public class ThreadA extends Thread {

        private MyserviceMoreCondition service;

        public ThreadA(MyserviceMoreCondition service) {
            super();
            this.service = service;
        }

        @Override
        public void run() {
            service.awaitA();
        }
    }
    static public class ThreadB extends Thread {

        private MyserviceMoreCondition service;

        public ThreadB(MyserviceMoreCondition service) {
            super();
            this.service = service;
        }

        @Override
        public void run() {
            service.awaitB();
        }
    }

}
```

MyserviceMoreCondition.java

```java
public class MyserviceMoreCondition {

    private Lock lock = new ReentrantLock();
    public Condition conditionA = lock.newCondition();
    public Condition conditionB = lock.newCondition();

    public void awaitA() {
        lock.lock();
        try {
            System.out.println("begin awaitA时间为" + System.currentTimeMillis()
                    + " ThreadName=" + Thread.currentThread().getName());
            conditionA.await();
            System.out.println("  end awaitA时间为" + System.currentTimeMillis()
                    + " ThreadName=" + Thread.currentThread().getName());
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    public void awaitB() {
        lock.lock();
        try {           
            System.out.println("begin awaitB时间为" + System.currentTimeMillis()
                    + " ThreadName=" + Thread.currentThread().getName());
            conditionB.await();
            System.out.println("  end awaitB时间为" + System.currentTimeMillis()
                    + " ThreadName=" + Thread.currentThread().getName());
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    public void signalAll_A() {
        lock.lock();
        try {           
            System.out.println("  signalAll_A时间为" + System.currentTimeMillis()
                    + " ThreadName=" + Thread.currentThread().getName());
            conditionA.signalAll();
        } finally {
            lock.unlock();
        }
    }

    public void signalAll_B() {
        lock.lock();
        try {       
            System.out.println("  signalAll_B时间为" + System.currentTimeMillis()
                    + " ThreadName=" + Thread.currentThread().getName());
            conditionB.signalAll();
        } finally {
            lock.unlock();
        }
    }
}
```

运行结果： 
![运行结果：](https://user-gold-cdn.xitu.io/2018/3/27/16265e6cc88bb577?w=815&h=171&f=jpeg&s=35885) 
只有A线程被唤醒了。

3. **使用Condition实现顺序执行**

ConditionSeqExec.java

```java
public class ConditionSeqExec {

    volatile private static int nextPrintWho = 1;
    private static ReentrantLock lock = new ReentrantLock();
    final private static Condition conditionA = lock.newCondition();
    final private static Condition conditionB = lock.newCondition();
    final private static Condition conditionC = lock.newCondition();

    public static void main(String[] args) {

        Thread threadA = new Thread() {
            public void run() {
                try {
                    lock.lock();
                    while (nextPrintWho != 1) {
                        conditionA.await();
                    }
                    for (int i = 0; i < 3; i++) {
                        System.out.println("ThreadA " + (i + 1));
                    }
                    nextPrintWho = 2;
                    //通知conditionB实例的线程运行
                    conditionB.signalAll();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } finally {
                    lock.unlock();
                }
            }
        };

        Thread threadB = new Thread() {
            public void run() {
                try {
                    lock.lock();
                    while (nextPrintWho != 2) {
                        conditionB.await();
                    }
                    for (int i = 0; i < 3; i++) {
                        System.out.println("ThreadB " + (i + 1));
                    }
                    nextPrintWho = 3;
                    //通知conditionC实例的线程运行
                    conditionC.signalAll();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } finally {
                    lock.unlock();
                }
            }
        };

        Thread threadC = new Thread() {
            public void run() {
                try {
                    lock.lock();
                    while (nextPrintWho != 3) {
                        conditionC.await();
                    }
                    for (int i = 0; i < 3; i++) {
                        System.out.println("ThreadC " + (i + 1));
                    }
                    nextPrintWho = 1;
                    //通知conditionA实例的线程运行
                    conditionA.signalAll();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } finally {
                    lock.unlock();
                }
            }
        };
        Thread[] aArray = new Thread[5];
        Thread[] bArray = new Thread[5];
        Thread[] cArray = new Thread[5];

        for (int i = 0; i < 5; i++) {
            aArray[i] = new Thread(threadA);
            bArray[i] = new Thread(threadB);
            cArray[i] = new Thread(threadC);

            aArray[i].start();
            bArray[i].start();
            cArray[i].start();
        }

    }
}
```

运行结果： 
![Condition实现顺序执行运行结果](https://user-gold-cdn.xitu.io/2018/3/27/162661b0506d4c60?w=197&h=537&f=jpeg&s=21966) 
通过代码很好理解，说简单就是在一个线程运行完之后通过condition.signal()/condition.signalAll()方法通知下一个特定的运行，就这样循环往复即可。由于其中的Lock只有一个，即在所有的情况下只能由一个线程获得当前的锁，从而保证了多个线程之间的串行。

**注意：** 默认情况下ReentranLock类使用的是非公平锁

### 2.4 公平锁与非公平锁

Lock锁分为：**公平锁** 和 **非公平锁**。公平锁表示线程获取锁的顺序是按照线程加锁的顺序来分配的，即先来先得的**FIFO先进先出顺序**。而非公平锁就是一种获取锁的抢占机制，是**随机获取**锁的，和公平锁不一样的就是先来的不一定先的到锁，这样可能造成某些线程一直拿不到锁，结果也就是不公平的了。

FairorNofairLock.java

```java
public class FairorNofairLock {

    public static void main(String[] args) throws InterruptedException {
        final Service service = new Service(true);//true为公平锁，false为非公平锁

        Runnable runnable = new Runnable() {
            @Override
            public void run() {
                System.out.println("★线程" + Thread.currentThread().getName()
                        + "运行了");
                service.serviceMethod();
            }
        };

        Thread[] threadArray = new Thread[10];
        for (int i = 0; i < 10; i++) {
            threadArray[i] = new Thread(runnable);
        }
        for (int i = 0; i < 10; i++) {
            threadArray[i].start();
        }

    }
    static public class Service {

        private ReentrantLock lock;

        public Service(boolean isFair) {
            super();
            lock = new ReentrantLock(isFair);
        }

        public void serviceMethod() {
            lock.lock();
            try {
                System.out.println("ThreadName=" + Thread.currentThread().getName()
                        + "获得锁定");
            } finally {
                lock.unlock();
            }
        }

    }
}
```

运行结果： 
![公平锁运行结果](https://user-gold-cdn.xitu.io/2018/3/27/162660df5094cc67?w=363&h=471&f=jpeg&s=44774) 
公平锁的运行结果是有序的。

把Service的参数修改为false则为非公平锁

```java
final Service service = new Service(false);//true为公平锁，false为非公平锁
12
```

![ 非公平锁运行结果](https://user-gold-cdn.xitu.io/2018/3/27/162660e6dbee2ff4?w=370&h=476&f=jpeg&s=45086) 
非公平锁的运行结果是无序的。

## 三 ReadWriteLock接口的实现类：ReentrantReadWriteLock

### 3.1 简介

我们刚刚接触到的ReentrantLock（排他锁）具有完全互斥排他的效果，即同一时刻只允许一个线程访问，这样做虽然虽然保证了实例变量的线程安全性，但效率非常低下。ReadWriteLock接口的实现类-ReentrantReadWriteLock读写锁就是为了解决这个问题。

读写锁维护了两个锁，一个是读操作相关的锁也成为共享锁，一个是写操作相关的锁 也称为排他锁。通过分离读锁和写锁，其并发性比一般排他锁有了很大提升。

多个读锁之间不互斥，读锁与写锁互斥，写锁与写锁互斥（只要出现写操作的过程就是互斥的。）。在没有线程Thread进行写入操作时，进行读取操作的多个Thread都可以获取读锁，而进行写入操作的Thread只有在获取写锁后才能进行写入操作。即多个Thread可以同时进行读取操作，但是同一时刻只允许一个Thread进行写入操作。读与读之间是共享的，只要含有写就是互斥的。

### 3.2 ReentrantReadWriteLock的特性与常见方法

**ReentrantReadWriteLock的特性：**

| 特性       | 说明                                                         |
| ---------- | ------------------------------------------------------------ |
| 公平性选择 | 支持非公平（默认）和公平的锁获取方式，吞吐量上来看还是非公平优于公平 |
| 重进入     | 该锁支持重进入，以读写线程为例：读线程在获取了读锁之后，能够再次获取读锁。而写线程在获取了写锁之后能够再次获取写锁也能够同时获取读锁 |
| 锁降级     | 遵循获取写锁、获取读锁再释放写锁的次序，写锁能够降级称为读锁 |

**ReentrantReadWriteLock常见方法：** 
构造方法

| 方法名称                             | 描述                                                         |
| ------------------------------------ | ------------------------------------------------------------ |
| ReentrantReadWriteLock()             | 创建一个 ReentrantReadWriteLock()的实例                      |
| ReentrantReadWriteLock(boolean fair) | 创建一个特定锁类型（公平锁/非公平锁）的ReentrantReadWriteLock的实例 |

常见方法： 
和ReentrantLock类 类似这里就不列举了。

### 3.3 ReentrantReadWriteLock的使用

1. **读读共享**

两个线程同时运行read方法，你会发现两个线程可以同时或者说是几乎同时运行lock()方法后面的代码，输出的两句话显示的时间一样。这样提高了程序的运行效率。

```java
    private ReentrantReadWriteLock lock = new ReentrantReadWriteLock();

    public void read() {
        try {
            try {
                lock.readLock().lock();
                System.out.println("获得读锁" + Thread.currentThread().getName()
                        + " " + System.currentTimeMillis());
                Thread.sleep(10000);
            } finally {
                lock.readLock().unlock();
            }
        } catch (InterruptedException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
```

2. **写写互斥**

把上面的代码的

```java
lock.readLock().lock();
```

改为：

```java
lock.writeLock().lock();
```

两个线程同时运行read方法，你会发现同一时间只允许一个线程执行lock()方法后面的代码

3. **读写互斥**

```java
    private ReentrantReadWriteLock lock = new ReentrantReadWriteLock();

    public void read() {
        try {
            try {
                lock.readLock().lock();
                System.out.println("获得读锁" + Thread.currentThread().getName()
                        + " " + System.currentTimeMillis());
                Thread.sleep(10000);
            } finally {
                lock.readLock().unlock();
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public void write() {
        try {
            try {
                lock.writeLock().lock();
                System.out.println("获得写锁" + Thread.currentThread().getName()
                        + " " + System.currentTimeMillis());
                Thread.sleep(10000);
            } finally {
                lock.writeLock().unlock();
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
```

测试代码：

```java
        Service service = new Service();

        ThreadA a = new ThreadA(service);
        a.setName("A");
        a.start();

        Thread.sleep(1000);

        ThreadB b = new ThreadB(service);
        b.setName("B");
        b.start();
```

运行两个使用同一个Service对象实例的线程a,b，线程a执行上面的read方法，线程b执行上面的write方法。你会发现同一时间只允许一个线程执行lock()方法后面的代码。记住：只要出现写操作的过程就是互斥的。

4. **写读互斥**

和读写互斥类似，这里不用代码演示了。记住：只要出现写操作的过程就是互斥的。

**参考：**

《Java多线程编程核心技术》

《Java并发编程的艺术》

> 欢迎关注我的微信公众号:”**Java面试通关手册**“（一个有温度的微信公众号，无广告，单纯技术分享，期待与你共同进步~~~坚持原创，分享美文，分享各种Java学习资源。您想关注便关注，��，公众号只是我记录文字和生活的地方，无所谓利益。)