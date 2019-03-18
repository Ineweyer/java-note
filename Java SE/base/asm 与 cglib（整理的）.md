# [asm 与 cglib（整理的）](https://www.cnblogs.com/clds/p/4985893.html)

摘抄自[博客](https://www.cnblogs.com/clds/p/4985893.html)

参考博客地址

http://www.oseye.net/user/kevin/blog/304#top

http://www.blogjava.net/vanadies10/archive/2011/02/23/344899.html

http://llying.iteye.com/blog/220452

http://www.cnblogs.com/liuling/archive/2013/05/21/CGlib-AOP.html

1 asm简介

[ASM](http://asm.ow2.org/)是一个Java字节码操纵框架，它能被用来动态生成类或者增强既有类的功能。ASM可以直接产生二进制class文件，也可以在类被加载入Java虚拟机之前动态改变类行为。Java class被存储在严格格式定义的.class文件里，这些类文件拥有足够的元数据来解析类中的所有元素：类名称、方法、属性以及 Java 字节码（指令）。ASM从类文件中读入信息后，能够改变类行为，分析类信息，甚至能够根据用户要求生成新类。

目前许多框架如cglib、Hibernate、Spring都直接或间接地使用ASM操作字节码，有些语言如Jython、JRuby、Groovy也是如此。而类ASM字节码工具还有：

1. BCEL：Byte Code Engineering Library (BCEL)，这是Apache Software Foundation 的Jakarta 项目的一部分。BCEL是 Java classworking 最广泛使用的一种框架,它可以让您深入 JVM 汇编语言进行类操作的细节。BCEL与Javassist 有不同的处理字节码方法，BCEL在实际的JVM 指令层次上进行操作(BCEL拥有丰富的JVM 指令级支持)而Javassist 所强调的源代码级别的工作。
2. JBET：通过JBET(Java Binary Enhancement Tool )的API可对Class文件进行分解，重新组合，或被编辑。JBET也可以创建新的Class文件。JBET用一种结构化的方式来展现Javabinary (.class)文件的内容，并且可以很容易的进行修改。
3. Javassist：Javassist是一个开源的分析、编辑和创建Java字节码的类库。是由东京技术学院的数学和计算机科学系的 Shigeru Chiba 所创建的。它已加入了开放源代码JBoss 应用服务器项目,通过使用Javassist对字节码操作为JBoss实现动态AOP框架。
4. cglib：是一个强大的,高性能,高质量的Code生成类库。它可以在运行期扩展Java类与实现Java接口，cglib封装了asm，可以在运行期动态生成新的 class，Hibernate和Spring都用到过它。cglib用于AOP，jdk中的proxy必须基于接口，cglib却没有这个限制。

而ASM与cglib、serp和BCEL相比，ASM有以下的优点 :

- ASM 具有简单、设计良好的 API，这些 API 易于使用；
- ASM 有非常良好的开发文档，以及可以帮助简化开发的 Eclipse 插件；
- ASM 支持 Java 6(ASM3)、Java7(ASM4)、Java(ASM5)；
- ASM 很小、很快、很健壮；
- ASM 有很大的用户群，可以帮助新手解决开发过程中遇到的问题；
- ASM 的开源许可可以让你几乎以任何方式使用它；

2 asm jar包简介

　　在ASM3.3.1中，提供了7个jar包，分别是

​         asm-3.3.1.jar

​         asm-commons-3.3.1.jar

　　　asm-tree-3.3.1.jar

​         asm-analysis-3.3.1.jar

​         asm-util-3.3.1.jar

​         asm-xml-3.3.1.jar

 

​         参看ASM的javadoc(http://asm.ow2.org/asm33/javadoc/user/index.html)，可以看到一共有7个package，package和jar的对应关系如下

 　　 asm-3.3.1.jar 包含了org.objectweb.asm和org.objectweb.asm.signature两个packages

 　　  asm-commons-3.3.1.jar包含了org.objectweb.asm.commons这个package

 　　  asm-tree-3.3.1.jar 包含了org.objectweb.asm.tree这个package

　　   asm-analysis-3.3.1.jar包含了org.objectweb.asm.tree.analysis这个package

 　　  asm-util-3.3.1.jar包含了org.objectweb.asm.util这个package

​        asm-xml-3.3.1.jar包含了org.objectweb.asm.xml这个package

​        其中asm-3.3.1.jar，是包含了核心的功能，而其他的jar，都是基于这个核心的扩展。

 

3  **Java字节码文件**

所谓 Java 字节码文件，就是通常用 javac 编译器产生的 .class 文件。这些文件具有严格定义的格式。为了更好的理解 ASM，首先对 Java 字节码文件格式作一点简单的介绍。Java 源文件经过 javac 编译器编译之后，将会生成对应的二进制文件（如下图所示）。每个合法的 Java 字节码文件都具备精确的定义，而正是这种精确的定义，才使得 Java 虚拟机得以正确读取和解释所有的 Java 字节码文件。

![img](http://www.oseye.net/upload/attached/image/20140717/20140717161417_51479.jpg)

Java 字节码文件是 8 位字节的二进制流。数据项按顺序存储在 class 文件中，相邻的项之间没有间隔，这使得 class 文件变得紧凑，减少存储空间。在 Java 字节码文件中包含了许多大小不同的项，由于每一项的结构都有严格规定，这使得 class 文件能够从头到尾被顺利地解析。下面让我们来看一下 Java 字节码文件的内部结构，以便对此有个大致的认识。

例如，一个最简单的 Hello World 程序：

1. public class HelloWorld {
2. public static void main(String[] args) {
3. System.out.println("Hello world");
4. }
5. }

从上图中可以看到，一个 Java 字节码文件大致可以归为 10 个项：

![img](http://www.oseye.net/upload/attached/image/20140717/20140717161436_14968.jpg)

- Magic：该项存放了一个 Java 字节码文件的魔数（magic number）和版本信息。一个 Java 字节码文件的前 4 个字节被称为它的魔数。每个正确的 Java 字节码文件都是以 0xCAFEBABE 开头的，这样保证了 Java 虚拟机能很轻松的分辨出 Java 文件和非 Java 文件。
- Version：该项存放了 Java 字节码文件的版本信息，它对于一个 Java 文件具有重要的意义。因为 Java 技术一直在发展，所以字节码文件的格式也处在不断变化之中。字节码文件的版本信息让虚拟机知道如何去读取并处理该字节码文件。
- Constant Pool：该项存放了类中各种文字字符串、类名、方法名和接口名称、final 变量以及对外部类的引用信息等常量。虚拟机必须为每一个被装载的类维护一个常量池，常量池中存储了相应类型所用到的所有类型、字段和方法的符号引用，因此它在 Java 的动态链接中起到了核心的作用。常量池的大小平均占到了整个类大小的 60% 左右。
- Access_flag：该项指明了该文件中定义的是类还是接口（一个 class 文件中只能有一个类或接口），同时还指名了类或接口的访问标志，如 public，private, abstract 等信息。
- This Class：指向表示该类全限定名称的字符串常量的指针。
- Super Class：指向表示父类全限定名称的字符串常量的指针。
- Interfaces：一个指针数组，存放了该类或父类实现的所有接口名称的字符串常量的指针。以上三项所指向的常量，特别是前两项，在我们用 ASM 从已有类派生新类时一般需要修改：将类名称改为子类名称；将父类改为派生前的类名称；如果有必要，增加新的实现接口。
- Fields：该项对类或接口中声明的字段进行了细致的描述。需要注意的是，fields 列表中仅列出了本类或接口中的字段，并不包括从超类和父接口继承而来的字段。
- Methods：该项对类或接口中声明的方法进行了细致的描述。例如方法的名称、参数和返回值类型等。需要注意的是，methods 列表里仅存放了本类或本接口中的方法，并不包括从超类和父接口继承而来的方法。使用 ASM 进行 AOP 编程，通常是通过调整 Method 中的指令来实现的。
- Class attributes：该项存放了在该文件中类或接口所定义的属性的基本信息。

事实上，使用 ASM 动态生成类，不需要像早年的 class hacker 一样，熟知 class 文件的每一段，以及它们的功能、长度、偏移量以及编码方式。ASM 会给我们照顾好这一切的，我们只要告诉 ASM 要改动什么就可以了 —— 当然，我们首先得知道要改什么：对字节码文件格式了解的越多，我们就能更好地使用 ASM 这个利器。

4 **ASM编程模型**

ASM 提供了两种编程模型：

- Core API，提供了基于事件形式的编程模型。该模型不需要一次性将整个类的结构读取到内存中，因此这种方式更快，需要更少的内存。但这种编程方式难度较大。
- Tree API，提供了基于树形的编程模型。该模型需要一次性将一个类的完整结构全部读取到内存当中，所以这种方法需要更多的内存。这种编程方式较简单。

Core API 中操纵字节码的功能基于 ClassVisitor 接口。这个接口中的每个方法对应了 class 文件中的每一项。Class 文件中的简单项的访问使用一个单独的方法，方法参数描述了这个项的内容。而那些具有任意长度和复杂度的项，使用另外一类方法，这类方法会返回一个辅助的 Visitor 接口，通过这些辅助接口的对象来完成具体内容的访问。例如 visitField 方法和 visitMethod 方法，分别返回 FieldVisitor 和 MethodVisitor 接口的对象。

ASM 提供了三个基于 ClassVisitor 接口的类来实现 class 文件的生成和转换：

- ClassReader：ClassReader 解析一个类的 class 字节码，该类的 accept 方法接受一个 ClassVisitor 的对象，在 accept 方法中，会按上文描述的顺序逐个调用 ClassVisitor 对象的方法。它可以被看做事件的生产者。
- ClassAdapter：ClassAdapter 是 ClassVisitor 的实现类。它的构造方法中需要一个 ClassVisitor 对象，并保存为字段 protected ClassVisitor cv。在它的实现中，每个方法都是原封不动的直接调用 cv 的对应方法，并传递同样的参数。可以通过继承 ClassAdapter 并修改其中的部分方法达到过滤的作用。它可以看做是事件的过滤器。
- ClassWriter：ClassWriter 也是 ClassVisitor 的实现类。ClassWriter 可以用来以二进制的方式创建一个类的字节码。对于 ClassWriter 的每个方法的调用会创建类的相应部分。例如：调用 visit 方法就是创建一个类的声明部分，每调用一次 visitMethod 方法就会在这个类中创建一个新的方法。在调用 visitEnd 方法后即表明该类的创建已经完成。它最终生成一个字节数组，这个字节数组中包含了一个类的 class 文件的完整字节码内容 。可以通过 toByteArray 方法获取生成的字节数组。ClassWriter 可以看做事件的消费者

5 ASMifer 工具

　　直接编码ASM其实对于新手来说是很困难的事，但幸运的是ASM给我们提供了ASMifer工具。一般我们会使用ASM的ASMifer工具生成ASM结构来对比，使用命令：

1. java org.objectweb.asm.util.ASMifier net.oseye.demoasm.HelloWorld

6 例子：参考地址

　　http://www.blogjava.net/vanadies10/archive/2011/02/23/344899.html

　　http://llying.iteye.com/blog/220452

　　http://www.cnblogs.com/liuling/archive/2013/05/21/CGlib-AOP.html

　　代码 ： ![img](https://images2015.cnblogs.com/blog/726177/201511/726177-20151122151038765-155138292.jpg)