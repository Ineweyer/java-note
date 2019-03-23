# Spring Cloud Gateway 入门

博文摘抄自[Spring Cloud Gateway 入门](https://juejin.im/post/5aa4eacbf265da237a4ca36f)

## Spring Cloud Gateway 介绍

前段时间刚刚发布了Spring Boot 2正式版，Spring Cloud Gateway基于Spring Boot 2，是Spring Cloud的全新项目，该项目提供了一个构建在Spring 生态之上的API网关，包括：Spring 5，Spring Boot 2和Project Reactor。 Spring Cloud Gateway旨在提供一种简单而有效的途径来发送API，并为他们提供横切关注点，例如：安全性，监控/指标和弹性。当前最新的版本是`v2.0.0.M8`，正式版最近也会到来。

Spring Cloud Gateway的特征：

- Java 8
- Spring Framework 5
- Spring Boot 2
- 动态路由
- 内置到Spring Handler映射中的路由匹配
- 基于HTTP请求的路由匹配 (Path, Method, Header, Host, etc…)
- 过滤器作用于匹配的路由
- 过滤器可以修改下游HTTP请求和HTTP响应 (Add/Remove Headers, Add/Remove Parameters, Rewrite Path, Set Path, Hystrix, etc…)
- 通过API或配置驱动
- 支持Spring Cloud DiscoveryClient配置路由，与服务发现与注册配合使用

## vs Netflix Zuul

Zuul基于servlet 2.5（使用3.x），使用阻塞API。 它不支持任何长连接，如websockets。而Gateway建立在Spring Framework 5，Project Reactor和Spring Boot 2之上，使用非阻塞API。 Websockets得到支持，并且由于它与Spring紧密集成，所以将会是一个更好的开发体验。

## Spring Cloud Gateway入门实践

笔者最近研读了Spring Cloud Gateway的源码，大部分功能的实现也写了源码分析的文章，但毕竟正式版没有发布，本文算是一篇入门实践，展示常用的几个功能，期待最近的正式版本发布。

示例启动两个服务：Gateway-Server和user-Server。模拟的场景是，客户端请求后端服务，网关提供后端服务的统一入口。后端的服务都注册到服务发现Consul（搭建zk(zookeeper?)，Eureka都可以，笔者比较习惯使用consul）。网关通过负载均衡转发到具体的后端服务。

### 用户服务

用户服务注册到Consul上，并提供一个接口`/test`。

#### 依赖

需要的依赖如下：

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-consul-discovery</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
```

#### 配置文件

```yaml
spring:
  application:
    name: user-service
  cloud:
    consul:
      host: 192.168.1.204
      port: 8500
      discovery:
        ip-address: ${HOST_ADDRESS:localhost}
        port: ${SERVER_PORT:${server.port}}
        healthCheckPath: /health
        healthCheckInterval: 15s
        instance-id: user-${server.port}
        service-name: user
server:
  port: 8005
management:
  security:
    enabled: false
```

#### 暴露接口

```java
@SpringBootApplication
@RestController
@EnableDiscoveryClient
public class GatewayUserApplication {

    public static void main(String[] args) {
        SpringApplication.run(GatewayUserApplication.class, args);
    }

    @GetMapping("/test")
    public String test() {
        return "ok";
    }
}
```

暴露`/test`接口，返回ok即可。

### 网关服务

网关服务提供多种路由配置、路由断言工厂和过滤器工厂等功能。

#### 依赖

需要引入的依赖：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-actuator</artifactId>
</dependency>
//依赖于webflux，必须引入
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-webflux</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-gateway-core</artifactId>
</dependency>
//服务发现组件，排除web依赖
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-consul-discovery</artifactId>
    <version>2.0.0.M6</version>
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </exclusion>
    </exclusions>
</dependency>
//kotlin依赖
<dependency>
    <groupId>org.jetbrains.kotlin</groupId>
    <artifactId>kotlin-stdlib</artifactId>
    <version>${kotlin.version}</version>
    <optional>true</optional>
</dependency>
<dependency>
    <groupId>org.jetbrains.kotlin</groupId>
    <artifactId>kotlin-reflect</artifactId>
    <version>${kotlin.version}</version>
    <optional>true</optional>
</dependency>
```

如上引入了kotlin相关的依赖，这里需要支持kotlin的路由配置。Spring Cloud Gateway的使用需要排除web相关的配置，引入的是webflux的引用，应用启动时会检查，必须引入。

#### 路由断言工厂

路由断言工厂有多种类型，根据请求的时间、host、路径、方法等等。如下定义的是一个基于路径的路由断言匹配。

```java
    @Bean
    public RouterFunction<ServerResponse> testFunRouterFunction() {
        RouterFunction<ServerResponse> route = RouterFunctions.route(
                RequestPredicates.path("/testfun"),
                request -> ServerResponse.ok().body(BodyInserters.fromObject("hello")));
        return route;
    }
```

当请求的路径为`/testfun`时，直接返回ok的状态码，且响应体为`hello`字符串。

#### 过滤器工厂

网关经常需要对路由请求进行过滤，进行一些操作，如鉴权之后构造头部之类的，过滤的种类很多，如增加请求头、增加请求参数、增加响应头和断路器等等功能。

```java
    @Bean
    public RouteLocator customRouteLocator(RouteLocatorBuilder builder, ThrottleGatewayFilterFactory throttle) {
        //@formatter:off
        //注意其中r和f应该分别是router和filter的实例
        return builder.routes()
                .route(r -> r.path("/image/webp")
                        .filters(f ->
                                f.addResponseHeader("X-AnotherHeader", "baz"))
                        .uri("http://httpbin.org:80")
                )
                .build();
        //@formatter:on
    }
```

如上实现了当请求路径为`/image/webp`时，将请求转发到`http://httpbin.org:80`，并对响应进行过滤处理，增加响应的头部`X-AnotherHeader: baz`。

#### 自定义路由

上面两小节属于API自定义路由，还可以通过配置进行定义：

```yaml
spring:
  cloud:
    gateway:
      locator:
        enabled: true
      default-filters:
      - AddResponseHeader=X-Response-Default-Foo, Default-Bar

      routes:
      # =====================================
      - id: default_path_to_http
        uri: blueskykong.com
        order: 10000
        predicates:
        - Path=/**
```

如上的配置定义了路由与过滤器。全局过滤器将所有的响应加上头部`X-Response-Default-Foo: Default-Bar`。定义了id为`default_path_to_http`的路由，只是优先级比较低，当该请求都不能匹配时，将会转发到`blueskykong.com`。

#### kotlin自定义路由

Spring Cloud Gateway可以使用kotlin自定义路由：

```kotlin
@Configuration
class AdditionalRoutes {

	@Bean
	fun additionalRouteLocator(builder: RouteLocatorBuilder): RouteLocator = builder.routes {
		route(id = "test-kotlin") {
			path("/image/png")
			filters {
				addResponseHeader("X-TestHeader", "foobar")
			}
			uri("http://httpbin.org:80")
		}
	}
}
```

当请求的路径是`/image/png`，将会转发到`http://httpbin.org:80`，并设置了过滤器，在其响应头中加上了`X-TestHeader: foobar`头部。

#### 服务发现组件

与服务注册于发现组件进行结合，通过serviceId转发到具体的服务实例。在前面的配置已经引入了相应的依赖。

```java
    @Bean
    public RouteDefinitionLocator discoveryClientRouteDefinitionLocator(DiscoveryClient discoveryClient) {
        return new DiscoveryClientRouteDefinitionLocator(discoveryClient);
    }
```

将`DiscoveryClient`注入到`DiscoveryClientRouteDefinitionLocator`的构造函数中，关于该路由定义定位器，后面的源码分析会讲解，此处不展开。

```yaml
spring:
  cloud:
    gateway:
      locator:
        enabled: true
      default-filters:
      - AddResponseHeader=X-Response-Default-Foo, Default-Bar
      routes:
      # =====================================
      - id: service_to_user
        uri: lb://user
        order: 8000
        predicates:
        - Path=/user/**
        filters:
        - StripPrefix=1
```

上面的配置开启了`DiscoveryClient`定位器的实现。路由定义了，所有请求路径以`/user`开头的请求，都将会转发到`user`服务，并应用路径的过滤器，截取掉路径的第一部分前缀。即访问`/user/test`的实际请求转换成了`lb://user/test`。

#### websocket

还可以配置websocket的网关路由：

```yaml
spring:
  cloud:
    gateway:
      default-filters:
      - AddResponseHeader=X-Response-Default-Foo, Default-Bar

      routes:
      - id: websocket_test
        uri: ws://localhost:9000
        order: 9000
        predicates:
        - Path=/echo
```

启动一个ws服务端`wscat --listen 9000`，将网关启动（网关端口为9090），进行客户端连接即可`wscat --connect ws://localhost:9090/echo`。

### 客户端的访问

上述实现的功能，读者可以自行下载源码进行尝试。笔者这里只展示访问用户服务的结果：



![img](https://user-gold-cdn.xitu.io/2018/3/11/16214326d0892ab5?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)





![img](https://user-gold-cdn.xitu.io/2018/3/11/16214326d0928f5c?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

网关成功负载均衡到user-server，并返回了ok。响应的头部中包含了全局过滤器设置的头部

```
X-Response-Default-Foo: Default-Bar
```



## 总结

在本文中，我们探讨了属于Spring Cloud Gateway的一些功能和组件。 这个新的API提供了用于网关和代理支持的开箱即用工具。期待Spring Cloud Gateway 2.0正式版。

### 源码地址

**github.com/keets2012/S…**