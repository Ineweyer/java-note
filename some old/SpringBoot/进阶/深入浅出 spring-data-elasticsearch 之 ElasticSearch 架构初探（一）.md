# 深入浅出 spring-data-elasticsearch 之 ElasticSearch 架构初探（一）



**本文目录**
一、Elasticsearch 基本术语
1.1 文档（Document）、索引（Index）、类型（Type）文档三要素
1.2 集群（Cluster）、节点（Node）、分片（Shard）分布式三要素
二、Elasticsearch 工作原理
2.1 文档存储的路由
2.2 如何健康检查
2.3 如何水平扩容
三、小结

> [Spring For All 社区](http://spring4all.com/)（[spring4all.com](http://spring4all.com/)）是新组建的关于 Spring 的纯技术交流社区。来社区找我吧。

**一、Elasticsearch 基本术语**

**1.1 文档（Document）、索引（Index）、类型（Type）文档三要素**
**文档（Document）**
文档，在面向对象观念就是一个对象。在 ES 里面，是一个大 JSON 对象，是指定了唯一 ID 的最底层或者根对象。文档的位置由 _index、_type 和 _id 唯一标识。

**索引（Index）**
索引，用于区分文档成组，即分到一组的文档集合。索引，用于存储文档和使文档可被搜索。比如项目存索引 project 里面，交易存索引 sales 等。

**类型（Type）**
类型，用于区分索引中的文档，即在索引中对数据逻辑分区。比如索引 project 的项目数据，根据项目类型 ui 项目、插画项目等进行区分。

和关系型数据库 MySQL 做个类比：
Document 类似于 Record
Type 类似于 Table
Index 类似于 Database

**1.2 集群（Cluster）、节点（Node）、分片（Shard）分布式三要素**
**集群（Cluster）**
服务器集群大家都知道，这里 ES 也是类似的。多个 ElasticSearch 运行实例（节点）组合的组合体是 ElasticSearch 集群。
ElasticSearch 是天然的分布式，通过水平扩容为集群添加更多节点。
集群是去中心化的，有一个主节点（Master）。主节点是动态选举，因此不会出现单点故障。

那分片和节点的配置呢？

**节点（Node）**
一个 ElasticSearch 运行实例就是节点。顺着集群来，任何节点都可以被选举成为主节点。主节点负责集群内所以变更，比如索引的增加、删除等。所以集群不会因为主节点流量的增大成为瓶颈。因为任何节点都会成为主节点。
下面有 3 个节点，第 1 个节点有：2 个主分片和 1 个副分片。如图：

![img](http://springforall.ufile.ucloud.com.cn/static/img/048734e259b2261e5eebdcc6703b42dd1512455)

那么，只有一个节点的 ElasticSearch 服务会存在瓶颈。如图：

![img](http://springforall.ufile.ucloud.com.cn/static/img/132464146de93a026165f723db4fe19b1512455)**分片（Shard）**
分片，是 ES 节点中最小的工作单元。分片仅仅保存全部数据的一部分，分片的集合是 ES 的索引。分片包括主分片和副分片，主分片是副分片的拷贝。主分片和副分片地工作基本没有大的区别。
在索引中全文搜索，然后会查询到每个分片，将每个分配的结果进行全局地收集处理，并返回。

**二、Elasticsearch 工作原理**

**2.1 文档存储的路由**
当索引到一个文档（如：报价系统），具体的文档数据（如：报价数据）会存储到一个分片。具体文档数据会被切分，并分别存储在分片 1 或者 分片 2 … 那么如何确定存在哪个分片呢?

存储路由过程由下面地公式决定：

```properties
shard = hash(routing) % number_of_primary_shards
```

routing 是可变值，支持自定义，默认文档 _id。
hash 函数生成数字，经过取余算法得到余数，那么这个余数就是分片的位置。
这是不是有点负载均衡的类似。

**2.2 如何健康检查**
集群名，集群的健康状态

```shell
GET http://127.0.0.1:9200/_cluster/stats
{
    "cluster_name": "elasticsearch",
    "status": "green",
    "timed_out": false,
    "number_of_nodes": 1,
    "number_of_data_nodes": 1,
    "active_primary_shards": 0,
    "active_shards": 0,
    "relocating_shards": 0,
    "initializing_shards": 0,
    "unassigned_shards": 0
}
```

status 字段是需要我们关心的。状态可能是下列三个值之一：

> green
> 所有的主分片和副本分片都已分配。你的集群是 100% 可用的。
> yellow
> 所有的主分片已经分片了，但至少还有一个副本是缺失的。不会有数据丢失，所以搜索结果依然是完整的。高可用会弱化把 yellow 想象成一个需要及时调查的警告。
> red
> 至少一个主分片（以及它的全部副本）都在缺失中。这意味着你在缺少数据：搜索只能返回部分数据，而分配到这个分片上的写入请求会返回一个异常。

active_primary_shards 集群中的主分片数量
active_shards 所有分片的汇总值
relocating_shards 显示当前正在从一个节点迁往其他节点的分片的数量。通常来说应该是 0，不过在 Elasticsearch 发现集群不太均衡时，该值会上涨。比如说：添加了一个新节点，或者下线了一个节点。
initializing_shards 刚刚创建的分片的个数。
unassigned_shards 已经在集群状态中存在的分片。

**2.3 如何水平扩容**
主分片在索引创建已经确定。读操作可以同时被主分片和副分片处理。因此，更多的分片，会拥有更高的吞吐量。自然，需要增加更多的硬件资源支持吞吐量。说明，这里无法提高性能，因为每个分片获得的资源会变少。动态调整副本分片数，按需伸缩集群，比如把副本数默认值为 1 增加到 2：

```shell
PUT /blogs/_settings
{
    "number_of_replicas" : 2
}
```

**三、小结**
简单初探了下 ElasticSearch 的相关内容。后面会主要落地到实战，关于 spring-data-elasticsearch 这块的实战。

最后，《 深入浅出 spring-data-elasticsearch 》小连载目录如下：
深入浅出 spring-data-elasticsearch - ElasticSearch 架构初探（一）
深入浅出 spring-data-elasticsearch - 概述（二）
深入浅出 spring-data-elasticsearch - 基本案例详解（三）
深入浅出 spring-data-elasticsearch - 复杂案例详解（四）
深入浅出 spring-data-elasticsearch - 架构原理以及源码浅析（五）

资料：
官方《Elasticsearch: 权威指南》
<https://www.elastic.co/guide/cn/elasticsearch/guide/current/index.html>

欢迎扫一扫我的公众号关注 — 及时得到博客订阅哦！
— <http://www.bysocket.com/> —
— <https://github.com/JeffLi1993> —