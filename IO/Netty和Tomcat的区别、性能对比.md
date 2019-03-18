# [Netty和Tomcat的区别、性能对比](https://www.cnblogs.com/pangguoming/p/9353536.html)



一、Netty和Tomcat有什么区别？ 

Netty和Tomcat最大的区别就在于通信协议，Tomcat是基于Http协议的，他的实质是一个基于**http协议的web容器**，但是Netty不一样，他能通过编程**自定义各种协议**，因为netty能够通过codec自己来编码/解码字节流，完成类似redis访问的功能，这就是netty和tomcat最大的不同。

有人说netty的性能就一定比tomcat性能高，其实不然，tomcat从6.x开始就支持了nio模式，并且后续还有arp模式——一种通过jni调用apache网络库的模式，相比于旧的bio模式，并发性能得到了很大提高，特别是arp模式，而netty是否比tomcat性能更高，则要取决于netty程序作者的技术实力了。 
为什么Netty受欢迎？

netty是一款收到大公司青睐的框架，在我看来，netty能够受到青睐的原因有三： 

- 并发高 
- 传输快 
- 封装好 

Netty为什么并发高 

Netty是一款基于NIO（Nonblocking I/O，非阻塞IO）开发的网络通信框架，对比于BIO（Blocking I/O，阻塞IO），他的并发性能得到了很大提高。

 

NIO 2.0里终于有AIO了，Linux上用AIO，Windows上用IOCP，都支持了概念上的最后一种IO asynchronous I/O

1. 就IO而言：概念上有5中模型：blocking I/O，nonblocking I/O，I/O multiplexing (select and poll)，signal driven I/O (SIGIO)，asynchronous I/O (the POSIX aio_functions)。 
2. 然后呢 不同的操作系统对上述模型支持不同: unix支持io多路复用，不同系统叫法不同 :freebsd里面叫 kqueue；linux 是epoll。而windows: 2000的时候就诞生了IOCP支持最后一种异步I/O 
3. java是一种跨平台语言，为了支持异步IO,诞生了nio,Java1.4引入的NIO 1.0是基于I/O复用的。在各个平台上会选择不同的复用方式。Linux用的epoll，BSD上用kqueue，Windows上应该是重叠I/O（肯定不是IOCP）

但是nio直接使用比较难用，所以有了mina，netty这些针对网络io部分（tcp/udp-传输层）的封装（nio也有非网络io部分），为了使nio更易用。 
http是应用层的协议。 
servlet3.0则是另外一种东西，不是对协议的封装，javaee6众多规范中的一个，但凡javaee6的实现（或者像tomcat这种web容器部分的实现），都会支持servlet3.0，servlet理论上可以支持多种应用层协议（不单单只是http），而servlet3.0以后提供的异步特性与javase提供的nio或aio无直接关系，就是使用bio一样可以实现servlet3.0中提供的异步特性。 
异步只是一种概念，异步与否要看，上层使用的异步，而支持的下层完全可能是阻塞的。

- tomcat就是针对http层的，所以我建议http还是选择tomcat(或者其他成熟的http-server)，并不是说netty不好，而是你的选择问题。
- netty是一个网络组件，tcp,udp,http都可以弄，但是官方文档都是些hello wolrd级别的。如果你非常了解http结构，完全可以基于netty搞出一个比tomcat牛的http server。如果做tcp开发，netty不二之选！

现在高并发分布式网站架构一般采用nginx（前端负载均衡）+ Netty/Tomcat（HTTP）

Netty是基于Java NIO开发的，而Tomcat是Apache下的针对HTTP的服务器项目，前者更像一个中间件框架，后者更像一个工具