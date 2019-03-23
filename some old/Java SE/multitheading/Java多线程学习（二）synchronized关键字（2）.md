# Java多线程学习（二）synchronized关键字（2）



 版权声明：本文为博主原创文章，未经博主允许不得转载。	https://blog.csdn.net/qq_34337272/article/details/79670775

转载请备注地址：<https://blog.csdn.net/qq_34337272/article/details/79670775>

**系列文章传送门:**

[Java多线程学习（一）Java多线程入门](https://blog.csdn.net/qq_34337272/article/details/79640870)

[Java多线程学习（二）synchronized关键字（1）](https://blog.csdn.net/qq_34337272/article/details/79655194)

[Java多线程学习（二）synchronized关键字（2）](https://blog.csdn.net/qq_34337272/article/details/79670775)

[Java多线程学习（三）volatile关键字](https://blog.csdn.net/qq_34337272/article/details/79680771)

**系列文章将被优先更新于微信公众号“Java面试通关手册”，欢迎广大Java程序员和爱好技术的人员关注。**

## (2) synchronized同步语句块

**本节思维导图：** 
![思维导图](https://ws1.sinaimg.cn/large/006rNwoDgy1fpmwg8mb6qj31480c7wf0.jpg)

思维导图源文件+思维导图软件关注[微信公众号](https://www.baidu.com/s?wd=%E5%BE%AE%E4%BF%A1%E5%85%AC%E4%BC%97%E5%8F%B7&tn=24004469_oem_dg)：**“Java面试通关手册”**回复关键字：**“Java多线程”**免费领取。

### 一 synchronized方法的缺点

使用synchronized关键字声明方法有些时候是有很大的弊端的，比如我们有两个线程一个线程A调用同步方法后获得锁，那么另一个线程B就需要等待A执行完，但是如果说A执行的是一个很费时间的任务的话这样就会很耗时。

先来看一个暴露synchronized方法的缺点实例，然后在看看如何通过synchronized同步语句块解决这个问题。

Task.java

```java
public class Task {

    private String getData1;
    private String getData2;

    public synchronized void doLongTimeTask() {
        try {
            System.out.println("begin task");
            Thread.sleep(3000);
            getData1 = "长时间处理任务后从远程返回的值1 threadName="
                    + Thread.currentThread().getName();
            getData2 = "长时间处理任务后从远程返回的值2 threadName="
                    + Thread.currentThread().getName();
            System.out.println(getData1);
            System.out.println(getData2);
            System.out.println("end task");
        } catch (InterruptedException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
}
```

CommonUtils.java

```java
public class CommonUtils {

    public static long beginTime1;
    public static long endTime1;

    public static long beginTime2;
    public static long endTime2;
}
```

MyThread1.java

```java
public class MyThread1 extends Thread {
    private Task task;
    public MyThread1(Task task) {
        super();
        this.task = task;
    }
    @Override
    public void run() {
        super.run();
        CommonUtils.beginTime1 = System.currentTimeMillis();
        task.doLongTimeTask();
        CommonUtils.endTime1 = System.currentTimeMillis();
    }
}
```

MyThread2.java

```java
public class MyThread2 extends Thread {
    private Task task;
    public MyThread2(Task task) {
        super();
        this.task = task;
    }
    @Override
    public void run() {
        super.run();
        CommonUtils.beginTime2 = System.currentTimeMillis();
        task.doLongTimeTask();
        CommonUtils.endTime2 = System.currentTimeMillis();
    }
}
```

Run.java

```java
public class Run {

    public static void main(String[] args) {
        Task task = new Task();

        MyThread1 thread1 = new MyThread1(task);
        thread1.start();

        MyThread2 thread2 = new MyThread2(task);
        thread2.start();

        try {
            Thread.sleep(10000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        //开始时间为最早的，结束时间为最晚的
        long beginTime = CommonUtils.beginTime1;
        if (CommonUtils.beginTime2 < CommonUtils.beginTime1) {
            beginTime = CommonUtils.beginTime2;
        }

        long endTime = CommonUtils.endTime1;
        if (CommonUtils.endTime2 > CommonUtils.endTime1) {
            endTime = CommonUtils.endTime2;
        }

        System.out.println("耗时：" + ((endTime - beginTime) / 1000));
    }
}
```

运行结果： 
![运行结果](https://user-gold-cdn.xitu.io/2018/3/22/1624dca14e084861?w=530&h=219&f=jpeg&s=88932) 
从运行时间上来看，synchronized方法的问题很明显。可以使用synchronized同步块来解决这个问题。但是要注意synchronized同步块的使用方式，如果synchronized同步块使用不好的话并不会带来效率的提升。

### 二 synchronized（this）同步代码块的使用

修改上例中的Task.java如下：

```java
public class Task {

    private String getData1;
    private String getData2;

    public void doLongTimeTask() {
        try {
            System.out.println("begin task");
            Thread.sleep(3000);

            String privateGetData1 = "长时间处理任务后从远程返回的值1 threadName="
                    + Thread.currentThread().getName();
            String privateGetData2 = "长时间处理任务后从远程返回的值2 threadName="
                    + Thread.currentThread().getName();

            synchronized (this) {
                getData1 = privateGetData1;
                getData2 = privateGetData2;
            }

            System.out.println(getData1);
            System.out.println(getData2);
            System.out.println("end task");
        } catch (InterruptedException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
}1234567891011121314151617181920212223242526272829
```

运行结果： 
![运行结果](https://user-gold-cdn.xitu.io/2018/3/22/1624dd0278c28aac?w=515&h=219&f=jpeg&s=85266) 
从上面代码可以看出当一个线程访问一个对象的synchronized同步代码块时，另一个线程任然可以访问该对象非synchronized同步代码块。

**时间虽然缩短了，但是大家考虑一下synchronized代码块真的是同步的吗？它真的持有当前调用对象的锁吗？** 其实这个一种增加并发度的方式，多个线程可以进入各自的数据处理部分，当且仅当在处理同步数据时才加锁，减少了等待数据处理的时间。

是的。不在synchronized代码块中就异步执行，在synchronized代码块中就是同步执行。

验证代码：[synchronizedDemo1包下](https://github.com/Snailclimb/threadDemo/tree/master/src/synchronizedDemo1)

### 三 synchronized（object）代码块间使用

MyObject.java

```java
public class MyObject {
}
```

Service.java

```java
public class Service {

    public void testMethod1(MyObject object) {
        synchronized (object) {
            try {
                System.out.println("testMethod1 ____getLock time="
                        + System.currentTimeMillis() + " run ThreadName="
                        + Thread.currentThread().getName());
                Thread.sleep(2000);
                System.out.println("testMethod1 releaseLock time="
                        + System.currentTimeMillis() + " run ThreadName="
                        + Thread.currentThread().getName());
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

ThreadA.java

```java
public class ThreadA extends Thread {

    private Service service;
    private MyObject object;

    public ThreadA(Service service, MyObject object) {
        super();
        this.service = service;
        this.object = object;
    }

    @Override
    public void run() {
        super.run();
        service.testMethod1(object);
    }
}
```

ThreadB.java

```java
public class ThreadB extends Thread {
    private Service service;
    private MyObject object;

    public ThreadB(Service service, MyObject object) {
        super();
        this.service = service;
        this.object = object;
    }

    @Override
    public void run() {
        super.run();
        service.testMethod1(object);
    }

}
```

Run1_1.java

```java
public class Run1_1 {

    public static void main(String[] args) {
        Service service = new Service();
        MyObject object = new MyObject();

        ThreadA a = new ThreadA(service, object);
        a.setName("a");
        a.start();

        ThreadB b = new ThreadB(service, object);
        b.setName("b");
        b.start();
    }
}
```

运行结果： 
![运行结果](https://user-gold-cdn.xitu.io/2018/3/23/16251d4a132c56ea?w=687&h=118&f=jpeg&s=25840) 
可以看出如下图所示，**两个线程使用了同一个“对象监视器”,所以运行结果是同步的。** 
![同一个对象监视器](https://user-gold-cdn.xitu.io/2018/3/23/16251d5276533606?w=591&h=217&f=jpeg&s=21154) 
**那么，如果使用不同的对象监视器会出现什么效果呢？**

修改Run1_1.java如下：

```java
public class Run1_2 {

    public static void main(String[] args) {
        Service service = new Service();
        MyObject object1 = new MyObject();
        MyObject object2 = new MyObject();

        ThreadA a = new ThreadA(service, object1);
        a.setName("a");
        a.start();

        ThreadB b = new ThreadB(service, object2);
        b.setName("b");
        b.start();
    }
}12345678910111213141516
```

运行结果： 
![运行结果：](https://user-gold-cdn.xitu.io/2018/3/23/16251d9b9bd2401d?w=673&h=108&f=jpeg&s=24859) 
可以看出如下图所示，**两个线程使用了不同的“对象监视器”,所以运行结果不是同步的了。** 
![不同的对象监视器](https://user-gold-cdn.xitu.io/2018/3/23/16251d8b4c273a3d?w=533&h=254&f=jpeg&s=24903)

### 四 synchronized代码块间的同步性

当一个对象访问synchronized(this)代码块时，其他线程对同一个对象中所有其他synchronized(this)代码块代码块的访问将被阻塞，这说明synchronized(this)代码块使用的“对象监视器”是一个。 
也就是说和synchronized方法一样，synchronized(this)代码块也是锁定当前对象的。

另外通过上面的学习我们可以得出两个结论。

1. 其他线程执行对象中synchronized同步方法（上一节我们介绍过，需要回顾的可以看上一节的文章）和synchronized(this)代码块时呈现同步效果;（只要锁住需要同步的对象即可实现同步的效果，即第二条嘛）
2. 如果两个线程使用了同一个“对象监视器”,运行结果同步，否则不同步.

### 五 静态同步synchronized方法与synchronized(class)代码块

synchronized关键字加到static静态方法和synchronized(class)代码块上都是是给Class类上锁，而synchronized关键字加到非static静态方法上是给对象上锁。

Service.java

```java
package ceshi;

public class Service {

    public static void printA() {
        synchronized (Service.class) {
            try {
                System.out.println(
                        "线程名称为：" + Thread.currentThread().getName() + "在" + System.currentTimeMillis() + "进入printA");
                Thread.sleep(3000);
                System.out.println(
                        "线程名称为：" + Thread.currentThread().getName() + "在" + System.currentTimeMillis() + "离开printA");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    synchronized public static void printB() {
        System.out.println("线程名称为：" + Thread.currentThread().getName() + "在" + System.currentTimeMillis() + "进入printB");
        System.out.println("线程名称为：" + Thread.currentThread().getName() + "在" + System.currentTimeMillis() + "离开printB");
    }

    synchronized public void printC() {
        System.out.println("线程名称为：" + Thread.currentThread().getName() + "在" + System.currentTimeMillis() + "进入printC");
        System.out.println("线程名称为：" + Thread.currentThread().getName() + "在" + System.currentTimeMillis() + "离开printC");
    }

}
```

ThreadA.java

```java
public class ThreadA extends Thread {
    private Service service;
    public ThreadA(Service service) {
        super();
        this.service = service;
    }
    @Override
    public void run() {
        service.printA();
    }
}
```

ThreadB.java

```java
public class ThreadB extends Thread {
    private Service service;
    public ThreadB(Service service) {
        super();
        this.service = service;
    }
    @Override
    public void run() {
        service.printB();
    }
}
```

ThreadC.java

```java
public class ThreadC extends Thread {
    private Service service;
    public ThreadC(Service service) {
        super();
        this.service = service;
    }
    @Override
    public void run() {
        service.printC();
    }
}
```

Run.java

```java
public class Run {
    public static void main(String[] args) {
        Service service = new Service();
        ThreadA a = new ThreadA(service);
        a.setName("A");
        a.start();

        ThreadB b = new ThreadB(service);
        b.setName("B");
        b.start();

        ThreadC c = new ThreadC(service);
        c.setName("C");
        c.start();
    }
}
```

运行结果： 
![运行结果](https://user-gold-cdn.xitu.io/2018/3/23/16251f995cdec0d1?w=434&h=152&f=jpeg&s=24963) 
从运行结果可以看出:静态同步synchronized方法与synchronized(class)代码块持有的锁一样，都是Class锁，Class锁对对象的所有实例起作用。synchronized关键字加到非static静态方法上持有的是[对象锁](https://www.baidu.com/s?wd=%E5%AF%B9%E8%B1%A1%E9%94%81&tn=24004469_oem_dg)。在具有Class锁的同时对象锁是可以进入的，从这也可以看出所谓的锁仅和被锁定的对象（或类）有关，如果不同的话那就不会进行检查。

线程A,B和线程C持有的锁不一样，所以A和B运行同步，但是和C运行不同步。 
![实例代码：](https://user-gold-cdn.xitu.io/2018/3/23/16251fcc0c0b750b?w=1006&h=547&f=jpeg&s=83897)

### 六 数据类型String的常量池属性

在Jvm中具有String常量池缓存的功能

```java
    String s1 = "a";
    String s2="a";
    System.out.println(s1==s2);//true123
```

上面代码输出为true.**这是为什么呢？**

**字符串常量池中的字符串只存在一份！ 即执行完第一行代码后，常量池中已存在 “a”，那么s2不会在常量池中申请新的空间，而是直接把已存在的字符串内存地址返回给s2。**

因为数据类型String的常量池属性，所以synchronized(string)在使用时某些情况下会出现一些问题，比如两个线程运行 
synchronized(“abc”)｛ 
｝和 
synchronized(“abc”)｛ 
｝修饰的方法时，这两个线程就会持有相同的锁，导致某一时刻只有一个线程能运行。所以尽量不要使用synchronized(string)而使用synchronized(object)

**参考：**

[《Java多线程编程核心技术》](https://www.baidu.com/s?wd=%E3%80%8AJava%E5%A4%9A%E7%BA%BF%E7%A8%8B%E7%BC%96%E7%A8%8B%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E3%80%8B&tn=24004469_oem_dg) 
[《Java并发编程的艺术》](https://www.baidu.com/s?wd=%E3%80%8AJava%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B%E7%9A%84%E8%89%BA%E6%9C%AF%E3%80%8B&tn=24004469_oem_dg)

> 欢迎关注我的[微信公众号](https://www.baidu.com/s?wd=%E5%BE%AE%E4%BF%A1%E5%85%AC%E4%BC%97%E5%8F%B7&tn=24004469_oem_dg):”**Java面试通关手册**“（一个有温度的微信公众号，无广告，单纯技术分享，期待与你共同进步~~~坚持原创，分享美文，分享各种Java学习资源。)