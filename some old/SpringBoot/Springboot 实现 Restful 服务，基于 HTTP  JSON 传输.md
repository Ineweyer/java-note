# Springboot 实现 Restful 服务，基于 HTTP / JSON 传输

摘要: 原创出处:www.bysocket.com 泥瓦匠BYSocket 希望转载，保留摘要，谢谢！

> “怎样的人生才是没有遗憾的人生？我的体会是：
>
> （1）拥有健康；
>
> （2）创造“难忘时刻”；
>
> （3）尽力做好自己，不必改变世界；
>
> （4）活在当下。”
>
> \- 《向死而生》李开复

Spring Boot 系列文章：《[Spring Boot 那些事](http://www.bysocket.com/?page_id=1639)》

基于上一篇《Springboot 整合 Mybatis 的完整 Web 案例》，这边我们着重在 控制层 讲讲。讲讲如何在 Springboot 实现 Restful 服务，基于 HTTP / JSON 传输。

### 一、运行 springboot-restful 工程

git clone 下载工程 [springboot-learning-example](https://github.com/JeffLi1993/springboot-learning-example) ，项目地址见 GitHub - <https://github.com/JeffLi1993/springboot-learning-example>。下面开始运行工程步骤（Quick Start）：

**1.数据库准备**

a.创建数据库 springbootdb：

```sql
CREATE DATABASE springbootdb;
```

b.创建表 city ：(因为我喜欢徒步)

```sql
DROP TABLE IF EXISTS  `city`;
CREATE TABLE `city` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT '城市编号',
  `province_id` int(10) unsigned  NOT NULL COMMENT '省份编号',
  `city_name` varchar(25) DEFAULT NULL COMMENT '城市名称',
  `description` varchar(25) DEFAULT NULL COMMENT '描述',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
```

c.插入数据

```sql
INSERT city VALUES (1 ,1,'温岭市','BYSocket 的家在温岭。');
```

**2. springboot-restful 工程项目结构介绍**

> springboot-restful 工程项目结构如下图所示：

> org.spring.springboot.controller - Controller 层
>
> org.spring.springboot.dao - 数据操作层 DAO
>
> org.spring.springboot.domain - 实体类
>
> org.spring.springboot.service - 业务逻辑层
>
> Application - 应用启动类
>
> application.properties - 应用配置文件，应用启动会自动读取配置

**3.改数据库配置**

打开 application.properties 文件， 修改相应的数据源配置，比如数据源地址、账号、密码等。（如果不是用 MySQL，自行添加连接驱动 pom，然后修改驱动名配置。）

**4.编译工程**

在项目根目录 springboot-learning-example，运行 maven 指令：

mvn clean install

**5.运行工程**

右键运行 springboot-restful 工程 Application 应用启动类的 main 函数。

用 postman 工具可以如下操作，

根据 ID，获取城市信息

GET http://127.0.0.1:8080/api/city/1

[![img](http://www.bysocket.com/wp-content/uploads/2017/02/getone.png)](http://www.bysocket.com/wp-content/uploads/2017/02/getone.png)

获取城市列表

GET http://127.0.0.1:8080/api/city

[![img](http://www.bysocket.com/wp-content/uploads/2017/02/getlist.png)](http://www.bysocket.com/wp-content/uploads/2017/02/getlist.png)

新增城市信息

POST http://127.0.0.1:8080/api/city

[![img](http://www.bysocket.com/wp-content/uploads/2017/02/createcity.png)](http://www.bysocket.com/wp-content/uploads/2017/02/createcity.png)

更新城市信息

PUT http://127.0.0.1:8080/api/city

[![img](http://www.bysocket.com/wp-content/uploads/2017/02/put.png)](http://www.bysocket.com/wp-content/uploads/2017/02/put.png)

删除城市信息

DELETE http://127.0.0.1:8080/api/city/2

[![img](http://www.bysocket.com/wp-content/uploads/2017/02/delete.png)](http://www.bysocket.com/wp-content/uploads/2017/02/delete.png)



### **二、springboot-restful 工程控制层实现详解**

**1.什么是 REST?**

REST 是属于 WEB 自身的一种架构风格，是在 HTTP 1.1 规范下实现的。Representational State Transfer 全称翻译为表现层状态转化。Resource：资源。比如 newsfeed；Representational：表现形式，比如用JSON，富文本等；State Transfer：状态变化。通过HTTP 动作实现。

理解 REST ,要明白五个关键要素：

> 资源（Resource）
>
> 资源的表述（Representation）
>
> 状态转移（State Transfer）
>
> 统一接口（Uniform Interface）
>
> 超文本驱动（Hypertext Driven）

6 个主要特性：

> 面向资源（Resource Oriented）
>
> 可寻址（Addressability）
>
> 连通性（Connectedness）
>
> 无状态（Statelessness）
>
> 统一接口（Uniform Interface）
>
> 超文本驱动（Hypertext Driven）

具体这里就不一一展开，详见[ http://www.infoq.com/cn/articles/understanding-restful-style](http://www.infoq.com/cn/articles/understanding-restful-style)

**2.Spring 对 REST 支持实现**

CityRestController.java 城市 Controller 实现 Restful HTTP 服务

```java
public class CityRestController {

    @Autowired
    private CityService cityService;

    @RequestMapping(value = "/api/city/{id}", method = RequestMethod.GET)
    public City findOneCity(@PathVariable("id") Long id) {
        return cityService.findCityById(id);
    }

    @RequestMapping(value = "/api/city", method = RequestMethod.GET)
    public List<City> findAllCity() {
        return cityService.findAllCity();
    }

    @RequestMapping(value = "/api/city", method = RequestMethod.POST)
    public void createCity(@RequestBody City city) {
        cityService.saveCity(city);
    }

    @RequestMapping(value = "/api/city", method = RequestMethod.PUT)
    public void modifyCity(@RequestBody City city) {
        cityService.updateCity(city);
    }

    @RequestMapping(value = "/api/city/{id}", method = RequestMethod.DELETE)
    public void modifyCity(@PathVariable("id") Long id) {
        cityService.deleteCity(id);
    }
}
```

具体 Service 、dao 层实现看代码[GitHub](https://github.com/JeffLi1993/springboot-learning-example/tree/master/springboot-restful) <https://github.com/JeffLi1993/springboot-learning-example/tree/master/springboot-restful>

**代码详解：**

@RequestMapping 处理请求地址映射。

> method - 指定请求的方法类型：POST/GET/DELETE/PUT 等
>
> value - 指定实际的请求地址
>
> consumes - 指定处理请求的提交内容类型，例如 Content-Type 头部设置application/json, text/html

> produces - 指定返回的内容类型



@PathVariable URL 映射时，用于绑定请求参数到方法参数



@RequestBody 这里注解用于读取请求体 boy 的数据，通过 HttpMessageConverter 解析绑定到对象中



**3.HTTP 知识补充**

> GET            请求获取Request-URI所标识的资源

> POST          在Request-URI所标识的资源后附加新的数据
>
> HEAD         请求获取由Request-URI所标识的资源的响应消息报头
>
> PUT            请求服务器存储一个资源，并用Request-URI作为其标识
>
> DELETE       请求服务器删除Request-URI所标识的资源
>
> TRACE        请求服务器回送收到的请求信息，主要用于测试或诊断
>
> CONNECT  保留将来使用
>
> OPTIONS   请求查询服务器的性能，或者查询与资源相关的选项和需求

详情请看《[JavaEE 要懂的小事：一、图解Http协议](http://www.bysocket.com/?p=282)》

### 三、小结

Springboot 实现 Restful 服务，基于 HTTP / JSON 传输，适用于前后端分离。这只是个小demo，没有加入bean validation这种校验。还有各种业务场景。

推荐：《 [Springboot 整合 Mybatis 的完整 Web 案例](http://www.bysocket.com/?p=1610)》