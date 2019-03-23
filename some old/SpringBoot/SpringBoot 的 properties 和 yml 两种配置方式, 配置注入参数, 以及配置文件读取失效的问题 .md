# SpringBoot 的 properties 和 yml 两种配置方式, 配置注入参数, 以及配置文件读取失效的问题 - zzzgd_666 的博客 - CSDN 博客

版权声明：转载请标明出处~~	https://blog.csdn.net/zzzgd_666/article/details/80316174

SpringBoot 支持两种配置方式, 一种是`properties`文件, 一种是`yml`

首先在 pom 文件中添加依赖:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>
```

### 1. yml 配置文件绑定属性

**使用 properties 可能会有中文乱码的问题, 而使用 yml 可以避免这种情况 **
,yml 的结构与 json 相似: 
注意: yml 冒号后要有空格, 如果不加空格, 会导致 **yml 配置读取失效** 

![img](https://img-blog.csdn.net/20180514175938225?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p6emdkXzY2Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

没有空格, 颜色都没有变色, 显然有问题.



list 用`-`, 使用 markdown 的都应该熟悉, map 是跟 json 一样 key: value

```yml
user:
   username: "小红"
   password: "${user.username}123"
   # 100以内的随机数
   age: ${random.int(100)}
   array : 1,2,3,4,5,6,7
   strlist: 
     - list1
     - list2
     - list3
   maplist: 
     - name: abc
       value: abcValue
     - name: 123
       value: 123Value

   map: 
     key1: value1
     key2: value2

server:
  # 端口号
   port: 8081
```

对应的 User 类: 
`@ConfigurationProperties`这个注解, 会将 yml 配置文件中属性名一样的值注入到 User 类对应的字段中, 相当于给每个字段加上 @Value

```java
@Component
//@ConfigurationProperties设置前缀user
@ConfigurationProperties(prefix = "user")
public class User {

    private int userid;

    private String username;

    private String password;

    private int age;

    private String[] array ;

    private List<Map<String, String>> maplist = new ArrayList<Map<String, String>>();


    private List<String> strlist = new ArrayList<>();

    private Map<String, String> map = new HashMap<>();

.....
```

比如`password`, 对应的配置是`user.password`, 所以设置一个前缀`user`

#### Controller 层

```java
@RestController
@RequestMapping("/boot")
public class MyController {

    @Autowired
    private User user;

    @RequestMapping("/hello")
    public String hello() {
        return "这是第一个springboot";
    }

    @RequestMapping("/user")
    public User getUser() throws JsonProcessingException {
        ObjectMapper objectMapper = new ObjectMapper();
        //测试加载yml文件
        System.out.println("username: " + user.getUsername());
        System.out.println("username: " + user.getPassword());
        System.out.println("age: " + user.getAge());
        System.out.println("array: " + objectMapper.writeValueAsString(user.getArray()));
        System.out.println("maplist: " + objectMapper.writeValueAsString(user.getMaplist()));
        System.out.println("strlist: " + objectMapper.writeValueAsString(user.getStrlist()));
        System.out.println("map: " + objectMapper.writeValueAsString(user.getMap()));
        return user;
    }

}
```

页面显示结果和控制台结果 

![img](https://img-blog.csdn.net/20180514191153177?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p6emdkXzY2Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![img](https://img-blog.csdn.net/20180514221424496?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p6emdkXzY2Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)



### 2. properties 配置方式

*.properties 文件中的中文默认以 ISO-8859-1 方式编码，因此需要对中文内容进行重新编码

```java
public String getTitle3() {
        try {
            return new String(title3.getBytes("ISO-8859-1"), "UTF-8");
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        }
        return title3;
    }
```

#### 2.1 @ConfigurationProperties 方式

application.properties

```properties
com.zyd.type3=Springboot - @ConfigurationProperties
com.zyd.title3=使用@ConfigurationProperties获取配置文件
#map
com.zyd.login[username]=zhangdeshuai
com.zyd.login[password]=zhenshuai
com.zyd.login[callback]=http://www.flyat.cc
#list
com.zyd.urls[0]=http://ztool.cc
com.zyd.urls[1]=http://ztool.cc/format/js
com.zyd.urls[2]=http://ztool.cc/str2image
com.zyd.urls[3]=http://ztool.cc/json2Entity
com.zyd.urls[4]=http://ztool.cc/ua
```

在需要赋值的类上打上注解`@ConfigurationProperties`

```java
@Component
@ConfigurationProperties(prefix = "com.zyd")
// PropertySource默认取application.properties
// @PropertySource(value = "config.properties")
public class PropertiesConfig {

    public String type3;
    public String title3;
    public Map<String, String> login = new HashMap<String, String>();
    public List<String> urls = new ArrayList<>();

    // 转换编码格式
    public String getTitle3() {
        try {
            return new String(title3.getBytes("ISO-8859-1"), "UTF-8");
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        }
        return title3;
    }

    ....getter setter
```

main Application 类:

```java
@SpringBootApplication
@RestController
public class Applaction {

    @Autowired
    private PropertiesConfig propertiesConfig;

    /**
     * 
     * 第一种方式：使用`@ConfigurationProperties`注解将配置文件属性注入到配置对象类中
     * 
     * @author zyd
     * @throws UnsupportedEncodingException
     * @since JDK 1.7
     */
    @RequestMapping("/config")
    public Map<String, Object> configurationProperties() {
        Map<String, Object> map = new HashMap<String, Object>();
        map.put("type", propertiesConfig.getType3());
        map.put("title", propertiesConfig.getTitle3());
        map.put("login", propertiesConfig.getLogin());
        map.put("urls", propertiesConfig.getUrls());
        return map;
    }

    public static void main(String[] args) throws Exception {
        SpringApplication application = new SpringApplication(Applaction.class);
        application.run(args);
    }
}
```

运行结果:

```java
{"title":"使用@ConfigurationProperties获取配置文件",
"urls":["http://ztool.cc","http://ztool.cc/format/js","http://ztool.cc/str2image","http://ztool.cc/json2Entity","http://ztool.cc/ua"],"login":{"username":"zhangdeshuai","callback":"http://www.flyat.cc","password":"zhenshuai"},"type":"Springboot - @ConfigurationProperties"}
缩进量：    引号   显示控制  展开 叠起 2级 3级 4级 5级 6级 7级 8级 
格式化JSON：
{
    "title": "使用@ConfigurationProperties获取配置文件", 
    "urls": [
        "http://ztool.cc", 
        "http://ztool.cc/format/js", 
        "http://ztool.cc/str2image", 
        "http://ztool.cc/json2Entity", 
        "http://ztool.cc/ua"
    ], 
    "login": {
        "username": "zhangdeshuai", 
        "callback": "http://www.flyat.cc", 
        "password": "zhenshuai"
    }, 
    "type": "Springboot - @ConfigurationProperties"
}
```

#### 2.2 @Value 注解方式

在需要赋值的属性上, 加上 @value 注解 
main Application 类:

```java
@SpringBootApplication
@RestController
public class Applaction {

    @Value("${com.zyd.type}")
    private String type;

    @Value("${com.zyd.title}")
    private String title;

    /**
     * 
     * 第二种方式：使用`@Value("${propertyName}")`注解
     * 
     * @author zyd
     * @throws UnsupportedEncodingException
     * @since JDK 1.7
     */
    @RequestMapping("/value")
    public Map<String, Object> value() throws UnsupportedEncodingException {
        Map<String, Object> map = new HashMap<String, Object>();
        map.put("type", type);
        // *.properties文件中的中文默认以ISO-8859-1方式编码，因此需要对中文内容进行重新编码
        map.put("title", new String(title.getBytes("ISO-8859-1"), "UTF-8"));
        return map;
    }

    public static void main(String[] args) throws Exception {
        SpringApplication application = new SpringApplication(Applaction.class);
        application.run(args);
    }
}
```

运行结果

```
{"title":"使用@Value获取配置文件","type":"Springboot - @Value"}
```

全文完本文由 [简悦 SimpRead](http://ksria.com/simpread) 优化，用以提升阅读体验。



[1. yml 配置文件绑定属性](https://blog.csdn.net/zzzgd_666/article/details/80316174#1-yml%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E7%BB%91%E5%AE%9A%E5%B1%9E%E6%80%A7-0)[Controller 层](https://blog.csdn.net/zzzgd_666/article/details/80316174#controller%E5%B1%82-1)[2. properties 配置方式](https://blog.csdn.net/zzzgd_666/article/details/80316174#2-properties%E9%85%8D%E7%BD%AE%E6%96%B9%E5%BC%8F-2)[2.1 @ConfigurationProperties 方式](https://blog.csdn.net/zzzgd_666/article/details/80316174#21-configurationproperties%E6%96%B9%E5%BC%8F-3)[2.2 @Value 注解方式](https://blog.csdn.net/zzzgd_666/article/details/80316174#22-value%E6%B3%A8%E8%A7%A3%E6%96%B9%E5%BC%8F-4)