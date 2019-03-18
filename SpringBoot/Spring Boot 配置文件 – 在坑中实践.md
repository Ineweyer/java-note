# Spring Boot 配置文件 – 在坑中实践



摘要: 原创出处 www.bysocket.com 「泥瓦匠BYSocket 」欢迎转载，保留摘要，谢谢！

**『 仓廪实而知礼节，衣食足而知荣辱 - 管仲 』**

**本文提纲**

一、自动配置

二、自定义属性

三、random.* 属性

四、多环境配置

**运行环境**：JDK 7 或 8，Maven 3.0+

**技术栈**：SpringBoot 1.5+

### 一、自动配置

Spring Boot 提供了对应用进行自动化配置。相比以前 XML 配置方式，很多显式方式申明是不需要的。二者，大多数默认的配置足够实现开发功能，从而更快速开发。

什么是**自动配置**？

Spring Boot 提供了默认的配置，如默认的 Bean ，去运行 Spring 应用。它是非侵入式的，只提供一个默认实现。

大多数情况下，自动配置的 Bean 满足了现有的业务场景，不需要去覆盖。但如果自动配置做的不够好，需要覆盖配置。比如通过命令行动态指定某个 jar ，按不同环境启动（这个例子在第 4 小节介绍）。那怎么办？这里先要考虑到配置的优先级。

Spring Boot 不单单从 application.properties 获取配置，所以我们可以在程序中多种设置配置属性。按照以下列表的优先级排列：

1.命令行参数

2.java:comp/env 里的 JNDI 属性

3.JVM 系统属性

4.操作系统环境变量

5.RandomValuePropertySource 属性类生成的 random.* 属性

6.应用以外的 application.properties（或 yml）文件

7.打包在应用内的 application.properties（或 yml）文件

8.在应用 @Configuration 配置类中，用 @PropertySource 注解声明的属性文件

9.SpringApplication.setDefaultProperties 声明的默认属性

可见，命令行参数优先级最高。这个可以根据这个优先级，可以在测试或生产环境中快速地修改配置参数值，而不需要重新打包和部署应用。

还有第 6 点，根据这个在多 moudle 的项目中，比如常见的项目分 api 、service、dao 等 moudles，往往会加一个 deploy moudle 去打包该业务各个子 moudle，应用以外的配置优先。

### 二、自定义属性

泥瓦匠喜欢按着代码工程来讲解知识。git clone 下载工程 springboot-learning-example ，项目地址见 GitHub - <https://github.com/JeffLi1993/springboot-learning-example>。

a. 编译工程

在项目根目录 springboot-learning-example，运行 maven 指令：

```
cd springboot-learning-example
mvn clean install
```

b. 运行工程 test 方法

运行 springboot-properties 工程 org.spring.springboot.property.PropertiesTest 测试类的 getHomeProperties 方法。可以在控制台看到输出，这是通过自定义属性获取的值:

```
HomeProperties{province='ZheJiang', city='WenLing', desc='dev: I'm living in ZheJiang WenLing.'}
```

怎么定义自定义属性呢？

首先项目结构如下：

```
├── pom.xml
└── src
    ├── main
    │   ├── java
    │   │   └── org
    │   │       └── spring
    │   │           └── springboot
    │   │               ├── Application.java
    │   │               └── property
    │   │                   ├── HomeProperties.java
    │   │                   └── UserProperties.java
    │   └── resources
    │       ├── application-dev.properties
    │       ├── application-prod.properties
    │       └── application.properties
    └── test
        ├── java
        │   └── org
        │       └── spring
        │           └── springboot
        │               └── property
        │                   ├── HomeProperties1.java
        │                   └── PropertiesTest.java
        └── resouorces
            └── application.yml
```

在 application.properties 中对应 HomeProperties 对象字段编写属性的 KV 值：

```
## 家乡属性 Dev
home.province=ZheJiang
home.city=WenLing
home.desc=dev: I'm living in ${home.province} ${home.city}.
```

这里也可以通过占位符，进行属性之间的引用。

然后，编写对应的 HomeProperties Java 对象：

```java
/**
 * 家乡属性
 *
 * Created by bysocket on 17/04/2017.
 */
@Component
@ConfigurationProperties(prefix = "home")
public class HomeProperties {

    /**
     * 省份
     */
    private String province;

    /**
     * 城市
     */
    private String city;

    /**
     * 描述
     */
    private String desc;

    public String getProvince() {
        return province;
    }

    public void setProvince(String province) {
        this.province = province;
    }

    public String getCity() {
        return city;
    }

    public void setCity(String city) {
        this.city = city;
    }

    public String getDesc() {
        return desc;
    }

    public void setDesc(String desc) {
        this.desc = desc;
    }

    @Override
    public String toString() {
        return "HomeProperties{" +
                "province='" + province + '\'' +
                ", city='" + city + '\'' +
                ", desc='" + desc + '\'' +
                '}';
    }
}
```

通过 @ConfigurationProperties(prefix = "home”) 注解，将配置文件中以 home 前缀的属性值自动绑定到对应的字段中。同是用 @Component 作为 Bean 注入到 Spring 容器中。

如果不是用 application.properties 文件，而是用 application.yml 的文件，对应配置如下：

```yaml
## 家乡属性
home:
  province: 浙江省
  city: 温岭松门
  desc: 我家住在${home.province}的${home.city}
```

键值对冒号后面，必须空一格。

注意这里，就有一个坑了：

application.properties 配置中文值的时候，读取出来的属性值会出现乱码问题。但是 application.yml 不会出现乱码问题。原因是，Spring Boot 是以 iso-8859 的编码方式读取 application.properties 配置文件。

注意这里，还有一个坑：

如果定义一个键值对 user.name=xxx ,这里会读取不到对应写的属性值。为什么呢？Spring Boot 的默认 StandardEnvironment 首先将会加载 “systemEnvironment" 作为首个PropertySource. 而 source 即为System.getProperties().当 getProperty时,按照读取顺序,返回 “systemEnvironment" 的值.即 System.getProperty("user.name")

（Mac 机子会读自己的登录账号，这里感谢我的死党 <http://rapharino.com/>）

### 三、random.* 属性

Spring Boot 通过 RandomValuePropertySource 提供了很多关于随机数的工具类。概括可以生成随机字符串、随机 int 、随机 long、某范围的随机数。

运行 springboot-properties 工程 org.spring.springboot.property.PropertiesTest 测试类的 randomTestUser 方法。多次运行，可以发现每次输出不同 User 属性值：

```
UserProperties{id=-3135706105861091890, age=41, desc='泥瓦匠叫做3cf8fb2507f64e361f62700bcbd17770', uuid='582bcc01-bb7f-41db-94d5-c22aae186cb4'}
```

application.yml 方式的配置如下（ application.properties 形式这里不写了）：

```yaml
## 随机属性
user:
  id: ${random.long}
  age: ${random.int[1,200]}
  desc: 泥瓦匠叫做${random.value}
  uuid: ${random.uuid}
```

### 四、多环境配置

很多场景的配置，比如数据库配置、Redis 配置、注册中心和日志配置等。在不同的环境，我们需要不同的包去运行项目。所以看项目结构，有两个环境的配置：

```
application-dev.properties：开发环境
application-prod.properties：生产环境
```

Spring Boot 是通过 application.properties 文件中，设置 spring.profiles.active 属性，比如 ，配置了 dev ,则加载的是 application-dev.properties ：

```properties
# Spring Profiles Active
spring.profiles.active=dev
```

那运行 springboot-properties 工程中 Application 应用启动类，从控制台中可以看出，是加载了 application-dev.properties 的属性输出：

```
HomeProperties{province='ZheJiang', city='WenLing', desc='dev: I'm living in ZheJiang WenLing.'}
```

将 spring.profiles.active 设置成 prod，重新运行，可得到 application-prod.properties的属性输出：

```
HomeProperties{province='ZheJiang', city='WenLing', desc='prod: I'm living in ZheJiang WenLing.'}
```

根据优先级，顺便介绍下 jar 运行的方式，通过设置 -Dspring.profiles.active=prod 去指定相应的配置:

```
mvn package
java -jar -Dspring.profiles.active=prod springboot-properties-0.0.1-SNAPSHOT.jar
```

### 五、小结

常用的样板配置在 Spring Boot 官方文档给出，我们常在 application.properties（或 yml）去配置各种常用配置：

<http://docs.spring.io/spring-boot/docs/current/reference/html/common-application-properties.html>

感谢资料：

<http://blog.didispace.com/springbootproperties/>

<https://docs.spring.io/spring-boot/docs>