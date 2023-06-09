### Redis

1.redis 是单线程还是多线程

因为 redis 是完全基于内存操作的，通常情况下CPU不会是redis的瓶颈，redis 的瓶颈最有可能是机器内存的大小或者网络带宽。

既然CPU不会成为瓶颈，那就顺理成章地采用单线程的方案了，因为如果使用多线程的话会更复杂，同时需要引入上下文切换、加锁等等，会带来额外的性能消耗。

而随着近些年互联网的不断发展，大家对于缓存的性能要求也越来越高了，因此 redis 也开始在逐渐往多线程方向发展。

最近的 6.0 版本就对核心流程引入了多线程，主要用于解决 redis 在网络 I/O 上的性能瓶颈。而对于核心的命令执行阶段，目前还是单线程的。


2.Redis 为什么使用单进程、单线程也很快
主要有以下几点：

1、基于内存的操作

2、使用了 I/O 多路复用模型，select、epoll 等，基于 reactor 模式开发了自己的网络事件处理器

3、对数据结构进行了优化，简单动态字符串、压缩列表等。

4、单线程可以避免不必要的上下文切换和竞争条件，减少了这方面的性能消耗。

如果题目换成：“Redis 为什么这么快？”，则第4点需要去掉，单线程不是 Redis 快的原因，更多是早期方便实现，后续 Redis 6 的多线程版本性能是更快的。

3.Redis 在项目中的使用场景

缓存（核心）、分布式锁（set + lua 脚本）、排行榜（zset）、计数（incrby）、消息队列（stream）、地理位置（geo）、访客统计（hyperloglog）等

4.Redis 常见的数据结构
基础的5种：

String：字符串，最基础的数据类型。

List：列表。

Hash：哈希对象。

Set：集合。

Sorted Set：有序集合，Set 的基础上加了个分值。

高级的4种：

HyperLogLog：通常用于基数统计。使用少量固定大小的内存，来统计集合中唯一元素的数量。统计结果不是精确值，而是一个带有0.81%标准差（standard error）的近似值。所以，HyperLogLog适用于一些对于统计结果精确度要求不是特别高的场景，例如网站的UV统计。

Geo：redis 3.2 版本的新特性。可以将用户给定的地理位置信息储存起来， 并对这些信息进行操作：获取2个位置的距离、根据给定地理位置坐标获取指定范围内的地理位置集合。

Bitmap：位图。

Stream：主要用于消息队列，类似于 kafka，可以认为是 pub/sub 的改进版。提供了消息的持久化和主备复制功能，可以让任何客户端访问任何时刻的数据，并且能记住每一个客户端的访问位置，还能保证消息不丢失。

内存和cpu的关系

简单的说程序是车、内存是跑马场而CPU则是拉车的马；