# Spring Boot 使用NoSQL数据库 MongoDB

本文摘抄自博客[http://www.spring4all.com/article/255](http://www.spring4all.com/article/255)

前段时间分享了关于[Spring Boot中使用Redis](http://blog.didispace.com/springbootredis/)的文章，除了Redis之后，我们在互联网产品中还经常会用到另外一款著名的NoSQL数据库MongoDB。

下面就来简单介绍一下MongoDB，并且通过一个例子来介绍Spring Boot中对MongoDB访问的配置和使用。

## MongoDB简介

MongoDB是一个基于分布式文件存储的数据库，它是一个介于关系数据库和非关系数据库之间的产品，其主要目标是在键/值存储方式（提供了高性能和高度伸缩性）和传统的RDBMS系统（具有丰富的功能）之间架起一座桥梁，它集两者的优势于一身。

MongoDB支持的数据结构非常松散，是类似json的bson格式，因此可以存储比较复杂的数据类型，也因为他的存储格式也使得它所存储的数据在Nodejs程序应用中使用非常流畅。

既然称为NoSQL数据库，Mongo的查询语言非常强大，其语法有点类似于面向对象的查询语言，几乎可以实现类似关系数据库单表查询的绝大部分功能，而且还支持对数据建立索引。

但是，MongoDB也不是万能的，同MySQL等关系型数据库相比，它们在针对不同的数据类型和事务要求上都存在自己独特的优势。在数据存储的选择中，坚持多样化原则，选择更好更经济的方式，而不是自上而下的统一化。

较常见的，我们可以直接用MongoDB来存储键值对类型的数据，如：验证码、Session等；由于MongoDB的横向扩展能力，也可以用来存储数据规模会在未来变的非常巨大的数据，如：日志、评论等；由于MongoDB存储数据的弱类型，也可以用来存储一些多变json数据，如：与外系统交互时经常变化的JSON报文。而对于一些对数据有复杂的高事务性要求的操作，如：账户交易等就不适合使用MongoDB来存储。

[MongoDB官网](https://www.mongodb.org/)

## 访问MongoDB

在Spring Boot中，对如此受欢迎的MongoDB，同样提供了自配置功能。

#### 引入依赖

Spring Boot中可以通过在`pom.xml`中加入`spring-boot-starter-data-mongodb`引入对mongodb的访问支持依赖。它的实现依赖`spring-data-mongodb`。是的，您没有看错，又是`spring-data`的子项目，之前介绍过`spring-data-jpa`、`spring-data-redis`，对于mongodb的访问，`spring-data`也提供了强大的支持，下面就开始动手试试吧。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-mongodb</artifactId>
</dependency>
```

#### 快速开始使用Spring-data-mongodb

若MongoDB的安装配置采用默认端口，那么在自动配置的情况下，我们不需要做任何参数配置，就能马上连接上本地的MongoDB。下面直接使用`spring-data-mongodb`来尝试对mongodb的存取操作。（记得mongod启动您的mongodb）

- 创建要存储的User实体，包含属性：id、username、age

```java
public class User {

    @Id
    private Long id;

    private String username;
    private Integer age;

    public User(Long id, String username, Integer age) {
        this.id = id;
        this.username = username;
        this.age = age;
    }

    // 省略getter和setter

}
```

- 实现User的数据访问对象：UserRepository

```java
public interface UserRepository extends MongoRepository<User, Long> {

    User findByUsername(String username);

}
```

- 在单元测试中调用

```java
@RunWith(SpringJUnit4ClassRunner.class)
@SpringApplicationConfiguration(Application.class)
public class ApplicationTests {

    @Autowired
    private UserRepository userRepository;

    @Before
    public void setUp() {
        userRepository.deleteAll();
    }

    @Test
    public void test() throws Exception {

        // 创建三个User，并验证User总数
        userRepository.save(new User(1L, "didi", 30));
        userRepository.save(new User(2L, "mama", 40));
        userRepository.save(new User(3L, "kaka", 50));
        Assert.assertEquals(3, userRepository.findAll().size());

        // 删除一个User，再验证User总数
        User u = userRepository.findOne(1L);
        userRepository.delete(u);
        Assert.assertEquals(2, userRepository.findAll().size());

        // 删除一个User，再验证User总数
        u = userRepository.findByUsername("mama");
        userRepository.delete(u);
        Assert.assertEquals(1, userRepository.findAll().size());

    }

}
```

#### 参数配置

通过上面的例子，我们可以轻而易举的对MongoDB进行访问，但是实战中，应用服务器与MongoDB通常不会部署于同一台设备之上，这样就无法使用自动化的本地配置来进行使用。这个时候，我们也可以方便的配置来完成支持，只需要在application.properties中加入mongodb服务端的相关配置，具体示例如下：

```properties
spring.data.mongodb.uri=mongodb://name:pass@localhost:27017/test
```

*在尝试此配置时，记得在mongo中对test库创建具备读写权限的用户（用户名为name，密码为pass），不同版本的用户创建语句不同，注意查看文档做好准备工作*

若使用mongodb 2.x，也可以通过如下参数配置，该方式不支持mongodb 3.x。

```properties
spring.data.mongodb.host=localhost spring.data.mongodb.port=27017
```