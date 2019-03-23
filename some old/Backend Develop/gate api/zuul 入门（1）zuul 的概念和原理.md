# zuul 入门（1）zuul 的概念和原理

博客转自[zuul 入门（1）zuul 的概念和原理](https://www.cnblogs.com/lexiaofei/p/7080257.html)

### 一、zuul 是什么

zuul 是 netflix 开源的一个 API Gateway 服务器, 本质上是一个 web servlet 应用。

Zuul 在云平台上提供动态路由，监控，弹性，安全等边缘服务的框架。Zuul 相当于是设备和 Netflix 流应用的 Web 网站后端所有请求的前门。

zuul 的例子可以参考 netflix 在 github 上的 simple webapp，可以按照 netflix 在 github wiki 上文档说明来进行使用。

### 二、zuul 的工作原理

#### **1、过滤器机制**

zuul 的核心是一系列的 **filters**, 其作用可以类比 Servlet 框架的 Filter，或者 AOP。

zuul 把 Request route 到 用户处理逻辑 的过程中，这些 filter 参与一些过滤处理，比如 Authentication，Load Shedding （负载均衡）等。  



![img](https://images2015.cnblogs.com/blog/486074/201702/486074-20170220185335288-1703224333.png)

**Zuul 提供了一个框架，可以对过滤器进行动态的加载，编译，运行。**

Zuul 的过滤器之间没有直接的相互通信，他们之间通过一个 RequestContext 的静态类来进行数据传递的。RequestContext 类中有 ThreadLocal 变量来记录每个 Request 所需要传递的数据。

Zuul 的过滤器是由 Groovy 写成，这些过滤器文件被放在 Zuul Server 上的特定目录下面，Zuul 会定期轮询这些目录，修改过的过滤器会动态的加载到 Zuul Server 中以便过滤请求使用。

下面有几种标准的过滤器类型：

Zuul 大部分功能都是通过过滤器来实现的。Zuul 中定义了四种标准过滤器类型，这些过滤器类型对应于请求的典型生命周期。

(1) PRE：这种过滤器在请求被路由之前调用。我们可利用这种过滤器实现身份验证、在集群中选择请求的微服务、记录调试信息等。

(2) ROUTING：这种过滤器将请求路由到微服务。这种过滤器用于构建发送给微服务的请求，并使用 Apache HttpClient 或 Netfilx Ribbon 请求微服务。

(3) POST：这种过滤器在路由到微服务以后执行。这种过滤器可用来为响应添加标准的 HTTP Header、收集统计信息和指标、将响应从微服务发送给客户端等。

(4) ERROR：在其他阶段发生错误时执行该过滤器。

**内置的特殊过滤器**

zuul 还提供了一类特殊的过滤器，分别为：StaticResponseFilter 和 SurgicalDebugFilter

StaticResponseFilter：StaticResponseFilter 允许从 Zuul 本身生成响应，而不是将请求转发到源。

SurgicalDebugFilter：SurgicalDebugFilter 允许将特定请求路由到分隔的调试集群或主机。

**自定义的过滤器**

除了默认的过滤器类型，Zuul 还允许我们创建自定义的过滤器类型。

例如，我们可以定制一种 STATIC 类型的过滤器，直接在 Zuul 中生成响应，而不将请求转发到后端的微服务。

 

#### 2、过滤器的生命周期

Zuul 请求的生命周期如图，该图详细描述了各种类型的过滤器的执行顺序。



![img](https://images2015.cnblogs.com/blog/1099841/201706/1099841-20170630111344414-1260445909.png)



 

#### 3、过滤器调度过程



![img](https://images2015.cnblogs.com/blog/1099841/201706/1099841-20170630111313743-1947521207.png)



####  4、动态加载过滤器



![img](https://images2015.cnblogs.com/blog/1099841/201706/1099841-20170630111612664-867331227.png)



 

![img](https://images2015.cnblogs.com/blog/1099841/201706/1099841-20170630111803243-1714992783.png)



### 三、zuul 能做什么？

Zuul 可以通过加载动态过滤机制，从而实现以下各项功能：

- **验证与安全保障**: 识别面向各类资源的验证要求并拒绝那些与要求不符的请求。
- **审查与监控**: 在边缘位置追踪有意义数据及统计结果，从而为我们带来准确的生产状态结论。
- **动态路由**: 以动态方式根据需要将请求路由至不同后端集群处。
- **压力测试**: 逐渐增加指向集群的负载流量，从而计算性能水平。
- **负载分配**: 为每一种负载类型分配对应容量，并弃用超出限定值的请求。
- **静态响应处理**: 在边缘位置直接建立部分响应，从而避免其流入内部集群。
- **多区域弹性**: 跨越 AWS 区域进行请求路由，旨在实现 ELB 使用多样化并保证边缘位置与使用者尽可能接近。

除此之外，Netflix 公司还利用 Zuul 的功能通过金丝雀版本实现精确路由与压力测试。

### 四、zuul 与应用的集成方式

#### 1、ZuulServlet - 处理请求（调度不同阶段的 filters，处理异常等） 

`ZuulServlet`类似 SpringMvc 的`DispatcherServlet`，所有的 Request 都要经过`ZuulServlet`的处理

三个核心的方法`preRoute()`,`route()`, `postRoute()`，zuul 对 request 处理逻辑都在这三个方法里

`ZuulServlet`交给`ZuulRunner`去执行。

由于`ZuulServlet`是单例，因此`ZuulRunner`也仅有一个实例。

`ZuulRunner`直接将执行逻辑交由`FilterProcessor`处理，`FilterProcessor`也是单例，其功能就是依据 filterType 执行 filter 的处理逻辑

`FilterProcessor`对 filter 的处理逻辑。

- 首先根据 **Type** 获取所有输入该 Type 的 filter，`List<ZuulFilter> list`。
- 遍历该 list，执行每个 filter 的处理逻辑，`processZuulFilter(ZuulFilter filter)`
- `RequestContext`对每个 filter 的执行状况进行记录，应该留意，此处的执行状态主要包括其执行时间、以及执行成功或者失败，如果执行失败则对异常封装后抛出。 
- 到目前为止，zuul 框架对每个 filter 的执行结果都没有太多的处理，它没有把上一 filter 的执行结果交由下一个将要执行的 filter，仅仅是记录执行状态，如果执行失败抛出异常并终止执行。



![img](https://images2015.cnblogs.com/blog/1099841/201706/1099841-20170630135418461-928600442.png)



#### 2、ContextLifeCycleFilter - RequestContext 的生命周期管理 

ContextLifecycleFilter 的核心功能是为了清除 RequestContext； 请求上下文 RequestContext 通过 ThreadLocal 存储，需要在请求完成后删除该对象。 

`RequestContext`提供了执行 filter Pipeline 所需要的 Context，因为 Servlet 是单例多线程，这就要求 RequestContext 即要**线程安全**又要 **Request 安全**。

context 使用`ThreadLocal`保存，这样每个 worker 线程都有一个与其绑定的`RequestContext`，因为 worker 仅能同时处理一个 Request，这就保证了 Request Context 即是线程安全的又是Request 安全的。



![img](https://images2015.cnblogs.com/blog/1099841/201706/1099841-20170630140453321-1334991726.png)



#### 3、GuiceFilter - GOOLE-IOC(Guice 是 Google 开发的一个轻量级，基于 Java5（主要运用泛型与注释特性）的依赖注入框架 (IOC)。Guice 非常小而且快。) 



![img](https://images2015.cnblogs.com/blog/1099841/201706/1099841-20170630140424961-647238290.png)



#### 4、StartServer - 初始化 zuul 各个组件 （ioc、插件、filters、数据库等）



![img](https://images2015.cnblogs.com/blog/1099841/201706/1099841-20170630135458774-1502406791.png)



#### 5、FilterScriptManagerServlet -  uploading/downloading/managing scripts， 实现热部署

Filter 源码文件放在 zuul 服务特定的目录， zuul server 会定期扫描目录下的文件的变化，动态的读取 \ 编译 \ 运行这些 filter,

如果有 Filter 文件更新，源文件会被动态的读取，编译加载进入服务，接下来的 Request 处理就由这些新加入的 filter 处理。



![img](https://images2015.cnblogs.com/blog/1099841/201706/1099841-20170630140816508-1603962164.png)



#### ![img](https://images2015.cnblogs.com/blog/1099841/201706/1099841-20170630140923649-1850091399.png)