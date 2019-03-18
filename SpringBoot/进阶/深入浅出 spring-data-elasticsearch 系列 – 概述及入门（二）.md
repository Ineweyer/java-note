# 深入浅出 spring-data-elasticsearch 系列 – 概述及入门（二）



本文目录
一、spring-data-elasticsearch 是什么？
1.1 Spring Data
1.2 Spring Data Elasticsearch
二、spring-data-elasticsearch 快速入门
2.1 pom.xml 依赖
2.2 ElasticsearchRepository
2.3 ElasticsearchTemplate
2.4 使用案例
三、spring-data-elasticsearch 和 elasticsearch 版本
四、小结

> 这里我们只是把人生大致分成“学习阶段”以及之后的“工作阶段”。
> -《未来简史》

**一、spring-data-elasticsearch 是什么？**
1.1 Spring Data
要了解 spring-data-elasticsearch 是什么，首先了解什么是 Spring Data。
Spring Data 基于 Spring 为数据访问提供一种相似且一致性的编程模型，并保存底层数据存储的。

1.2 Spring Data Elasticsearch
spring-data-elasticsearch 是 Spring Data 的 Community modules 之一，是 Spring Data 对 Elasticsearch 引擎的实现。
Elasticsearch 默认提供轻量级的 HTTP Restful 接口形式的访问。相对来说，使用 HTTP Client 调用也很简单。但 spring-data-elasticsearch 可以更快的支持构建在 Spring 应用上，比如在 application.properties 配置 ES 节点信息和 spring-boot-starter-data-elasticsearch 依赖，直接在 Spring Boot 应用上使用。

**二、spring-data-elasticsearch 快速入门**
2.1 pom.xml 依赖

```xml
<dependency>
    <groupId>org.springframework.data</groupId>
    <artifactId>spring-data-elasticsearch</artifactId>
    <version>x.y.z.RELEASE</version>
</dependency>
```

2.2 ElasticsearchRepository
ES 通用的存储接口的一种默认实现。Spring 根据接口定义的方法名，具体执行对应的数据存储实现。
ElasticsearchRepository 继承 ElasticsearchCrudRepository ，ElasticsearchCrudRepository 继承 PagingAndSortingRepository。所以一般 CRUD 带分页已经支持。如图：

![img](http://springforall.ufile.ucloud.com.cn/static/img/ed0a269bbe277c71e09e5e3e6647057d1512459)

2.3 ElasticsearchTemplate
ES 数据操作的中心支持类。和 JdbcTemplate 一样，几乎所有操作都可以使用 ElasticsearchTemplate 来完成。
ElasticsearchTemplate 实现了 ElasticsearchOperations 和 ApplicationContextAware 接口。ElasticsearchOperations 接口提供了 ES 相关的操作，并将 ElasticsearchTemplate 加入到 Spring 上下文。如图：

![img](http://springforall.ufile.ucloud.com.cn/static/img/a1d036e18b15e162f265183612591f021512459)

2.4 使用案例
拿官方案例来吧，详细介绍了 Book ES 对象的接口实现。
可以看出，book 拥有 name 和 price 两个属性。下面支持 name 和 price 列表 ES 查询，分页查询，范围查询等。还有可以利用注解实现 DSL 操作。

```java
    public interface BookRepository extends Repository<Book, String> {
        List<Book> findByNameAndPrice(String name, Integer price);
        List<Book> findByNameOrPrice(String name, Integer price);
        Page<Book> findByName(String name,Pageable page);
        Page<Book> findByNameNot(String name,Pageable page);
        Page<Book> findByPriceBetween(int price,Pageable page);
        Page<Book> findByNameLike(String name,Pageable page);
        @Query("{\"bool\" : {\"must\" : {\"term\" : {\"message\" : \"?0\"}}}}")
        Page<Book> findByMessage(String message, Pageable pageable);
    }
```

**三、spring-data-elasticsearch 和 elasticsearch 版本**
SpringBoot 1.5+ 目前仅支持 ElasticSearch 2.3.2，所以如果想要使用最新的 ES。可以通过默认的轻量级的 HTTP 去调用实现。其版本对应如下:

| spring data elasticsearch | elasticsearch |
| ------------------------- | ------------- |
| 3.2.x                     | 6.5.0         |
| 3.1.x                     | 6.2.2         |
| 3.0.x                     | 5.5.0         |
| 2.1.x                     | 2.4.0         |
| 2.0.x                     | 2.2.0         |
| 1.3.x                     | 1.5.2         |

**四、小结**
本小结介绍了 spring-data-elasticsearch 是概述以及它的入门，还有 spring-data-elasticsearch 核心接口及版本的情况。

资料：
项目地址
[https://github.com/spring-proj … earch](https://github.com/spring-projects/spring-data-elasticsearch)
官方文档
<http://docs.spring.io/spring-data/elasticsearch/docs/current/reference/html/>

> 本文作者： 泥瓦匠
> 原文链接： [http://www.bysocket.com](http://www.bysocket.com/)
> 版权归作者所有，转载请注明出处