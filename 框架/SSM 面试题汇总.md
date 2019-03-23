# SSM+Spring Boot 面试题汇总

## 1. Spring

### 1.1 基本问题

#### 1.1.1 怎么理解IOC和DI？以及作用？

> **两者其实同一个概念的不同角度描述**
>
> 所谓IoC，对于spring框架来说，就是**由spring（容器）来负责控制对象的生命周期和对象间的关系**，运行时被装配器对象来绑定耦合对象的一种编程技巧，对象之间耦合关系在编译时通常是未知的。
>
> IoC的一个重点是在系统运行中，动态的向某个对象提供它所需要的其他对象。这一点是通过DI（Dependency Injection，依赖注入）来实现的，**由容器帮我们查找及注入依赖对象，对象只是被动的接受依赖对象**
>
> **目的/作用**：依赖注入的目的并非为软件系统带来更多功能，而是为了**提升组件重用的频率，降低耦合度**，并为系统搭建一个灵活、可扩展的平台

#### 1.1.2 Spring事务，事务的作用

> • 编程式事务管理：这意味你通过编程的方式管理事务，给你带来极大的灵活性，但是难维护。 
> • 声明式事务管理：这意味着你可以将业务代码和事务管理分离，你只需用注解和XML配置来管理事务。

#### 1.1.3 Sping注解

> @Controller --->表现层的Bean
> @Service ---->对应是业务层的Bean，生命类是一个Bean，类的Bean的id是类名首字母小写
> @Component ------>受Spring管理的组件的通用形式
>
> @Repository------>对应的是持久层的Bean
>
> @RequestMapping ---->会将 HTTP 请求映射到 MVC 和 REST 控制器的处理方法上，支持统配符以及ANT风格的路径，Spring4.3引入了RESTful风格的注解形式
> @Resource---->功能与Autowired类似，但是模式是通过Name匹配，属于j2EE的注解
>
> @Autowired --->清除getter/setter与bean属性中的property，匹配时默认使用的是类型，可以添加required参数控制是否一定要找到注入，可以搭配@Qualifier注解来指定Bean的名称
> @ResponseBody -----> 通过适当的转换器转换为指定的格式之后，写入到response对象的body区，通常用来返回JSON或者XML数据
> @Transactional------>声明事务
>
> @PostConstruct 初始化注解
> @PreDestroy 摧毁注解 默认 单例  启动就加载
> @Async异步方法调用
> @Scope用于指定scope作用域的（用在类上）
> @PostConstruct用于指定初始化方法（用在方法上）
> @PreDestory用于指定销毁方法（用在方法上）
> @DependsOn：定义Bean初始化及销毁时的顺序
> @Primary：自动装配时当出现多个Bean候选者时，被注解为@Primary的Bean将作为首选者，否则将抛出异常
> @Lazy(true) 表示延迟初始化
> @Configuration把一个类作为一个IoC容器，它的某个方法头上如果注册了@Bean，就会作为这个Spring容器中的Bean。
> @Scope注解 作用域

#### 1.1.4 Spring DI注入几种方式

> (1)构造器注入：通过构造方法初始化 
> < constructor-arg name=”dao”< /constructor-arg> 
> (2)setter注入：通过setter方法初始化注入 
> < property name=”dao” ref=”dao2”>< / property > 
>
> (3) 接口注入

#### 1.1.5 Spring框架能够带来的好处？

```
使得各部分之间的依赖关系一目了然
相比较于EJB容器，更加轻量
利用已有的技术，如ORM框架、logging框架等
框架按模块来进行组织，开发者仅需要选择需要的模块就好
测试非常方便
便捷的事务管理接口
提供WEB MVC框架
```

#### 1.1.6 BeanFactory和ApplicationContext有什么区别？

> BeanFactory 可以理解为含有bean集合的工厂类。BeanFactory 包含了种bean的定义，以便在接收到客户端请求时将对应的bean实例化。BeanFactory还能在实例化对象的时生成协作类之间的关系。此举将bean自身与bean客户端的配置中解放出来。BeanFactory还包含了bean生命周期的控制，调用客户端的初始化方法（initialization methods）和销毁方法（destruction methods）。
>
> ApplicationContext在BeanFactory的基础上加了其他功能（支持国际化的文本消息、统一的资源文件读取方式、已在监听器中注册的Bean事件），其具有以下的三种实现： ClassPathXmlApplicationContext、FileSystemXmlApplicationContext、XmlWebApplicationContext

#### 1.1.7 Spring的几种配置方式

> - 基于XML的配置，最常见的形式
>
> - 基于java的配置，
>
> ```java
> @Configuration   
> @ComponentScan(basePackages = "com.somnus")   //可以使用这个注解进行组件扫描
> public class AppConfig{    
>     @Bean    
>     public MyService myService() {    
>         return new MyServiceImpl();    
>     }    
> }   
> // 对应的ApplicationContext为：
> ApplicationContext ctx = new AnnotationConfigApplicationContext(AppConfig.class);    
> ```
>
> web环境下，需要进行如下配置
>
> ```xml
> <web-app>    
>     <!-- Configure ContextLoaderListener to use AnnotationConfigWebApplicationContext    
>         instead of the default XmlWebApplicationContext -->    
>     <context-param>    
>         <param-name>contextClass</param-name>    
>         <param-value>    
>             org.springframework.web.context.support.AnnotationConfigWebApplicationContext    
>         </param-value>    
>     </context-param>    
>      
>     <!-- Configuration locations must consist of one or more comma- or space-delimited    
>         fully-qualified @Configuration classes. Fully-qualified packages may also be    
>         specified for component-scanning -->    
>     <context-param>    
>         <param-name>contextConfigLocation</param-name>    
>         <param-value>com.howtodoinjava.AppConfig</param-value>    
>     </context-param>    
>      
>     <!-- Bootstrap the root application context as usual using ContextLoaderListener -->    
>     <listener>    
>         <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>    
>     </listener>    
>      
>     <!-- Declare a Spring MVC DispatcherServlet as usual -->    
>     <servlet>    
>         <servlet-name>dispatcher</servlet-name>    
>         <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>    
>         <!-- Configure DispatcherServlet to use AnnotationConfigWebApplicationContext    
>             instead of the default XmlWebApplicationContext -->    
>         <init-param>    
>             <param-name>contextClass</param-name>    
>             <param-value>    
>                 org.springframework.web.context.support.AnnotationConfigWebApplicationContext    
>             </param-value>    
>         </init-param>    
>         <!-- Again, config locations must consist of one or more comma- or space-delimited    
>             and fully-qualified @Configuration classes -->    
>         <init-param>    
>             <param-name>contextConfigLocation</param-name>    
>             <param-value>com.howtodoinjava.web.MvcConfig</param-value>    
>         </init-param>    
>     </servlet>    
>      
>     <!-- map all requests for /app/* to the dispatcher servlet -->    
>     <servlet-mapping>    
>         <servlet-name>dispatcher</servlet-name>    
>         <url-pattern>/app/*</url-pattern>    
>     </servlet-mapping>    
> </web-app>
> ```
>
> - 基于注解的配置
>
> ```xml
> <!-- 默认是关闭的，需要进行如下配置来开启 -->
> <beans>    
>    <context:annotation-config/>    
>    <!-- bean definitions go here -->    
> </beans>    
> ```

#### 1.1.8 Spring 生命周期

Bean的生命周期由两组回调方法组成：

- 初始化之后调用的回调方法。
- 销毁之前调用的回调方法。

**如何管理？**

- InitializingBean和DisposableBean回调接口
- 针对特殊行为的其他Aware接口
- Bean配置文件中的Custom init()方法和destroy()方法
- @PostConstruct和@PreDestroy注解方式

#### 1.1.9 Spring Bean的作用域有什么区别？

> 1. singleton：这种bean范围是默认的，这种范围确保不管接受到多少个请求，每个容器中只有一个bean的实例，单例的模式由bean factory自身来维护。
> 2. prototype：原形范围与单例范围相反，为每一个bean请求提供一个实例。
> 3. request：在请求bean范围内会每一个来自客户端的网络请求创建一个实例，在请求完成以后，bean会失效并被垃圾回收器回收。
> 4. Session：与请求范围类似，确保每个session中有一个bean的实例，在session过期后，bean会随之失效。
> 5. global-session：global-session和Portlet应用相关。当你的应用部署在Portlet容器中工作时，它包含很多portlet。如果你想要声明让所有的portlet共用全局的存储变量的话，那么这全局变量需要存储在global-session中。

#### 1.1.10 什么是Spring inner Beans?

在Spring框架中，无论何时bean被使用时，当仅被调用了一个属性。

```xml
<bean id="CustomerBean" class="com.somnus.common.Customer">    
    <property name="person">    
        <!-- This is inner bean -->    
        <bean class="com.howtodoinjava.common.Person">    
            <property name="name" value="lokesh" />    
            <property name="address" value="India" />    
            <property name="age" value="34" />    
        </bean>    
    </property>    
</bean> 
```

#### 1.1.11 如何在Spring中注入一个java Collection?

```xml
<beans>    
   <!-- Definition for javaCollection -->    
   <bean id="javaCollection" class="com.howtodoinjava.JavaCollection">    
      <!-- java.util.List -->    
      <property name="customList">    
        <list>    
           <value>INDIA</value>    
           <value>Pakistan</value>    
           <value>USA</value>    
           <value>UK</value>    
        </list>    
      </property>    
     
     <!-- java.util.Set -->    
     <property name="customSet">    
        <set>    
           <value>INDIA</value>    
           <value>Pakistan</value>    
           <value>USA</value>    
           <value>UK</value>    
        </set>    
      </property>    
     
     <!-- java.util.Map -->    
     <property name="customMap">    
        <map>    
           <entry key="1" value="INDIA"/>    
           <entry key="2" value="Pakistan"/>    
           <entry key="3" value="USA"/>    
           <entry key="4" value="UK"/>    
        </map>    
      </property>    
     
    <!-- java.util.Properties -->    
    <property name="customProperies">    
        <props>    
            <prop key="admin">admin@nospam.com</prop>    
            <prop key="support">support@nospam.com</prop>    
        </props>    
    </property>    
     
   </bean>    
</beans>    
```

#### 1.1.12 构造方法注入和设值注入有什么区别？

> 1. 在设值注入方法支持大部分的依赖注入，在构造方法中不支持大部分的依赖注入（调用构造函数时参数需要完整）
> 2. 设值注入会重写构造方法的值
> 3. 设值法不能保证某种依赖已经注入
> 4. 构造器注入时如果存在互相依赖，则会抛出异常，而采用设值法解决了循环依赖的问题

#### 1.1.13 Spring 框架中有哪些不同类型的事件？

Spring的`ApplicationContext` 提供了支持事件和代码中监听器的功能。如果一个bean实现了`ApplicationListener`接口，当一个`ApplicationEvent` 被发布以后，bean会自动被通知

> 1. 上下文更新事件（ContextRefreshedEvent）：该事件会在ApplicationContext被初始化或者更新时发布。也可以在调用ConfigurableApplicationContext 接口中的refresh()方法时被触发。
> 2. 上下文开始事件（ContextStartedEvent）：当容器调用ConfigurableApplicationContext的Start()方法开始/重新开始容器时触发该事件。
> 3. 上下文停止事件（ContextStoppedEvent）：当容器调用ConfigurableApplicationContext的Stop()方法停止容器时触发该事件。
> 4. 上下文关闭事件（ContextClosedEvent）：当ApplicationContext被关闭时触发该事件。容器被关闭时，其管理的所有单例Bean都被销毁。
> 5. 请求处理事件（RequestHandledEvent）：在Web应用中，当一个http请求（request）结束触发该事件。
> 6. 通过继承ApplicationEvent,实现自定义的事件类型。

#### 1.1.14 Spring中用到的设计模式？

> - 代理模式—在AOP和remoting中被用的比较多。
> - 单例模式—在spring配置文件中定义的bean默认为单例模式。
> - 模板方法—用来解决代码重复的问题。比如. [RestTemplate](http://howtodoinjava.com/2015/02/20/spring-restful-client-resttemplate-example/), `JmsTemplate`, `JpaTemplate。`
> - 工厂模式—BeanFactory用来创建对象的实例。
>
> 
>
> - 前端控制器—Spring提供了`DispatcherServlet``来对请求进行分发。`
> - 视图帮助(View Helper )—Spring提供了一系列的JSP标签，高效宏来辅助将分散的代码整合在视图里。
> - 依赖注入—贯穿于`BeanFactory` / `ApplicationContext``接口的核心理念。`

## 2. Spring MVC

### 2.1 基本问题

#### 2.1.1 Spring MVC个工作流程

>1. 用户发送请求至前端控制器**DispatcherServlet**
>2. DispatcherServlet收到请求调用**HandlerMapping处理器映射器**。
>3. 处理器映射器根据请求**url找到具体的处理器**，生成**处理器对象**及**处理器拦截器**(如果有则生成)一并返回给**DispatcherServlet**。
>4. DispatcherServlet通过**HandlerAdapter处理器[适配器](https://www.baidu.com/s?wd=%E9%80%82%E9%85%8D%E5%99%A8&tn=24004469_oem_dg&rsv_dl=gh_pl_sl_csd)调用处理器**
>5. **执行处理器(Controller，也叫后端控制器)**。
>6. Controller执行完成返回**ModelAndView**
>7. HandlerAdapter将controller执行结果**ModelAndView返回给DispatcherServlet**
>8. DispatcherServlet**将ModelAndView传给ViewReslover视图解析器**
>9. ViewReslover解析后返回**具体View**
>10. DispatcherServlet对View进行**渲染视图**（即将模型数据填充至视图中）。
>11. DispatcherServlet**响应用户**

![这里写图片描述](https://img-blog.csdn.net/20180720135841385?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxMzgyNDk1NDE0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

#### 2.1.2 如果你也用过struts2.简单介绍下springMVC和struts2的区别有哪些?

>1. springmvc的入口是一个servlet即**前端控制器**，而struts2入口是一个**filter过虑器**。
>2. springmvc是**基于方法开发**(一个url对应一个方法)，请求参数传递到方法的形参，可以设计为单例或多例(建议单例)，struts2是**基于类开发**，传递参数是通过类的属性，只能设计为多例。
>3. Struts采用**值栈存储请求和响应的数据**，通过OGNL存取数据， springmvc通过**参数解析器**是将request请求内容解析，并给方法形参赋值，将**数据和视图封装成ModelAndView对象**，最后又将ModelAndView中的模型数据**通过reques域传输到页面**。Jsp视图解析器默认使用jstl。

#### 2.1.3 Spring MVC控制器是否是单例模式，如果是，有什么问题，怎么解决？

> 是单例模式，在多线程的访问的情况下有线程安全问题，尽量不要使用同步，会影响性能，解决方法是在控制器里面不能写字段。

#### 2.1.4 如何定向或者转发？

返回值前面添加：“forward:”或者“redirect:”，例如“redirect:[http://www.uu456.com](http://www.uu456.com/)”

#### 2.1.5 如何把数据放入到Session里面

> 可以声明一个request,或者session**先拿到session,然后就可以放入数据**,或者可以在类上面加上**@SessionAttributes注解**,里面包含的字符串就是要放入session里面的key

#### 2.1.6 当一个方法向AJAX返回对象,譬如Object,List等,需要做什么处理

> 要加上@ResponseBody注解,调用合适的编码器

#### 2.1.7 Spring MVC Framework特点

> - 基于组件技术
> - 不依赖于Servlet Api（但是实现时依赖）
> - 可以任意使用各种视图
> - 支持各种请求资源的映射策略
> - 易于拓展

#### 2.1.8 Spring MVC和 AJAX之间的相互调用？

> 通过JackSon框架把java里面对象直接转换成js可识别的json对象，具体步骤如下：
>
> 1. 加入JackSon.jar
> 2. 在配置文件中配置json的映射
> 3. 在接受Ajax方法里面直接返回Object，list等，方法前面需要加上注解@ResponseBody

#### 2.1.9 Structs2 和 SpringMVC的区别？

> 1. 入口不同：
>
> - Struts2：filter过滤器
> - SpringMvc：一个Servlet即前端控制器
>
> 1. 开发方式不同：
>
> - Struts2：基于类开发，传递参数通过类的属性，只能设置为多例
> - SpringMvc：基于方法开发(一个url对应一个方法)，请求参数传递到方法形参，可以为单例也可以为多例(建议单例)
>
> 1. 请求方式不同：
>
> - Struts2：值栈村塾请求和响应的数据，通过OGNL存取数据
> - SpringMvc：通过参数解析器将request请求内容解析，给方法形参赋值，将数据和视图封装成ModelAndView对象，最后又将ModelAndView中的模型数据通过request域传输到页面，jsp视图解析器默认使用的是jstl。

## 3. Mybaits

### 3.1 基本问题

#### 3.1.1 对mybatis的基本理解

>1. SqlMapConfig.xml，此文件作为mybatis的全局配置文件，配置了mybatis的运行环境等信息。
>2. mapper.xml文件即**sql映射文件**，文件中配置了操作数据库的sql语句。此文件需要在SqlMapConfig.xml中加载。
>3. 通过mybatis环境等配置信息构造**SqlSessionFactory即会话工厂**
>4. 由会话工厂创建**sqlSession即会话**，操作数据库需要通过sqlSession进行。
>5. **mybatis底层自定义了Executor执行器接口操作数据库**，Executor接口有两个实现，一个是基本执行器、一个是缓存执行器。
>6. **Mapped Statement**也是mybatis一个底层封装对象，它**包装了mybatis配置信息及sql映射信息**等。mapper.xml文件中一个sql对应一个Mapped Statement对象，sql的id即是Mapped statement的id。
>7. Mapped Statement对sql执行输入参数进行定义，包括HashMap、基本类型、pojo，**Executor通过Mapped Statement在执行sql前将输入的java对象映射至sql中**，输入参数映射就是jdbc编程中对preparedStatement设置参数。
>8. Mapped Statement对sql执行输出结果进行定义，包括HashMap、基本类型、pojo，Executor通过Mapped Statement在执行sql后将输出结果映射至java对象中，输出结果映射过程相当于jdbc编程中对结果的解析处理过程。

#### 3.1.2 Mybaits相对于Hibernate的优势？以及劣势？

优势：

>半orm模型，可直接编写原生态的sql，可严格控制sql执行性能，灵活度高
>
>学习门槛低，Hibernater学习门槛高

劣势：

>Dao层开发简单，前者需要自己维护SQL和结果映射
>
>对对象的维护和缓存比Mybaits好，对增删改查对象维护方便
>
>移植性好
>
>具有更好的二级缓存机制

#### 3.1.3 什么是MyBatis的接口绑定,有什么好处？

> 接口映射就是在IBatis中任意定义接口,然后把接口里面的方法和SQL语句绑定,我们直接调用接口方法就可以,这样比起原来了SqlSession提供的方法我们可以有更加灵活的选择和设置.

#### 3.1.4 什么情况下用注解绑定，什么情况下用XML绑定

>当Sql语句比较简单时候,用注解绑定,当SQL语句比较复杂时候,用xml绑定,一般用xml绑定的比较多

#### 3.1.5 如果要查询的表名和返回的实体Bean对象不一致,那你是怎么处理的?
> 在MyBatis里面最主要最灵活的的一个映射对象的ResultMap,在它里面可以映射键值对, 默认里面有id节点,result节点,它可以映射表里面的列名和对象里面的字段名. 并且在一对一,一对多的情况下结果集也一定要用ResultMap

```xml
<!--column不做限制，可以为任意表的字段，而property须为type 定义的pojo属性-->
<resultMap id="唯一的标识" type="映射的pojo对象">
  <id column="表的主键字段，或者可以为查询语句中的别名字段" jdbcType="字段类型" property="映射pojo对象的主键属性" />
  <result column="表的一个字段（可以为任意表的一个字段）" jdbcType="字段类型" property="映射到pojo对象的一个属性（须为type定义的pojo对象中的一个属性）"/>
  <association property="pojo的一个对象属性" javaType="pojo关联的pojo对象">
    <id column="关联pojo对象对应表的主键字段" jdbcType="字段类型" property="关联pojo对象的主席属性"/>
    <result  column="任意表的字段" jdbcType="字段类型" property="关联pojo对象的属性"/>
  </association>
  <!-- 集合中的property须为oftype定义的pojo对象的属性-->
  <collection property="pojo的集合属性" ofType="集合中的pojo对象">
    <id column="集合中pojo对象对应的表的主键字段" jdbcType="字段类型" property="集合中pojo对象的主键属性" />
    <result column="可以为任意表的字段" jdbcType="字段类型" property="集合中的pojo对象的属性" />  
  </collection>
</resultMap>
```

#### 3.1.6 MyBatis 的核心处理类交什么？

> MyBatis里面的核心处理类叫做SqlSession

#### 3.1.7 **讲下MyBatis的缓存** 
> MyBatis的缓存分为**一级缓存和二级缓存**,**一级缓存放在session里面,默认就有,二级缓存放在它的命名空间里**, 
> 默认是打开的,使用二级缓存属性类需要实现Serializable序列化接口(可用来保存对象的状态),可在它的映射文件中配置 
> < cache/ >

#### 3.1.8 MyBatis(IBatis)的好处是什么？

> 1. **代码分离，维护便利**, ibatis把sql语句从Java源程序中独立出来，放在单独的XML文件中编写，给程序的 维护带来了很大便利。 
> 2. **底层封装，调用容易**，ibatis封装了底层JDBC API的调用细节，并能自动将结果集转换成JavaBean对象，大大简化了Java数据库编程的重复工作。 
> 3. **自定义SQL，控制灵活**，因为Ibatis需要程序员自己去编写sql语句，程序员可以结合数据库自身的特点灵活控制sql语句， 
> 4. **可以实现复杂查询**，因此能够实现比hibernate等全自动orm框架更高的查询效率，能够完成复杂查询。
> 5. **调用方便**，入参无需用对象封装（或者map封装）,使用@Param注解

#### 3.1.9 Mybaits 一对一、一对多、多对一、多对多的理解

一对一的实现方式（将association修改为collection又可以变成一对多模型）：

```xml
<mapper namespace="com.yc.mybatis.test.classMapper">
    
        <!-- 
         方式一：嵌套结果：使用嵌套结果映射来处理重复的联合结果的子集
                 封装联表查询的数据(去除重复的数据)
         select * from class c, teacher t where c.teacher_id=t.t_id and c.c_id=1
     -->
    
    <select id="getClass" parameterType="int" resultMap="getClassMap">
        select * from class c, teacher t  where c.teacher_id = t.t_id and c.teacher_id=#{id}
    </select>
    
    <!-- resultMap:映射实体类和字段之间的一一对应的关系 -->
    <resultMap type="Classes" id="getClassMap">
        <id property="id" column="c_id"/>   
        <result property="name" column="c_name"/>
        <association property="teacher" javaType="Teacher">   
            <id property="id" column="t_id"/>
            <result property="name" column="t_name"/>
        </association>
    </resultMap>
    
     <!-- 
         方式二：嵌套查询：通过执行另外一个SQL映射语句来返回预期的复杂类型
         SELECT * FROM class WHERE c_id=1;
         SELECT * FROM teacher WHERE t_id=1   //1 是上一个查询得到的teacher_id的值
         property:别名(属性名)    column：列名 -->
          <!-- 把teacher的字段设置进去 -->
    <select id="getClass1" parameterType="int" resultMap="getClassMap1">
        select * from class where c_id=#{id}
    </select>
    
    <resultMap type="Classes" id="getClassMap1">
        <id property="id" column="c_id"/>   
        <result property="name" column="c_name"/>
        <association property="teacher" column="teacher_id" select="getTeacher"/>   
    </resultMap>
    <select id="getTeacher" parameterType="int" resultType="Teacher">
        select t_id id,t_name name from teacher where t_id =#{id}
    </select>
     
    <!-- 多对多的关系 -->
    <resultMap type="Group" id="groupUserMap" <span style="color:#ff0000;"><strong>extends</strong></span>="groupMap">      
		<collection property="user" ofType="User">  
   		 <!--collection:聚集    用来处理类似User类中有List<Group> group时要修改group的情况   user代表要修改的是Group中的List<user> -->
    		<id property="id" column="userId" />  
    		<result property="name" column="userName" />  
    		<result property="password" column="password" />  
    		<result property="createTime" column="userCreateTime" />  
		</collection>  
	</resultMap> 
</mapper>
```

#### 3.1.10 ${} 和 #{}的区别？

> - \${}：简单字符串替换，把${}直接替换成变量的值，不做任何转换，这种是取值以后再去编译SQL语句。
> - \#{}：预编译处理，sql中的#{}替换成？，补全预编译语句，有效的防止Sql语句注入，这种取值是编译好SQL语句再取值。
>   总结：一般用#{}来进行列的代替

#### 3.1.11 Mybatis如何分页，分页原理？

> - RowBounds对象分页
> - 在Sql内直接书写，带有物理分页

#### 3.1.12 MyBaits 工作原理

![img](https://img2018.cnblogs.com/blog/990913/201811/990913-20181130104254282-2144338717.png)

## 4. SSM整合篇

### 4.1 基本问题

#### 4.1.1 SSM优缺点、使用场景？和Spring Boot的关系？

优缺点：

> - Spring，IOC/DI解耦，提高代码的复用性，可维护性大幅度提升，支持AOP编程，增强生产力
>
> - Spring MVC，轻量和低入门，
> - Mybaits，sql可由开发者取掌控和调优，半ORM，小巧并且简单易学，减少了50%以上的代码量

不是一个东西，Spring Boot启动、配置、快速开发的辅助框架，本身针对的是微服务。

#### 4.1.2 Spring 在SSM中起什么作用？

> Spring所起的一个重要作用是Bean工厂，具有IOC和DI依赖注入，控制反转是把dao依赖注入到servic层，然后service层反转给action层。
>
> 降低耦合，让代码更加清晰，提供一些常见的模板
>
> Spring还提供了AOP支持，方便进行切面编程。

#### 4.1.3 Spring的配置文件有哪些内容？

> • 开启事务注解驱动 
> • 事务管理器 
> • 开启注解功能，并配置扫描包 
> • 配置数据源 
> • 配置SQL会话工厂、别名、映射文件 
> • 不用编写DAO层的实现类（代理模式） 

## 5. Spring Boot

### 5.1 基本问题

#### 5.1.0 什么是Spring Boot?

> Spring Boot 是 Spring 开源组织下的子项目，是 Spring 组件一站式解决方案，主要是简化了使用 Spring 的难度，简省了繁重的配置，提供了各种启动器，开发者能快速上手。

#### 5.1.1 Spring Boot总结,核心功能,优缺点

核心功能：

> 1、独立运行Spring项目
> 2、内嵌servlet容器
> 3、提供starter简化Maven配置
> 4、自动装配Spring 
> 5、准生产的应用监控，SpringBoot提供基于http ssh telnet对运行时的项目进行监控。
> 6、无代码生产和xml配置，通过条件注解实现

优点：

> 1、快速构建项目。
> 2、对主流开发框架的无配置集成。
> 3、项目可独立运行，无须外部依赖Servlet容器。
> 4、提供运行时的应用监控。
> 5、极大的提高了开发、部署效率。
> 6、与云计算的天然集成。

缺点(作为一个微服务框架的不足)：

![img](http://articles.csdn.net/uploads/allimg/160512/258_160512164317_1.jpg)

##### 5.1.2 什么是JavaConfig？

 Spring JavaConfig是Spring社区的产品，它提供了配置Spring IoC容器的纯Java方法。

优点：

> 面向对象的配置
>
> 较少或者消除XML
>
> 类型友好和重构安全

#### 5.1.3  如何重新加载Spring Boot上的更改，而无需重新启动服务器？

>  这可以使用DEV工具来实现。

#### 5.1.4 Spring Boot中的监视器是什么？
> Spring boot actuator是spring启动框架中的重要功能之一。Spring boot监视器可帮助您访问生产环境中正在运行的应用程序的当前状态。
>
> 有几个指标必须在生产环境中进行检查和监控。即使一些外部应用程序可能正在使用这些服务来向相关人员触发警报消息。监视器模块公开了一组可直接作为HTTP URL访问的REST端点来检查状态。

#### 5.1.5 如何在Spring Boot中禁用Actuator端点安全性？

> 默认情况下，所有敏感的HTTP端点都是安全的，只有具有ACTUATOR角色的用户才能访问它们。
>
> management.security.enabled = false 来禁用安全性
>
> 在指定的端口上运行， server.port = 8090

#### 5.1.6 什么是YAML？

> YAML是一种**人类可读的数据序列化语言**。它通常用于配置文件。 与属性文件相比，如果我们想要在配置文件中添加复杂的属性，YAML文件就更加结构化，而且更少混淆。可以看出YAML具有分层配置数据。

#### 5.1.7 **SpringBoot 实现热部署有哪几种方式？**

> 主要有两种方式：
>
> - Spring Loaded
> - Spring-boot-devtools
>
> 注意事项：
>
> - 生成环境devtools将被禁用
> - 打包应用默认不会包含devtools
> - Thymeleaf无需配置 `spring.thymeleaf.cache:false`

#### 5.1.8 **你如何理解 Spring Boot 配置加载顺序？**

> 在 Spring Boot 里面，可以使用以下几种方式来加载配置。
>
> 1）properties文件；
>
> 2）YAML文件；
>
> 3）系统环境变量；
>
> 4）命令行参数；
>
> 等等……

#### 5.1.9 保护Spring Boot应用的方法有哪些？

> - 在生产中使用HTTPS
> - 使用Snyk检查你的依赖关系
> - 升级到最新版本
> - 启用CSRF保护
> - 使用内容安全策略防止XSS攻击

#### 5.1.10 **Spring Boot 2.X 有什么新特性？与 1.X 有什么区别？**

> - 配置变更
> - JDK 版本升级
> - 第三方类库升级
> - 响应式 Spring 编程支持
> - HTTP/2 支持
> - 配置属性绑定

#### 5.1.11 **Spring Boot 可以兼容老 Spring 项目吗，如何做？**

>  可以兼容，使用 `@ImportResource` 注解导入老 Spring 项目配置文件。

#### 5.1.12 **如何使用Spring Boot实现分页和排序？**

> 使用Spring Boot实现分页非常简单。使用Spring Data-JPA可以实现将可分页的org.springframework.data.domain.Pageable传递给存储库方法。

#### 5.1.13 **Spring Boot 的核心配置文件有哪几个？它们的区别是什么？**

> Spring Boot 的核心配置文件是 **application **和 **bootstrap 配置文件**。
>
> application 配置文件这个容易理解，主要用于 **Spring Boot 项目的自动化配置**。
>
> bootstrap 配置文件有以下几个应用场景。
>
> - 使用 Spring Cloud Config 配置中心时，这时需要在 bootstrap 配置文件中添加连接到配置中心的配置属性来加载外部配置中心的配置信息；
> - 一些固定的不能被覆盖的属性；
> - 一些加密/解密的场景；

#### 5.1.14 **Spring Boot 的配置文件有哪几种格式？它们有什么区别？**

> .properties 和 .yml，它们的区别主要是书写格式不同。
>
> 1).properties
>
> ```
> app.user.name = javastack
> ```
>
> 2).yml
>
> ```
> app:
>   user:
>     name: javastack
> ```
>
> 另外，.yml 格式不支持 `@PropertySource` 注解导入配置。

#### 5.1.15 **Spring Boot 的核心注解是哪个？它主要由哪几个注解组成的？**

> 启动类上面的注解是**@SpringBootApplication**，它也是 Spring Boot 的**核心注解**，主要组合包含了以下 3 个注解：
>
> - @SpringBootConfiguration：组合了 @Configuration 注解，实现配置文件的功能。
>
> - @EnableAutoConfiguration：打开自动配置的功能，也可以关闭某个自动配置的选项，如关闭数据源自动配置功能： @SpringBootApplication(exclude = { DataSourceAutoConfiguration.class })。
>
> - @ComponentScan：Spring组件扫描。

#### 5.1.16 **开启 Spring Boot 特性有哪几种方式？**

> 1）继承spring-boot-starter-parent项目
>
> 2）导入spring-boot-dependencies项目依赖

#### 5.1.17 **运行 Spring Boot 有哪几种方式？**

> 1）打包用命令或者放到容器中运行
>
> 2）用 Maven/ Gradle 插件运行
>
> 3）直接执行 main 方法运行

#### 5.1.18 **Spring Boot 自动配置原理是什么？**

> 注解 @EnableAutoConfiguration, @Configuration, @ConditionalOnClass 就是自动配置的核心，首先它得是一个配置文件，其次根据类路径下是否有这个类去自动配置。

#### 5.1.19 Spring Boot 的目录结构？

主流推荐的做法：主入口类上加上 @SpringBootApplication 注解来开启 Spring Boot 的各项能力如自动配置、组件扫描等

```
cn
 +- javastack
     +- MyApplication.java
     |
     +- customer
     |   +- Customer.java
     |   +- CustomerController.java
     |   +- CustomerService.java
     |   +- CustomerRepository.java
     |
     +- order
         +- Order.java
         +- OrderController.java
         +- OrderService.java
         +- OrderRepository.java
```

#### 5.1.20 如何理解Spring Boot中的Starters?

> Starters可以理解为**启动器**，它包含了一系列可以集成到应用里面的依赖包，你可以**一站式集成 Spring 及其他技术**，而不需要到处找示例代码和依赖包。如你想使用 Spring JPA 访问数据库，只要加入 spring-boot-starter-data-jpa 启动器依赖就能使用了。

#### 5.1.21 **如何在 Spring Boot 启动的时候运行一些特定的代码？**

> 可以实现接口 ApplicationRunner 或者 CommandLineRunner，这两个接口实现方式一样，它们都只提供了一个 run 方法

#### 5.1.22 **Spring Boot 有哪几种读取配置的方式？**

> Spring Boot 可以通过 @PropertySource,@Value,@Environment, @ConfigurationProperties 来绑定变量
>
> ```java
> // 配置的文件源可以为properties或者yaml
> //@Value注解读取方式
> @Component 
> public class InfoConfig1 {
> 	@Value("${info.address}")
> 	private String address;
> }
> 
> // @ConfigurationProperties注解读取方式
> @Component 
> @ConfigurationProperties(prefix="info")
> public class InfoConfig1 {
> 	private String address;
> }
> 
> //指定文件源，但是注意：@PropertySource不支持yml文件读取
> // @PropertySource + @Value注解读取方式
> @Component 
> @PropertySource("config/db-config.properties")
> public class InfoConfig1 {
> 	@Value("${info.address}")
> 	private String address;
> }
> 
> // @PropertySource + @ConfigurationProperties注解读取方式
> @Component 
> @PropertySource("config/db-config.properties")
> @ConfigurationProperties(prefix="info")
> public class InfoConfig1 {
> 	private String address;
> }
> ```

#### 5.1.23 **Spring Boot 支持哪些日志框架？推荐和默认的日志框架是哪个？**

> Spring Boot 支持 Java Util Logging, Log4j2, Lockback 作为日志框架，如果你使用 Starters 启动器，Spring Boot 将使用 Logback 作为默认日志框架.
>
> - 属性配置日志(不是特别灵活)
>
> ```
> logging.level.root=DEBUG
> logging.level.org.springframework.web=DEBUG
> ....
> ```
>
> - 自定义日志文件夹
>
> 通过修改配置文件进行配置
>
> | Logging System          | Customization                                                |
> | ----------------------- | ------------------------------------------------------------ |
> | Logback                 | logback-spring.xml, logback-spring.groovy, logback.xml or logback.groovy |
> | Log4j2                  | log4j2-spring.xml or log4j2.xml                              |
> | JDK (Java Util Logging) | logging.properties                                           |

#### 5.1.24 **Spring Boot 如何定义多套不同环境配置？**

提供多套配置文件，如：

```
applcation.properties

application-dev.properties

application-test.properties

application-prod.properties
```

## 参考资料

- [SSM面试总结](https://blog.csdn.net/qq382495414/article/details/81131136)
- [IOC与DI的理解](<https://blog.csdn.net/ji1127780204/article/details/80291266>)
- [Spring系列之Spring常用注解总结](https://www.cnblogs.com/xiaoxi/p/5935009.html)
- [spring的@Transactional注解详细用法](https://www.cnblogs.com/yepei/p/4716112.html)
- [Mybatis 一对一，一对多，多对一，多对多的理解](https://www.cnblogs.com/yaobolove/p/5444046.html)
- [ssm常见面试题](<https://blog.csdn.net/qq_38262968/article/details/79474455>)
- [SSM框架优缺点和springboot 比起优缺点是什么？](<https://blog.csdn.net/qq_38238041/article/details/84729311>)
- [Spring Boot总结,核心功能,优缺点](<https://blog.csdn.net/qq_35216516/article/details/80529220>)
- [Spring Boot浅谈(是什么/能干什么/优点和不足)](<https://blog.csdn.net/fly_zhyu/article/details/76407830>)
- [Mybatis逻辑分页原理解析RowBounds](<https://blog.csdn.net/qq924862077/article/details/52611848>)
- [SSM框架面试题及答案整理](https://www.cnblogs.com/aishangJava/p/10043493.html)
- [15道常考SpringBoot面试题整理](<https://blog.csdn.net/qq_41701956/article/details/87907238>)
- [吐血整理 20 道 Spring Boot 面试题，我经常拿来面试别人！](<https://my.oschina.net/javaroad/blog/2245901>)