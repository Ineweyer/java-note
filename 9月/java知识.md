# java基础知识

## 并发

### 锁

锁升级图转换图，摘抄自博客[深入理解JVM-内存模型（jmm）和GC](https://www.jianshu.com/p/76959115d486)![](https://upload-images.jianshu.io/upload_images/10006199-318ad80ccb29abe4.png)

## 垃圾回收

[深入理解JVM-内存模型（jmm）和GC](https://www.jianshu.com/p/76959115d486)

![](https://upload-images.jianshu.io/upload_images/10006199-975ca350889de014.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/472/format/webp)

### 基本概念

#### 垃圾回收触发时机

##### minor GC回收时机：

- 当年轻代中的eden区分配满的时候触发

##### major Gc回收时机（full gc 导致concurrent mode failure）：

- 手动调用System.gc()方法
- 发现perm gen（如果存在永久代的话)需分配空间但已经没有足够空间
- 老年代空间不足，比如说新生代的大对象大数组晋升到老年代就可能导致老年代空间不足。
- CMS GC时出现Promotion Faield[pf]
- 统计得到的Minor GC晋升到旧生代的平均大小大于老年代的剩余空间

#### 空间分配担保：

在minor gc前，jvm会先检查老年代最大可用空间是否大于新生代所有对象总空间，如果是的话，则minor gc可以确保是安全的。

### G1垃圾回收器

[G1垃圾回收器](<https://www.oracle.com/technetwork/cn/articles/java/g1gc-1984535-zhs.html>)

基本工作原理：

- 划分Region，由region组成年轻代和老年代
- 通过并发标记查找老年代中存活对象
- 通过并行复制压缩存活对象，恢复空闲内存

回收类型（根据区域而不是根据分代）：

- young GC
- full GC
- mixed GC（G1特有的回收方式，收集整个young gen和部分old region）

### CMS

#### 问题

##### cmd垃圾回收器是否会扫描年轻代？

会，初始标记时会扫描新生代（为了空间分配担保，保证年轻代对象能够进入到老年代）

##### CMS晋升失败解决方案之偏方

[CMS晋升失败解决方案之偏方](<https://www.jianshu.com/p/fb6efff57fcc>)

- 并发模式失效（concurrent mode failure）
  - -XX:CMSInitiatingOccupancyFraction 调低这个参数，尽早进行CMS垃圾回收
- 晋升失败（promotion failed）
  - -XX:+UseCMSCompactAtFullCollection -XX:CMSFullGCsBeforeCompaction=0 
  - 业务低峰手动调用System.gc()，强制调用一次full gc进行压缩

## 基本技能

### 单点登录（single sign on）

- **sso-client拦截未登录请求，跳转到验证中心**，可以通过filter实现
- **sso-server拦截未登录请求，跳转到登录页面**，和上面一步相似
- **sso-server验证用户登录信息**
- **sso-server创建授权令牌**，一般就是一个uuid，保证不重复、不易伪造
- **sso-client取得令牌并校验**，
- **sso-server接收并处理校验令牌请求，同时需要记录注册地址（为了后面能够注销）**
- **sso-client校验令牌成功创建局部会话**
- **注销过程**



## 操作系统

### 内存页面置换算法

具体见[内存的页面置换算法](https://blog.csdn.net/qq_36132127/article/details/81126154)

- OPT，最佳置换算法
  - 被换出的页面是以后永不使用的，或者是最长时间内不再被访问（**理论上的算法，真实搞不了**）
- FIFO，先进先出页面置换算法
- LRU，最近最少为使用置换算法
- 第二次机会算法
  - 类似FIFO中加入标志位，遇到写时将标志位置为1，替换时选择最老的页面，判断标志位，如果标志为0则直接换出，否则标志位清零重新插入到队尾（**觉得能够再拯救一下，再给一次机会**）
  - 缺点：链表中移动页面，降低了效率
- 时钟算法
  - 类似第二次机会算法，不同的是页框使用环的组织形式，通过指针移动的方式避免进行链表的移动
- 改进时钟算法
  - 原因：对于被修改的页面涉及两次访问外存（第一次缺页造成、第二次数据写回外存）
  - 方法：加入写标志位
- 最不常用算法
  - 每个添加计数，每次将计数最小的换出

#### 抖动

刚刚换出的页面又要马上换入主存。

![](https://img-blog.csdn.net/20180720111340548?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTMyMTI3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)