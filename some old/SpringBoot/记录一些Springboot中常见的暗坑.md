# 记录一些Springboot中常见的暗坑

> 前言，集成程度越高黑箱程度越高，一个细小的问题可能导致一堆很严重的问题，是福是祸。

## 运行环境必须匹配问题

问题的起因：工程中含有需要web环境的代码，但是在测试的时候不需要使用这部分代码，简单的以为不用则不用加载，但是由于springboot的自动装载，造成如果不配置为web环境的话无法进行代码的测试。

报错会有很多但是从最后往上看就能知道问题应该就能知道问题：

```
at org.springframework.boot.autoconfigure.web.servlet.error.ErrorMvcAutoConfiguration$ErrorTemplateMissingCondition.getMatchOutcome(ErrorMvcAutoConfiguration.java:192)
	at org.springframework.boot.autoconfigure.condition.SpringBootCondition.matches(SpringBootCondition.java:47)
	... 48 more


Process finished with exit code -1
```



修改方法为，在SpringBootTest注解中添加webEnvironment：

```java
@SpringBootTest (webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
```

