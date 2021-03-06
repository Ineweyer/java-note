# JVM

## 1. 运行时数据区域

![img](https://cyc2018.github.io/CS-Notes/pics/83e9c5ed-35a1-41fd-b0dd-ce571969b5f3_200.png)

上图中需要说明的是：当前Hotspot使用堆中永久代来存放方法区，两者没有必然的关系（可以在另外的地方进行实现），只是一种实现而已（规范与实现的关系）。

私有和共享来分类（三三分）：

**线程私有**：程序计数器、虚拟机栈、本地方法栈

**线程共有的**：堆、方法区、直接内存

**虚拟机参数：**

-Xms 初始内存

-Xmx 最大可用堆内存

-Xmn 年轻代内存大小

-Xss 虚拟机栈的初始大小

### 1.1 程序计数器

程序计数器是一块较小的内存空间，可以看做是当前线程执行字节码的行号指示器。

**作用：**

1. 字节码指示器通过改变程序计数器依次读取指令，从而实现代码的流程控制，如顺序执行、选择、循环、异常处理
2. 在多线程的情况下，程序计数器用于记录当前线程的位置，从而当线程被切换回来的时候能够知道该线程上运行的位置。

**程序计数器是唯一不会出现OutOfMemoryError的内存区域**，它的生命周期随着线程的创建而创建，随着线程的终结而终结。

### 1.2 java虚拟机栈

线程私有，生命周期与线程相同，描述java方法执行的内存模型。局部变量主要存放编译器可知的各种数据类型（boolean、byte、char、short、int、float、long、double）、对象引用。

虚拟机栈会出现的两种异常：

- StackOverFlowError，栈的大小允许调整，达到了最大栈深，并且栈的深度不是一个确定的值
- OutOfMemoryError，栈的大小可以调整，但是内存已经用完了，无法在动态拓展时。

### 1.3 本地方法栈

和虚拟机栈所发挥的作用相似，区别是：**虚拟机栈为虚拟机执行java方法（字节码）服务，而本地方法栈则为虚拟机使用到Native方法服务**，hotspot中将两者合二为一。

为本地方法保存：局部变量表、操作数栈、动态链接、出口信息，同样会出现StackOverFlowError和OutOfMemoryError

### 1.4 堆

**唯一目的是存放对象实例，几乎所有的对象实例以及数组都在这里分配内存**，是垃圾收集器管理的主要区域，因此也被称为GC堆。

堆可以分为：新生代和老年代（细致划分的话为：Eden空间、From Survivor、To Survivor），进一步划分的目的是为了更好或者更快地回收分配内存。

**在 JDK 1.8中移除整个永久代，取而代之的是一个叫元空间（Metaspace）的区域（永久代使用的是JVM的堆内存空间，而元空间使用的是物理内存，直接受到本机的物理内存限制）。**

**永久代向元空间转换的原因？**

1. 字符串存在永久代中，容易出现性能问题和内存溢出
2. 类及方法等的信息比较难确定大小，因此对永久代大小的指定比较困难，太小容易导致出现永久代溢出，太大容易导致老年代溢出
3. 永久代为GC带来不必要的复杂度，并且回收效率偏低
4. Oracle可能将HotSpot和JRockit合二为一

### 1.5 方法区

**方法区与 Java 堆一样，是各个线程共享的内存区域，它用于存储已被虚拟机加载的类信息、常量、静态变量、即时编译器编译后的代码等数据。虽然Java虚拟机规范把方法区描述为堆的一个逻辑部分，但是它却有一个别名叫做 Non-Heap（非堆），目的应该是与 Java 堆区分开来**

HotSpot中这个方法区常被称为“永久代”，本质上两者不等价。

**相对而言，垃圾收集行为在这个区域是比较少见的，但并非数据进入方法区后就“永久存在了”**

### 1.6 运行时常量池

是方法区的一部分。

**JDK1.7及之后版本的 JVM 已经将运行时常量池从方法区中移了出来，在 Java 堆（Heap）中开辟了一块区域存放运行时常量池。**

![img](https://camo.githubusercontent.com/17620721a9f326a235aeec8956949cec03f3f125/687474703a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f31382d392d31342f32363033383433332e6a7067)

### 1.7 直接内存

直接内存不是虚拟机运行时数据区的一部分，也不是虚拟机规范中定义的内存区域，但是这部分内存也被频繁地使用。而且也可能导致OutOfMemoryError（由于机器的内存还是不够引起的。

## 2. HotSpot虚拟机对象探秘

### 2.1  对象的创建

![Javaå¯¹è±¡çåå"ºè¿ç¨](https://camo.githubusercontent.com/e99480df412dd718430d78094143a5485c908fa7/68747470733a2f2f757365722d676f6c642d63646e2e786974752e696f2f323031382f382f32322f313635363165353961343133353836393f773d39353026683d32373926663d706e6726733d3238353239)

1. 类加载检查：遇到new指令时，检查指令参数能否在常量池中定位到这个类的符号引用，并且检查类是否已经被加载、准备、解析、和初始化过。如果没有则必须执行相应的类加载过程。
2. 分配内存。分配方式有**指针碰撞**和**空闲列表**，选择哪种方式由Java堆是否规整决定，而java堆是否规整又由采用的垃圾收集器是否带有压缩功能决定。

![img](https://camo.githubusercontent.com/65fc0c035f1f70081f4dcdd113c3b2c7aa931a2a/68747470733a2f2f757365722d676f6c642d63646e2e786974752e696f2f323031382f382f32322f313635363165353961343061326333643f773d3134323626683d33333326663d706e6726733d3236333436)

创建对象的线程安全保证：

- CAS+失败重试，保证更新操作的原子性
- TLAB，为每一个线程预先在Eden区分配一块儿内存，JVM在给线程中对象分配内存时，首先在TALB中分配，当对象大于TLAB中的剩余内存或TALB内存已用尽时，再采用上述的CAS进行内存分配。

3. 初始化零值，将分配到的空间初始化为零值，保证对象的实例字段不用赋值就能直接使用。
4. 设置对象头，对对象进行必要的设置，将类似于元数据信息、对象的哈希码，对象的GC分代年龄、是否使用偏向锁等设置到对象头中。
5. 执行init方法，在jvm的角度，对象已经产生了，但是在java程序的角度，对象创建才刚开始，需要按照程序员意愿进行初始化。

### 2.2 对象的内存布局

布局分为：对象头、实例数据、对齐填充

对象头中包括：用于存储对象自身的自身运行时数据（哈希码、GC分代年龄、锁标志状态等）；类型指针，即对象指向它的类元数据的指针。

实例数据：是对象真正存储的有效信息

### 2.3 对象的访问定位

目前主流的访问方式有**①使用句柄**和**②直接指针**两种：

1. **句柄：** 如果使用句柄的话，那么Java堆中将会划分出一块内存来作为句柄池，reference 中存储的就是对象的句柄地址，而句柄中包含了对象实例数据与类型数据各自的具体地址信息； [![使用句柄](https://camo.githubusercontent.com/5923998f8408ea936a0416291faf1b2a1d215108/68747470733a2f2f757365722d676f6c642d63646e2e786974752e696f2f323031382f342f32372f313633303662393537333936383934363f773d37383626683d33363226663d706e6726733d313039323031)](https://camo.githubusercontent.com/5923998f8408ea936a0416291faf1b2a1d215108/68747470733a2f2f757365722d676f6c642d63646e2e786974752e696f2f323031382f342f32372f313633303662393537333936383934363f773d37383626683d33363226663d706e6726733d313039323031)
2. **直接指针：** 如果使用直接指针访问，那么 Java 堆对象的布局中就必须考虑如何放置访问类型数据的相关信息，而reference 中存储的直接就是对象的地址。

[![使用直接指针](https://camo.githubusercontent.com/af3d3845b1d5d9c1927e77f79d2cb96ea84090fe/68747470733a2f2f757365722d676f6c642d63646e2e786974752e696f2f323031382f342f32372f313633303662613361343162366236353f773d37363626683d33353326663d706e6726733d3939313732)](https://camo.githubusercontent.com/af3d3845b1d5d9c1927e77f79d2cb96ea84090fe/68747470733a2f2f757365722d676f6c642d63646e2e786974752e696f2f323031382f342f32372f313633303662613361343162366236353f773d37363626683d33353326663d706e6726733d3939313732)

**这两种对象访问方式各有优势。使用句柄来访问的最大好处是 reference 中存储的是稳定的句柄地址，在对象被移动时只会改变句柄中的实例数据指针，而 reference 本身不需要修改。使用直接指针访问方式最大的好处就是速度快，它节省了一次指针定位的时间开销。**

## 3. 重点补充内容

### 3.1 String类和常量池

```java
String str1 = "abcd";
String str2 = new String("abcd");
System.out.println(str1==str2);//false
```

两种方式的区别：

![img](https://camo.githubusercontent.com/83e5520e2e0fe8c2b4b1575da9b06d42bbac581f/68747470733a2f2f757365722d676f6c642d63646e2e786974752e696f2f323031382f382f32322f313635363165353961353963303837333f773d36393826683d33353526663d706e6726733d3130343439)

**String 类型的常量池比较特殊。它的主要使用方法有两种：**

- 直接使用双引号声明出来的 String 对象会直接存储在常量池中。
- 如果不是用双引号声明的 String 对象，可以使用 String 提供的 intern 方法。String.intern() 是一个 Native 方法，它的作用是：**如果运行时常量池中已经包含一个等于此 String 对象内容的字符串，则返回常量池中该字符串的引用；如果没有，则在常量池中创建与此 String 内容相同的字符串，并返回常量池中创建的字符串的引用。**

尽量避免多个字符串的拼接，因为这样会重新创建对象。如果需要改变使用StringBuilder或者StringBuffer。



**intern**

[几张图轻松理解String.intern()](<https://blog.csdn.net/soonfly/article/details/70147205>)

> 原来在常量池中找不到时，复制一个副本放到常量池，1.7后则是将在堆上的地址引用复制到常量池。

### 3.2 8种基本类型的包装类和常量池

- **Java 基本类型的包装类的大部分都实现了常量池技术，即Byte,Short,Integer,Long,Character,Boolean；这5种包装类默认创建了数值[-128，127]的相应类型的缓存数据，但是超出此范围仍然会去创建新的对象。**
- **两种浮点数类型的包装类 Float,Double 并没有实现常量池技术。**

通过如下的方式可以获得基本类型的缓存常量（以Integer类型为例）

```java
IntegerCache.cache[下标];  // 获取指定下标的常量缓存
IntegerCache.low;         //获取该类型最小的常量
IntegerCache.high;        //获取该类型最大的常量
```

## 4. JVM垃圾回收

### 4.1 堆内存

堆内存示意图：分为新生代和老年代，新生代中又可以分为Eden和Survivor 1区和Survivor 2区。在java1.8 中移出整个永久代，取而代之的是一个叫做元空间（Metaspace）的区域。

![img](https://camo.githubusercontent.com/05d1fe88e8ac4b03f299af3519b37e76ba4bebf3/68747470733a2f2f757365722d676f6c642d63646e2e786974752e696f2f323031382f382f32352f313635373033343461323963333433333f773d35393926683d32353026663d706e6726733d38393436)

堆内存分配策略：

![img](https://camo.githubusercontent.com/8da9ff51df49d4cf41107c6fc9db0f782bde9d38/687474703a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f31382d382d32372f38393239343534372e6a7067)

**对象优先在eden区分配：**通过添加`XX:+PrintGCDetails` 能够查看内存的分配情况

**Minor GC和Full GC有什么不同？**

- **新生代GC（Minor GC）**:指发生新生代的的垃圾收集动作，Minor GC非常频繁，回收速度一般也比较快。
- **老年代GC（Major GC/Full GC）**:指发生在老年代的GC，出现了Major GC经常会伴随至少一次的Minor GC（并非绝对），Major GC的速度一般会比Minor GC的慢10倍以上。

**分配担保机制**：发起一个Minor GC时发现对象无法存入Survivor空间时，把新生代的对象提前转移到老年代中去。

**大对象直接进入老年代：**由于分代管理思想的需要，虚拟机给每个对象一个对象年龄（Age）计数器，每存存活一次，年龄加1，当年龄增加到一定程度（默认为15，可以通过-XX:MaxTenuringThreshold设置），就会晋升到老年代中。

**长期存活对象将进入老年代：**如果 Survivor 空间中相同年龄所有对象大小的总和大于 Survivor 空间的一半，年龄大于或等于该年龄的对象就可以直接进入老年代

### 4.2 对象已经死亡？

![img](https://camo.githubusercontent.com/b3a4bf00f50b9981e3c7b933d54f276e20933b66/687474703a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f31382d382d32372f31313033343235392e6a7067)

#### 4.2.1. 判断对象已经失效方法

**1. 引用计数法**

给对象添加引用计数器，当存在引用，计数器加一，引用失效，计数器减1.

**这个方法实现简单，效率高，但是目前主流的虚拟机中并没有选择这个算法来管理内存，其最主要的原因是它很难解决对象之间相互循环引用的问题。**

**2. 可达性分析**

通过一系列"GC Roots"对象为起点进行搜索，节点走过的路径称为引用链，当对象没有与引用链连接时证明当前对象是不可用的。

#### 4.2.2 引用

将 引用分为强引用、软引用、弱引用、虚引用四种。

**1. 强引用**

大部分的引用都是强引用，垃圾回收器绝不会回收它。

**2. 软引用**

软引用是一个可有可无的对象，软引用可以和一个引用队列（ReferenceQueue）联合使用，如果软引用所引用的对象被垃圾回收，JAVA虚拟机就会把这个软引用加入到与之关联的引用队列中。

**3. 弱引用**

弱引用也是一个可有可无的对象，与软引用的区别是只具有弱引用的对象拥有更短暂的生命周期。一旦发现只具有弱引用的对象，立马就进行回收，但是由于回收扫描具有一定延迟不一定立马就能发现弱引用。

**4. 虚引用**

"虚引用"顾名思义，就是形同虚设，与其他几种引用都不同，虚引用并不会决定对象的生命周期。如果一个对象仅持有虚引用，那么它就和没有任何引用一样，在任何时候都可能被垃圾回收。主要用来跟踪对象被垃圾回收的活动。与软引用、弱引用的另外一个区别是：必须和引用队列联合使用，当垃圾回收时如果存在虚引用，会在回收对象之前把这个虚引用加入到与之关联的引用队列中。程序可以通过判断引用队列中是否加入了虚引用来了解被引用的对象是否将要被垃圾回收，程序如果发现某个虚引用已经被加入到引用队列，那么就可以在所引用的队列，那么就可以在所引用的对象内存被回收之前采取必要的行动。**软引用可以加速JVM对垃圾内存的回收速度，可以维护系统的运行安全，防止内存溢出（OutOfMemory）等问题的产生，所以很少使用弱引用和虚引用，而更多的使用软引用**。

#### 4.2.3 不可达对象并非“非死不可”

宣告一个对象死亡需要经历**两次标记**过程

第一次标记：可达性分析中不可达对象被第一次标记并进行筛选，筛选的条件为有没有必要执行finalize方法。不必要执行的条件为：没有重写，或者finalize方法已经被执行。

第二次标记：被判定为需要执行的对象将会被放在一个队列中进行第二次标记，除非在finalize中与引用链上的对象建立联系，否则就会真的被回收。

#### 4.2.4 判断常量为废弃常量

没有引用就证明是废弃常量，如果发生GC并且有必要的话，就会被系统清理出常量池（可以但是不一定做）

#### 4.2.5 如何判断类是一个无用的类

条件：

- 该类所有的实例都已经被回收，也就是 Java 堆中不存在该类的任何实例。
- 加载该类的 ClassLoader 已经被回收。
- 该类对应的 java.lang.Class 对象没有在任何地方被引用，无法在任何地方通过反射访问该类的方法。

### 4.3 垃圾回收算法

![åå¾æ¶éç®æ³](https://camo.githubusercontent.com/733140b59bdacba20708c9addd847f58ce7b4270/687474703a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f31382d382d32372f313134323732332e6a7067)

#### 4.3.1 标记-清除算法

标记需要回收的对象，标记完成后进行统一的回收。效率更好，但是会造成空间碎片化。

#### 4.3.2 复制算法

将内存划分为大小相同的两块：每次使用其中的 一块，每一块内存使用完成后就将还存活的对象复制到另一块中去，然后把使用空间一次清理。

这种方式不存在碎片化问题，但是频繁的数据移动性能低，每次只能利用一般内存浪费严重

#### 4.3.3 标记-整理算法

标记过程和“标记-清除”算法一样，后续让所有存活对象向一端移动，直接清理掉端边界以外的内存。

#### 4.3.4 分代收集算法

**根据对象存活的周期不同将内存分为不同的几块，在每块中使用最适合其特点的回收算法法。**

**在新生代中，每次收集有大量的对象死去，可以选择复制算法，只需要少量的对象复制成本就可以完成每次的垃圾处理。而老年代中对象存活几率比较高，而且没有额外的空间进行分配担保，所以必须选择“标记-清除”或者“标记-整理算法”进行垃圾收集。**

### 4.4 垃圾收集器

![img](https://camo.githubusercontent.com/0d92016056cd3fd259cc0565429132b26ed94207/687474703a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f31382d382d32372f34313436303935352e6a7067)

**没有最好的垃圾收集器，我们只能根据具体的应用场景选择适合自己的垃圾收集器**

#### 4.4.1 Serial收集器

特点：**单线程收集**+**stop the world收集**（需要停下所有的运行工作线程直到收集完成）

算法：新生代采用复制算法、老年代采用标记整理法

优缺点：停顿带来不良的用户体验，但是其简单高效，适合于client模式下的虚拟机

#### 4.4.2 ParNew收集器

**ParNew收集器其实就是Serial收集器的多线程版本，除了使用多线程进行垃圾收集外，其余行为（控制参数、收集算法、回收策略等等）和Serial收集器完全一样。**

特点：多线程收集+stop the world

算法：新生代采用复制算法、老年代采用标记整理算法

并发：多个线程同时进入可运行状态，存在同时运行也可能是交替执行

并行：多个线程同时执行（指令同时执行）

#### 4.4.3 Parallel Scavenge 收集器

**CMS等垃圾收集器的关注点更多的是用户线程的停顿时间（提高用户体验）。所谓吞吐量就是CPU中用于运行用户代码的时间与CPU总消耗时间的比值。** 

特点：关注吞吐量（交由虚拟机去进行优化，选择合适的参数）+stop the world

算法：新生代采用复制算法、老年代采用标记整理算法。

#### 4.4.4 Serial Old收集器

Serial收集器的老年代版本，同样是一个单线程收集器。

两大用途：在jdk1.5之前和Parrllel Scavenge收集器搭配使用；另外一种用途作为CMS收集器后备方案。

#### 4.4.5 Parallel Old收集器

Parrllel Scavenge收集器的老年代版本，适用于注重吞吐率和CPU资源的场所。

#### 4.4.6 CMS收集器

**CMS（Concurrent Mark Sweep）收集器是一种以获取最短回收停顿时间为目标的收集器。它而非常符合在注重用户体验的应用上使用。**

**CMS（Concurrent Mark Sweep）收集器是HotSpot虚拟机第一款真正意义上的并发收集器，它第一次实现了让垃圾收集线程与用户线程（基本上）同时工作。**

基于**标记-清除**算法实现，过程分为四步：

- **初始标识：**暂停所有的其他线程，记录直接与root相连的对象（速度很快）
- **并发标识：**同时开启GC和用户线程，用一个闭包结构取记录可达对象。由于用户线程会更新引用，所以需要记录更新的位置
- **重新标记：**停下所有用户线程（这个停顿的时间远比并发标识的时间少），修正并发标识期间修改的标识。
- **并发清除：**开启用户线程，同时GC开始对标记区域做清扫

优点：**并发收集、低停顿。**

缺点：

- **对cpu资源敏感**
- **无法处理浮动垃圾**
- **回收算法采用“标记-清除”，产生大量碎片**

#### 4.4.7 G1收集器

**G1 (Garbage-First)是一款面向服务器的垃圾收集器,主要针对配备多颗处理器及大容量内存的机器. 以极高概率满足GC停顿时间要求的同时,还具备高吞吐量性能特征.**

特点（JDK1.7 中重要的进化特征）：

- 并发与并行：充分利用多核资源，使得GC收集器和应用程序可以并发执行
- 分代收集：不需要其他的收集器配合就能独立管理，但是依然保留分代概念
- 空间整理：从整体上看属于“标记-整理”，从局部上看数据“标记-复制”算法。
- 可预测的停顿：还能建立可预测的停顿时间模型，让使用者明确指定在一个长度为M毫秒的时间片段内。

步骤：

- 初始标记
- 并发标记
- 最终标记
- 筛选回收

**G1收集器在后台维护了一个优先列表，每次根据允许的收集时间，优先选择回收价值最大的Region(这也就是它的名字Garbage-First的由来)**。

## 5. JVM性能监控和故障处理工具

### 5.1 常见的监控和故障处理工具

- **jps**：JVM Process Status Tool ,显示指定系统内所有的HotSpot虚拟机进程
- **jstat**: JVM Statistics Monitoring Tool ,用于收集HotSpot虚拟机各方面的运行数据。
- **jinfo**: Configuration Info forJava,显示虚拟机配置信息
- **jmap**: Memory Map for Java，生成虚拟机的内存转储快照（heapdump文件）
- **jhat**: JVM Heap Dump Browser ,用于分析heapdump文件，它会建立一个HTTP/HTML服务器，让用户可以在浏览器上查看分析结果
- **jstack**: Stack Trace forJava，显示虚拟机的线程快照

#### 5.1.1  jps 

列出正在运行的虚拟机进程，并显示主类名称以及进程的唯一id

![img](https://mmbiz.qpic.cn/mmbiz_png/hvUCbRic69sA677fYLaK097UvD0GXDjA4GOhViamNJUN6Y2iconiarDH43nfeFMkNE0m2y9ubibWuKKPtHdsVyNAtew/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

#### 5.1.2  jstat虚拟机信息监视工具

用于监视虚拟机各种运行状态信息的命令行工具。

![img](https://mmbiz.qpic.cn/mmbiz_png/hvUCbRic69sA677fYLaK097UvD0GXDjA4ZkYcAId5Kiak3SnezrPKJXFI8KeFZGiaGNhThwicGib6r0Mmm1hqPYA28Q/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

#### 5.1.3 jinfo 

作用是实时查看和调整虚拟机各项参数。

#### 5.1.4 jmap java内存映射工具

用于生成堆转储快照（也可以通过-XX: +HeapDumpOnOutOfMemoryError让虚拟机出现异常后自动生成dump文件），它还可以查询finalize执行队列，java堆、永久代的详细信息，如空间使用率，当前使用哪种收集器。

#### 5.1.5 jstack

用于生成当前时刻虚拟机的线程快照。线程快照就是当前虚拟机内每一条线程正在执行的方法堆栈的集合。生成线程快照的原因是定位线程长时间出现停顿的 原因。

### 5.2 JDK可视化工具

**JConsole和VisualVM是两个功能强大的可视化工具。**

## 6. 类加载机制

**java语言中类型的加载连接以及初始化过程都是在程序运行期间完成的**，增加了加载时的性能开销，但是提高灵活性。例如可以编写面向接口的程序，等到运行时指定其具体的实现类。

![img](https://mmbiz.qpic.cn/mmbiz_png/hvUCbRic69sAxRovHTCH3yyW1vpic22WVibSBjZicRXsFogpbuwicqugUaxFaIBQnXxwibQ0XTicKxxCVfb0L8sejJkjw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

### 6.1 类加载时机

必须对类进行初始化的五种情况：

- **new、getstatic、putstatic、invokestatic**使用new关键字实例化对象的时候、读取或设置一个类的静态字段的时候，已经调用一个类的静态方法的时候。
- 使用java.lang.reflect包的方法对类进行**反射调用**的时候，如果类没有初始化，则需要先触发其初始化。
- 当**初始化**一个类的时候，如果发现其父类没有被初始化就会先初始化它的**父类**。
- 当虚拟机启动的时候，用户需要指定一个要执行的**主类**（就是包含main()方法的那个类），虚拟机会先**初始化这个类**；
- 使用**Jdk1.7动态语言支持的时候的一些情况**。

**被动引用：**所有引用类的方式都不会触发初始化，三个例子如下所示：

- 通过子类引用父类静态字段，不会导致子类初始化。
- 通过数组定义引用类，不会触发此类的初始化
- 常量在编译阶段会存入调用类的常量池中，本质上没有直接引用定义常量的类，不会触发定义常量类的初始化。

### 6.2 类加载的过程

加载、验证、准备、解析和初始化。

#### 6.2.1 加载

三个基本动作：

- 1）通过类型的完全限定名，产生一个代表该类的二进制数据流
- 2）解析这个二进制数据流为方法区内的运行时数据结构
- 3）创建一个表示该类型的java.lang.Class类的实例，作为方法区这个类的各种数据的访问入口

通过类型完全限定名，产生一个代表该类型的二进制数据流的常见方式：

- 从zip包中读取，成为日后jar、ear、war格式的基础
- 从网络获取，典型应用时Applet
- 运行时计算生成，例如动态代理技术
- 由其他文件生成，比如JSP

加载可以有系统提供的类加载器完成，也可以由用户自行类的加载器去完成。

#### 6.2.2 验证

目的：**为了确保Class文件的字节流包含的信息符合当前虚拟机的要求，并且不会危害虚拟机自身的安全。**

主要完成四个阶段的校验工作：**文件格式、元数据、字节码、符号引用**

**1. 格式验证**

保证输入的字节流能正确地解析并存储于方法区之内。即完成操作后，字节流会存入方法区进行存储，之后不再直接操作字节流。

**2. 元数据验证**

**该阶段对字节码描述的信息进行语义分析，以保证其描述的信息符合Java语言规范的要求**

目的：**保证不存在不符合Java语言规范的元数据信息**

**3. 字节码验证**

主要工作：**工作时进行数据流和控制流分析，保证被校验类的方法在运行时不会做出危害虚拟机安全的行为**

JDK1.6之后引入一项优化技术，在方法体Code属性的属性表中增加一项“StackMapTable”属性，该属性描述了方法体中所有基本块开始时本地变量表和操作栈应有的状态，从而将字节码验证的类型推导转表为类型检查减少时间。

**通过验证也并非是安全的**

**4.符号引用验证**

**最后一个阶段的校验发生在虚拟机将符号引用转化为直接引用的时候，这个转化动作将在连接的第三个阶段——解析阶段中发生。符号引用验证的目的是确保解析动作能正常执行。**

验证主要内容：

- 通过字符串描述的全限定名能够找到对应的类
- 指定类中是否存在符号方法的字段描述及简单名称所描述的方法和字段
- 符号引用中类、字段、和方法的访问性（private、protected、public、default）是否可以被当前类访问

#### 6.2.3. 准备

**准备阶段是正式为类变量分配内存并设置类变量初始值的阶段，这些变量（static类型变量）所使用的内存都将在方法区中进行分配。**

**初始值通常是数据类型的零值**，准备阶段的初始化不同于后面的初始化，这个位置和程序员指定的初始化一般不具有关系。但是具有特殊情况，例如：static final 类型的变量在准备阶段就已经是程序员指定的初始值。

#### 6.2.4 解析

解析过程是虚拟机将常量池中符号引用直接替换为直接引用的过程。

符号引用（Symbolic References）：以一组符号来描述所引用的目标，与地址无关。

直接引用（Direct References）：是直接指向目标的指针、相对偏移量、或是一个能直接定位到目标的句柄。

对解析结果进行缓存

#### 6.2.5 初始化

之前的步骤都是有jvm主导，到了这个阶段才是和java语言对应上。

## 7. 类加载器

对于任意一个类，都需要类和类加载器来确定其唯一性，对于相同的类，如果类加载器不同那么他也不是同一个类。

### 7.1 类加载器分类

从虚拟机的角度：启动类加载器（BootStrap ClassLoader，由c++语言实现，是jvm的一部分）和其他类加载器（继承自ClassLoader类，由java语言实现）。

#### 7.1.1 启动类加载器（BootStrap  ClassLoader）

负责将**\lib**目录中，或者**被-Xbootclasspath参数指定的路径**中的并且是**虚拟机识别**的类库加载到虚拟机内存中。

#### 7.1.2 拓展类加载器（Extension ClassLoader）

这个加载器由sun.misc.Launcher$ExtClassLoader实现，它负责加载**\lib\ext**目录中的，或者被**java.ext.dirs系统变量**所指定的路径中的所有类库，开发者可以直接使用扩展类加载器。

#### 7.1.3 应用程序加载器（Application ClassLoader）

这个类加载器由sun.misc.Launcher$AppClassLoader实现。由于这个类加载器是ClassLoader中的getSystemClassLoader()方法的返回值，所以一般也称它为系统类加载器。它负责加载用户类路径（ClassPath）上所指定的类库，开发者可以直接使用这个类加载器，如果应用程序中没有自定义过自己的类加载器，一般情况下这个就是程序中默认的类加载器。

### 7.2 双亲委派模型

![img](https://mmbiz.qpic.cn/mmbiz_png/hvUCbRic69sAxRovHTCH3yyW1vpic22WVibsIR7t9TpI4LlxD1hewI5HEy12YhMNtaFohOVOxibnrxCWpXic8Aib9VibQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

模型好处：java随着它的类加载器一起具备了带优先级的层次关系

双亲原则的三次大破坏：

- 第一次：由于双亲委派模型引入是在jdk1.2中才引入，导致向之前的实现做了妥协。
- 第二次：模型自身存在缺陷，基础的类加载器需要调用用户代码，而基础类加载器不认识用户代码，于是引入了“线程上下文加载器”，可以在父类中请求子类加载器完成加载动作。
- 第三次：用于用户对程序动态性的追求导致。动态性指"代码热替换"、"模块热部署"。OSGI是当前业界”事实上“的java模块化标准，OSGI关键是其自定义的类加载器机制的实现，每一个程序模块中都有一个自己的类加载器，这是一种复杂的网状结果而不是双亲委派。

## 8. JMM（java内存模型）

### 8.1 java内存模型的承诺

#### 8.1.1原子性：

原子性指的是一个操作是不可中断的，即使是在多线程环境下，一个操作一旦开始就不会被其他线程影响。对于32位系统中double、long类型的读取需要进行两次读取，但是在jvm实现时已经保证这个操作的原子信。

指令重排（目的是为了提高性能）分类如下：

- 编译器优化的重排，编译器在不改变程序结果的前提下重新安排语句的执行顺序
- 执行并行的重排，指令级并行技术实现多条指令重叠执行
- 内存系统的重排，由于处理器使用缓存和读写缓冲区，使得加载和存储操作看起来乱序

从指令执行的角度来看一条指令可以分为如下的步骤执行：

- 取值if
- 译码和取寄存器操作数ID
- 执行或者有效地址计算EX
- 存储器访问MEM
- 写回WEB

为了实现CPU流水线不中断（即等待计算结果），可以对不存在数据依赖的语句进行顺序调整是CPU尽可能地不空闲。

**指令重排只能保证单线程下的结果正确性（语义一致性），并不保证多线程下的结果的正确性（语义一致性）**

#### 8.1.2可见性：

可见性，即修改后对于后续的操作是否可以看见修改，对于单线程，数据一直是可见的，但是对于多线程情况由于有缓存和寄存器的存在会导致操作不可见。指令重排以及编译器优化都会导致可见性问题。

#### 8.1.3有序性：

单线程的执行代码，执行的顺序总是顺序依次执行。在本线程内，所有的操作是有序的，不同线程之间来看任何两个操作都是无序的。

### 8.2 JMM提供的解决方案

通过JVM中synchronized、volatile以及各种锁和happens-before来保证多个线程之间的原子性、可见性和有序性。

happens-before八大原则：

- 程序顺序原则，即在一个线程内必须保证语义串行性，也就是说按照代码顺序执行。

- 锁规则 解锁(unlock)操作必然发生在后续的同一个锁的加锁(lock)之前，也就是说，如果对于一个锁解锁后，再加锁，那么加锁的动作必须在解锁动作之后(同一个锁)。

- volatile规则 volatile变量的写，先发生于读，这保证了volatile变量的可见性，简单的理解就是，volatile变量在每次被线程访问时，都强迫从主内存中读该变量的值，而当该变量发生变化时，又会强迫将最新的值刷新到主内存，任何时刻，不同的线程总是能够看到该变量的最新值。

- 线程启动规则 线程的start()方法先于它的每一个动作，即如果线程A在执行线程B的start方法之前修改了共享变量的值，那么当线程B执行start方法时，线程A对共享变量的修改对线程B可见

- 传递性 A先于B ，B先于C 那么A必然先于C

- 线程终止规则 线程的所有操作先于线程的终结，Thread.join()方法的作用是等待当前执行的线程终止。假设在线程B终止之前，修改了共享变量，线程A从线程B的join方法成功返回后，线程B对共享变量的修改将对线程A可见。

- 线程中断规则 对线程 interrupt()方法的调用先行发生于被中断线程的代码检测到中断事件的发生，可以通过Thread.interrupted()方法检测线程是否中断。
- 对象终结原则 对象的构造函数执行，限于finalize（）方法

### 8.3 volatile 内存定义

#### 8.3.1  作用

- 保证被volatile修饰变量对所有线程可见，修改后立刻得知

- 禁止指令重排序优化

禁止指令重排序优化通过内存屏障进行实现。内存屏障，又称内存栅栏，是一个CPU指令，它的作用有两个，一是**保证特定操作的执行顺序**，二是**保证某些变量的内存可见性**（利用该特性实现volatile的内存可见性）。由于编译器和处理器都能执行指令重排优化。如果在指令间插入一条Memory Barrier则会告诉编译器和CPU，不管什么指令都不能和这条Memory Barrier指令重排序，也就是说通过插入内存屏障禁止在内存屏障前后的指令执行重排序优化。Memory Barrier的另外一个作用是强制刷出各种CPU的缓存数据，因此任何CPU上的线程都能读取到这些数据的最新版本。

