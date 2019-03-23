# SpringCloud 是什么？

**参考链接：**

**http://blog.csdn.net/forezp/article/details/70148833** 

博客的原始地址：[原文出处](https://www.cnblogs.com/lexiaofei/p/6808152.html)

# 一、概念定义 

​      Spring Cloud 是一个微服务框架，相比 Dubbo 等 RPC 框架, **Spring Cloud 提供的全套的分布式系统解决方案**。 

​      Spring Cloud 对微服务基础框架 Netflix 的多个开源组件进行了封装，同时又实现了和云端平台以及和 Spring Boot 开发框架的集成。 

​      Spring Cloud 为微服务架构开发涉及的**配置管理，服务治理，熔断机制，智能路由，微代理，控制总线，一次性 token，全局一致性锁，leader 选举，分布式 session，集群状态**管理等操作提供了一种简单的开发方式。

​      Spring Cloud 为开发者提供了快速构建**分布式系统的工具**，开发者可以快速的启动服务或构建应用、同时能够快速和云平台资源进行对接。   

#  

# **二、Spring Cloud 的项目的位置**

**Sping Cloud 是 Spring 的一个顶级项目，Spring 的顶级项目列表如下****：**

-  Spring IO platform: 用于系统部署，是可集成的，构建现代化应用的版本平台，具体来说当你使用 maven dependency 引入 spring jar 包时它就在工作了。
-  **Spring Boot: 旨在简化创建产品级的 Spring 应用和服务，简化了配置文件，使用嵌入式 web 服务器，含有诸多开箱即用微服务功能，可以和 spring cloud 联合部署。**
-  Spring Framework: 即通常所说的 spring 框架，是一个开源的 Java/Java EE 全功能栈应用程序框架，其它 spring 项目如 spring boot 也依赖于此框架。
-  **Spring Cloud：微服务工具包，为开发者提供了在分布式系统的配置管理、服务发现、断路器、智能路由、微代理、控制总线等开发工具包。**
-  Spring XD：是一种运行时环境（服务器软件，非开发框架），组合 spring 技术，如 spring batch、spring boot、spring data，采集大数据并处理。
-  Spring Data：是一个数据访问及操作的工具包，封装了很多种数据及数据库的访问相关技术，包括：jdbc、Redis、MongoDB、Neo4j 等。
-  Spring Batch：批处理框架，或说是批量任务执行管理器，功能包括任务调度、日志记录 / 跟踪等。
-  Spring Security：是一个能够为基于 Spring 的企业应用系统提供声明式的安全访问控制解决方案的安全框架。
-  Spring Integration：面向企业应用集成（EAI/ESB）的编程框架，支持的通信方式包括 HTTP、FTP、TCP/UDP、JMS、RabbitMQ、Email 等。
-  Spring Social：一组工具包，一组连接社交服务 API，如 Twitter、Facebook、LinkedIn、GitHub 等，有几十个。
-  Spring AMQP：消息队列操作的工具包，主要是封装了 RabbitMQ 的操作。
-  Spring HATEOAS：是一个用于支持实现超文本驱动的 REST Web 服务的开发库。
-  Spring Mobile：是 Spring MVC 的扩展，用来简化手机上的 Web 应用开发。
-  Spring for Android：是 Spring 框架的一个扩展，其主要目的在乎简化 Android 本地应用的开发，提供 RestTemplate 来访问 Rest 服务。
-  Spring Web Flow：目标是成为管理 Web 应用页面流程的最佳方案，将页面跳转流程单独管理，并可配置。
-  Spring LDAP：是一个用于操作 LDAP 的 Java 工具包，基于 Spring 的 JdbcTemplate 模式，简化 LDAP 访问。
-  Spring Session：session 管理的开发工具包，让你可以把 session 保存到 redis 等，进行集群化 session 管理。
-  Spring Web Services：是基于 Spring 的 Web 服务框架，提供 SOAP 服务开发，允许通过多种方式创建 Web 服务。
-  Spring Shell：提供交互式的 Shell 可让你使用简单的基于 Spring 的编程模型来开发命令，比如 Spring Roo 命令。
-  Spring Roo：是一种 Spring 开发的辅助工具，使用命令行操作来生成自动化项目，操作非常类似于 Rails。
-  Spring Scala：为 Scala 语言编程提供的 spring 框架的封装（新的编程语言，Java 平台的 Scala 于 2003 年底 / 2004 年初发布）。
-  Spring BlazeDS Integration：一个开发 RIA 工具包，可以集成 Adobe Flex、BlazeDS、Spring 以及 Java 技术创建 RIA。
-  Spring Loaded：用于实现 java 程序和 web 应用的热部署的开源工具。
-  Spring REST Shell：可以调用 Rest 服务的命令行工具，敲命令行操作 Rest 服务。

# 三、Spring Cloud 的子项目

Spring Cloud 包含了很多子项目，如：

 



![img](https://images2015.cnblogs.com/blog/1099841/201705/1099841-20170519165628135-1090904605.png)



- **Spring Cloud Config：配置管理工具，支持使用 Git 存储配置内容，支持应用配置的外部化存储，支持客户端配置信息刷新、加解密配置内容等**
- Spring Cloud Bus：事件、消息总线，用于在集群（例如，配置变化事件）中传播状态变化，可与 Spring Cloud Config 联合实现热部署。
- **Spring Cloud Netflix：针对多种 Netflix 组件提供的开发工具包，其中包括 Eureka、Hystrix、Zuul、Archaius 等。**
- ​                      **Netflix Eureka：一个基于 rest 服务的服务治理组件，包括服务注册中心、服务注册与服务发现机制的实现，实现了云端负载均衡和中间层服务器的故障转移。**
- ​                      **Netflix Hystrix：容错管理工具，实现断路器模式，通过控制服务的节点, 从而对延迟和故障提供更强大的容错能力。**
- ​                      **Netflix Ribbon：客户端负载均衡的服务调用组件。**
- ​                      **Netflix Feign：基于 Ribbon 和 Hystrix 的声明式服务调用组件。**
- ​                      **Netflix Zuul：微服务网关，提供动态路由，访问过滤等服务。**
- ​                      **Netflix Archaius：配置管理 API，包含一系列配置管理 API，提供动态类型化属性、线程安全配置操作、轮询框架、回调机制等功能。**
- Spring Cloud for Cloud Foundry：通过 Oauth2 协议绑定服务到 CloudFoundry，CloudFoundry 是 VMware 推出的开源 PaaS 云平台。
- Spring Cloud Sleuth：日志收集工具包，封装了 Dapper,Zipkin 和 HTrace 操作。
- Spring Cloud Data Flow：大数据操作工具，通过命令行方式操作数据流。
- Spring Cloud Security：安全工具包，为你的应用程序添加安全控制，主要是指 OAuth2。
- Spring Cloud Consul：封装了 Consul 操作，consul 是一个服务发现与配置工具，与 Docker 容器可以无缝集成。
- Spring Cloud Zookeeper：操作 Zookeeper 的工具包，用于使用 zookeeper 方式的服务注册和发现。
- Spring Cloud Stream：数据流操作开发包，封装了与 Redis,Rabbit、Kafka 等发送接收消息。
- Spring Cloud CLI：基于 Spring Boot CLI，可以让你以命令行方式快速建立云组件。

# 四、Spring Cloud 的 版本

Spring Cloud 不像其他 Spring 子项目那样相对独立，它是一个拥有诸多子项目的大型综合项目。

Spring Cloud 可以说是微服务架构解决方案的综合套件组合，其包含的子项目也都独立进行着内容更新与迭代，各自都维护着自己的发布版本号。

因此每个 Spring Cloud 版本，包含着多个不同版本的子项目，为了管理每个版本的子项目清单，避免 SpringCloud 版本号与其子项目版本号混淆，没有采用版本号方式，而是采用命名方式。

这些版本的名字采用了伦敦地铁站的名字，根据字母表顺序来对应版本时间顺序, 如：Angel.SR6,Brixton.SR5,Brixton.SR7,Camden.M1.

注意：使用 Brixton 版本要注意 SpringBoot 的对应版本，必须要使用 1.3.x，而不能使用 1.4.x