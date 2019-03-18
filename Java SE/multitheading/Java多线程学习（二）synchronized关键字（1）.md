# Java多线程学习（二）synchronized关键字（1）

 版权声明：本文为博主原创文章，未经博主允许不得转载。	https://blog.csdn.net/qq_34337272/article/details/79655194

转载请备注地址： <https://blog.csdn.net/qq_34337272/article/details/79655194>

**Java多线程学习（二）**将分为两篇文章介绍**synchronized同步方法**另一篇介绍**synchronized同步语句块**。

### (1) synchronized同步方法

**本节思维导图：** 
![img](https://ws1.sinaimg.cn/large/006rNwoDgy1fplqhsdks6j30sk0dm3yz.jpg)
思维导图源文件+思维导图软件关注微信公众号：“Java面试通关手册”回复关键字：“Java多线程”免费领取。

### 一 简介

Java并发编程这个领域中synchronized关键字一直都是元老级的角色，很久之前很多人都会称它为“重量级锁”。但是，在JavaSE 1.6之后进行了主要包括为了减少获得锁和释放锁带来的性能消耗而引入的偏向锁和轻量级锁以及其它各种优化之后变得在某些情况下并不是那么重了。

这一篇文章不会介绍synchronized关键字的实现原理，更多的是synchronized关键字的使用。如果想要了解的可以看看方腾飞的《Java并发编程的艺术》。

### 二 变量安全性

“非线程安全”问题存在于“实例变量”中，如果是方法内部的私有变量，则不存在“非线程安全”问题，所得结果也就是“线程安全”的了。

如果两个线程同时操作对象中的实例变量，则会出现“非线程安全”，解决办法就是在方法前加上synchronized关键字即可。前面一篇文章我们已经讲过，而且贴过相应代码，所以这里就不再贴代码了。

### 三 多个对象多个锁

**先看例子：**

HasSelfPrivateNum.java

```java
public class HasSelfPrivateNum {

    private int num = 0;

    synchronized public void addI(String username) {
        try {
            if (username.equals("a")) {
                num = 100;
                System.out.println("a set over!");
                //如果去掉hread.sleep(2000)，那么运行结果就会显示为同步的效果
                Thread.sleep(2000);
            } else {
                num = 200;
                System.out.println("b set over!");
            }
            System.out.println(username + " num=" + num);
        } catch (InterruptedException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }

}
```

ThreadA.java

```java
public class ThreadA extends Thread {

    private HasSelfPrivateNum numRef;

    public ThreadA(HasSelfPrivateNum numRef) {
        super();
        this.numRef = numRef;
    }

    @Override
    public void run() {
        super.run();
        numRef.addI("a");
    }

}
```

ThreadB.java

```java
public class ThreadB extends Thread {

    private HasSelfPrivateNum numRef;

    public ThreadB(HasSelfPrivateNum numRef) {
        super();
        this.numRef = numRef;
    }

    @Override
    public void run() {
        super.run();
        numRef.addI("b");
    }

}
```

Run.java

```java
public class Run {

    public static void main(String[] args) {

        HasSelfPrivateNum numRef1 = new HasSelfPrivateNum();
        HasSelfPrivateNum numRef2 = new HasSelfPrivateNum();

        ThreadA athread = new ThreadA(numRef1);
        athread.start();

        ThreadB bthread = new ThreadB(numRef2);
        bthread.start();

    }

}
```

运行结果： 
a num=100停顿一会才执行 
![多个对象多个锁](https://user-gold-cdn.xitu.io/2018/3/21/16248d58ee63c79d?w=158&h=99&f=jpeg&s=4454) 
上面实例中两个线程ThreadA和ThreadB分别访问同一个类的不同实例的相同名称的同步方法，但是效果确实异步执行。

**为什么会这样呢？**

这是因为synchronized取得的锁都是对象锁，而不是把一段代码或方法当做锁。所以在上面的实例中，哪个线程先执行带synchronized关键字的方法，则哪个线程就持有该方法所属对象的锁Lock，那么其他线程只能呈等待状态，前提是多个线程访问的是同一个对象。本例中很显然是两个对象。

在本例中创建了两个HasSelfPrivateNum类对象，所以就产生了两个锁。当ThreadA的引用执行到addI方法中的runThread.sleep(2000)语句时，ThreadB就会“乘机执行”。所以才会导致执行结果如上图所示（备注：由于runThread.sleep(2000)，“a num=100”停顿了两秒才输出）

### 四 synchronized方法与锁对象

通过上面我们知道synchronized取得的锁都是对象锁，而不是把一段代码或方法当做锁。如果多个线程访问的是同一个对象，哪个线程先执行带synchronized关键字的方法，则哪个线程就持有该方法，那么其他线程只能呈等待状态。如果多个线程访问的是多个对象则不一定，因为多个对象会产生多个锁。

**那么我们思考一下当多个线程访问的是同一个对象中的非synchronized类型方法会是什么效果？**

**答案是：**会异步调用非synchronized类型方法，解决办法也很简单在非synchronized类型方法前加上synchronized关键字即可。

### 五 脏读

发生脏读的情况实在读取实例变量时，此值已经被其他线程更改过。

PublicVar.java

```java
public class PublicVar {

    public String username = "A";
    public String password = "AA";

    synchronized public void setValue(String username, String password) {
        try {
            this.username = username;
            Thread.sleep(5000);
            this.password = password;

            System.out.println("setValue method thread name="
                    + Thread.currentThread().getName() + " username="
                    + username + " password=" + password);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
     //该方法前加上synchronized关键字就同步了
     public void getValue() {
        System.out.println("getValue method thread name="
                + Thread.currentThread().getName() + " username=" + username
                + " password=" + password);
    }
}
```

ThreadA.java

```java
public class ThreadA extends Thread {

    private PublicVar publicVar;

    public ThreadA(PublicVar publicVar) {
        super();
        this.publicVar = publicVar;
    }

    @Override
    public void run() {
        super.run();
        publicVar.setValue("B", "BB");
    }
}
```

Test.java

```java
public class Test {

    public static void main(String[] args) {
        try {
            PublicVar publicVarRef = new PublicVar();
            ThreadA thread = new ThreadA(publicVarRef);
            thread.start();

            Thread.sleep(200);//打印结果受此值大小影响

            publicVarRef.getValue();
        } catch (InterruptedException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }

    }
}
```

运行结果： 
![运行结果](https://user-gold-cdn.xitu.io/2018/3/21/1624911ac7c86cc3?w=679&h=58&f=jpeg&s=57359) 
解决办法：getValue()方法前加上synchronized关键字即可。

加上synchronized关键字后的运行结果： 
![运行结果](https://user-gold-cdn.xitu.io/2018/3/21/162491341fe4d47c?w=712&h=66&f=jpeg&s=12218)

### 六 synchronized锁重入

“可重入锁”概念是：自己可以再次获取自己的内部锁。比如一个线程获得了某个对象的锁，此时这个对象锁还没有释放，当其再次想要获取这个对象的锁的时候还是可以获取的，如果不可锁重入的话，就会造成死锁。

- 继承是获取可重入锁的一种方式

Service.java

```java
public class Service {

    synchronized public void service1() {
        System.out.println("service1");
        service2();
    }

    synchronized public void service2() {
        System.out.println("service2");
        service3();
    }

    synchronized public void service3() {
        System.out.println("service3");
    }

}
```

MyThread.java

```java
public class MyThread extends Thread {
    @Override
    public void run() {
        Service service = new Service();
        service.service1();
    }

}
```

Run.java

```java
public class Run {
    public static void main(String[] args) {
        MyThread t = new MyThread();
        t.start();
    }
}
```

运行结果： 
![运行结果](https://user-gold-cdn.xitu.io/2018/3/21/162491b80b3753b1?w=168&h=73&f=jpeg&s=3303) 
另外可重入锁也支持在父子类继承的环境中

Main.java：

```java
public class Main {

    public int i = 10;

    synchronized public void operateIMainMethod() {
        try {
            i--;
            System.out.println("main print i=" + i);
            Thread.sleep(100);
        } catch (InterruptedException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
}
```

Sub.java：

```java
public class Sub extends Main {

    synchronized public void operateISubMethod() {
        try {
            while (i > 0) {
                i--;
                System.out.println("sub print i=" + i);
                Thread.sleep(100);
                this.operateIMainMethod();
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

MyThread.java：

```java
public class MyThread extends Thread {
    @Override
    public void run() {
        Sub sub = new Sub();
        sub.operateISubMethod();
    }
}
```

Run.java：

```java
public class Run {
    public static void main(String[] args) {
        MyThread t = new MyThread();
        t.start();
    }
}
```

运行结果： 
![运行结果](https://user-gold-cdn.xitu.io/2018/3/21/162492277b7d2bdd?w=242&h=239&f=jpeg&s=13811)

说明当存在父子类继承关系时，子类是完全可以通过“可重入锁”调用父类的同步方法。

另外出现异常时，其锁持有的锁会自动释放。

### 七 同步不具有继承性

如果父类有一个带synchronized关键字的方法，子类继承并重写了这个方法。 
但是同步不能继承，所以还是需要在子类方法中添加synchronized关键字。

**参考：** 
《Java多线程编程核心技术》