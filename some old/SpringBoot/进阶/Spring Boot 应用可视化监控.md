# Spring Boot 应用可视化监控

## 图文简介

![逻辑关系](http://upload-images.jianshu.io/upload_images/3424642-2ac606842ee5e854.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![效果演示](http://upload-images.jianshu.io/upload_images/3424642-5d9f9c691501727c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 快速开始

#### 1、Spring Boot 应用暴露监控指标【版本 1.5.7.RELEASE】

首先，添加依赖如下依赖：

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>

        <dependency>
            <groupId>io.prometheus</groupId>
            <artifactId>simpleclient_spring_boot</artifactId>
            <version>0.0.26</version>
        </dependency>
```

然后，在启动类 `Application.java` 添加如下注解：

```java
@SpringBootApplication
@EnablePrometheusEndpoint
@EnableSpringBootMetricsCollector
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

}
```

最后，配置默认的登录账号和密码，在 `application.yml` 中：

```yml
security:
  user:
    name: user
    password: pwd
```

> 提示：不建议配置 `management.security.enabled: false`

启动应用程序后，会看到如下一系列的 `Mappings`

![Mappings](http://upload-images.jianshu.io/upload_images/3424642-139fb802996c708f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

利用账号密码访问 <http://localhost:8080/application/prometheus> ，可以看到 Prometheus 格式的指标数据
![指标数据](http://upload-images.jianshu.io/upload_images/3424642-202218a016bb5650.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 2、Prometheus 采集 Spring Boot 指标数据

首先，获取 Prometheus 的 Docker 镜像：

```shell
$ docker pull prom/prometheus
```

然后，编写配置文件 `prometheus.yml` ：

```yml
global:
  scrape_interval: 10s
  scrape_timeout: 10s
  evaluation_interval: 10m
scrape_configs:
  - job_name: spring-boot
    scrape_interval: 5s
    scrape_timeout: 5s
    metrics_path: /application/prometheus
    scheme: http
    basic_auth:
      username: user
      password: pwd
    static_configs:
      - targets:
        - 127.0.0.1:8080  #此处填写 Spring Boot 应用的 IP + 端口号
```

接着，启动 Prometheus :

```shell
$ docker run -d \
--name prometheus \
-p 9090:9090 \
-m 500M \
-v "$(pwd)/prometheus.yml":/prometheus.yml \
-v "$(pwd)/data":/data \
prom/prometheus \
-config.file=/prometheus.yml \
-log.level=info
```

最后，访问 <http://localhost:9090/targets> , 检查 Spring Boot 采集状态是否正常。

![采集状态](http://upload-images.jianshu.io/upload_images/3424642-663296faa0985e69.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 3、Grafana 可视化监控数据

首先，获取 Grafana 的 Docker 镜像：

```shell
$ docker pull grafana/grafana
```

然后，启动 Grafana：

```shell
$ docker run --name grafana -d -p 3000:3000 grafana/grafana
```

接着，访问 <http://localhost:3000/> 配置 Prometheus 数据源：

> Grafana 登录账号 admin 密码 admin

![配置 DataSource](http://upload-images.jianshu.io/upload_images/3424642-17cfb4ab6b48eab8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

最后，配置单个指标的可视化监控面板：

![选择 Graph](http://upload-images.jianshu.io/upload_images/3424642-7716afba5950b709.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![编辑](http://upload-images.jianshu.io/upload_images/3424642-e3d0363d4536f424.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![配置需要监控的指标](http://upload-images.jianshu.io/upload_images/3424642-010ac34b85c13ede.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

提示，此处不能任意填写，只能填已有的指标点，具体的可以在 Prometheus 的首页看到，即 <http://localhost:9090/graph>

![指标](http://upload-images.jianshu.io/upload_images/3424642-cbded1e5761d0b1d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

多配置几个指标之后，即可有如下效果：

![Grafana 监控界面](http://upload-images.jianshu.io/upload_images/3424642-1eb1e5a0fee6dc2d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 参考文档

- [prometheus 官方文档](https://prometheus.io/docs/introduction/overview/)
- [Grafana Docker 安装](http://docs.grafana.org/installation/docker/)
- [Spring Boot 官方文档](http://projects.spring.io/spring-boot/)