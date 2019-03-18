# Spring 学习 -- SpEL 表达式

部分摘抄自[博客](https://www.cnblogs.com/goodcheap/p/6490896.html)

**目录**

**Spring 表达式语言 (简称 SpEL)：**是一个支持运行时查询和操作对象图的强大的表达式语言。

**语法类似于 EL：**SpEL 使用 #{...} 作为定界符 , 所有在大括号中的字符都将被认为是 SpEL , SpEL 为 bean 的属性进行动态赋值提供了便利。

**通过 SpEL 可以实现：**

- 通过 bean 的 id 对 bean 进行引用。
- 调用方式以及引用对象中的属性。
- 计算表达式的值
- 正则表达式的匹配。
Xml 使用的格式如下所示：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="car" class="com.itdjx.spring.spel.Car">
        <property name="brand" value="#{'玛莎拉蒂'}"></property>
        <property name="price" value="#{32000.78}"></property>
        <property name="perimeter" value="#{T(java.lang.Math).PI * 75.8f}"></property>
    </bean>

    <bean id="person" class="com.itdjx.spring.spel.Person">
        <property name='name' value='#{"华崽儿"}'></property>
        <property name="age" value="#{25}"></property>
        <property name="marriage" value="#{car.price > 400000 and age > 30}"></property>
        <property name="car" value="#{car}"></property>
        <property name="socialStatus" value="#{car.price > 30000 ? '金领' : '白领'}"></property>
        <property name="address" value="#{address.province + '省' + address.city + '市' + address.area + '区'}"/>
    </bean>

    <bean id="address" class="com.itdjx.spring.spel.Address">
        <property name="province" value="#{'辽宁'}"/>
        <property name="city" value="#{'大连'}"/>
        <property name="area" value="#{'沙河口'}"/>
    </bean>

</beans>
```

 **SpEL 字面量：**

- 整数：#{8}
- 小数：#{8.8}
- 科学计数法：#{1e4}
- String：可以使用单引号或者双引号作为字符串的定界符号。
- Boolean：#{true}

**SpEL 引用 bean , 属性和方法：**

- 引用其他对象:#{car}
- 引用其他对象的属性：#{car.brand}
- 调用其它方法 , 还可以链式操作：#{car.toString()}
- 调用静态方法静态属性：#{T(java.lang.Math).PI}

 **SpEL 支持的运算符号：**

- 算术运算符：+，-，*，/，%，^(加号还可以用作字符串连接)
- 比较运算符：<,> , == , >= , <= , lt , gt , eg , le , ge
- 逻辑运算符：and , or , not , |
- if-else 运算符 (类似三目运算符)：？:(temary), ?:(Elvis)
- 正则表达式：#{admin.email matches '[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\\.[a-zA-Z]{2,4}'}
- 其他类型 ： ?.，?[…]，![…]，^[…]，$[…]


以下部分来自于[博客](https://blog.csdn.net/ya_1249463314/article/details/68484422)
1. 什么是 SpEL 表达式
SpEL 表达式语言是一种表达式语言，是一种可以与一个基于 spring 的应用程序中的运行时对象交互的东西。有点类似于 ognl 表达式。总得来说 SpEL 表达式是一种简化开发的表达式，通过使用表达式来简化开发，减少一些逻辑、配置的编写。

2. SpEL 表达式使用方式
（1）xml 配置的方式：
（2）采用注解的方式

3. 分析器
SpEL 上下文中所定义的每一个表达式都应该首先被解析，然后在被评估。解析的过程采用了 ExpressionParser 接口的分析器进行处理的。SpelExpressionParser 主要负责将字符串表达式解析到已编译的 Expression 对象中。（创建的分析器实例是线程安全）

代码形式：

```java
ExpressionParser parser =new SpelExpressionParser();
```

注意：
在 SpEL 表达式中，默认情况下，表达式前缀为 '#' ，而后缀为 '}' 。如果表达式中没有前缀和后缀，那么表达式字符串就被当作纯文本。

分析器解析 Spel 中纯文本表达式 HelloWorld：
```java
public class HelloWorldTest {
	ExpressionParser parser;
	
	@Before
	public void setup(){
		//初始化创建SpEL表达式解析器
		parser =new SpelExpressionParser();
	}
	
	@Test
	public void test(){
		//使用解析器解析出表达式exp
		Expression exp=parser.parseExpression("'Hello World'");
		//在表达式中获取指定类型的值
		String value =exp.getValue(String.class);
		assertThat(value ,is("Hello World"));
	}
}
```
junit 测试结果：
![](https://img-blog.csdn.net/20170403140538386?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWFfMTI0OTQ2MzMxNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


4.  SpEL 表达式的调用方法
（1）使用 SpEL 调用普通方法
就是 SpEL 也支持在表达式中采用方法调用的形式

范例：

User.java：

```java
public class User {
	private String username;
	private String password;
	public void setUsername(String username) {
		this.username = username;
	}
	public void setPassword(String password) {
		this.password = password;
	}
	public String getUsername() {
		return "李四";
	}
	public String getPassword() {
		return "lisi123";
	}
	public void printUser(){
		System.out.println("当前用户的用户名："+username+" ,密码："+password);
	}
}
```
applicationContext.xml 配置文件：

```xml
		<bean id="user1" class="cn.spy.spel.method.User">
			<property name="username" value="张三"></property>
			<property name="password" value="zhangsan123"></property>
		</bean>		
				
		<bean id="user2" class="cn.spy.spel.method.User">
			<property name="username" value="#{user2.getUsername()}"></property>
			<property name="password" value="#{user2.getPassword()}"></property>
		</bean>	
```
测试：

```java
public class TestMethod {
	public static void main(String[] args) {
		ApplicationContext context =new ClassPathXmlApplicationContext("applicationContext.xml");
		User user1 =(User) context.getBean("user1");
		user1.printUser();
		User user2 =(User) context.getBean("user2");
		user2.printUser();
	}
}
```

结果：

![](https://img-blog.csdn.net/20170401170650241?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWFfMTI0OTQ2MzMxNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)





（2）使用 SpEL 调用构造方法

```java
@Test
public void test(){
	ExpressionParser parser =new SpelExpressionParser();
	Expression exp=parser.parseExpression("new Double(3.1415926)");
	Double value =exp.getValue(Double.class);
	assertThat(value ,is(3.1415926));
}
```



（3）使用 SpEL 调用静态方法

```java
@Test
public void test(){
	ExpressionParser parser =new SpelExpressionParser();
	Expression exp=parser.parseExpression("T(java.lang.Math).abs(-1)");
	Integer value =exp.getValue(Integer.class);
	assertThat(value ,is(1));
}
```



6. 使用 SpEL 表达式调用变量和函数
  （1）# 变量的表达式使用
  SpEL 使用**上下文 StandardEvaluationContext** 查找表达式中存在的变量。在表达式中使用变量名称前添加一个标签前缀 #，使用已注册的变量。

  ```java
  public class TestSpEL {
  	@Test
  	public void test(){
  		ExpressionParser parser =new SpelExpressionParser();
  		StandardEvaluationContext context =new StandardEvaluationContext();
  		context.setVariable("message", "Hello World");
  		String value =parser.parseExpression("#message").getValue(context, String.class);
  		assertThat(value, is("Hello World"));
  	}
  }
  ```

（2）#root 表达式的使用
可以在上下文中设置一个对象评估，可以使用 #root 访问该对象。

```java
public class TestSpEL {
	@Test
	public void test(){
		ExpressionParser parser =new SpelExpressionParser();
		StandardEvaluationContext context =new StandardEvaluationContext();
		context.setRootObject(new Var());
		Assert.assertTrue(parser.parseExpression("#root").getValue(context) instanceof Var);
	}
}
```

（3）访问系统的属性和环境
SpEL 预定义变量：systemProperties 和 systemEnvironment

```java
parser.parseExpression("@systemProperties['java.version']").getValue(context);
parser.parseExpression("@systemProperties[JAVA_HOME]").getValue(context);
```