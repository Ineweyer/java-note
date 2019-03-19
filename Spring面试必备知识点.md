# Spring面试必备知识点

## 1.  Spring AOP，IOC实现原理

### 1.1 AOP

AOP思想的实现一般都是基于 **代理模式** ，在JAVA中一般采用JDK动态代理模式，但是我们都知道，**JDK动态代理模式只能代理接口而不能代理类**。因此，Spring AOP 会这样子来进行切换，因为Spring AOP 同时支持 CGLIB、ASPECTJ、JDK动态代理。

- 如果目标对象的实现类实现了接口，Spring AOP 将会采用 JDK 动态代理来生成 AOP 代理类；
- 如果目标对象的实现类没有实现接口，Spring AOP 将会采用 CGLIB 来生成 AOP 代理类——不过这个选择过程对开发者完全透明、开发者也无需关心。

#### 1.1.1 基本概念

##### 1.1.1.1通知（Advice）

```
通知定义了要织入目标对象的逻辑，以及执行时机。
Spring 中对应了 5 种不同类型的通知：
· 前置通知（Before）：在目标方法执行前，执行通知
· 后置通知（After）：在目标方法执行后，执行通知，此时不关系目标方法返回的结果是什么
· 返回通知（After-returning）：在目标方法执行后，执行通知
· 异常通知（After-throwing）：在目标方法抛出异常后执行通知
· 环绕通知（Around）: 目标方法被通知包裹，通知在目标方法执行前和执行后都被会调用
```

##### 1.1.1.2 切点（PointCut）

```
如果说通知定义了在何时执行通知，那么切点就定义了在何处执行通知。所以切点的作用就是
通过匹配规则查找合适的连接点（Joinpoint），AOP 会在这些连接点上织入通知。
```

##### 1.1.1.3 切面（Aspect）

```
切面包含了通知和切点，通知和切点共同定义了切面是什么，在何时，何处执行切面逻辑。
```

##### 1.1.1.4 连接点（Join point）

比如：方法调用、方法执行、字段设置/获取、异常处理执行、类初始化、甚至是 for 循环中的某个点

理论上, 程序执行过程中的任何时点都可以作为作为织入点, 而所有这些执行时点都是 Joint point

但 Spring AOP 目前仅支持方法执行 (method execution) 也可以这样理解，连接点就是你准备在系统中执行切点和切入通知的地方（**一般是一个方法，一个字段**）

##### 1.1.1.5 引入（Introduction）

允许我们向现有的类添加新的方法或者属性

##### 1.1.1.6 织入（Weaving）

组装切面来创建一个被通知对象。这可以在编译时完成（例如使用AspectJ编译器），也可以在运行时完成。Spring和其他纯Java AOP框架一样，在运行时完成织入。

#### 1.1.2 基于JDK动态代理的AOP实现步骤

1. 定义一个包含切面逻辑的对象，这里假设叫 logMethodInvocation
2. 定义一个 Advice 对象（实现了 InvocationHandler 接口），并将上面的 logMethodInvocation 和 目标对象传入
3. 将上面的 Adivce 对象和目标对象传给 JDK 动态代理方法，为目标对象生成代理

#### 1.1.3 代理模式

![img](https://images2015.cnblogs.com/blog/756310/201609/756310-20160924153802543-1643119235.jpg)

##### 1.1.3.1 静态代理

直接定义另外的一个类在调用当前代理方法之前时对其进行封装，就是相当于手动实现了代理的所有过程。

```java
interface Person() {
 	void speak();   
}

class Actor implementd Person {
    private String content;
    public Actor(String content) {
        this.content = content;
    }
    
    @Override
    public void speak() {
        System.out.println(this.content);
    }
}

class Agent implemented Person {
    private Actor actor;
    public Agent(Actor actor) {
        this.actor = actor;
    }
    
    @Override
    public void speak() {
        System.out.println("before:");
        System.out.println(this.content);
        System.out.println("after");
    }
}
```

##### 1.1.3.2 动态代理

###### 1.1.3.2.1 JDK自带的方法

首先介绍一下最核心的一个接口和一个方法：

首先是java.lang.reflect包里的InvocationHandler接口：

```
public interface InvocationHandler {
    public Object invoke(Object proxy, Method method, Object[] args)
        throws Throwable;
}
```

我们对于被代理的类的操作都会由该接口中的invoke方法实现，其中的参数的含义分别是：

- proxy：被代理的类的实例
- method：调用被代理的类的方法
- args：该方法需要的参数

使用方法首先是需要实现该接口，并且我们可以在invoke方法中调用被代理类的方法并获得返回值，自然也可以在调用该方法的前后去做一些额外的事情，从而实现动态代理，下面的例子会详细写到。

另外一个很重要的静态方法是java.lang.reflect包中的Proxy类的newProxyInstance方法：

```
public static Object newProxyInstance(ClassLoader loader,
                                      Class<?>[] interfaces,
                                      InvocationHandler h)
    throws IllegalArgumentException
```

其中的参数含义如下：

- loader：被代理的类的类加载器
- interfaces：被代理类的接口数组
- invocationHandler：就是刚刚介绍的调用处理器类的对象实例

该方法会返回一个被修改过的类的实例，从而可以自由的调用该实例的方法。

```java
interface Fruit {
    void show();
}

class Apple implements Fruit {
    @Override
    public void show() {
        System.out.println("show method is invoked");
    }
}

public class DynamicAgent {
    static class MyHandler implements InvocationHandler {
        private Object proxy;
        public MyHander(Object proxy) {
            this.proxy = proxy;
        }
        
        public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
            System.out.pritln("before invoking");
            Object ret = method.invoke(this.proxy, args);
            System.out.pritln("after invoking");
            return ret;
        }
    }
    
    public static Object agent(Class interfaceClazz, Object proxy) {
        return Proxy.newProxyInstance(interfaceClazz.getClassLoader(), new Class[]{interfaceClazz}, new MyHandler(proxy));
    }
}
```

##### 1.1.3.2 CGLIB方法

```java
import net.sf.cglib.proxy.Enhancer;
import net.sf.cglib.proxy.MethodInterceptor;
import net.sf.cglib.proxy.MethodProxy;
import java.lang.reflect.Method;

public class CGlibAgent implements MethodInterceptor {

    private Object proxy;

    public Object getInstance(Object proxy) {
        this.proxy = proxy;
        Enhancer enhancer = new Enhancer();
        enhancer.setSuperclass(this.proxy.getClass());
        // 回调方法
        enhancer.setCallback(this);
        // 创建代理对象
        return enhancer.create();
    }
    //回调方法
    @Override
    public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
        System.out.println(">>>>before invoking");
        //真正调用
        Object ret = methodProxy.invokeSuper(o, objects);
        System.out.println(">>>>after invoking");
        return ret;
    }

    public static void main(String[] args) {
        CGlibAgent cGlibAgent = new CGlibAgent();
        Apple apple = (Apple) cGlibAgent.getInstance(new Apple());
        apple.show();
    }
}
```

#### 1.1.4 Spring Aop实例

- 定义主题接口，这些接口的方法可以成为我们的`连接点`

```java
package wokao666.club.aop.spring01;

public interface Subject {
	
	//登陆
	public void login();
	
	//下载
	public void download();
	
}
```

- 定义实现类,这是代理模式中真正的被代理人（如果你有参与代购，这个就像你在代购中的角色）

```java
package wokao666.club.aop.spring02;

import wokao666.club.aop.spring01.Subject;

public class SubjectImpl implements Subject {

	public void login() {
		
		System.err.println("借书中...");
		
	}

	public void download() {
		
		System.err.println("下载中...");
		
	}
}
```

- 定义切面（切面中有切点和通知）

```java
package wokao666.club.aop.spring01;

import org.aspectj.lang.JoinPoint;

public class PermissionVerification {
 /**
  * 权限校验
  * @param args 登陆参数
  */
	public void canLogin() {
		
		//做一些登陆校验
		System.err.println("我正在校验啦！！！！");
		
	}
	
	/**
	 * 校验之后做一些处理（无论是否成功都做处理）
	 * @param args 权限校验参数
	 */
	public void saveMessage() {
		
		//做一些后置处理
			System.err.println("我正在处理啦！！！！");
	}
	
}
复制代码
```

- 再来看看我们的`SpringAOP.xml`文件

```xml
    <?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:aop="http://www.springframework.org/schema/aop"
    xmlns:tx="http://www.springframework.org/schema/tx"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop-4.2.xsd">
        
        <bean id="SubjectImpl1" class="wokao666.club.aop.spring02.SubjectImpl" />
        <bean id="SubjectImpl2" class="wokao666.club.aop.spring02.SubjectImpl" />
        <bean id="PermissionVerification" class="wokao666.club.aop.spring01.PermissionVerification" />
        
        <aop:config>
        <!-- 这是定义一个切面，切面是切点和通知的集合-->
            <aop:aspect id="do" ref="PermissionVerification">
            	<!-- 定义切点 ，后面是expression语言，表示包括该接口中定义的所有方法都会被执行-->
                <aop:pointcut id="point" expression="execution(* wokao666.club.aop.spring01.Subject.*(..))" />
                <!-- 定义通知 -->
                <aop:before method="canLogin" pointcut-ref="point" />
                <aop:after method="saveMessage" pointcut-ref="point" />
            </aop:aspect>
        </aop:config>
</beans>
```

由于依赖于CGLIB和aspect，需要导入相关的依赖。

#### 1.1.5 AspectJ注解

**Spring 只是使用了与 AspectJ 5 一样的注解，但仍然没有使用 AspectJ 的编译器，底层依是动态代理技术的实现，因此并不依赖于 AspectJ 的编译器**。

##### 1.1.5.1 注解Demo

需要使用的依赖

```xml
   <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-core</artifactId>
      <version>4.2.3.RELEASE</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>4.2.3.RELEASE</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-aop</artifactId>
      <version>4.2.3.RELEASE</version>
    </dependency>
    <!--下面这两个aspectj的依赖是为了引入AspectJ的注解 -->
    <dependency>
      <groupId>org.aspectj</groupId>
      <artifactId>aspectjrt</artifactId>
      <version>1.6.12</version>
    </dependency>
    <dependency>
      <groupId>org.aspectj</groupId>
      <artifactId>aspectjweaver</artifactId>
      <version>1.6.12</version>
    </dependency>
	<!-- Spring AOP底层会使用CGLib来做动态代理 -->
    <dependency>
    <groupId>cglib</groupId>
    <artifactId>cglib</artifactId>
    <version>2.2</version>
  </dependency>
```

小狗类，会说话:

```java
public class Dog {

    private String name;


    public void say(){
        System.out.println(name + "在汪汪叫!...");
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

切面类:

```java
@Aspect //声明自己是一个切面类
public class MyAspect {
    /**
     * 前置通知
     */
     //@Before是增强中的方位
     // @Before括号中的就是切入点了
     //before()就是传说的增强(建言):说白了，就是要干啥事.
    @Before("execution(* com.zdy..*(..))")
    public void before(){
        System.out.println("前置通知....");
    }
}
```

这个类是重点，先用@Aspect声明自己是切面类，然后before()为增强，**@Before(方位)+切入点**可以具体定位到具体某个类的某个方法的方位. Spring配置文件:

```java
    //开启AspectJ功能.
    <aop:aspectj-autoproxy />

    <bean id="dog" class="com.zdy.Dog" />
    <!-- 定义aspect类 -->
    <bean name="myAspect" class="com.zdy.MyAspect"/>
```

然后Main方法:

```java
        ApplicationContext ac =new ClassPathXmlApplicationContext("applicationContext.xml");
        Dog dog =(Dog) ac.getBean("dog");
        System.out.println(dog.getClass());
        dog.say();
```

输出结果:

```
class com.zdy.Dog$$EnhancerBySpringCGLIB$$80a9ee5f
前置通知....
null在汪汪叫!...
```

##### 1.1.5.2 Aspect注解

- **前置通知@Before**: 前置通知通过@Before注解进行标注，并可直接传入切点表达式的值，该通知在目标函数执行前执行，注意JoinPoint，是Spring提供的静态变量，通过joinPoint 参数，可以获取目标对象的信息,如类名称,方法参数,方法名称等，该参数是可选的。

```java
@Before("execution(...)")
public void before(JoinPoint joinPoint){
    System.out.println("...");
}
```

- **后置通知@AfterReturning**: 通过@AfterReturning注解进行标注，该函数在目标函数执行完成后执行，并可以获取到目标函数最终的返回值returnVal，当目标函数没有返回值时，returnVal将返回null，必须通过returning = “returnVal”注明参数的名称而且必须与通知函数的参数名称相同。请注意，在任何通知中这些参数都是可选的，需要使用时直接填写即可，不需要使用时，可以完成不用声明出来。

```java
@AfterReturning(value="execution(...)",returning = "returnVal")
public void AfterReturning(JoinPoint joinPoint,Object returnVal){
   System.out.println("我是后置通知...returnVal+"+returnVal);
}
```

- **异常通知 @AfterThrowing**:该通知只有在异常时才会被触发，并由throwing来声明一个接收异常信息的变量，同样异常通知也用于Joinpoint参数，需要时加上即可.

```java
@AfterThrowing(value="execution(....)",throwing = "e")
public void afterThrowable(Throwable e){
  System.out.println("出现异常:msg="+e.getMessage());
}
```

- **最终通知 @After**:该通知有点类似于finally代码块，只要应用了无论什么情况下都会执行.

```java
@After("execution(...)")
public void after(JoinPoint joinPoint) {
    System.out.println("最终通知....");
}
```

- **环绕通知 @Around**: 环绕通知既可以在目标方法前执行也可在目标方法之后执行，更重要的是环绕通知可以控制目标方法是否指向执行，但即使如此，我们应该尽量以最简单的方式满足需求，在仅需在目标方法前执行时，应该采用前置通知而非环绕通知。案例代码如下第一个参数必须是ProceedingJoinPoint，通过该对象的proceed()方法来执行目标函数，proceed()的返回值就是环绕通知的返回值。同样的，ProceedingJoinPoint对象也是可以获取目标对象的信息,如类名称,方法参数,方法名称等等

```java
@Around("execution(...)")
public Object around(ProceedingJoinPoint joinPoint) throws Throwable {
    System.out.println("我是环绕通知前....");
    //执行目标函数
    Object obj= (Object) joinPoint.proceed();
    System.out.println("我是环绕通知后....");
    return obj;
}
```

##### 1.1.5.3 切入点表达式

```xml
//scope ：方法作用域，如public,private,protect
//returnt-type：方法返回值类型
//fully-qualified-class-name：方法所在类的完全限定名称
//parameters 方法参数
execution(<scope> <return-type> <fully-qualified-class-name>.*(parameters))
```

最常用的两个切入点表达式；

- execution(* com.zdy..*(..))：com.zdy包下所有类的所有方法.
- execution(* com.zdy.Dog.*(..)): Dog类下的所有方法.

##### 1.1.5.4 切入点使用技巧

在使用切入点时，还可以抽出来一个@Pointcut来供使用:

```java
/**
 * 使用Pointcut定义切点
 */
@Pointcut("execution(...)")
private void myPointcut(){}

/**
 * 应用切入点函数
 */
@After(value="myPointcut()")
public void afterDemo(){
    System.out.println("最终通知....");
}
```

#### 1.6 源码分析

具体见[Spring 源码分析（六）](https://blog.csdn.net/fighterandknight/article/details/51209822)

### 1.2 IOC

为了解决对象之间的耦合度过高的问题，提出了IOC理论。

IOC是Inversion of Control的缩写，多数书籍翻译成“控制反转”，还有些书籍翻译成为“控制反向”或者“控制倒置”，其观点大体是：**借助于第三方实现具有依赖关系的对象之间的解耦**

![img](https://pic002.cnblogs.com/images/2011/230454/2011052709391014.jpg)

对象之间原来的主动关系变成了被动关系，由IOC容器提供需要的对象。

IOC的别名：**依赖注入（DI）**，他们是从不同的角度描述同一件事情，就是指**通过引入IOC容器，利用依赖关系注入的方式，实现对象之间的解耦**。

**IOC具有的好处：**

- 解耦，具有相关性（只有使用时才发生联系）也具有无关性（两者之间不会互相影响）
- 代码复用
- 具有热插拔特性

**我们可以把IOC容器的工作模式看做是工厂模式的升华，可以把IOC容器看作是一个工厂，这个工厂里要生产的对象都在配置文件中给出定义，然后利用编程语言的的反射编程，根据配置文件中给出的类名生成相应的对象。从实现来看，IOC是把以前在工厂方法里写死的对象生成代码，改变为由配置文件来定义，也就是把工厂和对象生成这两者独立分隔开来，目的就是提高灵活性和可维护性。**

**IOC容器的产品：**轻量级的有Spring、Guice、Pico [Container](http://lib.csdn.net/base/4)、Avalon、HiveMind；重量级的有EJB；不轻不重的有JBoss，Jdon等等。

**使用IOC的注意事项：**

- 引入第三方IOC容器，生成对象的步骤变得复杂，需要新来者具有同样的知识体系
- 通过反射实现，效率上有一定的损失
- 需要一定的配置工作
- 产品本身的成熟度需要评估

**容器创建的过程**

- 1.初始化

![flow](https://cloud.githubusercontent.com/assets/1736354/7897341/032179be-070b-11e5-9ecf-d7befc804e9d.png)

- 2. 注入依赖

![bean_flow](https://cloud.githubusercontent.com/assets/1736354/7929429/615570ea-0930-11e5-8097-ae982ef7709d.png)

详细的源码分析见[Spring IOC 容器源码分析](https://javadoop.com/post/spring-ioc)

#### 1.2.1 基本概念

1. **BeanDefinition**

从字面意思上翻译成中文就是 “Bean 的定义”。从翻译结果中就可以猜出这个类的用途，即根据 Bean 配置信息生成相应的 Bean 详情对象。

1. **BeanReference**
2. **PropertyValues**
3. **PropertyValue**

![img](https://blog-pictures.oss-cn-shanghai.aliyuncs.com/bean%e9%85%8d%e7%bd%ae%e8%bd%acBeanDefinition.png)

#### 1.2.2 BeanFactory的生命流程

1. BeanFactory 加载 Bean 配置文件，将读到的 Bean 配置封装成 BeanDefinition 对象
2. 将封装好的 BeanDefinition 对象注册到 BeanDefinition 容器中
3. 注册 BeanPostProcessor 相关实现类到 BeanPostProcessor 容器中
4. BeanFactory 进入就绪状态
5. 外部调用 BeanFactory 的 getBean(String name) 方法，BeanFactory 着手实例化相应的 bean
6. 重复步骤 3 和 4，直至程序退出，BeanFactory 被销毁

#### 1.2.3 xml解析

XmlBeanDefinitionReader 做了如下几件事情：

1. 将 xml 配置文件加载到内存中
2. 获取根标签下所有的标签
3. 遍历获取到的标签列表，并从标签中读取 id，class 属性
4. 创建 BeanDefinition 对象，并将刚刚读取到的 id，class 属性值保存到对象中
5. 遍历标签下的标签，从中读取属性值，并保持在 BeanDefinition 对象中
6. 将 <id, BeanDefinition> 键值对缓存在 Map 中，留作后用
7. 重复3、4、5、6步，直至解析结束

#### 1.2.4  注册 BeanPostProcessor

BeanPostProcessor 接口是 Spring **对外拓展的接口之一**，其主要用途**提供一个机会，让开发人员能够插手 bean 的实例化过程**。通过实现这个接口，我们就可在 bean 实例化时，对bean 进行一些处理。比如，我们所熟悉的 AOP 就是在这里将切面逻辑织入相关 bean 中的。正是因为有了 BeanPostProcessor 接口作为桥梁，才使得 AOP 可以和 IOC 容器产生联系。关于这一点，我将会在后续章节详细说明。

接下来说说 BeanFactory 是怎么注册 BeanPostProcessor 相关实现类的。

XmlBeanDefinitionReader 在完成解析工作后，BeanFactory 会将它解析得到的 <id, BeanDefinition> 键值对注册到自己的 beanDefinitionMap 中。BeanFactory 注册好 BeanDefinition 后，就立即开始注册 BeanPostProcessor 相关实现类。这个过程比较简单：

1. 根据 BeanDefinition 记录的信息，寻找所有实现了 BeanPostProcessor 接口的类。
2. 实例化 BeanPostProcessor 接口的实现类
3. 将实例化好的对象放入 List中
4. 重复2、3步，直至所有的实现类完成注册

#### 1.2.5  getBean过程解析

外部程序一般都是通过调用 BeanFactory 的 getBean(String name) 方法来获取容器中的 bean。BeanFactory 具有延迟实例化 bean 的特性，也就是等外部程序需要的时候，才实例化相关的 bean。这样做的好处是比较显而易见的，第一是提高了 BeanFactory 的初始化速度，第二是节省了内存资源。下面我们就来详细说说 bean 的实例化过程：

[![bean实例化过程](https://blog-pictures.oss-cn-shanghai.aliyuncs.com/bean%e5%ae%9e%e4%be%8b%e5%8c%96%e8%bf%87%e7%a8%8b.png)](http://www.coolblog.xyz/)

## 2. Spring事务管理

事务是逻辑上的一组操作，要么全部执行，要么全部不执行。

事务具有ACID属性。

### 2.1 Spring事务管理接口

- **PlatformTransactionManager：** （平台）事务管理器
- **TransactionDefinition：** 事务定义信息(事务隔离级别、传播行为、超时、只读、回滚规则)
- **TransactionStatus：** 事务运行状态

#### 2.1.1 PlafformTransactionManager接口介绍

所谓**事务管理**，其实就是**“按照给定的事务规则来执行提交或回滚操作”**

**Spring并不直接管理事务，而是提供了多种事务管理器**，事务管理的职责委托给Hibernate或者JTA等持久化机制提供事务来实现。事务管理器的接口是：**org.springframework.transaction.PlatformTransactionManager** ，通过这个接口，Spring为各个平台如JDBC等都提供了对应的事务管理器，而具体的实现交由平台复杂。

```java
Public interface PlatformTransactionManager()...{  
    // Return a currently active transaction or create a new one, according to the specified propagation behavior（根据指定的传播行为，返回当前活动的事务或创建一个新事务。）
    TransactionStatus getTransaction(TransactionDefinition definition) throws TransactionException; 
    // Commit the given transaction, with regard to its status（使用事务目前的状态提交事务）
    Void commit(TransactionStatus status) throws TransactionException;  
    // Perform a rollback of the given transaction（对执行的事务进行回滚）
    Void rollback(TransactionStatus status) throws TransactionException; 
    } 
```

Spring中PlatformTransactionManager根据不同持久层框架所对应的接口实现类，常见的如下图所示：

![PlatformTransactionManageræ ¹æ®ä¸åæä¹å±æ¡æ¶æå¯¹åºçæ¥å£å®ç°](https://user-gold-cdn.xitu.io/2018/5/20/1637b21877cf626d?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

在使用JDBC或者iBatis(就是MyBaits)进行数据持久化操作时，xml配置通常如下所示：

```xml
	<!-- 事务管理器 -->
	<bean id="transactionManager"
		class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<!-- 数据源 -->
		<property name="dataSource" ref="dataSource" />
	</bean>
```

#### 2.1.2 TransactionDefinition接口介绍

事务管理器接口 **PlatformTransactionManager** 通过 **getTransaction(TransactionDefinition definition)** 方法来得到一个事务，这个方法里面的参数是 **TransactionDefinition类** ，这个类就定义了一些基本的事务属性。

##### 2.1.2.1 事务属性

![äºå¡å±æ§](https://user-gold-cdn.xitu.io/2018/5/20/1637b43a47916b2d?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

##### 2.1.2.2 TransactionDefinition接口中定义的方法

```java
public interface TransactionDefinition {
    // 返回事务的传播行为
    int getPropagationBehavior(); 
    // 返回事务的隔离级别，事务管理器根据它来控制另外一个事务可以看到本事务内的哪些数据
    int getIsolationLevel(); 
    // 返回事务必须在多少秒内完成
    //返回事务的名字
    String getName()；
    int getTimeout();  
    // 返回是否优化为只读事务。
    boolean isReadOnly();
} 
```

##### 2.1.2.3 事务并发带来的问题

- 脏读，事务读取到没有提交事务的数据
- 丢失修改，一个事务修改的数据被另外一个事务修改覆盖
- 不可重复读，同一个事务多次读入数据不同，这是由于读取到其他事务已经提交的数据造成
- 幻读，同一个事务在不同两次读取的过程中，记录数不同

**不可重复读和幻读的区别**

一个是针对某项数据的内容（针对修改），一个是针对符合读取条件的数据数量（针对新增或者删除）

##### 2.1.2.4 隔离级别（接口中定义的常量，同时也和数据库中事务隔离界别是对应的）

**TransactionDefinition.ISOLATION_DEFAULT:**	使用后端数据库默认的隔离级别，Mysql 默认采用的 REPEATABLE_READ隔离级别 Oracle 默认采用的 READ_COMMITTED隔离级别.

**TransactionDefinition.ISOLATION_READ_UNCOMMITTED:** 最低的隔离级别，允许读取尚未提交的数据变更，**可能会导致脏读、幻读或不可重复读**

**TransactionDefinition.ISOLATION_READ_COMMITTED:** 	允许读取并发事务已经提交的数据，**可以阻止脏读，但是幻读或不可重复读仍有可能发生**

**TransactionDefinition.ISOLATION_REPEATABLE_READ:** 	对同一字段的多次读取结果都是一致的，除非数据是被本身事务自己所修改，**可以阻止脏读和不可重复读，但幻读仍有可能发生。**

**TransactionDefinition.ISOLATION_SERIALIZABLE:** 	最高的隔离级别，完全服从ACID的隔离级别。所有的事务依次逐个执行，这样事务之间就完全不可能产生干扰，也就是说，**该级别可以防止脏读、不可重复读以及幻读**。但是这将严重影响程序的性能。通常情况下也不会用到该级别。

##### 2.1.2.5 事务传播行为（为了解决业务层方法之间互相调用的事务问题）

当事务方法被另一个事务方法调用时，必须制定应该如何传播。

**传播：**就是两个事务之间具有什么关系，比如开启新的事务，还是继续在原来的事务中执行等。

TransactionDefinition中定义的表示传播行为的常量：

**支持当前事务的情况：**

- **TransactionDefinition.PROPAGATION_REQUIRED：** 如果当前存在事务，则加入该事务；如果当前没有事务，则创建一个新的事务。
- **TransactionDefinition.PROPAGATION_SUPPORTS：** 如果当前存在事务，则加入该事务；如果当前没有事务，则以非事务的方式继续运行。
- **TransactionDefinition.PROPAGATION_MANDATORY：** 如果当前存在事务，则加入该事务；如果当前没有事务，则抛出异常。（mandatory：强制性）

**不支持当前事务的情况：**

- **TransactionDefinition.PROPAGATION_REQUIRES_NEW：** 创建一个新的事务，如果当前存在事务，则把当前事务挂起。
- **TransactionDefinition.PROPAGATION_NOT_SUPPORTED：** 以非事务方式运行，如果当前存在事务，则把当前事务挂起。
- **TransactionDefinition.PROPAGATION_NEVER：** 以非事务方式运行，如果当前存在事务，则抛出异常。

**其他情况：**

- **TransactionDefinition.PROPAGATION_NESTED：** 如果当前存在事务，则创建一个事务作为当前事务的嵌套事务来运行；如果当前没有事务，则该取值等价于TransactionDefinition.PROPAGATION_REQUIRED。

前面六种为Spring从EJB中引入，他们具有相同的概念，最后一个是Spring特有。嵌套的事务中，内部的事务不是一个独立的事务，依赖于外部事务的存在，只有外部事务提交，才能引起内部事务提交，外部事务的回滚也会导致内部事务的回滚。嵌套的子事务是JDBC中的保存点（SavePoint）的一个应用。

##### 2.1.2.6 事务超时属性（一个事务允许执行的最长时间）

所谓事务超时，就是指一个事务允许执行的最长时间，如果超过限制时间没有完成，则自动回滚事务。以int值来表示超时时间，单位是秒。

##### 2.1.2.7 事务只读属性

事务的只读属性是指，对事务性资源进行只读操作或者读写操作。

所谓事务性资源就是指那些被事务管理的资源，比如数据源、 JMS 资源，以及自定义的事务性资源等等，如果确定是只读操作，那么可以将事务标为只读以提高性能。

##### 2.1.2.8 回滚规则

默认情况下，只有遇到运行是异常才会回滚，而在检查型异常是 不回滚。但是这些都是开发人员可以定义回滚还是不回滚。

#### 2.1.3 TransactionStatus接口

TransactionStatus接口用来记录事务的状态 该接口定义了一组方法,用来获取或判断事务的相应状态信息.

PlatformTransactionManager.getTransaction(…) 方法返回一个 TransactionStatus 对象。返回的TransactionStatus 对象可能代表一个新的或已经存在的事务（如果在当前调用堆栈有一个符合条件的事务）。

**TransactionStatus接口接口内容如下：**

```java
public interface TransactionStatus{
    boolean isNewTransaction(); // 是否是新的事物
    boolean hasSavepoint(); // 是否有恢复点
    void setRollbackOnly();  // 设置为只回滚
    boolean isRollbackOnly(); // 是否为只回滚
    boolean isCompleted; // 是否已完成
} 
```

### 2.2 Spring 支持的两种方式的事务管理

- **编程式事务管理：** 通过Transaction Template手动管理事务，实际使用中特别少

- **使用XML配置声明式事务：**推荐使用（代码倾入性最小），实际是通过AOP实现

#### 2.2.1 实现声明式事务的四种方式

1. **基于 TransactionInterceptor 的声明式事务:** Spring 声明式事务的基础，通常也不建议使用这种方式，但是与前面一样，了解这种方式对理解 Spring 声明式事务有很大作用。

2. **基于 TransactionProxyFactoryBean 的声明式事务:** 第一种方式的改进版本，简化的配置文件的书写，这是 Spring 早期推荐的声明式事务管理方式，但是在 Spring 2.0 中已经不推荐了。

3. **基于<  tx> 和< aop>命名空间的声明式事务管理：** 目前推荐的方式，其最大特点是与 Spring AOP 结合紧密，可以充分利用切点表达式的强大支持，使得管理事务更加灵活。

4. **基于 @Transactional 的全注解方式：** 将声明式事务管理简化到了极致。开发人员只需在配置文件中加上一行启用相关后处理 Bean 的配置，然后在需要实施事务管理的方法或者类上使用 @Transactional 指定事务规则即可实现事务管理，而且功能也不必其他方式逊色。

#### 2.2.2 编程式事务管理

编程式实现的原理特别简单，就是在事务操作过程中，如果出现错误那么就进行具体的处理。即try/catch处理。

##### 2.2.2.1 数据基础

```sql
create table `account` (
	`username` varchar (99),
	`salary` int (11)
); 
insert into `account` (`username`, `salary`) values('小王','3000');
insert into `account` (`username`, `salary`) values('小马','3000');
```

##### 2.2.2.2 具体的实现

**OrdersDao.java(Dao层)**

```java
package cn.itcast.dao;

import org.springframework.jdbc.core.JdbcTemplate;

public class OrdersDao {
	// 注入jdbcTemplate模板对象
	private JdbcTemplate jdbcTemplate;

	public void setJdbcTemplate(JdbcTemplate jdbcTemplate) {
		this.jdbcTemplate = jdbcTemplate;
	}

	// 对数据操作的方法不包含业务操作
	/**
	 * 小王少钱的方法
	 */
	public void reduceMoney() {
		String sql = "update account set salary=salary-? where username=?";
		jdbcTemplate.update(sql, 1000, "小王");
	}

	/**
	 * 小马多钱的方法
	 */
	public void addMoney() {
		String sql = "update account set salary=salary+? where username=?";
		jdbcTemplate.update(sql, 1000, "小马");
	}
}
```

**OrdersService.java（业务逻辑层）**

```java
package cn.itcast.service;

import org.springframework.transaction.TransactionStatus;
import org.springframework.transaction.support.TransactionCallback;
import org.springframework.transaction.support.TransactionTemplate;

import cn.itcast.dao.OrdersDao;

public class OrdersService {
	// 注入Dao层对象
	private OrdersDao ordersDao;

	public void setOrdersDao(OrdersDao ordersDao) {
		this.ordersDao = ordersDao;
	}

	// 注入TransactionTemplate对象
	private TransactionTemplate transactionTemplate;

	public void setTransactionTemplate(TransactionTemplate transactionTemplate) {
		this.transactionTemplate = transactionTemplate;
	}

	// 调用dao的方法
	// 业务逻辑，写转账业务
	public void accountMoney() {
		transactionTemplate.execute(new TransactionCallback<Object>() {

			@Override
			public Object doInTransaction(TransactionStatus status) {
				Object result = null;
				try {
					// 小马多1000
					ordersDao.addMoney();
					// 加入出现异常如下面int
					// i=10/0（银行中可能为突然停电等。。。）；结果：小马账户多了1000而小王账户没有少钱
					// 解决办法是出现异常后进行事务回滚
					int i = 10 / 0;// 事务管理配置后异常已经解决
					// 小王 少1000
					ordersDao.reduceMoney();
				} catch (Exception e) {
					status.setRollbackOnly();
					result = false;
					System.out.println("Transfer Error!");
				}

				return result;
			}
		});

	}
}
```

**TestService.java（测试方法）**

```java
package cn.itcast.service;

import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class TestService {
	@Test
	public void testAdd() {
		ApplicationContext context = new ClassPathXmlApplicationContext(
				"beans.xml");
		OrdersService userService = (OrdersService) context
				.getBean("ordersService");
		userService.accountMoney();
	}
}
```

**配置文件：**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:context="http://www.springframework.org/schema/context"
	xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-2.5.xsd  
http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-2.5.xsd  
http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-2.5.xsd  
http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-2.5.xsd">
	<!-- 配置c3po连接池 -->
	<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
		<!-- 注入属性值 -->
		<property name="driverClass" value="com.mysql.jdbc.Driver"></property>
		<property name="jdbcUrl" value="jdbc:mysql://localhost:3306/wangyiyun"></property>
		<property name="user" value="root"></property>
		<property name="password" value="153963"></property>
	</bean>
	<!-- 编程式事务管理 -->
	<!-- 配置事务管理器 -->
	<bean id="dataSourceTransactionManager"
		class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<!-- 注入dataSource -->
		<property name="dataSource" ref="dataSource"></property>
	</bean>

	<!-- 配置事务管理器模板 -->
	<bean id="transactionTemplate"
		class="org.springframework.transaction.support.TransactionTemplate">
		<!-- 注入真正进行事务管理的事务管理器,name必须为 transactionManager否则无法注入 -->
		<property name="transactionManager" ref="dataSourceTransactionManager"></property>
	</bean>

	<!-- 对象生成及属性注入 -->
	<bean id="ordersService" class="cn.itcast.service.OrdersService">
		<property name="ordersDao" ref="ordersDao"></property>
		<!-- 注入事务管理的模板 -->
		<property name="transactionTemplate" ref="transactionTemplate"></property>
	</bean>

	<bean id="ordersDao" class="cn.itcast.dao.OrdersDao">
		<property name="jdbcTemplate" ref="jdbcTemplate"></property>
	</bean>
	<!-- JDBC模板对象 -->
	<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
		<property name="dataSource" ref="dataSource"></property>
	</bean>
</beans> 
```

#### 2.2.3 基于AspectJ的声明式事务管理

**OrdersService.java（业务逻辑层）**

```java
package cn.itcast.service;

import cn.itcast.dao.OrdersDao;

public class OrdersService {
	private OrdersDao ordersDao;

	public void setOrdersDao(OrdersDao ordersDao) {
		this.ordersDao = ordersDao;
	}

	// 调用dao的方法
	// 业务逻辑，写转账业务
	public void accountMoney() {
		// 小马多1000
		ordersDao.addMoney();
		// 加入出现异常如下面int i=10/0（银行中可能为突然停电等。。。）；结果：小马账户多了1000而小王账户没有少钱
		// 解决办法是出现异常后进行事务回滚
		int i = 10 / 0;// 事务管理配置后异常已经解决
		// 小王 少1000
		ordersDao.reduceMoney();
	}
}
```

**配置文件：**

```xml
	<!-- 配置c3po连接池 -->
	<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
		<!-- 注入属性值 -->
		<property name="driverClass" value="com.mysql.jdbc.Driver"></property>
		<property name="jdbcUrl" value="jdbc:mysql://localhost:3306/wangyiyun"></property>
		<property name="user" value="root"></property>
		<property name="password" value="153963"></property>
	</bean>
	<!-- 第一步：配置事务管理器 -->
	<bean id="dataSourceTransactionManager"
		class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<!-- 注入dataSource -->
		<property name="dataSource" ref="dataSource"></property>
	</bean>

	<!-- 第二步：配置事务增强 -->
	<tx:advice id="txadvice" transaction-manager="dataSourceTransactionManager">
		<!-- 做事务操作 -->
		<tx:attributes>
			<!-- 设置进行事务操作的方法匹配规则 -->
			<!-- account开头的所有方法 -->
	        <!--
	          propagation:事务传播行为； 
	          isolation：事务隔离级别；
	          read-only：是否只读；
	          rollback-for：发生那些异常时回滚 
	          timeout:事务过期时间
	         -->
			<tx:method name="account*" propagation="REQUIRED"
				isolation="DEFAULT" read-only="false" rollback-for="" timeout="-1" />
		</tx:attributes>
	</tx:advice>

	<!-- 第三步：配置切面 切面即把增强用在方法的过程 -->
	<aop:config>
		<!-- 切入点 -->
		<aop:pointcut expression="execution(* cn.itcast.service.OrdersService.*(..))"
			id="pointcut1" />
		<!-- 切面 -->
		<aop:advisor advice-ref="txadvice" pointcut-ref="pointcut1" />
	</aop:config>


	<!-- 对象生成及属性注入 -->
	<bean id="ordersService" class="cn.itcast.service.OrdersService">
		<property name="ordersDao" ref="ordersDao"></property>
	</bean>
	<bean id="ordersDao" class="cn.itcast.dao.OrdersDao">
		<property name="jdbcTemplate" ref="jdbcTemplate"></property>
	</bean>
	<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
		<property name="dataSource" ref="dataSource"></property>
	</bean>
```

#### 2.2.4 基于注解的方式

**OrdersService.java（业务逻辑层）**

```java
package cn.itcast.service;

import org.springframework.transaction.annotation.Isolation;
import org.springframework.transaction.annotation.Propagation;
import org.springframework.transaction.annotation.Transactional;

import cn.itcast.dao.OrdersDao;

//注意这个位置加了一个注解
@Transactional(propagation = Propagation.REQUIRED, isolation = Isolation.DEFAULT, readOnly = false, timeout = -1)
public class OrdersService {
	private OrdersDao ordersDao;

	public void setOrdersDao(OrdersDao ordersDao) {
		this.ordersDao = ordersDao;
	}

	// 调用dao的方法
	// 业务逻辑，写转账业务
	public void accountMoney() {
		// 小马多1000
		ordersDao.addMoney();
		// 加入出现异常如下面int i=10/0（银行中可能为突然停电等。。。）；结果：小马账户多了1000而小王账户没有少钱
		// 解决办法是出现异常后进行事务回滚
		// int i = 10 / 0;// 事务管理配置后异常已经解决
		// 小王 少1000
		ordersDao.reduceMoney();
	}
}
```

**配置文件：**

```xml
	<!-- 配置c3po连接池 -->
	<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
		<!-- 注入属性值 -->
		<property name="driverClass" value="com.mysql.jdbc.Driver"></property>
		<property name="jdbcUrl" value="jdbc:mysql://localhost:3306/wangyiyun"></property>
		<property name="user" value="root"></property>
		<property name="password" value="153963"></property>
	</bean>
	<!-- 第一步：配置事务管理器 (和配置文件方式一样)-->
	<bean id="dataSourceTransactionManager"
		class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<!-- 注入dataSource -->
		<property name="dataSource" ref="dataSource"></property>
	</bean>
	<!-- 第二步： 开启事务注解 -->
	<tx:annotation-driven transaction-manager="dataSourceTransactionManager" />
	<!-- 第三步 在方法所在类上加注解 -->
	
	
	<!-- 对象生成及属性注入 -->
	<bean id="ordersService" class="cn.itcast.service.OrdersService">
		<property name="ordersDao" ref="ordersDao"></property>
	</bean>
	<bean id="ordersDao" class="cn.itcast.dao.OrdersDao">
		<property name="jdbcTemplate" ref="jdbcTemplate"></property>
	</bean>
	<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
		<property name="dataSource" ref="dataSource"></property>
	</bean>
```

## 3. Spring单例和线程安全

具体见博客(又是一堆源码)[由Spring框架中的单例模式想到的](http://www.cnblogs.com/chengxuyuanzhilu/p/6404991.html)

## 4. Spring Bean

bean就是由于IOC容器初始化、转配及管理的对象，除此之外，bean就是 与应用程序中的其他对象没有什么区别了。而bean的定义即bean相互间的依赖关系通过配置元数据描述。

Spring的bean默认是**单例**的，这些单例的bean在多线程下如何保证单例？Spring单例是基于BeanFactory也就是Spring容器的，单例Bean在此容器内只有一个，**java的单例是基于jvm，每个jvm内只有一个实例。** 但是在类是易变的情况下，单例的对象会被污染，此时使用单例模式不是特别合适，此时Spring定义了各种作用域的Bean。

## 4.1 Bean的作用域

![img](https://camo.githubusercontent.com/adf4379800711a819fda44c2f478c27469ee5a86/687474703a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f31382d392d31372f313138383335322e6a7067)

五种作用域中，**request、session** 和 **global session** 三种作用域仅在基于web的应用中使用（不必关心你所采用的是什么web应用框架），只能用在基于 web 的 Spring ApplicationContext 环境。

#### 4.1.1 singleton-唯一bean实例（默认的作用域）

当一个Bean的作用域为singleton，那么spring ioc容器只会存在一个bean实例，并且所有对bean的请求，只要id与该bean定义相匹配，则只会返回bean的同一实例。定义方式为：

```xml
<bean id="ServiceImpl" class="cn.ServiceImple" scope="singleton">
```

或者基于@Scope注解方式

```java
@Service
@Scope("singleton")
public class ServiceImpl {

}
```

#### 4.1.2 prototype - 每次请求都会创建一个新的bean实例

**当一个bean的作用域为 prototype，表示一个 bean 定义对应多个对象实例。** **prototype 作用域的 bean 会导致在每次对该 bean 请求**（将其注入到另一个 bean 中，或者以程序的方式调用容器的 getBean() 方法**）时都会创建一个新的 bean 实例。

```xml
<bean id="account" class="com.foo.DefaultAccount" scope="prototype"/>  
 或者
<bean id="account" class="com.foo.DefaultAccount" singleton="false"/> 
```

或者使用@Scope注解方式

#### 4.1.3 request - 每一次Http请求都会产生一个新的Bean实例，该bean实例仅在当前Http request内有效

**request只适用于Web程序，每一次 HTTP 请求都会产生一个新的bean，同时该bean仅在当前HTTP request内有效，当请求结束后，该对象的生命周期即告结束。**

```xml
<bean id="loginAction" class=cn.csdn.LoginAction" scope="request"/>
```

#### 4.1.4  session——每一次HTTP请求都会产生一个新的 bean，该bean仅在当前 HTTP session 内有效

**session只适用于Web程序，session 作用域表示该针对每一次 HTTP 请求都会产生一个新的 bean，同时该 bean 仅在当前 HTTP session 内有效.与request作用域一样，可以根据需要放心的更改所创建实例的内部状态，而别的 HTTP session 中根据 userPreferences 创建的实例，将不会看到这些特定于某个 HTTP session 的状态变化。当HTTP session最终被废弃的时候，在该HTTP session作用域内的bean也会被废弃掉。**

```xml
<bean id="userPreferences" class="com.foo.UserPreferences" scope="session"/>
```

#### 4.1.5 globalSession

global session 作用域类似于标准的 HTTP session 作用域，不过仅仅在**基于 portlet 的 web 应用中才有意义**。

```xml
<bean id="user" class="com.foo.Preferences "scope="globalSession"/>
```

### 4.2 Bean的生命周期

#### 4.2.1 如何在initialization和destroy之前做一些事情？

##### 4.2.1.1 实现InitializingBean和DisposableBean接口

```java
public class GiraffeService implements InitializingBean,DisposableBean {
    @Override
    public void afterPropertiesSet() throws Exception {
        System.out.println("执行InitializingBean接口的afterPropertiesSet方法");
    }
    @Override
    public void destroy() throws Exception {
        System.out.println("执行DisposableBean接口的destroy方法");
    }
}
```

但是这种方式会导致和Spring框架紧耦合，所以不建议使用。

##### 4.2.1.2 在bean的配置文件中指定init-method和destroy-method方法

```java
public class GiraffeService {
    //通过<bean>的destroy-method属性指定的销毁方法
    public void destroyMethod() throws Exception {
        System.out.println("执行配置的destroy-method");
    }
    //通过<bean>的init-method属性指定的初始化方法
    public void initMethod() throws Exception {
        System.out.println("执行配置的init-method");
    }
}
```

配置文件中的配置（方法可以配置为类中任意的方法）：

```xml
<bean name="giraffeService" class="com.giraffe.spring.service.GiraffeService" init-method="initMethod" destroy-method="destroyMethod">
</bean>
```

自定义的init和des方法能够抛出异常，但是不能添加参数。

##### 4.2.1.3 使用@PostConstruct和@PreDestroy注解

```java
public class GiraffeService {
    @PostConstruct
    public void initPostConstruct(){
        System.out.println("执行PostConstruct注解标注的方法");
    }
    @PreDestroy
    public void preDestroy(){
        System.out.println("执行preDestroy注解标注的方法");
    }
}
```

配置文件：

```xml
<bean class="org.springframework.context.annotation.CommonAnnotationBeanPostProcessor" />
```

### 4.3 实现Aware接口在Bean中使用Spring框架中的一些对象

#### 4.3.1 重要的接口

- **ApplicationContextAware**: 获得ApplicationContext对象,可以用来获取所有Bean definition的名字。
- **BeanFactoryAware**:获得BeanFactory对象，可以用来检测Bean的作用域。
- **BeanNameAware**:获得Bean在配置文件中定义的名字。
- **ResourceLoaderAware**:获得ResourceLoader对象，可以获得classpath中某个文件。
- **ServletContextAware**:在一个MVC应用中可以获取ServletContext对象，可以读取context中的参数。
- **ServletConfigAware**： 在一个MVC应用中可以获取ServletConfig对象，可以读取config中的参数。

#### 4.3.2 BeanPostProcessor

上面的Aware接口是针对某个实现这些接口的Bean定制初始化的过程， Spring同样可以针对容器中的所有Bean，或者某些Bean**定制初始化过程**，只需提供一个实现BeanPostProcessor接口的类即可。 

```java
public class CustomerBeanPostProcessor implements BeanPostProcessor {
    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("执行BeanPostProcessor的postProcessBeforeInitialization方法,beanName=" + beanName);
        return bean;
    }
    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("执行BeanPostProcessor的postProcessAfterInitialization方法,beanName=" + beanName);
        return bean;
    }
}
```

要将BeanPostProcessor的Bean一样定义在配置文件中

```xml
<bean class="com.giraffe.spring.service.CustomerBeanPostProcessor"/>
```

### 4.4 Bean的生命周期

![img](https://camo.githubusercontent.com/d275f148f8928ccc284180731f90991891be1a35/687474703a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f31382d392d31372f34383337363237322e6a7067)

![img](https://camo.githubusercontent.com/a3d4415162d30d4659779f6db3717f9a68fd3c97/687474703a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f31382d392d31372f353439363430372e6a7067)

- Bean容器找到配置文件中 Spring Bean 的定义。
- Bean容器利用Java Reflection API创建一个Bean的实例。
- 如果涉及到一些属性值 利用set方法设置一些属性值。
- 如果Bean实现了BeanNameAware接口，调用setBeanName()方法，传入Bean的名字。
- 如果Bean实现了BeanClassLoaderAware接口，调用setBeanClassLoader()方法，传入ClassLoader对象的实例。
- 如果Bean实现了BeanFactoryAware接口，调用setBeanClassLoader()方法，传入ClassLoader对象的实例。
- 与上面的类似，如果实现了其他*Aware接口，就调用相应的方法。
- 如果有和加载这个Bean的Spring容器相关的BeanPostProcessor对象，执行postProcessBeforeInitialization()方法
- 如果Bean实现了InitializingBean接口，执行afterPropertiesSet()方法。
- 如果Bean在配置文件中的定义包含init-method属性，执行指定的方法。
- 如果有和加载这个Bean的Spring容器相关的BeanPostProcessor对象，执行postProcessAfterInitialization()方法
- 当要销毁Bean的时候，如果Bean实现了DisposableBean接口，执行destroy()方法。
- 当要销毁Bean的时候，如果Bean在配置文件中的定义包含destroy-method属性，执行指定的方法。

#### 4.4.1  单例管理的对象

当scope=”singleton”，即默认情况下，会在启动容器时（即实例化容器时）时实例化。但我们可以指定Bean节点的lazy-init=”true”来延迟初始化bean，这时候，只有在第一次获取bean时才会初始化bean，即第一次请求该bean时才初始化。**创建和回收都交由IOC容器负责**

#### 4.4.2  非单例管理的对象

当`scope=”prototype”`时，容器也会延迟初始化 bean，Spring 读取xml 文件的时候，并不会立刻创建对象，而是在第一次请求该 bean 时才初始化（如调用getBean方法时）。在第一次请求每一个 prototype 的bean 时，Spring容器都会调用其构造器创建这个对象，然后调用`init-method`属性值中所指定的方法。**对象销毁的时候，Spring 容器不会帮我们调用任何方法，因为是非单例，这个类型的对象有很多个，Spring容器一旦把这个对象交给你之后，就不再管理这个对象了。**

即创建是相同的，但是删除已由程序开发者自己负责

**如果 bean 的 scope 设为prototype时，当容器关闭时，destroy 方法不会被调用。对于 prototype 作用域的 bean，有一点非常重要，那就是 Spring不能对一个 prototype bean 的整个生命周期负责：容器在初始化、配置、装饰或者是装配完一个prototype实例后，将它交给客户端，随后就对该prototype实例不闻不问了，Spring容器不在跟踪其生命周期。**

**清除prototype作用域的对象并释放任何prototype bean所持有的昂贵资源，都是客户端代码的职责**

## 5. MVC工作原理详解

![img](https://camo.githubusercontent.com/0ce15ef13ad65c271362ec48b2ac573a6811c98b/687474703a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f31382d31302d31312f36303637393434342e6a7067)

### 5.1 Spring MVC使用

需要在 web.xml 中配置 DispatcherServlet 。并且需要配置 Spring 监听器ContextLoaderListener

```xml
<listener>
	<listener-class>org.springframework.web.context.ContextLoaderListener
	</listener-class>
</listener>
<servlet>
	<servlet-name>springmvc</servlet-name>
	<servlet-class>org.springframework.web.servlet.DispatcherServlet
	</servlet-class>
	<!-- 如果不设置init-param标签，则必须在/WEB-INF/下创建xxx-servlet.xml文件，其中xxx是servlet-name中配置的名称。 -->
	<init-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>classpath:spring/springmvc-servlet.xml</param-value>
	</init-param>
	<load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
	<servlet-name>springmvc</servlet-name>
	<url-pattern>/</url-pattern>
</servlet-mapping>
```

### 5.2 SpringMVC 工作原理

**简单来说：**

客户端发送请求-> 前端控制器 DispatcherServlet 接受客户端请求 -> 找到处理器映射 HandlerMapping 解析请求对应的 Handler-> HandlerAdapter 会根据 Handler 来调用真正的处理器开处理请求，并处理相应的业务逻辑 -> 处理器返回一个模型视图 ModelAndView -> 视图解析器进行解析 -> 返回一个视图对象->前端控制器 DispatcherServlet 渲染数据（Moder）->将得到视图对象返回给用户

**如下图所示：** [![SpringMVC运行原理](https://camo.githubusercontent.com/6889f839138de730fce5f6a0d64e33258a2cf9b5/687474703a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f31382d31302d31312f34393739303238382e6a7067)](https://camo.githubusercontent.com/6889f839138de730fce5f6a0d64e33258a2cf9b5/687474703a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f31382d31302d31312f34393739303238382e6a7067)

上图的一个笔误的小问题：Spring MVC 的入口函数也就是前端控制器 DispatcherServlet 的作用**是接收请求，响应结果。**

**流程说明（重要）：**

（1）客户端（浏览器）发送请求，直接请求到 DispatcherServlet。

（2）DispatcherServlet 根据请求信息调用 HandlerMapping，解析请求对应的 Handler。

（3）解析到对应的 Handler（也就是我们平常说的 Controller 控制器）后，开始由 HandlerAdapter 适配器处理。

（4）HandlerAdapter 会根据 Handler 来调用真正的处理器开始处理请求，并处理相应的业务逻辑。

（5）处理器处理完业务后，会返回一个 ModelAndView 对象，Model 是返回的数据对象，View 是个逻辑上的 View。

（6）ViewResolver 会根据逻辑 View 查找实际的 View。

（7）DispaterServlet 把返回的 Model 传给 View（视图渲染）。

（8）把 View 返回给请求者（浏览器）

### 5.3 SpringMVC重要组件说明

**1、前端控制器DispatcherServlet（不需要工程师开发）,由框架提供（重要）**

作用：**Spring MVC 的入口函数。接收请求，响应结果，相当于转发器，中央处理器。有了 DispatcherServlet 减少了其它组件之间的耦合度。用户请求到达前端控制器，它就相当于mvc模式中的c，DispatcherServlet是整个流程控制的中心，由它调用其它组件处理用户的请求，DispatcherServlet的存在降低了组件之间的耦合性。**

**2、处理器映射器HandlerMapping(不需要工程师开发),由框架提供**

作用：根据请求的url查找Handler。HandlerMapping负责根据用户请求找到Handler处理器（Controller），SpringMVC提供了不同的映射器实现不同的映射方式，例如：配置文件方式，实现接口方式，注解方式等。

**3、处理器适配器HandlerAdapter**

作用：按照特定规则（HandlerAdapter要求的规则）去执行Handler 通过HandlerAdapter对处理器进行执行，这是适配器模式的应用，通过扩展适配器可以对更多类型的处理器进行执行。

**4、处理器Handler(需要工程师开发)**

注意：编写Handler时按照HandlerAdapter的要求去做，这样适配器才可以去正确执行Handler Handler 是继DispatcherServlet前端控制器的后端控制器，在DispatcherServlet的控制下Handler对具体的用户请求进行处理。 由于Handler涉及到具体的用户业务请求，所以一般情况需要工程师根据业务需求开发Handler。

**5、视图解析器View resolver(不需要工程师开发),由框架提供**

作用：进行视图解析，根据逻辑视图名解析成真正的视图（view） View Resolver负责将处理结果生成View视图，View Resolver首先根据逻辑视图名解析成物理视图名即具体的页面地址，再生成View视图对象，最后对View进行渲染将处理结果通过页面展示给用户。 springmvc框架提供了很多的View视图类型，包括：jstlView、freemarkerView、pdfView等。 一般情况下需要通过页面标签或页面模版技术将模型数据通过页面展示给用户，需要由工程师根据业务需求开发具体的页面。

**6、视图View(需要工程师开发)**

View是一个接口，实现类支持不同的View类型（jsp、freemarker、pdf...）

**注意：处理器Handler（也就是我们平常说的Controller控制器）以及视图层view都是需要我们自己手动开发的。其他的一些组件比如：前端控制器DispatcherServlet、处理器映射器HandlerMapping、处理器适配器HandlerAdapter等等都是框架提供给我们的，不需要自己手动开发。**

### 5.4 DispatchServlet详细解析

DispatcherServlet类中的属性beans：

- HandlerMapping：用于handlers映射请求和一系列的对于拦截器的前处理和后处理，大部分用@Controller注解。
- HandlerAdapter：帮助DispatcherServlet处理映射请求处理程序的适配器，而不用考虑实际调用的是 哪个处理程序。
- ViewResolver：根据实际配置解析实际的View类型。
- ThemeResolver：解决Web应用程序可以使用的主题，例如提供个性化布局。
- MultipartResolver：解析多部分请求，以支持从HTML表单上传文件。-
- FlashMapManager：存储并检索可用于将一个请求属性传递到另一个请求的input和output的FlashMap，通常用于重定向。

#### 5.4.1 HandlerMapping

[![HandlerMapping](https://camo.githubusercontent.com/fa01f5e13dd0380697bfcac4116d5d3a3c456dd2/687474703a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f31382d31302d31312f39363636363136342e6a7067)](https://camo.githubusercontent.com/fa01f5e13dd0380697bfcac4116d5d3a3c456dd2/687474703a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f31382d31302d31312f39363636363136342e6a7067)

HandlerMapping接口处理请求的映射HandlerMapping接口的实现类：

- SimpleUrlHandlerMapping类通过配置文件把URL映射到Controller类。
- DefaultAnnotationHandlerMapping类通过注解把URL映射到Controller类。

#### 5.4.2 HandlerAdapter

[![HandlerAdapter](https://camo.githubusercontent.com/ef6a14e4c8748d0e281f69f280461ab254925c2b/687474703a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f31382d31302d31312f39313433333130302e6a7067)](https://camo.githubusercontent.com/ef6a14e4c8748d0e281f69f280461ab254925c2b/687474703a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f31382d31302d31312f39313433333130302e6a7067)

HandlerAdapter接口-处理请求映射

AnnotationMethodHandlerAdapter：通过注解，把请求URL映射到Controller类的方法上。

#### 5.4.3 **HandlerExceptionResolver**

[![HandlerExceptionResolver](https://camo.githubusercontent.com/f141ed48dc7da1b82884970d2c398494a2afeeca/687474703a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f31382d31302d31312f35303334333838352e6a7067)](https://camo.githubusercontent.com/f141ed48dc7da1b82884970d2c398494a2afeeca/687474703a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f31382d31302d31312f35303334333838352e6a7067)

HandlerExceptionResolver接口-异常处理接口

- SimpleMappingExceptionResolver通过配置文件进行异常处理。
- AnnotationMethodHandlerExceptionResolver：通过注解进行异常处理。

#### 5.4.4 ViewResolver

[![ViewResolver](https://camo.githubusercontent.com/7c2907a6ca88d25ebea98d488318f72b89de5176/687474703a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f31382d31302d31312f34393439373237392e6a7067)](https://camo.githubusercontent.com/7c2907a6ca88d25ebea98d488318f72b89de5176/687474703a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f31382d31302d31312f34393439373237392e6a7067)

ViewResolver接口解析View视图。

UrlBasedViewResolver类 通过配置文件，把一个视图名交给到一个View来处理。