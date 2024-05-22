---
title: 高并发思路
tags:
- 高并发
---
服务器性能、数据库性能、网络连接甚至编程语言都会影响并发数。但总结起来，高并发无非就是**拆拆拆分分分**。
# 乐观锁
乐观锁是数据库优化的典范。即，通过对数据条目的“版本控制”，来约束数据，防止脏读写操作。在实际操作中并不独占资源。在设计思路上是通过引入“版本”概念来放弃资源约束。
举例：
数据库中设置`CREATE TABLE tbl (
    id varchar(32) ,
    /** 乐观锁字段 **/
    optimistic_lock numeric(12)
)`
java中使用spring *@version* 关键字，
	@Version
    @Column(name = "optimistic_lock", columnDefinition = "INTEGER")
    private long optimisticLock;

# 读写分离
数据库中的读写分离知识数据库集群的一种典型。并不一定需要按读写分离数据库，也可以根据特定的业务逻辑来进行分开操作。
我们以读写分离举例：
通常设计两个数据库master和slave数据服务器，在spring中配置两个datasource
```xml
<!-- 定义数据源，使用自己实现的数据源 -->
<bean id="dataSource" class="cn.itcast.usermanage.spring.DynamicDataSource">
<!-- 设置多个数据源 -->
<property name="targetDataSources">
<map key-type="java.lang.String">
<!-- 这个key需要和程序中的key一致 -->
<entry key="master" value-ref="masterDataSource"/>
<entry key="slave" value-ref="slave01DataSource"/>
</map>
</property>
<!-- 设置默认的数据源，这里默认走写库 -->
<property name="defaultTargetDataSource" ref="masterDataSource"/>
</bean>
```
并在dao层调用时进行读写分别调用。 
为保证master/slave服务器的数据一致性，两个服务器间会有同步。
# 分离resource存储
我们以图片分离存储为例，在web场景中，页面加载的图片是非常消耗资源的，通常我们会放在其他的服务器上来进行存储，针对图片资源进行优化加速。这就像是java编程理念中的“解耦”。
同理，js文件、css文件、zip文件等皆可通过这种方式进行分离，再配合CDN加速技术，实现访问速度和并发能力的提升。
# CDN加速
CDN加速就是在靠近用户的物理位置上架设服务器，根据就近原则使用户访问物理上最近的服务器来节省网络传输时间。
通常这种CDN加速的服务器分散到全国设置世界各地，并适当采用的缓存、专线等技术。
为保证数据的一致性，服务器间进行同步。
# 动态转静态
我们把一次HTTP请求的时间分成几段：request--> calculate--> response，那么静态资源简化甚至省略了calculate步骤，实现请求-->响应的简单模型。
我们可以将“幂等”的请求进行静态化处理。我们举例来理解这件事：
比如用户想快速的查询自己近一个月的交易总额，按照传统模式我们需要服务器在用户查询后进行累加计算来统计用户这一个月的交易数据。那么我们可以在每天凌晨运行一次spring batch 来统计所有用户的交易总额信息，并存储在用户对应的表里，当用户查询时，直接获取。
# 缓存
缓存可以理解为动态转静态的一个实例。也可以理解为将硬盘上的数据存入内存方便读取。通常设计为key-value形式。
以常用的memcache举例：
```java
MemCachedClient mc = new MemCachedClient();
String key = "cacheKey1";
Object value = SomeClass.getObject();
mc.set(key, value);
```
# 服务器镜像
与CDN加速的设计类似，根据不同地域、网络服务商等网络条件假设多个服务器的“镜像”来实现网络传输环节的优化。
以此我们可以引出“负载均衡”。
# 负载均衡
负载均衡的设计理念是根据资源请求消耗情况来自动调节平衡。
比如我们在多个端口配置启动tomcat：

```xml
<Connector port="11009" protocol="AJP/1.3" redirectPort="8443" />
<Engine name="Catalina" defaultHost="localhost" jvmRoute="tomcat1">
<Cluster className="org.apache.catalina.ha.tcp.SimpleTcpCluster"/>

<Connector port="12009" protocol="AJP/1.3" redirectPort="8443" />
<Engine name="Catalina" defaultHost="localhost" jvmRoute="tomcat2">
<Cluster className="org.apache.catalina.ha.tcp.SimpleTcpCluster"/>
```

# 其他
1. 分库、分表
2. 合理的Synchronized
3. 基本的SQL优化，比如尽量避免全表扫描

我个人理解的高并发就是，把传统的“单元操作”进行拆分，分的越细致越好。

