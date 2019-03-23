# 深入浅出 spring-data-elasticsearch – 实战案例详解

运行环境：JDK 7 或 8，Maven 3.0+
技术栈：SpringBoot 1.5+， Spring Data Elasticsearch 1.5+ ，ElasticSearch 2.3.2

#### 本文提纲

- 一、搜索实战场景需求
- 二、运行 spring-data-elasticsearch-query 工程
- 三、spring-data-elasticsearch-query 工程代码详解

## 一、搜索实战场景需求

搜索的场景会很多，常用的搜索场景，需要搜索的字段很多，但每个字段匹配到后所占的权重又不同。比如电商网站的搜索，搜到商品名称和商品描述，自然商品名称的权重远远大于商品描述。而且单词匹配肯定不如短语匹配。这样就出现了新的需求，如何确定这些短语，即自然分词。那就利用分词器，即可得到所需要的短语，然后进行搜索。

下面介绍短语如何进行按权重分匹配搜索。

## 二、运行 spring-data-elasticsearch-query 工程

### 1. 后台起守护线程启动 Elasticsearch

```bash
cd elasticsearch-2.3.2/
./bin/elasticsearch -d
```

git clone 下载工程 springboot-elasticsearch ，项目地址见 GitHub - <https://github.com/JeffLi1993/> … ample。

下面开始运行工程步骤（Quick Start）：

### 2. 项目结构介绍

```
org.spring.springboot.controller - Controller 层
org.spring.springboot.repository - ES 数据操作层
org.spring.springboot.domain - 实体类
org.spring.springboot.service - ES 业务逻辑层
Application - 应用启动类
application.properties - 应用配置文件，应用启动会自动读取配置
```

本地启动的 ES ，就不需要改配置文件了。如果连测试 ES 服务地址，需要修改相应
配置

### 3.编译工程

在项目根目录 spring-data-elasticsearch-query，运行 maven 指令：

```
mvn clean install
```

### 4.运行工程

右键运行 Application 应用启动类（位置：org/spring/springboot/Application.java）的 main 函数，这样就成功启动了 spring-data-elasticsearch-query 案例。
用 Postman 工具新增两个城市

a. 新增城市信息

```
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

下面是实战搜索语句的接口：

```
GET http://localhost:8080/api/city ... nt%3D城市
```

获取返回结果：
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

应用的控制台中，日志打印出查询语句的 DSL ：

```javascript
 DSL  = 
 {
  "function_score" : {
    "functions" : [ {
      "filter" : {
        "match" : {
          "name" : {
            "query" : "城市",
            "type" : "phrase"
          }
        }
      },
      "weight" : 1000.0
    }, {
      "filter" : {
        "match" : {
          "description" : {
            "query" : "城市",
            "type" : "phrase"
          }
        }
      },
      "weight" : 500.0
    } ],
    "score_mode" : "sum",
    "min_score" : 10.0
  }
}
```

## 三、spring-data-elasticsearch-query 工程代码详解

具体代码见 GitHub - <https://github.com/JeffLi1993/springboot-learning-example>

### 1.pom.xml 依赖

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

这里依赖的 spring-boot-starter-data-elasticsearch 版本是 1.5.1.RELEASE，对应的 spring-data-elasticsearch 版本是 2.1.0.RELEASE。对应官方文档：<http://docs.spring.io/spring-d> … html/。后面数据操作层都是通过该 spring-data-elasticsearch 提供的接口实现。

application.properties 配置 ES 地址

```properties
# ES
spring.data.elasticsearch.repositories.enabled = true
spring.data.elasticsearch.cluster-nodes = 127.0.0.1:9300
```

默认 9300 是 Java 客户端的端口。9200 是支持 Restful HTTP 的接口。
更多配置：

- spring.data.elasticsearch.cluster-name Elasticsearch 集群名。(默认值: elasticsearch)
- spring.data.elasticsearch.cluster-nodes 集群节点地址列表，用逗号分隔。如果没有指定，就启动一个客户端节点。
- spring.data.elasticsearch.propertie 用来配置客户端的额外属性。
- spring.data.elasticsearch.repositories.enabled 开启 Elasticsearch 仓库。(默认值:true。)

### 3. ES 数据操作层

```java
/**
 * ES 操作类
 * <p>
 * Created by bysocket on 17/05/2017.
 */
public interface CityRepository extends ElasticsearchRepository<City, Long> {
}
接口只要继承 ElasticsearchRepository 接口类即可，具体使用的是该接口的方法：

    Iterable<T> search(QueryBuilder query);
    Page<T> search(QueryBuilder query, Pageable pageable);
    Page<T> search(SearchQuery searchQuery);
    Page<T> searchSimilar(T entity, String[] fields, Pageable pageable);
4. 实体类

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

注意
a. City 属性名不支持驼峰式。
b. indexName 配置必须是全部小写，不然会出异常。
org.elasticsearch.indices.InvalidIndexNameException: Invalid index name [provinceIndex], must be lowercase

### 5. 城市 ES 业务逻辑实现类

代码如下：

```java
/**
 * 城市 ES 业务逻辑实现类
 * <p>
 * Created by bysocket on 20/06/2017.
 */
@Service
public class CityESServiceImpl implements CityService {
    private static final Logger LOGGER = LoggerFactory.getLogger(CityESServiceImpl.class);
    /* 分页参数 */
    Integer PAGE_SIZE = 12;          // 每页数量
    Integer DEFAULT_PAGE_NUMBER = 0; // 默认当前页码
    /* 搜索模式 */
    String SCORE_MODE_SUM = "sum"; // 权重分求和模式
    Float  MIN_SCORE = 10.0F;      // 由于无相关性的分值默认为 1 ，设置权重分最小值为 10
    @Autowired
    CityRepository cityRepository; // ES 操作类
    public Long saveCity(City city) {
        City cityResult = cityRepository.save(city);
        return cityResult.getId();
    }
    @Override
    public List<City> searchCity(Integer pageNumber, Integer pageSize, String searchContent) {
        // 校验分页参数
        if (pageSize == null || pageSize <= 0) {
            pageSize = PAGE_SIZE;
        }
        if (pageNumber == null || pageNumber < DEFAULT_PAGE_NUMBER) {
            pageNumber = DEFAULT_PAGE_NUMBER;
        }
        LOGGER.info("\n searchCity: searchContent [" + searchContent + "] \n ");
        // 构建搜索查询
        SearchQuery searchQuery = getCitySearchQuery(pageNumber,pageSize,searchContent);
        LOGGER.info("\n searchCity: searchContent [" + searchContent + "] \n DSL  = \n " + searchQuery.getQuery().toString());
        Page<City> cityPage = cityRepository.search(searchQuery);
        return cityPage.getContent();
    }
    /**
     * 根据搜索词构造搜索查询语句
     *
     * 代码流程：
     *      - 权重分查询
     *      - 短语匹配
     *      - 设置权重分最小值
     *      - 设置分页参数
     *
     * @param pageNumber 当前页码
     * @param pageSize 每页大小
     * @param searchContent 搜索内容
     * @return
     */
    private SearchQuery getCitySearchQuery(Integer pageNumber, Integer pageSize,String searchContent) {
        // 短语匹配到的搜索词，求和模式累加权重分
        // 权重分查询 https://www.elastic.co/guide/c ... .html
        //   - 短语匹配 https://www.elastic.co/guide/c ... .html
        //   - 字段对应权重分设置，可以优化成 enum
        //   - 由于无相关性的分值默认为 1 ，设置权重分最小值为 10
        FunctionScoreQueryBuilder functionScoreQueryBuilder = QueryBuilders.functionScoreQuery()
                .add(QueryBuilders.matchPhraseQuery("name", searchContent),
                ScoreFunctionBuilders.weightFactorFunction(1000))
                .add(QueryBuilders.matchPhraseQuery("description", searchContent),
                ScoreFunctionBuilders.weightFactorFunction(500))
                .scoreMode(SCORE_MODE_SUM).setMinScore(MIN_SCORE);
        // 分页参数
        Pageable pageable = new PageRequest(pageNumber, pageSize);
        return new NativeSearchQueryBuilder()
                .withPageable(pageable)
                .withQuery(functionScoreQueryBuilder).build();
    }
}
```

可以看到该过程实现了，短语精准匹配以及匹配到根据字段权重分求和，从而实现按权重搜索查询。代码流程如下:

- 权重分查询
- 短语匹配
- 设置权重分最小值
- 设置分页参数

注意：

- 字段对应权重分设置，可以优化成 enum
- 由于无相关性的分值默认为 1 ，设置权重分最小值为 10

权重分查询文档：<https://www.elastic.co/guide/c> … .html。
短语匹配文档： <https://www.elastic.co/guide/c> … .html。

## 四、小结

Elasticsearch 还提供很多高级的搜索功能。这里提供下需要经常逛的相关网站：
Elasticsearch 中文社区 <https://elasticsearch.cn/topic/elasticsearch>
Elasticsearch: 权威指南-在线版 <https://www.elastic.co/guide/cn/elasticsearch/guide/current/index.html>