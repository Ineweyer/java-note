# Java 程序员面试笔试

## 第四章 Java基础知识

### 4.1 基本概念

#### 1. java语言优点

   ```
   面向对象（类别）
   平台无关（通用性）
   丰富类库，对web应用的支持（实用性）
   安全和健壮性（性能）
   去除c++中难以理解和容易混淆的特性（来源）
   ```
#### 2. java与c/c++的异同

```
相同点：
面向对象（封装、继承、多态，抽象等）

不同点：
java为解释语言，c/c++为编译型语言
java为纯面向对象语言（所有的方法都在类中，除去基本类型外所有类型都是类），c++为面向对象和面向过程语言（可以定义全局变量和全局函数）
java没有指针概念，更加安全
java无需开发人员管理内存交由垃圾管理机制释放无用垃圾
java不支持运算符重载，没有预处理器（但是import提供类似的功能），不支持默认函数参数，不提供goto语句，不支持自动强制类型转换，平台无关性（类型的长度和平台无关），提供对注释文档的内建支持
```

#### 3. public static void main(String[] args)方法

```
程序入口，public static和void三者必须有并且前两者的顺序无所谓，可以加其他的修饰，但不能添加abstract，识别与类文件同名类中的方法为入口。
静态方法块一定在main方法之前执行
```

#### 4. java程序初始化的顺序
原则
```
静态对象（变量）优先于非静态对象（变量）初始化
父类优先于子类初始化
按照成员变量定义的顺序进行初始化
```
执行顺序
```
父类静态变量>父类静态代码>子类静态变量>子类静态代码>父类非静态变量>父类非静态代码块>父类构造函数>子类非静态变量>子类非静态代码块>子类构造函数
```

#### 5. 作用域

变量分为三类：成员变量、局部变量和静态变量（全局变量），作用域由{}决定，由且仅有成员变量还可以通过作用域修饰符确定作用域，成员修饰符具体如下：

| 作用域和可见性 | 当前类 | 同一package | 子类 | 其他package |
| -------------- | ------ | ----------- | ---- | ----------- |
| public         | 可     | 可          | 可   | 可          |
| private        | 可     | 不可        | 不可 | 不可        |
| protected      | 可     | 可          | 可   | 不可        |
| default        | 可     | 可          | 不可 | 不可        |

#### 6. 构造函数

```
构造函数与类同名，没有返回值（void也不行，就是没有返回字段)，有返回值就变成普通方法
可以有多个构造函数，可以通过控制参数进行重载
在父类中没有无参构造函数，则在子类中需要通过super显式调用父类构造函数
主要完成对象初始化工作，不能被继承也不能被覆盖
没有定义构造函数时会自动定义一个无参的构造函数且修饰符仅和当前类修饰符有关，但是一旦自定义构造函数后这个自动的步骤消失，如果希望使用无参构造函数那么需要自己定义
和new一起搭配使用,由系统调用，仅执行一次
```

#### 7. 接口

```
引入目的：克服单继承的缺点
接口成员的修饰符都是public
接口中常量值默认使用public static final进行修饰
没有任何方法的接口：标识接口（起到标识作用）
```

#### 8. clone

```
存在的意义：通过赋值语句无法完成对象的拷贝，对象的拷贝需要重写clone方法实现
需要实现Cloneable接口
浅复制和深复制：super.clone()能够得到当前对象的一个浅层复制，浅层复制中基本数据类型已经正确的复制，但是对象类型数据仅拷贝了引用，相当于共用同一块堆上内存，对其进行修改时会造成错误。深复制解决对象中对象类型的成员变量没有复制问题，针对对象类型数据进行拷贝处理，直到其中的数据都为基本类型。
```

#### 9. 反射机制

```
定义：运行进行自我检查同时能对其内部成员进行操作
功能：得到一个对象所属的类，获取一个类的所有变量和方法，运行时创建对象（最为重要），运行时调用对象的方法
获取class类的方法：
1. class.forName("类的路径")
2. 类型.class
3. 实例.getClass()
```

#### 10. 创建实例的四种方法：

```
new
反射机制
clone
反序列化
```

#### 11. package 作用

宗旨：把java文件、class文件、以及其他的resource文件(xml文件、avi文件、MP3文件、txt文件)有条理地进行组织

作用：提供多层命名空间、解决命名冲突问题

#### 12. 如何实现函数指针（或者说函数作为参数）

为方法定义接口，传入接口即可

### 4.2 面向对象技术

#### 1. 面向对象和面向过程的区别

```
出发点不同，前者是以符合常规思维的方式来处理客观世界的问题，把问题映射到对象和对象之间的接口上，后者强调的是过程的抽象化和模块化，是以过程为中心。
层次逻辑关系不同，前者使用计算机逻辑来模拟客观世界中的物理存在，而后者是把客观问题抽象成为计算机可以处理的过程
数据处理方式和控制程序方式不同，前者中将数据和对数据的操作封装在对象中，通过事件驱动来激活和运行程序，后者直接通过程序来处理数据
分析设计和编码方式不同，面向对象贯穿于整个设计过程中，是一种无缝连接，后者强调分析、设计和编码之间按照规则进行转换，是一种有缝连接
```

#### 2. 面向对象的特征

1. 抽象，过程抽象和数据抽象
2. 继承，在父类的基础上继承演化出新的类
3. 封装，将客观事物封装为类，每个类对自身的数据和方法实行保护
4. 多态，指允许不同类的对象对同一消息作出响应，包括参数多态和包含多态

#### 3. 继承

1. 不支持多重继承
2. 子类只能继承非私有成员变量和方法
3. 子类中成员变量与父类中成员变量同名时，覆盖
4. 子类中成员函数和父类中成员函数的函数签名相同是（名称、返回值类、参数类型等相同），覆盖

#### 4. 组合和继承的区别

区别：

```
继承：is - a 关系
组合：has - a关系
```

共同点：实现代码的复用

使用原则：

```
除非两个类之间是is-a关系，否则不要轻易使用继承
不要仅仅为了实现多态而使用继承
能使用组合就尽量不要使用继承
```

#### 5.多态

定义：同一个操作作用在不同对象时，会有不同的语义，从而产生不同的结果

两种表现形式：

1. 编译时多态：方法的重载，重载代表着一个类中 有多个同名的方法，但这些方法具有不同的参数，因此可以在编译时就能够确定需要调用哪个方法
2. 运行时多态：方法的覆盖，调用的方法在运行的过程中动态绑定

注意：只有成员方法才具有重载，成员变量没有重载

#### 6. 面向对象开发方式的优点

1. 较高的开发效率
2. 保证软件的鲁棒性
3. 保证软件的高可维护性

#### 7. 重载和覆盖有什么区别

重载：

```
通过参数类型和个数进行区分
不能通过返回值、访问权限和抛出的异常来进行重载
如果父类的方法为private那么无法进行重载，只是在子类中定义了一个方法
```

覆盖：

```
必须具有相同的函数名和参数
返回值类型必须相同
抛出异常必须一致
被覆盖方法不能为private
```

两者的区别：

```
覆盖是子类和父类之间的关系，是垂直关系，重载是同一个类中方法之间的关系，是水平关系
覆盖只能由一个方法或者一对方法产生关系，重载是多个方法之间产生关系
覆盖要求具有相同的参数列表，而重载要求其不同
覆盖中根据对象的类型来调用方法，而重载中是根据实参和形参之间的匹配情况来选择方法体
```

#### 8. 抽象类和接口的异同

抽象类是一个实体，接口是一个概念。

相同点：

```
都不能被实例化
接口的实现类或者抽象类的子类都只有实现了接口或抽象类中的方法后才能被实例化
```

不同点：

```
接口中有定义没有实现，而抽象类中可以实现部分方法
接口需要实现（implement）而抽象类只能被继承
接口强调特定功能的实现，是has-a关系，抽象类强调所属关系，其设计理念为is-a
接口中成员变量默认是public static final(即只有静态的不能被修改的数据成员)，抽象类中的成员变量默认为default，接口中方法只能使用public 和 abstract修饰
接口倾向于比较常用的功能，便于日后的维护或者添加删除方法，而抽象类更倾向于充当公共类的角色，不适用于日后重新对立面的代码进行修改
```

抽象类与接口的选择标准：

抽象类：子类和父类之间存在逻辑上的层次结构时

接口：希望支持差别较大的两个或者更多对象之间的特定交互行为时

抽象类可以继承接口，可以实现接口，可以继承具体类，可以有静态的main方法

#### 9.内部类

嵌套类和内部类类似，只是嵌套类是c++的说法

四种内部类：

```java
class OutClass {
    static class StaticInnerClass{}    //静态内部类，只能访问外部类中的静态成员变量和静态方法
    class MemberInnerClass{}           //成员内部类，可以随意引用外部类中成员变量和方法，但是类中不能够定义静态方法和成员变量，需要外部类实例化后才能进行被实例化
    public OutClass() {
        class LocalInnerClass{}        //局部内部类，不能被public、protected、private、static修饰，只能访问final的类型的成员变量、局部变量没有限制
        Object o = new Cloneable() {   // 匿名内部类，不适用class、extends、implements修饰，没有构造函数，必须继承其他类或者实现其他接口，只能访问方法中定义为final的类型的成员变量、局部变量没有限制
        }
    }
}
```

使用匿名内部类的原则：

```
内部不能有构造函数
不能定义静态成员、方法和类
不能是public、protected、private、static
只能创建匿名内部类的一个实例
匿名内部类一定是在new的后面，这个匿名类必须继承一个父类或者实现一个接口
局部内部类的限制的对其生效（最主要的是，只能访问方法中定义为final类型的局部变量）
```

#### 10.获取父类的的类名

获取类名的方法：this.getClass().getName()

getClass在Object中被定义为final和native,子类中无法进行覆盖，使用super.getClass().getName()获取到的是运行时对象的类名

获取父类类名的方法：this.getClass().getSuperClass().getName()

#### 11. this和super

用途：this和super都能够辨别同名覆盖问题，this用于解决局部变量和成员变量之间的覆盖问题，super用于解决子类和父类之间的同名覆盖问题，此外super还代表调用父类构造函数（super构造函数的调用一定得是第一行），this代表此类的构造函数

### 4.3 关键字

#### 1. 变量命名规则
标识符：变量名、函数名、数组名

标识符组成：以字母、下划线、$开头，且仅由以上三种和数字组成

标识符不能是关键字或者是保留字（如char等）

#### 2.break、continue、return

break: 结束循环，跳到循环的下一句

continue：结束一次循环，跳到循环的开头进行下一次循环

return: 从一个方法中返回

多重循环：使用label定位跳转的位置,但是这不等价于goto语句

```java
public void main() {
    out:
    for(int i=0; i<10; i++){
        for(int j=0; j<10; j++) {
            break out;
        }
    }
}
```

#### 3. final、finally和finalize

1. final 用于声明属性、方法和类，分别表示属性不可变、方法不可覆盖、和类不可被继承(与Abstract冲突)

final属性（**在类（或对象）初始化之后能够完成初始化就行**）：代表引用不可变，初始化时符合：1.在定义的时候初始化、2.final成员变量可以在初始化块中初始化，但不可以在静态初始块中初始化。3.静态final成员变量在静态初始快中初始化，但不可以在初始块中初始化。4.在类的构造器初始化，但静态final成员变量不可以在构造函数中初始化；总之就一句话，在**使用之前需要已经初始化完成**。

```java
public class TestFinal {
    public static final String staticFinal; //静态成员初始化位置1
    public final String memberFinal;        //非静态成员初始化位置1
    static {
        staticFinal = "";   //静态成员初始化位置2
    }
    {
//        this.memberFinal = ""; //非静态成员初始化位置2
    }
    public TestFinal() {
        this.memberFinal = ""; //非静态成员初始化位置3
    }
}

```

2.finally紧跟在异常处理代码块的后面，代表无论上面处理流程如何都要进行的代码块

3.finalize是Object类的一个方法，在进行垃圾回收时先调用finalize方法，第二次回收动作发生时真正收回对象占用的内存

#### 4. assert

在实际的开发中，assert主要用来保证程序的正确性，通过-ea参数开启断言调试，断言的作用范围为：

```
控制流程检查
检查输入参数是否有效
检查函数结果是否有效
检查程序不变量
```

但是需要注意的是不能够使用assert来充当if使用

与c语言assert的区别：

```
java中使用assert关键字实现其功能，但是c语言使用库函数实现
java语言中是在运行时开启，而c语言是在编译时开启
```

#### 5. static 关键字的作用

切记：不能在方法体中定义static变量

1. static 成员变量，所有对象共享这个静态变量

2. static 成员方法，方法中不能使用this和super，只能调用static方法和变量，重要的用途是实现单例模式，

   ```java
   class Singleton{
       private static Singleton instance = null;
       private Singleton() {}
       public static Singleton getInstance() {
           if(instance == null) {
               instance = new Singleton();
   		}
           return instance;
       }
   }
   ```

   

3. static 代码块，独立于成员变量和成员函数的代码块，一般用户static成员变量的初始化

4. static 内部类，只能访问外部类中的静态成员和方法，

四种变量：

| 变量类型  | 变量解释                                                     |
| --------- | ------------------------------------------------------------ |
| 实例变量  | 变量归对象所有，即没有使用static修饰的成员变量               |
| 局部变量  | 在方法中定义的变量                                           |
| 类变量    | 变量归类所有，即使用static修饰的成员变量                     |
| final变量 | 表示这个变量 一旦初始化后就不允许修改，但是可以通过对象引用修改其内部 |

#### 6.使用switch时的注意事项

能够枚举的类型为整型（以及能够隐式转化为int类型的short、byte、char）

case 后面可以也仅可以编译时期能够确定的常量（常量、final修饰、常量表达式）

每次case后面最好跟上break语句，防止穿透问题

java7以后支持String类型的枚举

#### 7. volatile作用

类型修饰符（type specifier)，用来修饰被不同线程访问和修改的变量，使用该修饰的变量每次直接从内存中读取数据不使用缓存，避免读取写修改延迟问题（更新不及时，读取时使用的是修改之前的数据）还有一个作用是禁止指令重排序

但是需要注意的是volatile修饰的变量不保证原子性

#### 8. instanceof作用

是一个二元 运算符，用于判断一个引用类型的变量所指的对象是否是一个类（接口、抽象类、父类）的实例

#### 9. strictfp作用

是strict float point的缩写，指的是精确浮点，保证浮点运算使用IEEE 754标准执行，在不同的平台上结果一致

可以用来修饰方法和类，如果修饰类等价于修饰类中所有的方法

### 4.4 基本类型与运算

#### 1. 基本数据类型

1. 基本数据类型包括：boolean， byte、char、short、int、long、float、double
2. 各种基本类型所使用的位数是固定的，每种基本类型都有对应的包装类，但是包装类中的值为final类型，无法进行修改，即使在函数中传入的是其包装类对象，但是依然没有办法对其进行修改。且包装类不能被继承。
3. 默认的小数是double类型，如果期望是float类型需要显示转换（gui绘图中的一个坑），在使用常量进行赋值时需要使用类型标识

```java
float f = 1.0f;
long l = 26012402244L;
```

3.  关于null

代表当前没有指向任何对象，不是一个合法的Object实例，只能够用于引用类型，基本数据类型不能够使用。虽然其没有指向任何对象，但是可用其对**对象进行初始化**（代表已经初始化，在后面的合适时机进行具体的实例初始化）。

4. int 和 Integer区别

前者原始类型，后者是引用类型，两者存在不同的特征和用法，但是java中具有自动装箱和解包，两个在一定程度上用起来感觉相似。

#### 2. 不可变类

创建这种类的实例后，就不允许修改它的值了，**基本类型包装类和String类。**

遵循的规则：

> 类中所有的成员变量使用**private修饰**，
> 类中**没有修改成员变量的方法**
> 确保类中的方法不会被子类的覆盖
> 如果一个**类成员是不可变量**，在**初始化和get获取时**，需要使用**clone确保类的不可变性**
> 如果必要，可使用覆盖Object类的equals和hasCode方法

#### 3. 值传递和引用传递

个人理解：两者都可以理解成为是值传递，只是后面一种传递的是地址，可以根据地址修改内存单元的数据

1. 值传递：

   使用实参值初始化临时空间，两者具有不同的存储空间

2. 引用传递：

   传递对象（或者说是对象的地址），需要注意的是包装类都是按照引用传递的

#### 4. 不同数据类型转换规则

精度：byte<short<char<int<long<float<double

1. 自动转换：低精度到高精度

注意：

```
1. char转为高级类型时使用的ascall码
2. byte、char、short类型参与运算时自动转化为int，但是+=符号时不会产生类型的转换（其实可以理解为有隐性的类型强制转换）
3. boolean和其他基本类型不能互相转化
```

2. 强制类型转化：注意会丢失精度，截取式缩减（取低位）

#### 5. 强制类型转换的注意事项

```java
short s1 = 1;
short s2 = s1 + s1; //报错
short s3 = (short)(s1 + s1); //正确写法
short s4 = s1;      //正确写法
s4 += s1;           //+= 不会产生类型的转换
```

#### 6.运算符优先级

![img](https://images2015.cnblogs.com/blog/1001990/201610/1001990-20161025144757265-1724289762.png)

#### 7.Math类的round、ceil和floor

1. round: 四舍五入，如果.5那么向大靠齐
2. ceil:向上取整，大于当前数值的最小整数
3. floor:向下取整，小于当前数值的最大整数

#### 8. 自增/自减

++i: 先加，立即将当前的i值加1

i++: 后加，现在维持i值，后面用到时再加1

#### 9. 左移右移

由于位数的限制，移动大于当前数字时位数时，利用求模运算得到需要移动的位数

1. 右移

   有符号右移：正数高位补1，负数高位补0

   无符号右移：高位补零

2. 左移

   只有一种情况，就是低位补零

   需要注意的是，针对byte、char、short三种类型进行移位处理时，存在一个将其补为int（补高位）后移位，最后再截取（低位）的过程

#### 10. char存储中文汉字

java中默认使用Unicode编码方式，每个字符占用两个字节，但注意其中英文仅占用一个字符。

### 4.5 字符串与数组

#### 1.字符串创建和存储机制

```java
String s1 = "abc";
String s2 = "abc";
System.out.println(s1 == s2);  //true, 存在一个字符串常量池，直接将地址指过去就可以了
System.out.println("ab"+"c" == s2);  //true, 存在一个字符串常量池，直接将地址指过去就可以了

String s3 = new String("abc");
String s4 = new String("abc");
System.out.println(s3 == s4);  //false, 只要是使用new关键词后，会创建一个新的对象，创建是需要将当前的字符串放到字符串常量池中（如果不存在才放），所以可能创建 一个或者两个对象
System.out.println(s1.equals(s2));  //true
```

#### 2. == equals 和hashCode有什么区别

==： 比较两个变量的值是否相等，用于**基本类型相等比较 **以及判断两个**引用是否为同一个**

equals：Object继承的方法，没有覆盖时，等价于==，如果已经覆盖就按照当前逻辑判断两个对象是否相等（用于比较内容）

hashCode：Object继承的方法，也可以用来鉴定两个对象是否相等，Object中返回对象在内存中地址换成的一个int值，重写后按照重写的逻辑进行判断

一般覆盖equals方法的同时也需要覆盖hashCode方法，从而导致该类无法与所有基于散列值的集合类（HashMap、HashSet、HashTable结合在一起正常运行。

hashCode相等，但是equals不一定相等

equals相等，hashCode一定相等

#### 3. String、StringBuffer、StringBuilder、StringTokenizer

 String适用于数据量比较小，单线程下操作大量数据 优先使用StringBuilder（线程不安全），多线程模式下操作大量数据，优先使用StringBuffer(可以实现线程安全)，StringTokenizer用来进行单词的切分操作

执行效率：StringBuilder>StringBuffer>String

#### 4. Java数组

java中数组是对象，可以使用instance判断是否是某个的实例

与c++的不同：定义时不分配空间，需要进行初始化（分配空间），初始化时全部填入默认值，数组中第二维的长度可以不相同

初始化方式：

```java
int[] a = new int[5]; //全部初始化为0
int[] a1;
a1 = new int[]{0, 1, 2, 3, 4, 5};
int b[] = {1, 2, 3, 4, 5}; //按照给定值初始化
int[][] c = {{2, 3}, {4, 5, 6}};
```

#### 5. length属性和length()方法和size()方法

length: 代表数组的长度

length()：代表字符串的长度

size()：代表泛型集合中元素的个数

### 4.6 异常处理

#### 1. finally块中代码什么时候被执行

finally语句在try或者catch语句之后执行，如果finally中有return语句那么将会覆盖之前的返回值，具体如下所示：

```java
	// 最终返回的是 finally 字符串
    public static String testInteger() {
        try {
            Object o = new Object();
            return "try";
        }
        catch (Exception e) {
            e.printStackTrace();
            return "Exception";
        }
        finally {
            return "finally"; //如果把这句话注释，在执行当前语句之前先将之前的return结果保存，在执行完返回之前缓存的结果
        }
    }  
```

finally语句不一定执行的特例：

```
当在try/catch之前就已经出现异常时不会执行
在程序块中强制退出时，如System.exit(0)
```

#### 2. 异常处理的原理

异常：程序运行时所发生的的非正常情况或错误，当程序违反语义规则时，JVM将会将出现的错误表示为一个异常并抛出。

所有异常的基类为：Throwable

语义规则包括：java类库内置的语义检查（如空指针异常、数组越界），开发人员拓展的语义检查（Thowable的子类）

#### 3. 运行时异常和普通异常有什么区别

Error: 表示程序在运行期间出现了严重的错误，并且该错误是 不可恢复的，由于这属于JVM层次的严重错误，这种错误会导致程序的终止执行。不推荐去捕捉Error类型错误（正确的程序不包含Error），常见的Error包括（OutOfMemoryError、ThreadDeath）

Exception：表示可以恢复的 异常，包括：检查异常（checked exception）和运行时异常（runtime exception）

1. 检查异常

   将会出问题代码块使用try语句进行包围（或者将异常上一级调用抛出交由上一级调用进行处理），在后面紧跟使用catch语句进行异常的处理，这类异常**发生在编译阶段**。

   使用的情况（**错了也没事 + 与不可靠的设备交互**）：

   ```
   异常的发生不会导致出错，进行处理后可以继续执行后续操作
   程序依赖于不可靠的外部条件，系统IO
   ```

2. 运行时异常

   编译器没有强制对其进行捕获并处理，如果不对这种异常进行处理，当出现这种异常时由JVM进行处理，常见的异常包括NullPointerException、ClassCastException、ArrayIndexOutOfBoundsException、ArrayStoreException、BufferOverflowException、ArithmeticException等。出现异常后会把异常一直往上层抛出，直到遇到处理代码为止，如果 不处理运行异常会导致主程序终止或者线程终止。

使用异常处理注意的问题：

```
Java异常处理时使用了多态的概念，在异常处理的过程中需要先处理子类再处理基类，否则会导致子类无法被处理
尽早抛出异常，同时对捕获的 异常进行处理，或者从错误中恢复
可以根据实际的需求自定义异常类，只要继承自Exception类即可
异常能处理就处理，不能处理就抛出
```

### 4.7 输入输出流

#### 1. Java IO流的实现机制是什么

流分为输入流和输出流，根据数据类型可以分为：字节流和字符流（后者需要使用缓存）、FilterInputStream为一个封装类的基类，可以针对IO进行封装（再输入或者输出时针对数据进行修改）使用适配器模式进行包装。

#### 2. 管理文件和目录的类File

| 方法                  | 作用                                                   |
| --------------------- | ------------------------------------------------------ |
| File(String pathName) | 根据指定的路径创建一个File对象                         |
| createNewFile()       | 若目录或者文件存在，则返回false,否则创建文件或者文件夹 |
| delete()              | 删除文件或者文件夹                                     |
| isFile()              | 判断这个对象表示的是否是文件                           |
| isDirectory()         | 判断这个对象表示是否是文件夹                           |
| listFiles()           | 若对象代表目录，则返回目录中所有文件的File对象         |
| mkdir()               | 根据当前指定的目录创建目录                             |
| exists()              | 判断对象对应的文件是否存在                             |

#### 3. Java Socket是什么

Socket也称套接字，网络上的两个程序通过一个双向的通信连接实现数据的交换，这个双向链路的一端称为一个Socket。

分类：面向连接的Socket通信和面向无连接的Socket通信

TCP通信：Server监听某个指定的端口，Client向Server发出Connect请求，Server端向Client端发出Accept消息，会话随即产生

Socket生命周期：打开Socket、使用Socket收发数据和关闭Socket

具体的例子如下所示：

Server.java文件代码

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.PrintWriter;
import java.net.ServerSocket;
import java.net.Socket;

public class Server {
    public static void main(String[] args) {
        BufferedReader br = null;
        PrintWriter pw = null;

        try {
            ServerSocket serverSocket = new ServerSocket(2000);
            Socket socket = serverSocket.accept();
            //获取输入流
            br = new BufferedReader(new InputStreamReader(socket.getInputStream()));
            pw = new PrintWriter(socket.getOutputStream(), true);
            String s = br.readLine();
            pw.print(s);
        } catch (IOException e) {
            e.printStackTrace();
        }
        finally {
            try {
                br.close();
                pw.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}

```

Client.java 文件代码

```java
import java.io.*;
import java.net.Socket;

public class Client {
    public static void main(String[] args) {
        BufferedReader br = null;
        PrintWriter pw = null;
        try {
            Socket socket = new Socket("localhost", 2000);
            br = new BufferedReader(new InputStreamReader(socket.getInputStream()));
            pw = new PrintWriter(socket.getOutputStream(), true);
            pw.print("Hello");
            String s = null;
            while (true) {
                s = br.readLine();
                if(s != null) {
                    break;
                }
            }
            System.out.println(s);
        } catch (IOException e) {
            e.printStackTrace();
        }
        finally {
            try {
                br.close();
                pw.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}

```

#### 4. Java NIO

全称：非阻塞IO（Nonblocking IO, NIO）

阻塞：指暂停一个线程的执行以等待某个条件的发生

由于阻塞问题的存在，一般使用多线程实现多个连接，同时阻塞会造成线程上下文的切换，使得运行效率低下

NIO通过Selector、Channel、Buffer来实现非阻塞IO操作。

NIO主要采用了Reactor（反应器）设计模式，这种模式与Observer模式类似，不同的是这种模式可以用来处理多个事件源。

NIO原理：

```
Channel被实现为一个双向的非阻塞通道，通道的两边可以进行数据的读和写操作，Selector实现使用一个线程来管理多个通道。在实现时，Channel的IO事件注册给Selector。Selector的实现原理为：对所有注册的事件进行轮询访问，一旦轮询到一个Channel有注册的事件发生，他就通过传回Selection-Key的方式通知Channel进行数据的读或写操作。Buffer用来保存数据，可以存放读取的数据或者要发送的数据。
```

#### 5. java序列化

方式：序列化和外部序列化

1. 序列化

   序列化是一种将对象以一连串的字节描述的过程，用户解决在对对象进行读写操作时所引发的问题，要实现序列化的类都要**实现Serializable接口（但是接口中没有任何方法）*

   **特点：**

   1. 如果一个类能够被序列化，那么他的子类也能够被序列化

   2. 由于static（静态）代表类的成员，transient代表对象的临时数据，因此被声明为这两种类型的数据成员是不能够被序列化的。

   序列化和反序列化的写法：

   ```java
           //序列化
   		try {
               Object obj = new Object();
               FileOutputStream fos = new FileOutputStream("Class.out");
               ObjectOutputStream oos = new ObjectOutputStream(fos);
               oos.writeObject(obj);
               oos.close();
           } catch (IOException e) {
               e.printStackTrace();
           }
   
   		//反序列化
           try {
               FileInputStream fis = null;
               fis = new FileInputStream("Class.out");
               ObjectInputStream ois = new ObjectInputStream(fis);
               Object obj = ois.readObject();
               fis.close();
           } catch (Exception e) {
               e.printStackTrace();
           }
   ```

   **什么情况下使用序列化？**

   1. 需要通过网络发送对象，或对象的状态需要被持久化到数据库或者文件中
   2. 序列化能够实现深复制，可以复制引用的对象

   **serialVersionUID的作用：** 通过其在反序列化的过程中判断类的兼容性

   **serialVersionUID的优点（都是由于id值确定不用计算得来的收益）：**

   1. 提高程序的运行效率，如果不设置在序列化的时候也会计算生成
   2. 提高不同平台上的兼容性，由于不同平台的序列化id计算方式不用，可能存在兼容性问题
   3. 增加程序各个版本的可兼容性，

2. 外部序列化（Externalizable）

   外部序列化和序列化的区别在于内置的API，增加了灵活性，同时也增加了开发难度

   ```java
   public interface Externalizable extends Serializable {
       void writeExternal(ObjectOutput var1) throws IOException;
   
       void readExternal(ObjectInput var1) throws IOException, ClassNotFoundException;
   }
   ```

   如何实现只序列化部分属性？

   1. 使用外部序列化，自定义方法

   2. 使用transient

#### 6. System.out .println()方法的注意事项

无法可说，就是对象的话调用其toString()方法，还有就是表达式的结合性。

#### 7. 泛型

泛型提供了**编译时类型安全检测机制**，该机制允许程序员在编译时检测到非法的类型。泛型的本质是**参数化类型，也就是说所操作的数据类型被指定为一个参数**。

```
 // 泛型方法 printArray
 public static < E > void printArray( E[] inputArray )
 
 //泛型类
 public class Box<T> {}
 
 // <? extends T>表示该通配符所代表的类型是 T 类型或者其子类。
 // <? super T>表示该通配符所代表的类型是 T 类型或者其父类。
```

##### 7.1 **类型擦除**

Java 中的泛型基本上都是在编译器这个层次来实现的。在生成的 **Java 字节代码中是不包含泛
型中的类型信息的**。**也就是说是在编译阶段进行类型的检查，在运行阶段如果需要需要自己手动进行类型的检查**

所以，具有以下几个注意事项：

> 1. 泛型的class对象是相同的
> 2. 泛型数组初始化时不能声明泛型类型
> 3.  instanceof 不允许存在泛型参数
> 4. 静态变量是被泛型类的所有实例所共享的
> 5. 泛型的类型参数不能用在Java异常处理的catch语句中

##### 7.2 通配符与上下界

正因为类型未知，就不能通过new ArrayList<?>()的方法来创建一个新的ArrayList对象

##### 7.3 类型系统

- 相同类型参数的泛型类的关系取决于泛型类自身的继承体系结构。
- 当泛型类的类型声明中使用了通配符的时候， 其子类型可以在两个维度上分别展开。
- 如果泛型类中包含多个类型参数，则对于每个类型参数分别应用上面的规则。

##### 7.4 最佳实践

- 在代码中避免泛型类和原始类型的混用。
- 在使用带通配符的泛型类的时候，需要明确通配符所代表的一组类型的概念。由于具体的类型是未知的，很多操作是不允许的。
- 泛型类最好不要同数组一块使用。
- 不要忽视编译器给出的警告信息。

### 4.8 Java平台与内存管理

#### 1. java语言的平台独立性

保证java具有平台独立性的机制称为“中间码”和“Java虚拟机（Java Virtual Machine”，不同的平台上装有不同的JVM，jvm负责将“中间码“翻译称为硬件平台能执行的代码

解释执行过程：代码的装入、代码的校验、代码的执行

java字节码的执行方式：即时编译方式和解释执行方式

即时编译代表将字节码转化为机器码，之后在执行机器码

解释执行方式指的是解释器通过每次解释执行一小段代码来完成java字节码程序的所有操作

#### 2. Java平台和其他语言平台有哪些区别

java平台是一个纯软件的平台，这个平台可以运行在一些基于硬件的平台（windows、linux等）上，主要包括JVM和Java API。

JVM是一个虚构出来的计算机，每次java程序运行时会有一个对应的JVM实例，只有当程序结束运行后，这个JVM才会退出。

#### 3. java加载class文件的原理机制

加载过程由类加载器来完成，具体来说，就是由ClassLoader和它的子类来实现，类加载器本身也是一个类，其实质就是把类文件从硬盘取到内存。

加载方式：隐式加载和显示加载。隐式加载代表使用new等方式创建对象时，会隐式调用类的加载器把对应的类加载到JVM中，显示加载指的是通过直接的class.forName()方法把所需要的类加载到JVM中。

Java中类的加载是动态的，并不是一次将所有的类全部加载后再运行，而是保证程序运行的基础类完成加载到JVM中，其他的使用时再进行加载。

系统将需要加载的类分为三个种类：系统类、拓展类和自定义类。分别由三个加载器进行三种类别的加载，分别是Bootstrap Loader（是用C++实现，无法在java语言中看到）、ExtClassLoader、AppClassLoader，并且这三者之间存在基础关系。三个类之间通过委托的方式进行协同工作，首先要求父类加载，如果父类加载不到，就交由当前类进行加载，如果还没找到交给子类进行加载。

类加载的主要步骤

```
装载，找到对应的class文件，然后导入
链接，分为三个小步骤：检查，检查加载类的正确性，准备，给类中的静态变量分配存储空间，解析，将符号引用转化为直接引用（可选）
初始化，对静态变量和静态代码块执行初始化工作
```

#### 4. GC

作用：减轻开发人员的工作，提高开发效率，同时屏蔽释放内存的方法避免开发人员不规范使用内存造成程序的崩溃，增加系统安全性和稳定性，自动检测对象的作用域，自动将不再被使用的空间释放掉。

任务：分配内存、保证被引用的对象不会被错误的回收、回收不再具有引用的对象的内存空间

如何管理：通过有向图进行管理堆中的对象（可以理解为一个存储的数据结构）

需要注意的是线程没有指向时依然可以运行不被释放。类似于main（即有向图的起点）

**常用的垃圾回收算法**

1. 引用计数法（Reference Counting Collector）：原理为给每个对象建立一个引用计数器，当存在指向其的引用时引用个数加一，这种方法无法解决相互引用的问题（jvm没有采用这种方法）。
2. 追踪回收算法（Tracing Collector)，从根节点开始遍历对象的引用图，被遍历到的节点打上标签，遍历完成后没有被打上便签的对象为不可达对象，可以删除。这个方法会造成内存具有很对碎片（即没有碎片整理的功能）。
3. 压缩回收算法（Compacting Collector)，把堆中活动的对象移动到堆中的一端，这样就会在堆中一端留出一段很大的空间，相当于碎片整理，但是每次的处理带来性能的损失。
4. 复制回收算法（Coping Collector)，将堆划分为大小相同的区域，在任何时候只有一个区域被使用，直到耗尽，此时垃圾回收会中断程序，通过遍历的方式将活动的对象复紧挨制到另外的一个区域中。这个算法的优点是在复制的同时解决碎片问题，空间仅利用了一半，中断式的复制降低了程序的执行效率。
5. 按代回收算法（Generational Collector） ,由于程序有“程序创建的大部分的声明周期都很短，只有一部分对象具有较长的周期的特点”。将堆划分为两个或者多个子堆，每次扫描最年轻的对象，如果经过多次扫描对象依然存活则将当前对象移到高一级的堆中，减少对其的扫描次数。

开发人员可以通知进行GC处理，但是GC系统不一定立马开始垃圾回收。

#### 5. java是否存在内存泄漏问题

虽然java中内存由垃圾管理机制进行统一管理，但是仍然存在内存泄漏的问题。

垃圾回收标准：

 	1. 给对象赋空值，以后再没有被使用过
 	2. 给对象赋予了新值，重新分配了内存空间

内存泄漏的情况：

 	1. 在堆中申请的空间没有释放。
 	2. 对象已经不再使用但是仍然在内存中保存（主要是第二种）。

引起泄漏的原因（即不想用了，但是还在内存中）：

 	1. 静态集合类，如HashMap和Vector
 	2. 各种连接，例如数据库连接，网络连接和IO
 	3. 监听器，在释放被监听对象但是忘了释放当前监听器时
 	4. 变量不合理的作用域
 	5. 单例模式可能会造成内存泄漏，单例指向的无用数据

#### 6. Java堆和栈有什么区别

变量分为基本数据类型和引用类型，基本数据类型和对象的引用变量（引用变量就相当于为对象或者数组起的一个名字）其内存都分配在栈上，变量出了作用域就会自动释放，而引用类型的变量，其内存分配在堆上或者常量池中，需要通过new进行创建。

堆内存用来存放运行时创建的对象。JVM是基于堆栈的虚拟机，每个java程序运行在一个单独的jvm实例上，每个实例唯一对应一个堆，多线程也运行在同一个jvm实例上，即线程之间共享堆内存，访问数据时需要进行同步处理。

栈的存取速度快，但是栈的大小和生存期必须是确定的，因此缺乏一定的灵活性，而堆却可以在运行时动态地分配内存，生存期不用提前告诉编译器，这也导致了存取速度慢。

需要注意的是栈中的数据在跳出作用域后就自动删除，而堆中的对象需要等到JVM的垃圾回收系统等待合适的时机进行释放。

### 4.9 容器

#### 1. Java Collection框架

![img](https://s2.ax1x.com/2019/03/23/AJpc0H.md.png)

Note: 上面的继承图中存在错误，其中HashTable实现的是Map接口而不是AbstractMap接口。

Java Collection框架中主要提供了List、Queue、Set、Stack(栈)和Map等数据结构。

三个接口：

1. Set表示数学意义上的集合概念，主要的特点是集合中的元素不能重复，需要定义equals方法确保对象的唯一性。其两个实现类为HashSet和TreeSet，TreeSet容器中元素是有序的。
2. List又称有序的Collection，按对象进入的顺序保存对象，允许重复，LinkedList、ArrayList和Vector都实现了List接口。
3. Map 提供一个从键映射到值的数据结构，用户保存键值对，键是唯一的，值可以重复。实现该接口的类有：HashMap、TreeMap、LinkedHashMap、WeakHashMap、IdentityHashMap，HashMap采用散列表实现，采用对象的HashCode可以进行快速查询。LinkedHashMap采用列表维护内部的顺序。TreeMap采用红黑树的数据结构来实现，内部元素按需排列。

#### 2. 迭代器

迭代器是一个对象，它的工作是遍历并选择序列中的对象，它提供了一种访问一个容器（container）对象中的各个元素，而又不暴露该对象内部细节的方法。创建迭代器的代价较小，迭代器也被称为轻量级容器。

使用迭代器的注意事项：

1. 使用容器的iterator()方法返回一个Iterator，然后通过Iterator的next()方法第一个元素。
2. 使用Iterator的hasNext() 方法返回一个Iterator,如果有，可以使用next()方法获取下一个元素
3. 可以通过remove()方法删除迭代器返回的元素

**ConcurrentModificationException异常**：在使用Iterator遍历容器的同时又对容器进行增加或者删除操作导致，或者是由于多线程同时使用迭代遍历容器（某个线程遍历的同时，存在其他线程对其进行增加或者删除操作）导致，（经过测试发现删除不会抛出异常），此外只存在于List中的ListIterator的迭代器支持在迭代的同时添加(iterator.add(object))或者删除(iterator.remove())元素，同时支持双向滚动。

```java
for(ListIterator<String> i = list.listIterator(); i.hasNext(); ) {
	if(i.next().equals("2")) {
        //迭代器上删除、添加都没有问题
        i.remove();   
		i.add("4");
	}
}

for(Iterator<String> i = list.iterator(); i.hasNext(); ) {
	if(i.next().equals("2")) {
        i.remove();     //迭代器上删除不会出错
		list.remove("2"); //容器上删除会出错
        list.add("4");    //容器上添加会出错
	}
}
/**
ConcurrentModifycationException原因（简言之，就是迭代过程中不允许修改（删除或者）容器中内容，但是可以在迭代器上删除和添加）：
	在调用iterator方法返回Iterator对象时，把容器中包含对象的个数赋值给expectedModCount,在调用next()方法时会 比较expectedModCount和容器中实际对象个数是否相等，不等则抛出上述的异常。
*/
```

异常解决方法：

单线程：将需要添加或者删除的对象先保存，遍历完成后进行统一的添加，或者使用迭代器上的删除或者添加方法

多线程：使用线程安全的容器（如ConcurrentHashMap、CopyOnWriteArrayList等），或者对代码块进行加锁。使用Collections.工具类生成线程安全的代理对象，具体用法实例为Collections.synchronizedMap(map)

#### 3. ArrayList、Vector、LinkedList区别

都在java.util包中，都为可伸缩数组。

**ArrayList和Vector：**都是基于Object数组进行实现，数组的存储是连续的支持下标访问，但是添加数据时需要移动数据（插入时的性能比较弱），数组的长度是动态变化的，当容量不够时添加为原来的2倍（Vector）或者1.5倍（ArrayList）。Vector是线程安全的，但是ArrayList不是，所以ArrayList性能比Vector稍好。

**LinkedList：**基于双向链表实现，插入快速，查找需要遍历，非线程安全。

**如何选择：** 只在末尾添加删除时使用ArrayList或者Vector，需要指定位置添加删除的使用LinkedList，多线程中使用Vector较为合适。

#### 4. HashMap、HashTable、TreeMap、WeakHashMap

HashMap、HashTable、TreeMap 是接口java.util.Map的实现，

HashMap、HashTable都采用了hash的方法进行索引，其相似和不同为：

1. HashMap是HashTable的轻量级实现（非线程安全），前者允许关键字为null（仅一条），后者不允许
2. HashMap把contains方法细化为containsKey和containsValue，避免歧义。
3. HashTable为线程安全，HashMap为非线程安全，后者效率更高
4. HashTable使用Enumeration,HashMap使用Iterator
5. 两者中使用的hash和reHash相同，性能上相同
6. HashTable中默认hash数组的大小是11，增加的方式是oldx2+1，HashMap中hash数组的默认大小是16，而且一定是2的倍数
7. hash值的使用不同，HashTable直接使用对象的hashCode()

上面的两种容器中，存入和取出没有固定的顺序，适合插入、删除、定位元素。TreeMap实现SortMap接口，能够把保存的记录根据键排序。LinkedHashMap存入的顺序和取出的顺序相同。

WeakHashMap与HashMap相似，不同的是前者的key采用的是若弱引用，只要外部中不在使用就可以将当前的key对象释放。

同步：在某个时刻只允许某个线程访问

如何实现HashMap同步：通过Map  m = Collections.synchronizedMap(new HashMap())达到同步效果。

#### 5. 使用自定义类型作为HashMap或HashTable的key需要注意的问题

向HashMap添加元素的过程（获取的过程类似）：

1. 调用key对象的hashCode方法得到hash值
2. 调用key对象的equals方法和指向当前hash值的所有key（没有指向时为空）进行比较，如果不存在添加进去（如果已经有key指向了当前的hash值则需要使用冲突解决方法处理），如果存在则替换原来的旧值

key假象问题（由于没有实现key的hashCode和equals逻辑造成）：由上面的过程可知，HashMap依赖于key类的hashCode和equals方法，即为了实现正确的性需要覆盖这两个方法。Object中的hashCode返回对象在内存中的地址，equals判断两个对象的地址是否相同，所以在不覆盖的情况下任何两个不同对象的hashCode和equals是不同的

自定义类作为key的注意事项：

1. 如果重写equals需要重写hashCode
2. 如果多项为key最好将类设置为不可变类
3. 对象相等其hashCode相等，反之不成立

#### 6. Collection和Collections区别

Collection：是一个集合接口，提供了对集合对象进行基本操作的通用接口方法，实现的类主要有List和Set,该接口的设计目标是为各种具体的集合提供最大化的统一的操作方式。

Collections：是针对集合类的一个包装类，提供一系列的方法实现对各种集合的搜索、排序、线程安全化等操作，其中大多数是处理线性表。其不能实例化，服务于Collection框架，如果传入的参数为空，会抛出空指针异常。

#### 7. 快速失败(fail-fast)和安全失败(fail-safe)的区别

- 快速失败（fail-fast）

  如果检测到modCount！=expectedmodCount这个条件时，就会抛出异常。

> - 在用迭代器遍历一个集合对象时，如果遍历过程中对集合对象的内容进行了修改（增加、删除、修改），则会抛出Concurrent Modification Exception。
> - 原理：迭代器在遍历时直接访问集合中的内容，并且在遍历过程中使用一个 modCount 变量。集合在被遍历期间如果内容发生变化，就会改变modCount的值。每当迭代器使用hashNext()/next()遍历下一个元素之前，都会检测modCount变量是否为expectedmodCount值，是的话就返回遍历；否则抛出异常，终止遍历。
> - 注意：这里异常的抛出条件是检测到**modCount！=expectedmodCount 这个条件**。如果集合发生变化时修改modCount值刚好又设置为了expectedmodCount值，则异常不会抛出。因此，不能依赖于这个异常是否抛出而进行并发操作的编程，这个异常只建议用于检测并发修改的bug。
> - 场景：java.util包下的集合类都是快速失败的，不能在多线程下发生并发修改（迭代过程中被修改）。


- 安全失败（fail-safe）

  > - 采用安全失败机制的集合容器，在遍历时不是直接在集合内容上访问的，而是**先复制原有集合内容，在拷贝的集合上进行遍历**。
  > - 原理：由于迭代时是对原集合的拷贝进行遍历，所以在遍历过程中对原集合所作的修改并不能被迭代器检测到，所以不会触发Concurrent Modification Exception。
  > - 缺点：基于拷贝内容的优点是避免了Concurrent Modification Exception，但同样地，迭代器并不能访问到修改后的内容，即：迭代器遍历的是开始遍历那一刻拿到的集合拷贝，在遍历期间原集合发生的修改迭代器是不知道的。
  > - java.util.concurrent包下的集合类都是安全失败的，能在多线程下发生并发修改（迭代过程中被修改）


#### 8. List、Map、Set三个接口存取元素时，各有什么特点？

> Set里面不允许有重复的元素
>
> List表示有先后顺序的集合
>
> Map是双列的集合，存放时key不可重复

### 4.10 多线程

#### 1. 线程

进程是系统进行资源分配和调度的基本单位（也就说如果存在比当前更小的执行时共用资源），进程指一段正在执行的程序。

线程也可以称为轻量级进程，它是程序执行的最小单元，一个进程拥有多个线程，线程之间共用资源（包括内存空间、输入输出设备等），但是各个线程拥有自己的栈空间。

线程的优点：

1. 减少程序的响应时间
2. 线程的创建和切换的开销较小
3. 可以充分利用多核计算资源
4. 简化程序的结构

**进程与线程的区别**

- 进程是资源分配的最小单位，线程是程序执行的最小单位。
- 进程有自己的独立地址空间，每启动一个进程，系统就会为它分配地址空间，建立数据表来维护代码段、堆栈段和数据段，这种操作非常昂贵。而线程是共享进程中的数据的，使用相同的地址空间，因此CPU切换一个线程的花费远比进程要小很多，同时创建一个线程的开销也比进程要小很多。
- 线程之间的通信更方便，同一进程下的线程共享全局变量、静态变量等数据，而进程之间的通信需要以通信的方式（IPC)进行。不过如何处理好同步与互斥是编写多线程程序的难点。
- 但是多进程程序更健壮，多线程程序只要有一个线程死掉，整个进程也死掉了，而一个进程死掉并不会对另外一个进程造成影响，因为进程有自己独立的地址空间。
- 一个进程中包含多个线程

#### 2. 同步和异步的区别

**同步：**

想实现同步操作，必须要获得每一个线程对象的锁，获得它可以保证在同一时刻只有一个线程能够进入临界区（访问互斥资源的代码块）,并且在每个锁被释放之前，其他线程就不能够再进入这个临界区直到当前已经进入临界区的线程释放锁。如果还有其他的线程希望获得该对象的锁，只能进入等待队列等待。

实现同步的方法：利用同步代码块，或者利用同步方法

**异步与非阻塞类似**

每个线程拥有运行时自身所需要的数据或者方法，因此在输入输出处理时，不必担心其他线程的状态或者行为，也不必等到输入输出处理完毕才返回。

#### 3. Java多线程

多线程的三种创建方式：

```java
import java.util.concurrent.Callable;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

public class MultiThread {
    static class MyThread1 extends Thread {
        @Override
        public void run() {
            System.out.println("My thread1");
        }
    }
    static class MyThread2 implements Runnable {
        /**
        更推荐使用实现Runnable接口，原因如下：
        1. 接口中明确表明了需要实现的方法，更清晰
        2. 一般只能增强功能时才采用继承，实现功能使用接口比较合适
        */
        @Override
        public void run() {
            System.out.println("My thread2");
        }
    }

    static class MyThread3 implements Callable<String> {
		/**
		与Runnable的不同点：
		1. Callable可以在任务结束后提供一个返回值
		2. call() 方法可以抛出异常
		3. 运行Callable可以得到一个Future对象，Future对象表示异步计算的结果，提供检查计算是否完成的方法。
		*/
        @Override
        public String call() throws Exception {
            return "My thread 3";
        }
    }

    public static void main(String[] args) {
        new MyThread1().start();
        new Thread(new MyThread2()).start();
        ExecutorService threadPool = Executors.newSingleThreadExecutor();
        Future<String> future = threadPool.submit(new MyThread3());
        try {
            System.out.println(future.get());
        }
        catch (Exception e) {
			e.printStackTrace();
        }
    }
}
```

可以同时继承自Thread同时实现Runnable接口，接口中必须实现的run方法重写了父类的方法

#### 4. run和start两个方法的区别

start调用后该线程出于就绪状态，等待着JVM调度执行，当调度到当前线程时，jvm调用其run方法

如果直接调用run方法就相当于直接调用了一个普通的方法，依然在原来的线程中运行，是同步执行，只用通过start调用后才能起到异步调用的作用。

简单理解：线程中的处理逻辑已经写在run方法中，并且run中没有开启线程的逻辑代码，所以交由start方法创建线程并把run方法放到该线程上去运行。

#### 5.同步的实现方法

同步方法：synchronized+wait/notify 和使用Lock

1. synchronized

在java中每个对象都有一个对象锁与之相关联，该锁表明对象在任何时候只允许一个线程所拥有，当一个线程调用对象的一段synchronized代码时，需要先获取这个锁，然后再去执行相应的代码，代码结束后释放锁。

两种方式：

```java
//在没有获取到跟在synchronized后边的对象的锁，被synchronized修饰的代码无法进入，即只允许一个访问
synchronized(lock) {
    // 通过lock对象的锁进行进行控制，锁的粒度相对较少，灵活
}
public synchronized void multi() {
    //锁直接加载this对象上，锁的粒度较大
}
```

2. wait() 和notify()两个一起使用

```java
//需要注意的是 需要使用synchronized 关键字，否则会抛出IllegalMonitorStateException异常
//线程1中
synchronized(lock) {
	lock.notify();  //通知等待获取当前对象的锁的线程，同时释放锁
}

//线程2中
synchronized(lock) {
	lock.wait();    //自己释放锁，等待别人notify，wait后当前线程挂起
}
```


3. Lock，功能上理解，Lock和synchronize是一样的，都是提供一个对象然后在这个对象进行加锁和是否持有锁的判断，只是synchronized使用的对象是需要显式或者隐式指定，而Lock方法使用的对象就是Lock实例对象本身，即从理解上看，Lock自己本身是一把锁，而synchronized是为别人加锁的机制。

五个常用的方法（最后一个为实现线程的等待通知机制时使用）：

1. lock() 以阻塞的方式获得锁，等待直到获得锁
2. tryLock() 以非阻塞的方式获取锁，立即返回是否获得锁
3. tryLock(long timeout, TimeUnit unit) 以非阻塞的方式获取锁，如果获取成功则返回，否则等到直到超时
4. lockInterruptibly()，如果获得锁立即返回，否则休眠等待获得锁，与lock的不同是非阻塞（允许打断），lock方法不支持被打断。即调用lock的代码段不能捕捉其打断异常。
5. unlock()，Lock对象的经过加锁后一定需要执行unlock操作，手动释放锁

ReentrantLock中newCondition()得到一个Condition实例，通过该对象的await()、await(long time, TimeUnit unit)、signal()、singalAll()实现等价于Object中的wait()、wait(long time)、notify()、notifyAll()方法，从而实现等待通知机制，不同于Object的wait和notify机制的时，Condition中可以有选择地进行通知（即通过创建多个不同的Condition进行实现，每个等待队列由Condition进行维护，如果需要实现顺序等待则一定需要实现环形通知）。

```java
public class LockAndCondition {
 
    private Lock lock = new ReentrantLock();
    private Condition condition = lock.newCondition();
 
    public void mockWait(){
 
        try {
            // 加锁
            lock.lock();
            System.out.println("已经加锁");
            condition.await();   //condition使用之前一定要先加锁
            System.out.println("已经await");
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            // 释放锁
            lock.unlock();
            System.out.println("已经释放锁");
        }
    }
}
```



阻塞的理解：不是一直占满CPU，而是没人能通过正常的方式搞死他

#### 6. sleep方法和wait方法的区别

1. 原理不同，sleep是Thread的静态方法，是线程用来控制自身流程，暂停执行一段时间，将计算资源让给其他线程。wait是Object中的方法，用来进行线程之间通信，会使拥有当前锁的线程等待其他线程notify（或者也可以设置等待时间自己醒）
2. 对锁的处理机制不同，sleep仅涉及一个线程，如果当前线程拥有锁那么不会释放锁，但如果之前没有加锁在睡觉的过程中被别人加锁了就得等待。而wait调用后会释放锁。
3. 使用的区域不同，wait必须用于同步代码块或者是同步方法中，而sleep放在什么位置都可以。

sleep必须要捕捉异常，而wait、notify、notifyall不需要捕捉异常。

sleep不释放锁容易导致死锁，一般推荐使用wait()

sleep和yield有什么区别？

1. sleep给其他线程运行机会时不考虑线程的优先级（给低优先级线程机会），而yield只给高优先级的机会。
2. sleep后进入阻塞状态，当前的线程不会执行，而yield调用后将自己变为可执行状态，即有可能方法调用后接着就执行。
3. sleep声明抛出InterruptException，而yield不声明任何异常、
4. sleep比yield具有更好的移植性

在多线程的情况下，调用sleep、wait、yield后不是在等待指定的时间后就运行，而是在指定的时间后变为可运行状态（被别人捷足先登不算，这种情况要么等，或者互相死磕），具有争抢cpu的资格。

#### 7. 终止线程的方法

stop() 方法：释放所有已经锁定的所有监视资源，容易导致其他监视这些资源的线程的状态不一致，从而导致程序执行的不确定，这种问题也难以定位。

suspend() 方法：不释放锁，程序容易发生死锁（死锁是指两个或者两个以上的线程因争夺资源而导致的一种互相等待的现象，如果无外力作用，他们都无法推进）

上面的两个方法只有在获取到计算资源是才能停止，如果之前线程已经阻塞则需要等待线程重新运行时才能结束。对于能够打断的线程（例如IO时，线程在这种状态无法被打断），捕捉打断异常也能结束。

**stop和suspend方法已经不推荐使用。**

**推荐方法：**让程序自动结束，即通过标志位等控制循环条件，让线程自己走到结尾

##### 7.1 终止线程四种方式

- 正常运行结束
- 使用退出标志退出线程
- Interrupt方法结束线程，
  - 如果线程处于阻塞状态，一定要先捕捉InterruptException异常之后再通过break跳出循环
  - 线程未处于阻塞状态，使用 isInterrupted()判断线程的中断标志来退出循环。
- stop 方法终止线程（线程不安全），导致该线程所持有的所有锁突然释放，造成不可控制

#### 8.  synchronized 与Lock有什么异同？

synchronized使用 Object对象本身的notify、wait、notifyAll调度机制，而Lock使用Condition进行线程之间的调度，完成synchronized实现的所有功能。

主要的区别：

1. 用法不一样，synchronized即可以加在方法上也可以加载特定的代码块上，Lock需要显示地指定起始位置，synchronized是通过jvm托管的，但是Lock的的锁定是通过代码实现的，具有更精确的语义。
2. 性能不一样，ReentrantLock是Lock的一个实现，不仅拥有和synchronized相同的并发性和内存语义，还多了锁投票、定时锁、等候和中断锁等，他们的性能不同，在竞争不是特别激烈的时候，synchronized的性能优于ReetrantLock，但是在竞争激烈的情况下，synchronized的性下降非常快，而后者的性能基本保持不变。
3. 锁机制不一样。synchronized获得锁和释放的方式都在块结构中，当获取到多个时以相反的顺序释放，并且是自动解锁。而Lock需要开发人员手动释放，并且在finally中释放，否则可能会引起死锁问题。
4. Lock锁支持非公平锁（通知时随机唤醒）和公平锁（按照申请的顺序排队等待），而synchronized只支持非公平锁

两种锁的实现机制不同，最好不要同时混用两种锁，即锁不住。

当一个线程进入一个对象的synchronized()方法后，其他线程依然可以调用其他的非synchronized的方法，由于锁住类和锁住对象两者不同，可以调用所有的静态方法。

#### 9.守护进程

线程：用户线程和守护线程（又称为“服务进程”、“精灵进程”、“后台线程”）。

守护线程：是指在程序运行时在后台提供一种通用服务的线程，所有守护线程都是所有非守护线程的“保姆”。几乎和用户线程一样，不同点是当所有的用户线程结束后守护线程终止。守护线程一般具有较低的优先级，在调用start之前调用setDaemon(true)将线程设置为守护线程，其中的参数表示当前线程是否是守护线程，这个参数具有传递性，即创建的子线程具有与其相同的类型。

守护线程最典型例子是：垃圾回收器。

 ##### 9.1 Hotspot JVM后台运行的系统线程

| 线程类型                | 描述                                                         |
| ----------------------- | ------------------------------------------------------------ |
| 虚拟机线程（VM thread） | 这个线程等待 JVM 到达安全点操作出现。这些操作必须要在独立的线程里执行，因为当堆修改无法进行时，线程都需要 JVM 位于安全点。这些操作的类型有：stop-theworld 垃圾回收、线程栈 dump、线程暂停、线程偏向锁（biased locking）解除。 |
| 周期性任务线程          | 这线程负责定时器事件（也就是中断），用来调度周期性操作的执行。 |
| GC 线程                 | 这些线程支持 JVM 中不同的垃圾回收活动                        |
| 编译器线程              | 这些线程在运行时将字节码动态编译成本地平台相关的机器码       |
| 信号分发线程            | 这个线程接收发送到 JVM 的信号并调用适当的 JVM 方法处理       |

#### 10.join() 方法作用是什么

线程对象调用后将其合并到父线程中，即父线程等待子线程执行完成（或者等待参数指定的时间）。

#### 11. 如何减少上下文的切换

1. 减少锁的使用，因为多线程竞争锁时会引起上下文切换
2. 使用CAS（compare and swap）算法，这种该算法减少了锁的使用，CAS是一种无锁算法
3. 减少线程的使用，任务很少的时候创建大量的进程会导致大量线程处于等待状态
4. 使用协程（可以说是微线程，它占用的内存更少而且更加灵活，虽然java没有官方的实现，但是通过Quasar开源库能够实现）或者纤程等更细粒度的并发方式

#### 12. 如何避免死锁

1. 避免一个线程同时获得多个锁
2. 避免一个线程在锁内同时占用多个资源，尽量保证每个锁只占用一个资源
3. 尝试使用定时锁，使用lock.tryLock(timeout)来替代使用内部机制
4. 对于数据库锁，加锁和解锁必须在一个数据库连接里面，否则可能会出现解锁失败的情况。

#### 13. 协程

使用quasar框架实现，实现了和go语言中的goroutine对标的编程概念Fiber。

#### 14. 线程池

**使用线程池的好处**

1. 降低资源消耗
2. 提高响应速度
3. 提高线程的可管理性

**this逃逸**

this逃逸是指在构造函数返回之前其他的线程就已经持有该对象的引用，调用尚未构造安全的对象方法可能引发令人疑惑的错误。

**Executor框架结构**

1. 任务，执行任务需要实现Runnable接口或Callable接口
2. 任务的执行，核心接口Executor以及继承自它的ExecutorService接口，ScheduledThreadPoolExecutor和ThreadPoolExecutor这两个关键类实现了ExecutorService
3. 异步计算的结果，Future接口以及Future接口的实现类FutureTask

**基本要求**

1. 明确指出线程必须通过线程池提供，不允许在应用中自行显示创建线程
2. 不允许直接使用Executors去创建线程，而是通过ThreadPoolExecutor方式，这种处理方式让写的同坐更加明确线程池的运行规则，避免资源耗尽的风险。

**线程池创建的方式：**

1. 通过构造方法实现

![通过构造函数创建线程池](http://my-blog-to-use.oss-cn-beijing.aliyuncs.com/18-4-16/17858230.jpg)

2. 通过Executor框架的工具类Executors来实现

![通过Executor框架的工具类Executor来实现](http://my-blog-to-use.oss-cn-beijing.aliyuncs.com/18-4-16/13296901.jpg)

**ScheduledThreadPoolExecutor和Timer的比较**

1. Timer对系统时钟变化比较敏感，ScheduledThreadPoolExecutor不是
2. Timer只有一个执行程序，因此长时间运行的任务可以延迟其他任务，而后者可以配置任意数量的线程
3. 在TimerTask中抛出一个异常会杀死以个线程，从而导致Timer死机，后者不仅捕捉运行时异常，还允许在需要的时候处理他们。抛出异常的任务会被取消，但是其他任务继续 运行。

**各种线程池的适用场景**

FixedThreadPool： 适用于为了满足资源管理需求，而需要限制当前线程数量的应用场景。它适用于负载比较重的服务器；

SingleThreadExecutor： 适用于需要保证顺序地执行各个任务并且在任意时间点，不会有多个线程是活动的应用场景。

CachedThreadPool： 适用于执行很多的短期异步任务的小程序，或者是负载较轻的服务器；

ScheduledThreadPoolExecutor： 适用于需要多个后台执行周期任务，同时为了满足资源管理需求而需要限制后台线程的数量的应用场景，

SingleThreadScheduledExecutor： 适用于需要单个后台线程执行周期任务，同时保证顺序地执行各个任务的应用场景。


### 4. 11 Java数据库操作

#### 1. 如何通过JDBC访问数据库

```java
Connection con = null;
Statement stmt = null;
ResultSet rs = null;
try {
    //1. 添加JDBC驱动器， 添加jar或者maven依赖
    //2. 加载JDBC驱动
    Class.forName("com.mysql.jdbc.Driver");
    //3. 建立数据库连接
    con = DriverManager.getConnection("jdbc:mysql://localhost:3306/Test", "root", "123456");
    //4. 建议StateMent或者是PrepareStatement对象
    stmt = con.createStatement();
    //5. 执行SQL语句
    stmt.execute("insert into Employee values(1, 'LQ', 25)");
    //6. 依次访问结果集ResultSet
    rs = stmt.executeQuery("select * from Employee");
    while(rs.next) {
        System.out.println(rs.getInt(1) + " " + rs.getString(2) + " " + rs.getInt(3));
    }
}
catch(Exception e) {
    e.printStackTrace();
}
finally {
    try {
        //7. 依次关闭ResultSet、Statement、PrepareStatement、Connection对象
        if(rs != null) {
        	rs.close();
        }
        if(stmt != null) {
     	   stmt.close();
    	}
        if(con != null) {
        	con.close();
        }
    }
    catch(Exception e) {
        e.printStackTrance();
    }
}
```

#### 2. JDBC处理事务

事务：由一条或多条数据库操作SQL语句组成的一个不可分割的工作单元。

JDBC中默认设置事务是自动提交的，可以通过调用setAutoCommit(false)进行手工控制。

操作#正常结束调用commit方法提交事务、在异常捕获代码中调用rollback()回滚事务。

事务隔离界别（通过setTransactionLevel方法进行设置）：

1. TRANSACTION_NONE，不支持事务
2. TRANSACTION_READ_UNCOMMITED，未提交读
3. TRANSACTION_READ_COMMITED，已提交读
4. TRANSACTION_REPEATABLE_READ，可重复读
5. TRANSACTION_SERIALIZABLE，可序列化

脏读：事务读取另外事务未提交的数据

不可重复读：同一事务不同两次读取的数据不同

虚读：一个事务两次相同的读得到的结果数量 不同

#### 3. Class.forName作用

将类加载到JVM中，返回该类的一个对象，并且JVM会加载这个类，同时JVM会执行该类的静态代码段。JDBC规范要求Driver类在使用前要向DriverManager注册自己，在Driver类中的存在静态代码片段实现上面的功能。

```java
static {
	try {
		java.sql.DriverManager.registerDriver(new Driver());
	}
	catch(SQLException E) {
        throw new RuntimeException("Can't register driver!");
	}
}
```

之所以采用这种类加载的方式是为了提高软件的可拓展性（相比于new方法），可以通过配置文件进行灵活的替换。

#### 4. Statement、PrepareStatement和CallableStatement

PrepareStatement比Statement具有的优点：

1. 效率更高，Sql命令会被编译和解析放入命令缓冲区，执行同一个PrepareStatement对象时，由于在缓冲区中可以发现预编译的命令，虽然会被再解析一次，不会被再次编译可以再次使用提高效率。
2. 代码的可读性和可维护性更好。
3. 安全性更好，能够预防SQL注入攻击。

CallableStatement由prepareCall方法创建，它为所有的DBMS提供一种标准形式调用存储过程的方法。继承了用于处理输入参数的方法还增加了调用数据库中存储过程和函数以及设置输出类型参数的功能。

#### 5.getString和getObject的区别

JDBC提供getString、getInt、getDate等方法从ResultSet中获取数据，当查询的数据量较小是没有问题，不用考虑考虑性能，但是查询结果中数据量非常大（内存放不下时）,则会抛出异常。使用getObject可以解决这个问题。

#### 6. 使用JDBC的注意事项

1. 数据库连接的数量是有限的，一定要保证打开的连接在用完会被关闭。
2. creteStatement和prepareStatement最好放在循环外面，并且使用这些Statement后需要及时关闭，因为每次创建这些就相当于在数据库中打开一个游标，不停地打开可能会抛出异常。

#### 7. JPO

Java数据对象是一个用于存储某种数据仓库中的对象的标准化API，他使开发人员能够间接地访问数据库。JPO是JDBC的一个补充，存储数据对象完全不需要额外的代码，相比较于JDBC，JPO更加灵活、通用，提供了到任何数据底层的存储功能，使得应用的可移植性更强。

#### 8. JDBC与Hibernate有什么区别

Hibernate是JDBC的的封装，采用配置文件的形式将数据库的连接参数写到XML文件中，至于对数据库的访问还是通过JDBC完成。

Hibernate是一个持久层框架，将表的信息映射到XML文件中，再从XML文件映射到持久化类中，这样可以使用HQL查询语言进行处理，HQL查询语言返回的是List<Object[.]>类。另外一个重要的区别是具有Dao（Data Access Object）类层，该层是sql语句出现的唯一位置，从而Hibernate具有很好的维护性和拓展性。

### 4.12 类图阅读

[JAVA类图](https://www.cnblogs.com/mazi12/p/4609208.html)

#### 4.12.1 基本元素符号

- 类，包含三部分：类名、属性、提供的方法
- 包，常规用途的组合机制
- 接口，接口是一些列操作的集合，它指定了一个类所提供的服务。

#### 4.12.2 类之间的关系

![img](https://images0.cnblogs.com/blog2015/700919/201506/300002162128214.png)

- **关联关系**（双向、单向、自、重数性关联），使用**带箭头的实线**连接有关联的对象，关联线上可以注明角色名。

  用于表示一类对象与另一类对象之间有联系，通常将一个类的对象作为另一个类的属性

- **聚合关系**，聚合关系用带**空心菱形**的直线表示

  表示一个整体与部分的关系，成员类是整体类的一部分，即成员对象是整体对象的一部分，但是成员对象可以**脱离整体对象独立存在**

- **组合关系**，组合关系用带**实心菱形**的直线表示

  也表示类之间整体和部分的关系，但是组合关系中部分和整体具有**统一的生存期**

- **依赖关系**，依赖关系用**带箭头的虚线**表示，由依赖的一方指向被依赖的一方

  依赖关系是一种**使用关系**，依赖关系体现在某个类的方法使用另一个类的对象作为**参数**。

- **泛化关系**，泛化关系用带空心三角形的直线来表示

  实际上就是继承关系

- **接口与实现关系**，类与接口之间的实现关系用带空心三角形的虚线来表示

## Java Web

#### 1. 页面请求的工作流程

照抄自[用户访问网站基本流程及原理(史上最全,没有之一)](https://blog.csdn.net/yonggeit/article/details/72857630)

![](https://img-blog.csdn.net/20151010124247204)

#### 2. http中的get和post的区别

请求的方法具有很多：GET、POST、HEAD、TRACE、OPTIONS等，

但是常用知识get和post

get方法常用来获取获取服务器上的资源，post可以用来获取资源同时更多用来传输数据

推荐使用post上传数据的原因：

1. 采用get方式数据直接添加在地址后面，受最大地址的限制，数据量较小
2. get方式传输的数据可见，存在安全隐患。
3. get支持传输数据类型简单，没法上传文件等数据

#### 3. Servlet

简单理解：生成动态网页的技术，和CGI（使用Perl语言写的公共网关接口）一样样的

定义：采用Java语言编写的服务器端程序，他运行于Web服务器中的Servlet容器（比如tomcat）中，其主要的功能是提供请求/响应的Web服务模式，可以生成动态的文本内容，而这正是html不具备的。

和其他生成动态页面的技术相比，其优点是：

1. 较好的移植性（有个好爹很重要）
2. 执行效率高
3. 功能强大，能和Web服务器进行交互
4. 使用方便，提供非常多非常有用的接口来读取或设置HTTP头消息，处理Cookie和跟踪会话状态
5. 可拓展性强，

servlet和tomcat 的关系，一个是技术一个运行当前程序的容器。

Servlet处理客户端请求的几个步骤？

1. 用户通过点击一个链接向servlet发起请求，
2. Web服务器收到请求后，会把该请求交给相应的容器来处理，当容器发现这是对Servlet发起的请求后，容器会创建两个对象，HttpServletResponseg和HTTPServletRequest。
3. 容器根据请求消息中的URL消息找到对应的Servlet，然后针对该请求创建一个单独的线程，同时把第二步创建的的两个对象以参数的形式传递到新创建的线程中。
4. 容器调用Servlet的service方法来完成对完成用户请求的响应，service会调用doPost或doGet方法来完成具体的响应任务，同时把生成的动态页面返回给容器。
5. 容器把响应消息组装成为HTTP格式，返回给客户端。此时，这个线程运行结束，同时删除第二步创建的两个对象

Servlet与CGL区别：前者使用线程实现，后者使用进程实现，前者的效率更高。

#### 4. doPost和doGet方法怎么选择

最终的请求交给Servlet来处理，而Servlet是通过调用service()方法来处理请求，service方法中根据请求的类型选择调用doGet或者doPost进行处理，在Servlet中两个方法都得实现，但是没有特殊的要求可以直接让两个具有相同的处理逻辑。

#### 5. Servlet的生命周期

Servlet生命周期只有两个状态：未创建状态和初始化状态，状态的转换通过init、service、destroy三个方法进行控制

init：是servlet生命的起点，用于创建或打开与Servlet相关资源以及执行初始化工作。

service：真正处理客户端发送过来的请求的方法

destroy：释放init方法中打开的与servlet相关的资源

具体而言，Servlet的生命周期可以分为下面五个阶段：

1. 加载。容器通过类加载器使用Servlet类对应的文件来加载Servlet
2. 创建。调用Servlet构造函数创建一个Servlet实例
3. 初始化。通过调用init的方法完成初始化，这个方法在创建后向客户端提供服务调用之前调用，并且只调用一次
4. 处理客户端请求。新的请求到来时创建新的线程，调用service完成客户端请求
5. 卸载。容器在卸载Servlet时需要调用destroy方法，让Servlet自己释放占用的系统资源，同样只调用一次

#### 6. Jsp的优点

是一种动态技术标准，原理其实就是在html中混入了java代码以及标签，简化了之前的println方式。其理念的引入让每个servlet只负责其对应的业务逻辑的处理，让JSP来负责用户的HTML显示，实现了业务逻辑和视图实现的分离，提高了系统的可拓展性。

#### 7. JSP与Servlet异同

JSP可以看成是一个特殊的Servlet，JSP最终需要被转换为Servlet来运行，因此处理请求的实际上是编译后的Servlet

不同点：

1. Servlet的实现方式是java中嵌入html代码，修改html代码非常不方便，适合用来做流程控制，JSP是html中插入java代码，比较适合页面的展示
2. Servlet没有内置对象，JSP中的内置对象都是通过HttpServlet、HttpServletResponse、以及HttpServlet三个对象得到。

#### 8. 如何使用JSP与Servlet实现MVC模型

MVC：是Model（模型）、View（视图）和Controller（控制器）三个单词的首字母组合，模型层实现系统中的业务逻辑（JavaBean、EJB），视图层用于与用户的交互（JSP），控制层是模型和视图之间沟通的桥梁，他可以把用户请求分派并选择恰当的视图来显示他们，同时可以解释用户的输入并将其映射为模型层能够执行的操作。各个模型之间的关系如图所示：

![](http://r.photo.store.qq.com/psb?/V12MyHON4TqWm3/OpejeOdAwPAFeYxZmrH4YFFesDWmJBX6b52rp3Bn6Bc!/o/dA4AAAAAAAAA&ek=1&kp=1&pt=0&bo=vwLVAb8C1QEDACU!&su=158663601&sce=0-12-12&rf=2-9)

模型：表示企业数据和业务逻辑，业务模型的设计是MVC中的核心部分。业务模型中还有一个重要的模型是数据模型，主要指对实体对象数据的持久化，例如将一张订单保存到数据库，或者从数据库中读取数据。

视图：是用户看到与交互的界面，在视图中其实没有真正的业务处理发生，它只是作为一种输出数据并允许用户操纵的方式。视图强大的原因：根据客户的类型显示信息、显示商业结构，而不关心如何获得数据。

控制器：接收用户的输入并调用模型和视图去完成用户的需求。

MVC优点：

1. 低耦合性
2. 高重用性和可适用性
3. 较低的生命周期成本
4. 可维护性
5. 有利于软件工程化管理

#### 9. forward和redirect的区别

forward是服务器内部的重定向，整个定向的过程用的是同一个request（附带之前的请求信息），在浏览器中看到的是同一个地址

redirect：是客户端的重定向，是完全的跳转，即**客户端会获得跳转后的地址重新发送请求**，浏览器会显示跳转后的地址。比forward 多一次跳转，所以效率低于forward。

filter不是servlet不能产生response但是可以在到达前修改request和在返回时修改response，其实是一个”servlet链“

filter 作用：

1. 在Servlet被调用前截获
2. 在Servlet调用之前检查request
3. 根据需要修改Request和Response
4. 在Servlet调用之后截获

#### 10. JSP内置对象
1、request对象
request 对象是 javax.servlet.httpServletRequest类型的对象。 该对象代表了客户端的请求信息，主要用于接受通过HTTP协议传送到服务器的数据。（包括头信息、系统信息、请求方式以及请求参数等）。request对象的作用域为一次请求。
 2、response对象
response 代表的是对客户端的响应，主要是将JSP容器处理过的对象传回到客户端。response对象也具有作用域，它只在JSP页面内有效。
 3、session对象
session 对象是由服务器自动创建的与用户请求相关的对象。服务器为每个用户都生成一个session对象，用于保存该用户的信息，跟踪用户的操作状态。session对象内部使用Map类来保存数据，因此保存数据的格式为 “Key/value”。 session对象的value可以使复杂的对象类型，而不仅仅局限于字符串类型。
4、application对象
 application 对象可将信息保存在服务器中，直到服务器关闭，否则application对象中保存的信息会在整个应用中都有效。与session对象相比，application对象生命周期更长，类似于系统的“全局变量”。
 5、out 对象
out 对象用于在Web浏览器内输出信息，并且管理应用服务器上的输出缓冲区。在使用 out 对象输出数据时，可以对数据缓冲区进行操作，及时清除缓冲区中的残余数据，为其他的输出让出缓冲空间。待数据输出完毕后，要及时关闭输出流。
6、pageContext 对象
pageContext 对象的作用是取得任何范围的参数，通过它可以获取 JSP页面的out、request、reponse、session、application 等对象。pageContext对象的创建和初始化都是由容器来完成的，在JSP页面中可以直接使用 pageContext对象。
7、config 对象
config 对象的主要作用是取得服务器的配置信息。通过 pageConext对象的 getServletConfig() 方法可以获取一个config对象。当一个Servlet 初始化时，容器把某些信息通过 config对象传递给这个 Servlet。 开发者可以在web.xml 文件中为应用程序环境中的Servlet程序和JSP页面提供初始化参数。
8、page 对象
page 对象代表JSP本身，只有在JSP页面内才是合法的。 page隐含对象本质上包含当前 Servlet接口引用的变量，类似于Java编程中的 this 指针。
 9、exception 对象
exception 对象的作用是显示异常信息，只有在包含 isErrorPage=true 的页面中才可以被使用，在一般的JSP页面中使用该对象将无法编译JSP文件。excepation对象和Java的所有对象一样，都具有系统提供的继承结构。exception 对象几乎定义了所有异常情况。在Java程序中，可以使用try/catch关键字来处理异常情况； 如果在JSP页面中出现没有捕获到的异常，就会生成 exception 对象，并把 exception 对象传送到在page指令中设定的错误页面中，然后在错误页面中处理相应的 exception 对象。

#### 11. request对象主要方法

见名知意。

setAttribute(String name,Object)：设置名字为name的request的參数值
getAttribute(String name)：返回由name指定的属性值
getAttributeNames()：返回request对象全部属性的名字集合，结果是一个枚举的实例
getCookies()：返回client的全部Cookie对象，结果是一个Cookie数组
getCharacterEncoding()：返回请求中的字符编码方式
getContentLength()：返回请求的Body的长度
getHeader(String name)：获得HTTP协议定义的文件头信息
getHeaders(String name)：返回指定名字的request Header的全部值，结果是一个枚举的实例
getHeaderNames()：返回所以request Header的名字，结果是一个枚举的实例
getInputStream()：返回请求的输入流，用于获得请求中的数据
getMethod()：获得client向server端传送数据的方法
getParameter(String name)：获得client传送给server端的有name指定的參数值
getParameterNames()：获得client传送给server端的全部參数的名字，结果是一个枚举的实例
getParameterValues(String name)：获得有name指定的參数的全部值
getProtocol()：获取client向server端传送数据所根据的协议名称
getQueryString()：获得查询字符串
getRequestURI()：获取发出请求字符串的client地址
getRemoteAddr()：获取client的IP地址
getRemoteHost()：获取client的名字
getSession([Boolean create])：返回和请求相关Session
getServerName()：获取server的名字
getServletPath()：获取client所请求的脚本文件的路径
getServerPort()：获取server的port号
removeAttribute(String name)：删除请求中的一个属性

#### 12.JSP动作

jsp:include,  用来在页面被请求时引入一个文件

jsp:useBean 用来寻找或者实例化一个JavaBean

jsp:setProperty 用来设置已经实例化的Bean对象的属性

jsp:getProperty 用来获取已经实例化的Bean对象的属性

jsp:forward 把请求转到一个新页面

jsp:plugin 用于在浏览器器中播放或者显示一个对象，例如applet

#### 13 . include指令和include动作的区别

作用用来引入一个另外的一个文件，指令的写法<%@ include file="test.jsp" %>

区别（二（编译时和运行时）加二（共享和不共享））：

1. include指令在编译时导入，适合包含静态页面的方式，include动作在运行导入，适合动态页面
2. 使用include动作时，页面生成的变量不可以用于另一个文件（除非放置于request、response等中），使用include指令时两个页面可以共享变量。
3. 当使用include指令时需要新生成的界面要符合JSP语法要求，应该避免变量名冲突。而使用动作没有冲突。
4. 使用include指令时修改被包含的jsp，但不会立即修改，但使用include动作引入界面发生修改后会立即生效。

#### 14. 会话跟踪技术有哪些

1、 会话：客户端打开与服务器的连接并发出请求到服务器响应客户端请求的全过程。

2、 四个作用域：page、request、session、application

3、 四种会话跟踪技术（摘抄自博客[会话跟踪技术的四种实现方法及特点整理](https://blog.csdn.net/qq_33098039/article/details/78184535)）：

a) URL重写： 
URL(统一资源定位符)是Web上特定页面的地址，URL地址重写的原理是将该用户Session的id信息重写 到URL地址中，以便在服务器端进行识别不同的用户。**URL重写能够在客户端停用cookies或者不支持cookies的时候仍然能够发挥作用。**

b) 隐藏表单域： 
将会话ID添加到HTML表单元素中提交到服务器，**此表单元素并不在客户端显示，浏览时看不到，源代码中有。**

c) Cookie： 
Cookie是Web服务器发送给客户端的一小段信息，客户端请求时可以读取该信息发送到服务器端，进而进行用户的识别。对于客户端的每次请求，服务器都会将Cookie发送到客户端,在客户端可以进行保存,以便下次使用。 **服务器创建保存于浏览器端，不可跨域名性，大小及数量有限。**客户端可以采用两种方式来保存这个Cookie对象，一种方式是 保存在 客户端内存中，称为临时Cookie，浏览器关闭后 这个Cookie对象将消失。另外一种方式是保存在 [客户机](https://www.baidu.com/s?wd=%E5%AE%A2%E6%88%B7%E6%9C%BA&tn=24004469_oem_dg&rsv_dl=gh_pl_sl_csd)的磁盘上，称为永久Cookie。以后客户端只要访问该网站，就会将这个Cookie再次发送到服务器上，前提是 这个Cookie在有效期内。 这样就实现了对客户的跟踪。 
**Cookie是可以被禁止的。**

d) session： 
每一个用户都有一个不同的session，各个用户之间是不能共享的，是每个用户所独享的，在session中可以存放信息。 **保存在服务器端。需要解决多台服务器间共享问题。如果Session内容过于复杂，当大量客户访问服务器时可能会导致内存溢出。因此，Session里的信息应该尽量精简。 **
在服务器端会创建一个session对象，产生一个sessionID来标识这个session对象，然后将这个sessionID放入到Cookie中发送到客户端，下一次访问时，sessionID会发送到服务器，在服务器端进行识别不同的用户。 
**Session是依赖Cookie的，如果Cookie被禁用，那么session也将失效。**

#### 15、web开发如何制定字符串的编码

三个编码：

<% page contentType= %>    服务器端编码

< meta http-equiv = >             客户端编码

response.setContentType()   JSP页面显示编码

编码之间的互相转换：

```java
String res = new String("str".getBytes("ISO-8859-1"), "GBK");
```

#### 16 Ajax

Asychronous JavaScript and XML，异步JavaScript与Xml，是一个结合后端技术、xml、以及JavaScript的编程技术。

目的：在不刷新页面的情况下通过与服务器进行少量数据交换提高页面的交互性。

优点：

1. 降低服务器的网络负载，指向服务器请求必须的数据，较少了数据量
2. 降低服务器的处理时间，使用SOAP（简单对象访问协议）或者其他协议或者规范，在客户端采用JavaScript进行处理
3. 提高系统的响应时间，不需要重新加载整个页面，提高系统的稳定性和可用性，提高用户的满意程度

#### 17. Session和Cookie区别

a) cookie数据存放于客户端，session数据存放于服务器端

b) cookie是不安全的，别人可以分析存放在本地的cookie进行cookie欺骗，考虑安全应该使用session

c) session会在一定时间内保存在服务器上，当访问增多时会降低服务器的性能，考虑减轻服务器压力方面应当使用cookie

d) 单个cookie保存的数据不超过4K，很多浏览器限制一个站点最多保存20个cookie

5、应用：账号密码、购物车一般在cookie中，验证码私人信息一般在session中

**客户端保存数据的方式**

- 本地存储localstorage
- 本地存储SessionStorage
- 离线缓存application cache
- Web Sql 
- IndexedDb 索引数据库

### 5.2 J2EE和EJB

#### 1. 什么是J2EE

J2EE（Java2 Platform， Enterprise Edition）是Java平台企业版的简称，是用来开发和部署企业级应用的一个构架，主要由构件、服务和通信三个模块组成。

构件：包括客户端和服务器端，客户端构建包含Applets和Application Clients，服务器构建分为：Web构建（Servlet和Jsp）和EJBs（Enterprise java Beans）

服务由J2EE平台提供商提供，分为Service Api和运行时服务

通信由容器提供的支持协作构件之间的通信。

#### 2.  J2EE中常用术语

1. Web服务器，运行在Internet上的计算机程序
2. Web容器，他是一种服务程序，用来给运行在其中的程序提供运行环境。
3. EJB容器，相当于分布式组件对象模型
4. Applet容器，嵌入在浏览器中轻量级客户端
5. Application Client容器，重量级客户端，实现大多数的Service和API。
6. JNDI，java命名和目录接口，提供目录接口。
7. JMS，java消息服务，面向消息中间件的API
8. JTA，java事务服务。
9. JAF，java Beans激活框架，专用的数据处理框架
10. RMI，远程方法调用，主要用于远程调用服务。

#### 3. EJB有哪些不同的类别

简化分布式应用的开发过程，定义了一组可重用的组件，Enterprise Beans。其分为三类：

1. Session Bean，用来实现服务器端的业务逻辑，同时协调Bean之间的交互。根据Session Bean是否有状态，可以分为两类：Stateless Session Bean（可以被共享，同时处理多个客户应用的请求）和Stateful Session Bean（可以记录用户的请求状态，只能处理一个客户的请求）
2. Entity Bean，主要是资料组件，代表数据库中的记录，因此与数据库中的数据有着相同的生存周期，可以被多个客户应用共享。两种数据库持久化处理方法：CMP（Container Managed Persisitence，容器管理的持续性，构件的相关数据库操作由容器自动完成）和BMP（Bean Managed Persistence, Bean 管理的持续性，构件的相关数据库操作由开发人员在代码中通过JDBC编程实现）。Entity Bean具有三种状态：no-state（实例没有被创建）， pooled（实例已经被创建，但是还没有被关联到EJB 对象），ready（已经关联到EJB对象）
3. Message Driver Bean，用来处理异步消息，一般不是由用户调用。调用方式：异步消息发送到Message Driver Bean后，容器调用Message Driver Bean对象的onMessage方法来处理异步请求

#### 4. 此处跳过了很多关于EJB的内容，实在没有看懂在讲什么

略

#### 5. Web服务器和Web应用服务器区别

1. web服务器是可以向发出请求的浏览器提供资源的程序，而web应用服务器提供访问业务逻辑的途径供客户端应用程序使用。
2. web服务器一般是通用的，而后者一般是专用的

#### 6. Web Service

是一种基于网络的分布式模块化组件，可以将可调用的功能发布到web上以供应用程序调用。

web service基于下面的一些协议实现

1. 可拓展可标记语言（xml），实现的基础，适合用来传输数据
2. web服务描述语言（Web Service Markup Language， WSDL），使用xml定义web service可以做什么，在哪里以及怎样能调用
3. 通用描述、发现、与集成服务（Universal Description，Discovery and Integration， UDDI），他是由OASIS指定的规范。为Web服务提供三个重要的技术支持：标准、透明、专门描述web服务的机制；调用Web服务的机制；可以访问的web服务注册中心。
4. 简单对象存取协议（Simple Object Access Protocol， SOAP）

web service 实现方式的优点：

1. 完好的封装性，只需要知道提供的功能不需要知道其具体的实现
2. 松耦合，接口不改变的前提下可以随便改变实现方式，不会影响使用者
3. 高度客户操作性，可以跨平台、跨语言进行调用
4. 动态性，可以自动发现服务并进行调用

#### 7. SOAP和REST

Soap是一个严格定义的信息交换协议，用于在Web Service中把远程调用和返回封装成机器可读的格式化数据，SOAP使用了XML数据格式，定义了一套复杂的标签，来描述调用过程、参数、返回值、出错信息等内容。

REST（Representational State Transfer，表述性状态转移），通过申请资源来实现状态的转换，可以被看做一台虚拟的状态机。

两者对比：

|          | SOAP                                                         | REST                                                |
| -------- | ------------------------------------------------------------ | --------------------------------------------------- |
| 寻址模型 | URI只用来定位SOAP端点，资源与URI是一一对应，一端点对应多个资源 | 标准化的URI、DNS；URI与资源一一对应                 |
| 接口     | 无通用接口，自行定义                                         | 提供通用接口，即Http的Get、PUT、POST、DELETE        |
| 中间媒介 | 不兼容传统的Web中间媒介                                      | 兼容传统的Web中间媒介，包括代理，缓存服务器，网管等 |
| 安全性   | 十分复杂，不能使用现有的防火墙                               | 简单有效，可用现有的防火墙                          |

#### 8. 什么是XML

可拓展标记语言（eXtensible Markup Language, XML）是一套定义语义标记规则的语言，可以被用来描述业务数据，数学数据等。

优点：

1. 实用性强
2. 访问速度快
3. 可拓展性好
4. 跨平台性好

缺点：数量量大时存储效率低

定义方式：文档类型定义（Document TypeDefine， DTD）与Schema，他们一方面用于定义XML文档的结构，另一方面用于验证XML文档是否满足指定的结构

解析方式：DOM和SAX（Simple API for XML, XML简单API），前者需要将所有的数据加载到内存中进行树的构建，后者以事件驱动，不会再内存中存储XML文件的内容，通过遍历文件来获取用户所需的数据。

#### 9. 数据库连接池的工作机制

数据库连接管理好坏直接影响整个系统的性能，因为：

1. 创建一个连接耗时
2. 连接的数量是有限的。

数据库连接池负责分配、管理、并释放数据库连接，允许应用程序重复使用一个现有的数据库连接，而不是新建，同时释放空闲时间超过最大空闲时间，避免连接遗漏。

连接池的好处？

1. 缩短用户的响应时间，提高运行效率
2. 提高了数据库操作的性能

#### 10. J2EE 开发调优方法

1. 优化设计
2. 尽可能使用连接池
3. 给web容器分配合理的线程数量来处理客户端的http请求
4. 根据实际的大小设置Java虚拟机堆空间的大小
5. 使用框架来提高系统的效率
6. 把一些经常被访问的Servlet或者Jsp缓存起来
7. 使用EJB时合理配置线程池大小
8. 优化IO性能
9. 优化查询
10. 对session进行合理管理和设置

### 5.3 框架

#### 1. Struts框架

跳过，以后进行详细的补充

#### 2. IoC

控制反转（Inverse of Control, Ioc）有时也被称做依赖注入，是一种降低对象之间耦合关系的设计思想。

在 分层体系结构中，上层调用下层接口，上层依赖于下层执行，即调用者依赖于被调用者。通过Ioc使得上层不在依赖于下层的接口，使得由调用者来决定被调用者。

Spring中采用Ioc的方式来实现把实例化的对象注入到开发人员自定义的对象中，使其具有较强的可拓展性。

Ioc优点：

1. 不需要关注对象如何被创建，同时增加新类也非常方便，只需要修改配置文件就可以实现对象的“热插拔”
2. Ioc可以通过配置文件来确定需要注入的实例化对象，非常便于进行单元测试

IoC缺点：

1. 对象是通过反射机制实例化出来的，因此对系统的性能有一定的影响
2. 创建对象的流程变得复杂。

#### 3. AOP

面向切面编程（Aspect-Oriented Programming， AOP）是对面向对象开发的一种补充，它允许开发人员在不改变原来模型的基础上动态地修改模型以满足新的需求。

#### 4. Spring 框架

Spring是一个J2EE框架，实现轻量级Ioc的良好支持，同时提供对AOP技术非常好的封装。

三大思想：DI（依赖注入）、IOC（控制反转）、AOP（面向切面编程）

七大模块：

| 模块名         | 功能                                                         |
| -------------- | ------------------------------------------------------------ |
| Spring Core    | Core封装包是框架的最基础部分，提供IOC和依赖注入特性。这里的基础概念是BeanFactory，它提供对Factory模式的经典实现来消除对程序性单例模式的需要，并真正地允许你从程序逻辑中分离出依赖关系和配置。 |
| Spring Context | 构建于Core封装包基础上的 Context封装包，提供了一种框架式的对象访问方法，有些象JNDI注册器。Context封装包的特性得自于Beans封装包，并添加了对国际化（I18N）的支持（例如资源绑定），事件传播，资源装载的方式和Context的透明创建，比如说通过Servlet容器。 |
| Spring Dao     | DAO (Data Access Object)提供了JDBC的抽象层，它可消除冗长的JDBC编码和解析数据库厂商特有的错误代码。 并且，JDBC封装包还提供了一种比编程性更好的声明性事务管理方法，不仅仅是实现了特定接口，而且对所有的POJOs（plain old Java objects）都适用。 |
| Spring ORM     | ORM 封装包提供了常用的“对象/关系”映射APIs的集成层。 其中包括JPA、JDO、Hibernate 和 iBatis 。利用ORM封装包，可以混合使用所有Spring提供的特性进行“对象/关系”映射，如前边提到的简单声明性事务管理。 |
| Spring AOP     | Spring的 AOP 封装包提供了符合AOP Alliance规范的面向方面的编程实现，让你可以定义，例如方法拦截器（method-interceptors）和切点（pointcuts），从逻辑上讲，从而减弱代码的功能耦合，清晰的被分离开。而且，利用source-level的元数据功能，还可以将各种行为信息合并到你的代码中。 |
| Spring Web     | Spring中的 Web 包提供了基础的针对Web开发的集成特性，例如多方文件上传，利用Servlet listeners进行IOC容器初始化和针对Web的ApplicationContext。当与WebWork或Struts一起使用Spring时，这个包使Spring可与其他框架结合。 |
| Spring Web MVC | Spring中的MVC封装包提供了Web应用的Model-View-Controller（MVC）实现。Spring的MVC框架并不是仅仅提供一种传统的实现，它提供了一种清晰的分离模型，在领域模型代码和Web Form之间。并且，还可以借助Spring框架的其他特性。 |

Spring好处：

1. 开发多层应用程序时，代码非常容易单独测试
2. 培养开发人员面向接口编程的良好习惯
3. 对数据的存取提供一个一致框架
4. 通过支持不同的事务处理API（例如JTA、JDBC、Hibernate）的方法对事物的管理提供一致的抽象方法
5. Spring框架编写的大部分业务对象不依赖于Spring

##### 4.1  Spring 依赖注入的四种方式

```xml
<!-- 1. 构造器注入 -->
<bean id="CatDaoImpl" class="com.CatDaoImpl">
	<constructor-arg value=" message "></constructor-arg>
</bean>

<!-- 2.setter 方法注入 -->
<bean id="id" class="com.id "> 
    <property name="id" value="123"></property> 
</bean> 

<!-- 3. 静态工厂注入 -->
 <bean name="springAction" class=" SpringAction" >
 	<!--使用静态工厂的方法注入对象,对应下面的配置文件-->
 	<property name="staticFactoryDao" ref="staticFactoryDao"></property>
 </bean>
 <!--此处获取对象的方式是从工厂类中获取静态方法-->
<bean name="staticFactoryDao" class="DaoFactory"
	factory-method="getStaticFactoryDaoImpl">
</bean>

<!-- 4. 实例工厂注入 -->
 <bean name="springAction" class="SpringAction">
 	<!--使用实例工厂的方法注入对象,对应下面的配置文件-->
 	<property name="factoryDao" ref="factoryDao"></property>
 </bean>
 <!--此处获取对象的方式是从工厂类中获取实例方法-->
<bean name="daoFactory" class="com.DaoFactory"></bean>
<bean name="factoryDao" factory-bean="daoFactory"
	factory-method="getFactoryDaoImpl">
</bean> 
```

##### 4.2  Spring装配

**手动装配**：基于XML装配、构造方法、setter方法

**5种不同方式的自动装配**

```
1. no：默认的方式是不进行自动装配，通过显式设置 ref 属性来进行装配。
2. byName：通过参数名 自动装配，Spring 容器在配置文件中发现 bean 的 autowire 属性被设置成 byname，之后容器试图匹配、装配和该 bean 的属性具有相同名字的 bean。
3. byType：通过参数类型自动装配，Spring 容器在配置文件中发现 bean 的 autowire 属性被设置成 byType，之后容器试图匹配、装配和该 bean 的属性具有相同类型的 bean。如果有多
个 bean 符合条件，则抛出错误。
4. constructor：这个方式类似于 byType， 但是要提供给构造器参数，如果没有确定的带参数
的构造器参数类型，将会抛出异常。
5. autodetect：首先尝试使用 constructor 来自动装配，如果无法工作，则使用 byType 方式。
```

##### 4.3 Spring APO原理

所谓"切面"，简单说就是那些与**业务无关**，却为业务模块所**共同调用**的逻辑或责任封装起来，便于减少系统的重复代码，降低模块之间的**耦合度**，并有利于未来的**可操作性和可维护性**。

主要的应用场景：

````
1. Authentication 权限
2. Caching 缓存
3. Context passing 内容传递
4. Error handling 错误处理
5. Lazy loading 懒加载
6. Debugging 调试
7. logging, tracing, profiling and monitoring 记录跟踪 优化 校准
8. Performance optimization 性能优化
9. Persistence 持久化
10. Resource pooling 资源池
11. Synchronization 同步
12. Transactions 事务
````

##### 4.4 Spring MVC

 ![img](https://s2.ax1x.com/2019/03/20/AlTw80.png)

![img](https://s2.ax1x.com/2019/03/20/Al73J1.png)

##### 4.5 Spring Boot

特点：

```
1. 创建独立的 Spring 应用程序
2. 嵌入的 Tomcat，无需部署 WAR 文件
3. 简化 Maven 配置
4. 自动配置 Spring
5. 提供生产就绪型功能，如指标，健康检查和外部配置
6. 绝对没有代码生成和对 XML 没有要求配置
```

##### 4.6 JPA 原理（java 事务）

- 本地事务：紧密依赖于底层资源管理器（例如数据库连接 )，事务处理局限在当前事务资源内。此种事务处理方式不存在对应用服务器的依赖，因而部署**灵活却无法支持多数据源的分布式事务**。
- 分布式事务：Java 事务编程接口（JTA：Java Transaction API）和 Java 事务服务 (JTS；Java TransactionService) 为 J2EE 平台提供了分布式事务服务。分布式事务（Distributed Transaction）包括**事务管理器（Transaction Manager）**和**一个或多个支持 XA 协议的资源管理器 ( ResourceManager )**。我们可以**将资源管理器看做任意类型的持久化数据存储；事务管理器承担着所有事务参与单元的协调与控制。**

##### 4.7 Mybaits缓存

- 一级缓存（SQLSession级别，默认开启，需要配置）：发生commit操作时，缓存会被清空

key：MapperID+offset+limit+Sql+所有的入参
value：用户信息

- 二级缓存（mapper级别）：以命名空间为单位创建缓存数据结构，结构是map，通过CacheExecutor实现。

key：MapperID+offset+limit+Sql+所有的入参

使用时需要使用下面的方法进行配置：

```
1. Mybatis 全局配置中启用二级缓存配置
2. 在对应的 Mapper.xml 中配置 cache 节点
3. 在对应的 select 查询节点中添加 useCache=true
```



![img](https://s2.ax1x.com/2019/03/21/A1g7GD.png)

##### 4.8 Tomcat 架构（先挖坑）

[Tomcat 架构设计](https://www.ibm.com/developerworks/cn/java/j-lo-tomcat1/)

![图 1.Tomcat 的总体结构](https://www.ibm.com/developerworks/cn/java/j-lo-tomcat1/image001.gif)

#### 5. Hibernate

是一个开放源代码的对象关系映射（ORM）框架，不仅可以运行在J2EE容器内，还可以运行在容器外，实现了java对象和关系数据库记录的映射关系，简化了开发人员访问数据库的流程，提高开发效率，即可以用来替代JDBC。

五个核心接口：

| 接口名         | 描述                                                         |
| -------------- | ------------------------------------------------------------ |
| Session        | 一个轻量级的非线程安全的对象，主要负责被持久化对象与数据库的操作。 |
| SessionFactory | 负责初始化Hibernate。可以看成是数据源的代理，可以用来创建Session对象。其为线程安全的，一般在初始化时，会创建一次，并且其应该使用单例模式实现。 |
| Transcaction   | 负责事务相关操作，通过Session的beginTransaction创建，主要方法有commit和rollback |
| Query          | 负责执行各种数据库查询，可以使用HQL或SQL语言，通过session的createQuery创建Query，此外还提供QBC(Query By Criteria)查询方式 |
| Configuration  | 用于读取Hibernate配置文件，并生成SessionFactory对象。配置文件类型：hibernate.cfg.xml（优先级更高，能够覆盖hibernate..properties） 或hibernate.properties 和 映射文件。 |

**使用过程： **

1. 通过Configuration类读取配置文件并创建SessionFactory对象

```java
SessionFactory sessionFactory = new Configuration().configure().buildSessionFactory();
```

2. 通过SessionFactory生成一个Session对象

```java
Session session = sessionFactory.openSession();
```

3. 通过Session对象的beginTransaction创建一个任务，进行持久化操作

```java
Transcation t = session.beginTransaction();
//接着可以带哦用session对象的get、load、save、update、delete、saveOrUpdate方法操作数据,也可以通过session生成 一个Query对象进行查询操作
t.commit(); //or t.rollback();
```

4. 关闭Session和SessionFactory

**优点：**

1. 提高开发效率
2. 使得开发完全采用面向对象的方法，不用关心数据库的关系模型
3. 良好的可移植性
4. 支持透明持久化，没有侵入性（不需要继承自某个类）

**缺点**

只适用于单一对象修改，批量修改不适合。

**如何提高性能**

1. 延迟加载，只有数据被使用时才会取数据库中读取数据
2. 缓存技术，提供一级缓存和二级缓存
3. 优化查询语句

**如何实现类与类之间的关系? **

通过配置文件的many-to-one, one-to-many, many-to-many配置

**两级缓存**

一级由Session管理（必须有，非线程安全，不能被其他线程共享，实际中对效率的提升不明显）二级为SessionFactory管理（可有可无，全局缓存，多线程共享）。

二级缓存是独立于Hibernate的软件部件，属于第三方产品（常见的产品有EhCache（Hibernate 3 默认使用）、OSCache、JbossCache），CacheProvider接口提供插件与Hibernate之间的适配，缓存可以是内存也可以是硬盘。

二级缓存适用情况：

1. 数据量小
2. 数据修改少
3. 不会被大量共享的数据
4. 不是很重要的数据

**session的update和saveOrUpdate**

Hibernate对象具有三个状态：瞬时态（Transient）、持久态（Persistent）和托管状态（Detached），出于持久态的对象被称为PO（Persistence Object），其他两种状态的称为VO（value Object）

saveOrUpdate方法首先检查对象状态，如果是持久态则直接返回，否则判断数据是否在数据库中在调用update方法，不在调用save方法，从这可以看出如果能够知道对象的状态就不要使用saveOrUpdate方法，效率低

**session的get和load方法**

都是从数据库中加载数据创建持久化类

不同：

1. 如果数据不存在，load抛出异常，后者返回空
2. get先查一级缓存再查二级最后查数据库，load先查一级缓存，如果查不到就创建代理对象，实际使用时再接着查，即实现延迟加载。
3. get永远只返回实体类，load可以返回实体类的代理类实例
4. get和find都是直接一路查找到底，而load先查缓存，查不到判断lazy，非lazy则也是查到底，如果是lazy则建立代理对象，initialized属性为false，target属性为null，在使用数据时查找记录，找到将initiallize修改为true值填入targe，否则抛出异常。

**Hibernate 主键生成策略**

1. assigned，由外部负责，在save之前设置，缺点是，新增是需要判断是否重复，并且容易产生主键冲突。
2. Hilo，使用高/低位算法，可以在很少的连接次数内产生多条记录，缺点是需要数据库表保存主键的生成机制，并且只能保证一个数据库主键唯一性，多个数据库不保证。
3. SeqHilo，同样使用高低位算法，不同的是历史状态保存在Sequence中
4. Increment（Hibernate维护），维持最大作为主键，需要数据库支持Sequence（例如Oracle），缺点是，新增时需要先查询，主键只能是int或long，会产生编发问题。
5. Identity，使用数据库的方式处理，存在移植性问题
6. Sequence，要求数据库提供这种方式
7.  Native，根据底层数据库自行选择Identity、Hilo。Sequence一种作为主键
8. UUID，基于128位的唯一值生成16进制数值作为主键，缺点是浪费空间
9. Foreign GUID，用于在一对一的映射关系中采用特殊的算法生成主键
10. select采用触发器方式生成主键，现在用得比较少。

**如何实现分页：**

1. 使用Hibernate的方式

```java
Query q = session.createQuery("from Student as a");
q.setFristResult(开始值);
q.setMaxResult(页容量);
List l = q.list();
```

2. 使用Sql语句来实现分页（不同数据库不同）

```sql
select * from tableName limit begin, count;
```

#### 6. SSH

是Structs、Spring、Hibernate首字母的组合

优点：

1. Struts实现了MVC模式，提供良好的分层，使得开发人员把开发重点放在业务逻辑上。此外提供标签库，提高开发效率。
2. Spring可以用来管理对象，通过IOC容器把对象之间的依赖交给Spring降低组件组件之间的耦合性，让开发人员专注于业务逻辑开发。Spring基于Ioc和AOP思想，使其具有很好的模块性，可以按需使用，此外提供有用功能如事务管理。
3. Hibernate作为轻量级的持久性框架，实现高效的对象关系映射，使得开发可以采用完全面向对象的思想，而不需要考虑关系数据库的关系模型，此外还具有很好的可移植性。

#### 7. 日志

##### 7.1 Simple Loging Facade for java(slf4j)

仅仅是一个为 Java 程序提供日志输出的统一接口，类似于JDBC，需要搭配具体的日志实现方案，比如apache的org.apache.log4j.Logger, jdk自带的java.util.logging.Logger等。

##### 7.2 Log4j

**功能：**

- 日志输送目的可控：控制台、文件、GUI 组件，甚至是套接口服务器、NT 的事件记录器、UNIX Syslog 守护进程
- 每条日志输出格式可控
- 通过级别控制可以精细生成日志生成过程

**组成部分**：

- Logger：控制要启用或禁用哪些日志记录语句，并对日志信息进行级别限制
- Appenders : 指定了日志将打印到控制台还是文件中
- Layout : 控制日志信息的显示格式

**log信息级别**：DEBUG、INFO、WARN、ERROR 和 FATAL

##### 7.3 LogBack

**组成模块**

- **logback-core** 是其它模块的基础设施，其它模块基于它构建，显然，logback-core 提供了一些关键的通用机制。
- **logback-classic** 的地位和作用等同于 Log4J，它也被认为是 Log4J 的一个改进版，并且它实现了简单日志门面 SLF4J；
- **logback-access** 主要作为一个与 Servlet 容器交互的模块，比如说 tomcat 或者 jetty，提供一些与HTTP 访问相关的功能。

**优点：**

```
 同样的代码路径，Logback 执行更快
 更充分的测试
 原生实现了 SLF4J API（Log4J 还需要有一个中间转换层）
 内容更丰富的文档
 支持 XML 或者 Groovy 方式配置
 配置文件自动热加载
 从 IO 错误中优雅恢复
 自动删除日志归档
 自动压缩日志成为归档文件
 支持 Prudent 模式，使多个 JVM 进程能记录同一个日志文件
 支持配置文件中加入条件判断来适应不同的环境
 更强大的过滤器
 支持 SiftingAppender（可筛选 Appender）
 异常栈信息带有包信息
```

##### 7.4 ELK

ELK 是软件集合 Elasticsearch、Logstash、Kibana 的简称，由这三个软件及其相关的组件可以打
造大规模日志实时处理系统。

- Elasticsearch 是一个基于 Lucene 的、支持全文索引的分布式存储和索引引擎，主要负责将
  日志索引并存储起来，方便业务方检索查询。
- Logstash 是一个日志收集、过滤、转发的中间件，主要负责将各条业务线的各类日志统一收
  集、过滤后，转发给 Elasticsearch 进行下一步处理。
- Kibana 是一个可视化工具，主要负责查询 Elasticsearch 的数据并以可视化的方式展现给业
  务方，比如各类饼图、直方图、区域图等。

## 第六章 数据库原理

### 6.1 Sql语言功能

语句类型：

数据操纵语句（DML）：插入数据、修改数据、删除数据

数据定义语句（DDL）：对数据库用户、基本表、视图、索引、进行定义与撤销

数据控制语句（DCL）：对数据库进行统一的控制管理，保证数据在多用于共享的情况下能够安全

基本sql语句的使用方式：

|          | 关键字 | 语法格式                                                     |
| -------- | ------ | ------------------------------------------------------------ |
| 数据查询 | select | Select * from table where 条件                               |
| 数据操纵 | insert | insert into table(字段1,字段2...) values(值1,值2,...)        |
| 数据操纵 | update | update table set 字段=值 where 条件表达式                    |
| 数据操纵 | delete | delete from table where 条件表达式                           |
| 数据定义 | create | create table 表名 (字段1,字段2,...)                          |
| 数据定义 | drop   | drop table 表名                                              |
| 数据控制 | grant  | grant <系统权限> \| <角色>[, <系统权限>] \| ... to <用户> \| <角色> \| public [,< 用户> \| <角色>]... [with admin option] |
| 数据控制 | revoke | grant <系统权限> \| <角色>[, <系统权限>] \| ... to <用户> \| <角色> \| public [,< 用户> \| <角色>]... |

delete 与truncate之间的区别：

1. truncate是一个数据定义语言，他会被隐式地提交，一旦执行后就不能回滚，delete每次删除一条记录，同时将删除操作记录在日志中方便后面进行恢复。
2. 用delete删除后，被删除数据的空间还在，可以恢复。truncate删除数据后数据空间立即释放，被删除的数据不能够进行恢复。
3. truncate的速度比delete的速度快

### 6.2 内连接和外连接区别

内连接：也称为自然连接，只有两个表相匹配的行才能在结果集中出现。

外连接：分为左外连接（left outer join包括左表所有数据）、右外连接（right outer join包括右表所有数据）和全外连接（full outer join包括两个表的所有数据）

### 6.3 事务

事务：由一些不可分割操作组成的操作集合，这些操作要么全做，要么全不做

事务特性：

1. 原子性：事务是一个不可以分割的整体，要么全执行、要么全不执行。
2. 一致性：一个事务执行前后，数据库数据必须保持一致性状态（例如总和相等）
3. 隔离性：也称为独立性，多个事务并发执行时，不同事务的内部操作互相隔离，互不影响。
4. 持久性：事务完成后DBMS保证对数据库中数据的修改是永久的

可以通过commit和rollback两者来控制事务的终止，commit后数据持久化，rollback撤销所有操作重新回到开始状态，都能保持一致性。

### 6.4 什么是存储过程，它与函数有什么区别和联系

存储过程：在大型数据库中，为了提高效率，将为了完成特定功能的SQL语句集进行编译优化，存储在数据库服务器中，用户通过制定的存储过程名字来调用执行。

```sql
# 创建：
create procedure sp_name @ [参数名] [类型] 
	as
	begin
	...
	end
# 调用
exec sp_name [参数]
# 删除
drop procedure sp_name
```

存储过程和函数的不同：

1. 存储过程一般作为单独的部分来执行，而函数可以作为查询语句的一部分来进行调用
2. 一般而言，存储过程实现的功能复杂，函数实现的功能具有针对性
3. 函数需要用括号包住输入的参数，且只返回一个值或者表对象，而存储过程可以返回多个参数
4. 函数可以嵌入在sql中使用，可以在select中调用，存储过程不行
5. 函数不能操作实体表，只能操作内建表
6. 存储过程在创建时即在服务器上进行了编译，其执行速度较快

### 6.5 各种范式的区别

1. 1NF：指数据库表中的每一列都是不可分割的基本数据项，降低数据的冗余性
2. 2NF：是一范式，且数据库中每个实例或行必须可以被唯一地区分，解决非主属性对主属性的部分依赖（即要求非主属性必须要完全依赖于主属性，减少数据的冗余和插入异常）
3. 3NF：是二范式，且每个非主属性都不传递依赖于R的候选键，则称R是第三范式。
4. BCNF：构建在三范式之上，如果关系模式R是第一范式，且每个属性都不传递依赖于R的候选键。消除删除异常、插入异常、和更新异常。
5. 4NF：设R是一个关系模式，D是R的多值依赖集合，如果D中存在平凡多值依赖$X \rightarrow Y​$时，X必是R的超键。符合4NF必然符合3NF及其以前。

### 6.6 触发器

触发器是一中特殊类型的存储过程，它由事件触发，而不是程序调用或者手工启动。

与存储过程的不同：

| 触发器                          | 存储过程                            |
| ------------------------------- | ----------------------------------- |
| 当某类数据操作DML语句时隐性发生 | 它从一个应用或过程中显示地调用      |
| 触发器内禁止commit和rollback    | 能使用所有PL\SQL中都能使用的SQL语句 |
| 不接受参数                      | 接受参数                            |

分类：DML触发器（包括After 和Instead of两种触发器，前者在记录改变之后调用做准备工作，后者在记录修改之前调用做收尾工作）DLL触发器（在响应数据定义语言事件时执行）

作用：

1. 增加安全性
2. 利用触发器记录所进行的修改和相关信息，实现审计功能
3. 实现复杂的完整性约束和对数据库中的特定事件进行监控和响应
4. 实现复杂的非标准数据库相关完整性约束，同步实时复制表中的数据
5. 触发器是自动的，在特定的条件下自动激活能够实现某些自动功能

语句级出发器和行级触发器：前者在语句执行前后，后者在所影响的每一行触发一次

### 6.7 游标

游标提供一种从表中检索出数据进行操作的灵活手段，实际上是一种能从包含多条数据记录的结果集中每次提取一条记录的机制。游标总是与一条SQL选择语句相关联，因为游标是由结果集和结果集中指向特定记录的游标位置组成。

游标允许对select返回的结果中每一行进行相同或者不同的操作。

游标优点：

1. 在使用游标的表中，对行提供删除和更新功能
2. 游标将面向集合的数据库管理和面向行的程序设计连接起来

### 6. 8  日志

功能：记录所有对数据库数据的修改，主要是保护数据库以防故障发生以及恢复数据时使用

日志特点：

1. 每个数据库至少包含两个日志文件组，每个日志文件组至少包含两个日志文件成员
2. 日志记录以循环方式进行写操作 
3. 每一个日志文件成员对应一个物理文件

**如果日志满了：**则只能进行查询操作，不能进行修改以及备份操作（因为写需要写日志）

### 6. 9 Union和union all区别

union：会对结果集进行去重处理，速度慢

union all：不会进行去重处理，速度快

### 6. 10 视图

视图是由数据库中的基本表中选取出来的数据组成的逻辑接口，与基本表一致但是是虚表，数据库中只存放定义。

视图作用：

1. 简化数据查询语句
2. 使用户从多个角度看待同一份数据
3. 提高数据的安全性
4. 提供一定程度的逻辑独立性

但是引入视图不能加快查询的效率。

## 第七章 设计模式

设计模式：是一套被反复使用，为多数人所知道，经过分类编目的、代码设计经验的总结。

使用设计模式的目的：为了代码的重用，避免程序的大量修改，同时使代码易于理解

23种常用的设计模式分类如下：

![img](http://cmsblogs.com/wp-content/uploads/2014/02/thumb.jpg)

### 7.1 单例模式

作用：保证在程序的整个生命周期中，任何一个时刻，单例类的实例都只存在一个（当然也可以不存在），并且是自行实例化。

全局变量与单例模式的区别：

1. 全局变量不能保证应用程序只有一个实例
2. 编码规范明确指出，应少用全局变量，会造成代码难读
3. 全局变量不能继承

单例模式中构造函数必须私有，其次必须提供全局的访问点

线程安全的单例模式写法：

```java
//缺点，初始化的时间较长
class Test {
	private static Test uniqueInstance = new Test();
	public static Test getInstance() {
        return uniqueInstance;
	}
}
```

写法2：

```java
public class Singleton {  
      private static Singleton instance;  
      private Singleton (){
          
      }   
      public static synchronized Singleton getInstance(){    //对获取实例的方法进行同步，锁太重
        if (instance == null)     
          instance = new Singleton(); 
        return instance;
      }
  } 
```

写法3（效果好）：

```java
// 双重同步锁
public class Singleton {  
     private static Singleton instance;  
     private Singleton (){
     }   
     public static Singleton getInstance(){    //对获取实例的方法进行同步
       if (instance == null){
           synchronized(Singleton.class){
               if (instance == null)
                   instance = new Singleton(); 
           }
       }
       return instance;
     }
 }
```

### 7.2 工厂模式

工厂模式专门负责实例化大量公共接口的类。工厂模式可以动态决定将哪一类实例化，而不必事先知道每次要实例化哪一个类。

工厂模式的形态：

1. 简单工厂模式：根据提供的参数从几种中返回一种的实例，返回的类都有一个公共的类和公共的方法。
2. 工厂方法：是类的创建模式，用意是定义一个创建产品对象的工厂的接口，而将实际创建工作推迟到工厂接口的子类中。
3. 抽象工厂模式：指有多个抽象角色使用时的一种工厂模式，抽象工厂模式可以向客户端提供一个接口，使客户端在不必指定产品的具体情况下，能够创建多个产品族中的产品对象。

### 7.3 设配器模式

也称为变压器模式，他是把一个类的接口转换成客户端所期望的另一种接口。使原本英文接口不匹配而不能在一起工作的类在一起工作。

适配方式：类适配器模式（采用多继承实现，引起高耦合，不推荐使用），对象适配器（采用对象组合的方式，耦合度低，应用范围更加广泛）

### 7.4 观察者模式

提供了避免组件之间紧密耦合的另一种方法，它将观察者和被观察者对象分离。该模式中通过添加一个方法（该方法允许另一个对象，即观察者注册自己）使本身变得可观察。当可观察的对象更改时，它会将消息发送到已注册的观察者。

## 第八章 数据结构与算法

就写大概，具体敲去呗。。

### 8.1 链表

链表中的一个诀窍：如果是要找链表中节点顺序有关（倒数、中间、第K个，检测有环）的一般可以采用双指针法，核心是找到两个指针的走法。双指针很好用！

**检测有环：**

1. 判断是否存在环（使用快慢指针法很容易就能判断是否存在环）
2. 找到环的入口节点（一个从头走，一个从相遇点走，再次相遇就是入口，有严密的数学推导，具体见书，此处的结果和剑指Offer上有一点区别，但是结果是对的）

**不知道头结点的情况下删除节点：**

1. 如果为尾无法删除

2. 非尾就进行值拷贝，删除最后一个节点

**判断两链相交**

如果两链相交那么尾部一定相同(通过此可以判断两个链是否相交)，找到长的链先走多于的(把过程倒过来想容易)，再两个一起走就能得到结果

###  8.2 栈和队列

栈：先进后出

队列：先进先出

**栈的实现方式**

1. 数组：维护当前的栈顶位置
2. 链表：每次操作头结点即可（所以更加推荐使用链表实现）

**O(1)实现具有min()的栈**

使用额外的栈来记录当前最小，每次添加元素时压入最小即可

**队列的实现方式**

1. 数组法：还需要额外的两个头尾下标指向当前头尾，每次取头插尾，注意需要实现循环下标
2. 链表法：记录头和尾，取头插尾

**使用两个栈模拟队列**

两个桶倒水法

### 8.3 排序

有一篇讲得不错的博客[十大经典排序算法（动图演示）](https://www.cnblogs.com/onepixel/p/7674659.html)

分类：

![img](https://images2018.cnblogs.com/blog/849589/201804/849589-20180402132530342-980121409.png)

各个算法的时间复杂度（其中k为桶的数量或者是分组的数量）：

![img](https://images2018.cnblogs.com/blog/849589/201804/849589-20180402133438219-1946132192.png)

分析：

1. 稳定（排序的过程中，相等数经过排序后维持原来的顺序）的排序算法有：插入排序、冒泡排序、计数排序、桶排序、基数排序，不稳定排序算法 有：希尔、快速、简单选择（交换的过程导致）、堆排序
2. 时间复杂度为O(n2)的算法有：直接插入排序、冒泡排序、选择排序，时间复杂度为O(nlogn)的算法有：堆、快、归并
3. 空间复杂度为O(1)的为：简单选择、直接插入、冒泡、希尔、堆。为O(logn)的为快速排序，为O(n)的为归并排序
4. 

具体理解：

1. 选择排序：每次选出最小或者最大的来，
2. 冒泡排序：两两交互，将当前最小或最大往前冒
3. 快速排序：每次选择一个数，小的放在一边，大的放在一边，变成两个子问题一直递归
4. 堆排序：利用大顶堆或者小顶堆生成法逐渐构建完整的堆，并且堆的规模在不断的减小
5. 直接插入排序：每次选择最小或者最小的元素插入到有序数组中
6. 希尔排序：是一种分组插入算法，开始分为d组（按到开始位置的距离分组），进行组内排序，减小d直到d=1时排序完成
7. 归并排序：将有序的子数组进行归并（每次选出有序中的最小或最大）处理，实现时从上按下递归处理。
8. 计数排序（位图排序）：将数转换为键存储在额外的空间中，要求输入的数据必须在确定范围的整数，四步法：找出最大最小，统计每个值出现的次数，计数累加，反向填充数组（注意是用下标填）
9. 桶排序：计数排序的升级版，利用了函数的映射关系。原理：假设输入的数据服从均匀分布，将数据分配到有限数量的桶里，在桶里进行排序。桶之间是具有顺序关系。
10. 基数排序：是先按照低位排序，然后收集，接着按照高位排序，然后收集，以此类推，直到最高位。





 排序算法的选择：

局部有序：插入排序或者冒泡排序更快，快排会变慢

排序数量小且不要求稳定时，直接选择排序效率最好，要求稳定性，冒泡最好

位图排序是最快的方法

### 8.4 位运算

1. 如何判断一个数是否为2的n次方：

所有位中只有一个1，以正数为例，该数减1后，原来是1位后的树全为1，唯一为1的位被搞掉，所以可以使用m&(m-1) == 0进行判断

2. 判断一个数位数中1的个数

利用上面的思想，使用m&(m-1)操作每次砍掉一个最低位的1(高位没有动)，直到砍完所有的一。

### 8.5 数组

1. 寻找数组中的最小值和最大值：

   a）问题分解，一个最大 一个最小两个问题，比较2N

   b）取单元素法，记录最大和最小，每次比较，比较2N

   c）取双元素法，记录最大和最小，每次比较两个相邻的元素，较大者和最大比，较小者和最小比，比较1.5N

   d）数组元素移位法：将数组中相邻的两个数分在一组，每次比较两个相邻的数，大的交换之左边，小的交换到右边，对大者组扫描得到最大，对小者组扫描得到最小，比较1.5N~2N

   e）分治法：分为对半分找法，比较1.5N

2. 找出数组中第二大的数

   a）排序后再取

   b）维持最大和第二大，大于最大时当前为最大，之前最大为第二大，小于最大大于第二大，更新第二大为当前

3. 最大子数组之和

   a）蛮力法：找出所有子数组然后再求最大

   b）动态规划法：记录当前最大，考虑当前元素在其中或者不在其中，不在其中那么就是之前的最大，在其中那么从当前往前遍历直到和变小了，再从这两个策略中挑出最大的作为最大

4. 找出数组中重复次数最多的数

   a）位图法：定义长度为MAX的数组，每次往数组里计数

   b）使用Map

5. 如何求数组中两两相加等于20的组合个数

   a）暴力法，组合所有的可能判断是否符合条件

   b）排序法，先排序，之后进行两端走法，如果两数之和小了左边走，大了右边走，等于两边都走。

6. 数组循环右移k位

   a）使用额外的空间记录K位数值

   b）如果不使用额外的空间可以直接使用每次移一位法（循环一位）

7. 找出数组中第k个最小的数

   a）排序法

   b）剪枝法，利用快速排序的划分函数（左边的数比其小，右边的数比其大），再结合二分查找，找到第k为位置的元素就行

   c）额外维护最小k个数的数组，这种方式不会打乱原先数组的排序

   d）使用堆排序，使用容量为k个大顶堆

8. 找出数组中只出现一次的数字，其他数字都出现了两次

   a）使用map记录每个数字出现的次数，再用一次遍历找出其中只出现一次的数字

   b）利用Set，遍历数组如果set中没有，则添加，如果存在则删除，则set中最后只有一个唯一的数字即为所求

   c）排序，后再遍历法

   d）异或法，利用相同数字的异或为零，将所有数字进行异或即可求解（O(1)时间复杂度）

9. 找出数组中只出现一次的数字，其他数字都出现了m次

   思想：数字出现m次，那么数字中为1的位的次数统计结果是m次，即如果某个位的次数统计不能被m整除那么当前位是只出现一次数字的不为1的位**使用一个定长数组（32）记录每个位置出现1的次数，之后如果能被m整除就是唯一只出现一次的数**

10. 找出数组中唯一的重复数字，1~N-1的数存放在长度为N的数组中，其中有一个数字重复，求出重复的数字

    a）利用和差原理，计算1~N-1所有数字的求和，再用数组的和减去其得到重复的数**利用的是数组的只出现一次的元素是可知的**

    b）位图法：在排序的情况下，数字的值和下标相差1，利用这个性质进行位图的排序（将元素交换到合适的位置上），如果在对某个位置赋值时其值等于下标加1，那么该数值就是重复项。此外这种算法也可以使用额外的空间来避免进行交换，提高运行效率

    c）异或法：数组的异或，1~N-1的异或，再将两者的结果异或

11. 取值为1~N-1的数存放在长度为N的数组中，至少有一个数字重复，求出任意的一个重复数字

    a）位图法

    b）数组排序法

    c）取反法：原理，如果对一个数进行取反操作后那么结果就变成原来数的负数，还利用数值可以当做下标使用，如果数是重复的那么存在两个取反结果依然为正，只出现一次那么处理完成后是负值。那么利用额外的数组存储当前取反情况，使用数组中的值作为下标对辅助数组进行取反操作（如果为空，先填再取反），处理完成后再遍历辅助数组第一个不为负的下标加1即为第一次出现重复的值。

    c）诡异算法：思想：判断单链表是否存在环。原理：将数组中的结果看做是指向的下一个节点，由此当前的数组可以看成是一个链表。那么次问题就可以看出是已知一个链表存在环，找出其一个环入口节点。

12. 如何用一个递归算法求一个整数数组中的最大元素

    a）递归法：1和n-1切分法，返回两者中的最大

13. 如何求解数对之差的最大值（数组中的数和其右子数组中的最大差，拓展后可以求解最大子数组的和）

    a）右数组最小数辅助数组法（有点动态规划的感觉）：利用一个数组存放右子数组的最小值，从数组的末尾进行填写，之后再正向遍历原始数组，求解两个数组对应元素的最大差值

    b）二分法：分为左右两个子数组，则原始问题分为：减数和被减数都在左边，减数和被减数都在右边，被减数（左边最大）在左边减数（右边最小）在右边，利用这个策略进行递归

    c）动态规划：利用辅助数组（存储以i为界的左边数组的子数组的最大差）和max当前的最大值，递推公式为：要么是之前的最大差，要么是之前最大数减当前值，两者中取最大值。

14. 如何求绝对值最小的数（一个排序的整数数组，求解其中绝对值最小的数）

    边界条件判断，如果当前的数组是全正或者全负，则不用遍历就能得到结果

    a）变形的二分查找：利用二分查找的思想，只是左右子数组的选择条件不同，如果当前的数字两端与其存在异号问题，那么就在异号的两个元素中找出一个最小即为所求，否则（同号）往绝对值减少的一侧移动。

    b）朴素想法：通过遍历找到异号位置，从中选出绝对值最小的即可。

15. 如何求解数组中两个元素（坐标）的最小距离

    a）两两比法（暴力ing）

    b）排序法，排序后再求解相连两两的最小距离

16. 如何求解数组中两个元素的最小距离，重复元素的最小距离

    a）确定重复元素值后再遍历计算两者之间的距离

    b）使用空间换时间法：遍历数组记录元素（值为key，value为位置，使用Map存储）出现的位置，如果遇到相同的元素，那就计算当前位置和之前之前位置之差并返回

17. 数组中含有重复元素，给出两个数n1和n2，求这两个数在数组中的最小距离

    a）暴力：定位第一个n1，之后寻找n2，计算两者之间的距离更新的当前最小，n1往前走在n2之前遇到n1那么更新当前的最小距离，之后从当前位置重新开始这个过程。完成后还需要交换n1和n2的值，再进行一次。

    b）暴力优化版：使用l1和l2记录n1和n2两个位置初始为-1， 从l1当前值遍历找到n1并更新l1，如果n2的位置已经确定（n2已经有值）计算n1和n2的距离更新最小距离，从l2的当前值遍历找到n2并更新l2，计算n1和n2的距离更新最小距离，重复上面的过程l1或者l2达到数组长度**使用两个元素记录n1和n2的位置，从起始位置进行遍历，如果遇到n1则更新其位置如果n2之前已经找到，则计算差值，遇到n2 做同样的处理**

18. 指定数字在数组中第一次出现的位置，并且数组中相邻元素的差值都为1

    a）暴力法：遍历

    b）跳跃搜索法：由于相邻元素的差值为1，当前数与查找数之间差值的绝对值就是最短的走法，所以每次搜索可以利用这个特性每次走差值步绝对值步，一定能找到结果或者走到最后。

19. O（1）空间复杂度内有序子数组（0到mid-1， mid到num）的合并

    a）不看题目的条件直接进行空间复杂度为O（1）的排序即可，如插入排序

    b）利用有序的特性：如果第mid-1和第mid元素之间已经符合题目要求的顺序（升序时，第mid-1比第mid个元素小），那么结果已经正确，可以直接退出，否则比较第一个子数组中当前数和后子数组中第一个数，两者顺序不对则交换两个数，并将后面的数移动到合适的位置。移动算法可以同对第二个数组进行插入排序实现，每次比时符合条件（如升序时，前面数组中的值大于就后面数组中的值）就交换。

20. 如何计算两个有序整数数组的交集

    a）两个数组长度相当的情况

    ​	1）二路归并算法

    ​	2）顺序遍历法

    ​	3）散列法，将一个存在Map中另外一个进行查

    b）数组不等长

    ​	1）依次遍历长度短的数组，将遍历得到的数组元素在长数组中进行二分查找

    ​	2）采用与方法一类似，但是每次查找是在前一次查找的基础上进行，大大缩小了查找表的长度

    ​	3）采用与方法二类似，但是从头部和尾部开始遍历短数组（头尾交叉），这样可以进一步缩小查找表的长度

21. 如何判断一个数组中的数值是否连续相邻（一个数组序列，值介于0~65535,除去0为不会出现相同的数字，从中找出n个数字判断这n个数字是否相邻，其中0可以代替任意的数值）

    a）暴力法：首先对取出的数字进行排序，计算非零部分的差值，看能够使用0补齐

    b）优化算法：首先找到五个数字中的最大和最小值（不算0），只要最大值减去最小值的差大于n-1，则这是一个连续数组。

22. 如何求解数组中的反序对的个数（就在保持数组相对顺序的情况下，前面的数大于后面的数组成的数对）

    a）暴力法：排列组合，然后再进行判断是否符合

    b）借鉴归并排序的方法：先将数组进行拆分直到拆分到最小粒度后继续进行归并，在合并的过程中记录反序对的个数。两个数组归并时从尾部进行，如果第一个数组的元素大于第二个数组中的元素，那么必然也大于之前的元素，所以可以得到多个反序对，反之在第二个数组中移动直到结束或者是遇到小于的元素，并且在这个过程中需要将数据填入保存（归并后的结果要保留），如此重复直到所有的元素都已经都处理完成。

23. 如何求解最小三元组距离

    三个升序的整数数组，在三个数组中各找一个元素使得三元组的距离最小，三元组距离的定义为：三个数中的最大差值。
    a）暴力法：三重循环呗。

    b）二分搜索：为使得差值最小，即三个数应当尽可能地接近，所以遍历一个数组，然后从其他两个数组中找到最接近的计算三元组距离，时间复杂度为O(nlogn)

    c）最小距离法：每个数组中各取一个元素后可以得到一个组合，对这三个数进行排序，每次让最小的往前走，计算一次距离并更新记录最小的距离，当某个数组走完后就可以得到结果。**最小值先走（笨鸟先飞）**

###  8.6 字符串

1. 字符串的反转（把句子中每个单词进行反转）

   a）两次转换法：先将整个句子（全部字符一视同仁）进行反转，其次再将每个单词内部（以空格为界）进行反转

2. 判断两个字符串是否由相同的字符组成，（字母类型以及字母个数都是一样，只是排列不一样）

   a）数组计数法（map也是同样的道理）：根据字母表定义计数数组，第一个数组进行计数（加），第二个数组进行验证（减），如果是一样的那么计数数组中应该全部为0。

   b）排序法，拍完序后应该是同样的两个数组

3. 删除字符串中重复的字符

   a）暴力法，首先判断是否重复，其次进行删除处理。

   b）Map（Set）去重法，首先判断字符是否已经是在集合中，如果不在则加入，如果在则删除当前字符

   c） 正则表达式法，(? s)(.) (?=. * \\\1)

4. 统计一行字符中有多少单词

   以空格进行分割即可

5. 按要求打印数组的排列情况（针对一组数字，打印所有不同的排列，并且排列有一定的限制，如某位不能为某值，相邻不能为指定的数字）

   a）带条件的排列组合问题，借鉴剑指offer上面的排列组合问题，再加上限制条件。排列组合采用递归的思想进行处理，即确定最高位后如何排列剩下的子数组。

   b）构造无向连通图，从节点出发进行深度优先遍历，每次遍历完所有的节点，如果遍历的路径符合条件则将其加入结果集中。

6. 不重复字符串的组合问题

   a）递归法：即每个字符取或者不取的所有情况的组合，将原问题拆分为子问题递归地解决。但是当n特别大时容易造成堆栈溢出。

   b）通过等长的标识数组来标识每个字符是否在其中，算法有点难看懂，但是记住能用位模拟就行，最蠢的方式是使用模拟加1操作得到所有的排列组合。

### 8.7 二叉树

结合了有序数组和链表的优点，查找数据速度快，同时添加删除数据的速度也快。又称二分树、二元树、对分树。

1. 基本概念

   节点度、叶节点、左孩子、右孩子、路径、路径长度、祖先、子孙、层数、深度、度、满二叉树（所有的分支节点都存在左子树和右子树，且叶子节点在同一层）、完全二叉树（节点标号和满二叉树相同，叶子节点只能在最底层或者次底层）

2. 二叉树的基本性质

   a）一棵非空二叉树第i层上最多有$2^{i-1}$个节点

   b）一棵深度为k的二叉树中，最多具有$2^k-1$个节点

   c）对于一棵非空的二叉树，度为0的节点比度为2的节点多一个,（通过$n_1+n_2+n_3 = n_1 + 2*n_2 + 1$,等式的两边都表示节点的个数）

   d）具有n个节点的完全二叉树的深度为$\lfloor log_2 n \rfloor + 1$

   e）对于具有n个节点的完全二叉树，对其进行从上到下从左到右的标号，那么对于任意的标号为i的节点，有，如果$i>1$则 其双亲节点为 i//2;如果$2i\le n$,则i节点的左孩子为2i，否则i没有左孩子；如果$2i + 1\le n$,则i节点的右孩子为2i+1，否则i没有右孩子；

3. 实现二叉排序树，根据前序、中序、后序三者中选出一种找到插入的位置插入即可

    二叉排序树特点：如果左子树不空，那么左子树上所有的节点小于根节点，如果右子树不为空，则右子树上的所有节点大于根节点。   左右子树都为二叉排序树。

4. 层次遍历二叉树

    利用队列实现，首先将根节点放置于队列中，之后出队列中取节点，如果具有左右节点则将其添加进去，循环直到队列为空。如果需要知道每一层的结果则在每层的后面使用一个空来标识当前层结束。或者使用双队列也行每层节点一定只放在同一个队列中，这样就能够知道节点层信息。

5. 已知先序遍历和中序遍历如何求后序遍历

    根据核心，定位根节点，然后将分为划分为子问题进行递归求解。

6. 二叉树结点的最大距离

    采用递归的思想分别求解左子树到根的最大距离和右子树到根的最大距离，之后二叉树中最远节点的距离就是两者之和

### 8.8 其他

1. 消除嵌套的括号

   a）使用栈进行处理，左括号压榨，右括号弹栈，如果栈操作出错则返回出错。

   b）括号计数法，由于只有一种括号，所以只需要判断能不能匹配上就可以。左括号加1 ，右括号减一。如果数小于0则出错。

2. 不使用比较符比较两个数的最大值和最小值

   取巧处理：Max(a, b) = (a + b + |b -  a|)/2，Min(a, b) = (a + b - |b - a|)/2

   需要注意的是可能存在数据溢出，需要使用long进行处理

## 第九章 海量数据处理

### 9.1 问题分析

海量数据存在的问题：

1. 数据量过大
2. 除了要有良好的软硬件配置，还需要合理地使用工具，合理分配系统资源

### 9.2 海量数据的处理方法

**1. Hash 方法**

 Hash一般称为散列，散列函数就是一种将任意长度消息压缩到某一固定长度消息摘要的函数。表长应该为质数，散列函数是用于关联字和存储地址之间的一种映射关系，但是不能保证不同的key得到不同的函数值，即存在冲突。

作用：可以快速存取，统计某些数据

散列函数特点：

1. 运算尽可能简单
2. 函数的值域必须散列在散列表的范围内
3. 尽可能地减少冲突

常见的散列函数构建方法：

1. 直接寻址法，取关键字或关键字的某个线性函数值为散列地址，集合大时不能用
2. 取模法，取一个合适的正整数p，令hash(key) = key mod p, p为较大的素数比较好，p一般取列表的长度，取模尽可能让商大
3. 除留余数法，取一个合适的正整数p， 令hash(key) = key % p, p为较大的素数比较好，p一般取列表的长度，取余尽可能让商小
4. 数字分析法，关键字是d位以r为基数的数，共有n个关键字，关键字可能在不同的数符出现，但这r个数符在各个位上出现的频率不一定相同。因此选择其中分布比较均匀的那些位重新组成新的数，用作其散列地址。方法比较简单，但是需要事先知道每个关键字的情况，限制了使用范围。
5. 折叠法，将关键字拆段，每段的长度为t，之后将每段对齐后进行按位相加，将进位丢弃，然后留下t为作为散列结果。当关键字很多时且每位上分别均匀时比较适合使用这种方法。
6. 平方取中法，将关键字进行平方处理，然后从结果的中间取出若干位
7. 随机数法，选择一个随机函数，然后使用随机函数的值作为散列地址

常用的解决冲突的办法：

1. 开放地址法，冲突时按照某种方法继续探测其他的存储地址，直到找到空闲的地址为止。

   即$H(key) = (H(key) + d_i) mod m( i=1, 2, ..., k(k <= m-1))​$,其中$d_i​$的取值方法有：

   1,2，...,m-1（线性探测再散列）、12，-12,22，-22，...,-k2(k<=m/2)(二次探测再散列)、伪随机序列（伪随机再散列，这种散列中不能真正删除数据）

2. 链地址法，列表中存放的是链的头结点，适合于冲突较大的情况

3. 再散列法，使用第二个、第三个散列函数重新计算地址，直到不冲突位置，这种方式缺点是计算时间大幅度增加。

4. 建立一个公共溢出区，利用额外的空间存储发生冲突的记录。

**2. Bit-map法**

​	最简单直观的理解将元素的值看出是bit-map中位，填完所有的数组就在里面了，绕了一个弯后原来的数使用下标代替。是一种使用空间换时间的方法，适合于稠密的数组。用来判断数据中是否存在重复，检查一个元素是否在数组中。

**3. Bloom Filter法**

一种用来快速判断一个元素是否在一个集合中的高空间效率和高时间效率（插入和查找时间都是常数）的数据结构，使用一组哈希函数计算得到下标，如果是存数组则将计算出来的位全部置1，如果是判断是否存在那么久判断计算出来的位是否都为1，如果是则存在。

使用的难点：如何根据输入元素个数n来确定数组m的大小以及Hash函数，当hash函数的个数为$k=(ln2)*(m/n)$时错误率最小，如果错误率不大于E的情况下，m至少要等于$n*lg(1/E)$才能保证数组里至少一半为0，所以m应该>=nlg(1/E)*lge，lge大概等于1.44

作用：实现数据字典、数据的判重、集合求交集，

优点是：快、安全、缺点是没法删除

CBF和SBF是两种拓展，CBF（counting Bloom Filter）是将位数组中的每一位扩展为Container，支持元素的删除。SBF（Spectral Bloom Filter）将其与集合元素出现的次数关联，采用container的最小值来近似表示元素的出现频率。

**4. 数据库优化法**

需要从数据库中提取数据，实际数据的查询等相关技术。

1. 优秀的数据库管理工具
2. 数据分区
3. 索引
4. 缓存机制
5. 加大虚存
6. 分批处理
7. 使用临时表和中间表
8. 优化查询语句
9. 使用视图
10. 使用存储过程
11. 用排序来取代非顺序存取
12. 使用数据采用进行数据挖掘

**5. 倒排索引**

倒排索引也称为反向索引，置入档案或反向档案，本质上是一种索引方法，被用来存储在全文搜索下某个单词在一个文档或者一组文档中的存储位置的映射，是文档管理系统中最常用的数据结构，有两种不同的反向索引形式：第一种形式是一条记录的水平反向索引包含每个引用单词的文档的列表。第二种形式是一个单词的水平反向索引又包含每个单词在一个文档中的位置。

**6. 外排序法**

当数据量较大无法一次放入内存时需要使用外排序算法。主要的算法有归并排序，将文件划分为多段可以直接加载如内存的子文件并将每个子文件调入内存进行排序，之后再进行多路归并排序。缺点是需要大量的IO。

**7. Trie树**

又称字典树或键树，他是一种用于快速字符串检索的多叉树结构，其原理是利用字符串的公共前缀来减小时空开销，即空间换时间。优点是：最大限度减少无谓的字符串比较，查询效率比散列表高。

特点：

1. 根节点不包括字符，除根节点外每个节点都只包含一个字符
2. 从根节点到某一节点，路径上经过的字符串连接起来为该节点的字符串
3. 每个节点的所有子节点包含的字符都不同

Trie适用于：数据量大，重复多，但是数据的种类少能够放入内存。

判断一个字符串是否是另一个字符串的子串：

1. 迭代法，对于每一个单词进行查找前面的单词中是否包含它，看某个字符串是否是字符串集中某个字符串的前缀
2. 使用Hash方法存储所有字符串的所有前缀子串，建立hash的时间复杂度为（O(n*len)），查询的时间复杂度为O(n)*O(1) = O(n)
3. Trie树法。

**8. 堆**

堆是一种树形数据结构，每个节点都有一个值，通常说的堆指的是二叉堆。

最大堆：求解前n小，首先取m个元素否后建最大堆，堆顶元素就是m个中最大的，其余都比它小，具有替换操作时，一定是替换栈顶元素。

最小堆：求解前n大

双堆：求解中位数

**9. 双层桶法**

双层桶法不是一种数据结构，而是一种算法，类似于分治思想，类似于分治思想。因为元素的范围很大，不能利用直接寻址表，所以通过多次划分，逐步确定范围，然后在一个可以接受的范围内进行。

桶排序只适用于关键字取值很小的情况，否则需要的桶的数目太多导致浪费存储空间和计算时间。

桶排序适用于寻找第k大的数，寻找中位数，寻找不重复或者重复的数字。

**10. MapReduce**

简化分布式并行计算的分布式编程模型。核心操作是Map和Reduce，Map程序将数据切割成为不相关的区块，分配给大量的计算机处理达到分布计算的效果，然后通过制定的Reduce函数将结果进行汇总，保证所有映射键值对中的每一个共享相同的键组。

Hadoop：开源社区用于实现MapReduce算法，它能够把应用程序分割成许多很小的工作单元，每个单元可以在任何集群的节点上执行或者重复执行。

GFS: 可拓展，结构化，具备日志的分布式文件系统，支持大型，分布式大数据量的读写操作，容错性强。

### 9.3 经典案例分析

#### 1. Top K问题

从海量的数据中找出出现频率最高（最大）的前k个数

一亿个浮点数，找出其中最大的10000个

1. 数据全部排序
2. 局部淘汰算法，使用一个容器存储10000个最大的数，时间复杂度O(n+m*m)
3. 分治法，例如利用快速排序可以不断缩减问题的规模
4. hash方法，首先通过hash进行去重后再进行处理。
5. 采用最小堆。首先取m个元素建立最小堆，之后遍历后面的元素如果比当前最小堆的堆顶元素小那么跳过，否则将元素插入最小堆中，处理完数据之后利用中序遍历就能得到最终的结果。时间复杂度为：O(nmlogm)

针对下面的场景分析解决方案：

（1）单机+单核+足够大内存

​	排序法，或者使用hashmap求词频然后再求解最大

（2）单机+多核+足够大内存

​	通过hash将数据划分为多份，分配至不同的线程进行处理，但是如果数据倾斜，则性能被最慢线程拖累

（3）单机+单核+受限内存

​	使用hash将数据划分为多个可以放入内存的小段，之后依次处理每个片段

（4）多机+受限内存

​	合理利用机器资源，将数据分发（Hash+socket）到多个机器上，每个机器采用（3）的方法解决问题。

**从实际应用角度：**算法的拓展性和容错性才是需要考虑。

#### 2. 重复问题

在海量数据中查找重复出现的元素或者去除重复出现的元素，一般通过位图法进行实现。

#### 3. 排序问题

当数据不能一次载入内存时，常用的数据排序算法不能使用，常用以下的算法进行处理：

1. 数据库排序法

2. 分治法，通过hash将数据进行分段，之后依次处理每段

   使用位图法时显然没有办法能够得到一个位数特别多的数，但是可以使用数组来进行模拟（位分段了，例如定义int数组后，就是每32个位一管理）。

3. 位图法，（之前的排序算法中有讲过），将数值映射到位，用位来表示数值。

