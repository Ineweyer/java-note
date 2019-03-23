# 深入浅出 spring-data-elasticsearch – 基本案例详解（三)

『 风云说：能分享自己职位的知识的领导是个好领导。 』
**运行环境**：JDK 7 或 8，Maven 3.0+
**技术栈**：SpringBoot 1.5+， Spring Data Elasticsearch 1.5+ ，ElasticSearch 2.3.2
**本文提纲**
一、spring-data-elasticsearch-crud 的工程介绍
二、运行 spring-data-elasticsearch-crud 工程
三、spring-data-elasticsearch-crud 工程代码详解

**一、spring-data-elasticsearch-crud 的工程介绍**
spring-data-elasticsearch-crud 的工程，介绍 Spring Data Elasticsearch 简单的 ES 操作。Spring Data Elasticsearch 可以跟 JPA 进行类比。其使用方法也很简单。

**二、运行 spring-data-elasticsearch-crud 工程**
注意的是这里使用的是 ElasticSearch 2.3.2。是因为版本对应关系 <https://github.com/spring-projects/spring-data-elasticsearch/wiki/Spring-Data-Elasticsearch---Spring-Boot---version-matrix;>

Spring Boot Version (x) Spring Data Elasticsearch Version (y) Elasticsearch Version (z)
x <= 1.3.5 y <= 1.3.4 z <= 1.7.2
x >= 1.4.x 2.0.0 <=y < 5.0.0 2.0.0 <= z < 5.0.0
\ - 只需要你修改下对应的 pom 文件版本号
** - 下一个 ES 的版本会有重大的更新

**1. 后台起守护线程启动 Elasticsearch**

```shell
cd elasticsearch-2.3.2/
./bin/elasticsearch -d
```

git clone 下载工程 springboot-elasticsearch ，项目地址见 GitHub - [https://github.com/JeffLi1993/ … ample](https://github.com/JeffLi1993/springboot-learning-example)。
下面开始运行工程步骤（Quick Start）：

1. 项目结构介绍

```properties
org.spring.springboot.controller - Controller 层
org.spring.springboot.repository - ES 数据操作层
org.spring.springboot.domain - 实体类
org.spring.springboot.service - ES 业务逻辑层
Application - 应用启动类
application.properties - 应用配置文件，应用启动会自动读取配置
```

本地启动的 ES ，就不需要改配置文件了。如果连测试 ES 服务地址，需要修改相应配置

3.编译工程
在项目根目录 spring-data-elasticsearch-crud，运行 maven 指令：

```shell
mvn clean install
```

4.运行工程
右键运行 Application 应用启动类（位置：/springboot-learning-example/springboot-elasticsearch/src/main/java/org/spring/springboot/Application.java）的 main 函数，这样就成功启动了 springboot-elasticsearch 案例。
用 Postman 工具新增两个城市

a. 新增城市信息

```javascript
POST http://127.0.0.1:8080/api/city
{
    "id”:"1",
    "score":"5",
    "name":"上海",
    "description":"上海是个热城市"
}
POST http://127.0.0.1:8080/api/city
{
    "id":"2",
    "score”:"4",
    "name”:”温岭",
    "description":”温岭是个沿海城市"
}
```

可以打开 ES 可视化工具 head 插件：<http://localhost:9200/_plugin/head/>：
（如果不知道怎么安装，请查阅 《Elasticsearch 和插件 elasticsearch-head 安装详解》 <http://www.bysocket.com/?p=1744>。）
在「数据浏览」tab，可以查阅到 ES 中数据是否被插入，插入后的数据格式如下：

```javascript
{
"_index": "cityindex",
"_type": "city",
"_id": "1",
"_version": 1,
"_score": 1,
"_source": {
  "id":"2",
    "score”:"4",
    "name”:”温岭",
    "description":”温岭是个沿海城市"
}
}
```

下面是基本查询语句的接口：
a. 普通查询，查询城市描述

```
GET http://localhost:8080/api/city ... on%3D温岭
```

返回 JSON 如下：

```javascript
[
    {
        "id": 2,
        "name": "温岭",
        "description": "温岭是个沿海城市",
        "score": 4
    }
]
```

b. AND 语句查询

```
GET http://localhost:8080/api/city ... on%3D温岭&score=4
```

返回 JSON 如下:

```javascript
[
    {
        "id": 2,
        "name": "温岭",
        "description": "温岭是个沿海城市",
        "score": 4
    }
]
```

如果换成 score=5 ，就没有结果了。

c. OR 语句查询

```
GET http://localhost:8080/api/city ... on%3D上海&score=4
```

返回 JSON 如下：

```javascript
[
    {
        "id": 2,
        "name": "温岭",
        "description": "温岭是个沿海城市",
        "score": 4
    },
    {
        "id": 1,
        "name": "上海",
        "description": "上海是个好城市",
        "score": 3
    }
]
```

d. NOT 语句查询

```
GET http://localhost:8080/api/city ... on%3D温州
```

返回 JSON 如下：

```javascript
[
    {
        "id": 2,
        "name": "温岭",
        "description": "温岭是个沿海城市",
        "score": 4
    },
    {
        "id": 1,
        "name": "上海",
        "description": "上海是个好城市",
        "score": 3
    }
]
```

e. LIKE 语句查询

```
GET http://localhost:8080/api/city ... on%3D城市
```

返回 JSON 如下：

```javascript
[
    {
        "id": 2,
        "name": "温岭",
        "description": "温岭是个沿海城市",
        "score": 4
    },
    {
        "id": 1,
        "name": "上海",
        "description": "上海是个好城市",
        "score": 3
    }
]
```

**三、spring-data-elasticsearch-crud 工程代码详解**
具体代码见 GitHub - <https://github.com/JeffLi1993/springboot-learning-example>

**1.pom.xml 依赖**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/ma ... gt%3B
    <modelVersion>4.0.0</modelVersion>
    <groupId>springboot</groupId>
    <artifactId>spring-data-elasticsearch-crud</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>spring-data-elasticsearch-crud :: spring-data-elasticsearch - 基本案例 </name>
    <!-- Spring Boot 启动父依赖 -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.1.RELEASE</version>
    </parent>
    <dependencies>
        <!-- Spring Boot Elasticsearch 依赖 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-elasticsearch</artifactId>
        </dependency>
        <!-- Spring Boot Web 依赖 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!-- Junit -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
    </dependencies>
</project>
```

这里依赖的 spring-boot-starter-data-elasticsearch 版本是 1.5.1.RELEASE，对应的 spring-data-elasticsearch 版本是 2.1.0.RELEASE。对应官方文档：[http://docs.spring.io/spring-d … html/](http://docs.spring.io/spring-data/elasticsearch/docs/2.1.0.RELEASE/reference/html/)。后面数据操作层都是通过该 spring-data-elasticsearch 提供的接口实现。

**2. application.properties 配置 ES 地址**

```properties
# ES
spring.data.elasticsearch.repositories.enabled = true
spring.data.elasticsearch.cluster-nodes = 127.0.0.1:9300
默认 9300 是 Java 客户端的端口。9200 是支持 Restful HTTP 的接口。
```

更多配置：
 spring.data.elasticsearch.cluster-name Elasticsearch 集群名。(默认值: elasticsearch)
 spring.data.elasticsearch.cluster-nodes 集群节点地址列表，用逗号分隔。如果没有指定，就启动一个客户端节点。
 spring.data.elasticsearch.propertie 用来配置客户端的额外属性。
 spring.data.elasticsearch.repositories.enabled 开启 Elasticsearch 仓库。(默认值:true。)

**3. ES 数据操作层**

```java
/**
 * ES 操作类
 * <p>
 * Created by bysocket on 17/05/2017.
 */
public interface CityRepository extends ElasticsearchRepository<City, Long> {
    /**
     * AND 语句查询
     *
     * @param description
     * @param score
     * @return
     */
    List<City> findByDescriptionAndScore(String description, Integer score);
    /**
     * OR 语句查询
     *
     * @param description
     * @param score
     * @return
     */
    List<City> findByDescriptionOrScore(String description, Integer score);
    /**
     * 查询城市描述
     *
     * 等同于下面代码
     * @Query("{\"bool\" : {\"must\" : {\"term\" : {\"description\" : \"?0\"}}}}")
     * Page<City> findByDescription(String description, Pageable pageable);
     *
     * @param description
     * @param page
     * @return
     */
    Page<City> findByDescription(String description, Pageable page);
    /**
     * NOT 语句查询
     *
     * @param description
     * @param page
     * @return
     */
    Page<City> findByDescriptionNot(String description, Pageable page);
    /**
     * LIKE 语句查询
     *
     * @param description
     * @param page
     * @return
     */
    Page<City> findByDescriptionLike(String description, Pageable page);
}
```

接口只要继承 ElasticsearchRepository 类即可。默认会提供很多实现，比如 CRUD 和搜索相关的实现。类似于 JPA 读取数据，是使用 CrudRepository 进行操作 ES 数据。支持的默认方法有： count(), findAll(), findOne(ID), delete(ID), deleteAll(), exists(ID), save(DomainObject), save(Iterable<DomainObject>)。

另外可以看出，接口的命名是遵循规范的。常用命名规则如下：

| 关键字  | 方法命名          |
| ------- | ----------------- |
| And     | findByNameAndPwd  |
| Is     | findById  |
| Or      | findByNameOrSex   |
| Between | findByIdBetween   |
| Like    | findByNameLike    |
| NotLike | findByNameNotLike |
| OrderBy | findByIdOrderByXDesc |
| Not | findByNameNot |


**4. 实体类**

```java
/**
 * 城市实体类
 * <p>
 * Created by bysocket on 03/05/2017.
 */
@Document(indexName = "province", type = "city")
public class City implements Serializable {
    private static final long serialVersionUID = -1L;
    /**
     * 城市编号
     */
    private Long id;
    /**
     * 城市名称
     */
    private String name;
    /**
     * 描述
     */
    private String description;
    /**
     * 城市评分
     */
    private Integer score;
    public Long getId() {
        return id;
    }
    public void setId(Long id) {
        this.id = id;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public String getDescription() {
        return description;
    }
    public void setDescription(String description) {
        this.description = description;
    }
    public Integer getScore() {
        return score;
    }
    public void setScore(Integer score) {
        this.score = score;
    }
}
```

**注意**
a. City 属性名不支持驼峰式。
b. indexName 配置必须是全部小写，不然会出异常。
org.elasticsearch.indices.InvalidIndexNameException: Invalid index name [provinceIndex], must be lowercase

**四、小结**
预告下
下一篇《深入浅出 spring-data-elasticsearch - 实战案例详解》，会带来实战项目中涉及到的权重分 & 短语精准匹配的讲解。

> 摘要: 原创出处 www.bysocket.com 「泥瓦匠BYSocket 」欢迎转载，保留摘要，谢谢！