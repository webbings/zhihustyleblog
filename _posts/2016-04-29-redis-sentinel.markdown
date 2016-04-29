---
layout: post
title: Redis Sentinel配置
tags: redis
---

### Redis、Memcached、mongoDB 江湖争议

Redis：一个开源的使用ANSI C语言编写、遵守BSD协议、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API
Memcached：一套分布式的高速缓存系統
mongoDB：文档型的非关系型数据库，查询功能比较强大，能存储海量数据

*1.存储数据结构*

redis 不仅仅支持简单的key-value类型的数据，同时还提供list，set，zset，hash等数据结构的存储

memcache 支持key-value数据

*2.内存和数据量*

redis在2.0版本后增加了自己的VM特性，突破物理内存的限制；可以对key value设置过期时间，具有持久化机制，可以定期将内存中的数据持久化到硬盘上。

memcache可以修改最大可用内存,采用LRU算法

*3.可靠性（持久化）*

redis支持（快照、AOF）：依赖快照进行持久化，aof增强了可靠性的同时，对性能有所影响

memcache不支持，通常用在做缓存,提升性能；

*4.使用场景*

redis 数据量较小的更性能操作和运算上

memcache 用于在动态系统中减少数据库负载，提升性能;做缓存，提高性能（适合读多写少，对于数据量比较大，可以采用sharding）

### Redis初探

·使用6379端口写，使用26379端口读

·提供Sentinel哨兵机制，分发客户端请求

·可以配置多个Sentinel端口，分别处理不同请求

·Sentinel不断的检测redis实例是否可以正常工作，通过API向其他程序报告redis的状态，如果redis 				master不能工作，则会自动启动故障转移进程，将其中的一个slave提升为master，其他的slave重新设置新的master实例

·支持集群部署


### Redis开启服务（Linux）
{% highlight html %}
src/redis-server &
src/redis-sentinel /home/path/to/redis-3.0.7/sentinel.conf --sentinel &
{% endhighlight %}

### sentinel.conf配置
{% highlight html %}
monitor myredis 127.0.0.1 6379 2
down-after-milliseconds mymaster 60000
{% endhighlight %}

`monitor myredis 127.0.0.1 6379 2`

上面的ip地址如果配置成localhost，那么远程连接就会失败，建议配置成ip地址
这个主实例判断为失效至少需要 2 个 Sentinel 进程的同意，只要同意 Sentinel 的数量不达标，自动failover就不会执行。同时，一个Sentinel都需要获得系统中大多数Sentinel进程的支持， 才能发起一次自动failover， 并预留一个新主实例配置的编号。而当超过半数Redis不能正常工作时，自动故障转移是无效的

`down-after-milliseconds`

指定了 Sentinel 认为Redis实例已经失效所需的毫秒数。
当实例超过该时间没有返回PING，或者直接返回错误， 那么 Sentinel 将这个实例标记为主观下线（subjectively down，简称 SDOWN ）。只有一个 Sentinel进程将实例标记为主观下线并不一定会引起实例的自动故障迁移： 只有在足够数量的 Sentinel 都将一个实例标记为主观下线之后，实例才会被标记为客观下线（objectively down， 简称 ODOWN ）， 这时自动故障迁移才会执行。


### Redis集群





引用：

[http://hellosure.github.io/cache/2014/10/14/redis-memcached/](http://hellosure.github.io/cache/2014/10/14/redis-memcached/)

[http://debugo.com/redis-sentinel/](http://debugo.com/redis-sentinel/)
