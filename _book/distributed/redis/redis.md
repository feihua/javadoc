# 1.概述

## 1.1 简介

## 1.2 特点

## 1.3 场景

## 1.4 架构

## 1.5 工作原理

# 2.环境搭建

## 2.1redis 集群方案比较

### 2.1.1 哨兵模式

![图片](https://uploader.shimo.im/f/aFavHpcp0XB3JYyX.png!thumbnail?fileGuid=9c6PyX99KDryjwdh)

在 redis3.0 以前的版本要实现集群一般是借助哨兵 sentinel 工具来监控 master 节点的状态，如果 master 节点异常，则会做主从切换，将某一台 slave 作为 master，哨兵的配置略微复杂，并且性能和高可用性等各方面表现一般，特别是在主从切换的瞬间存在访问瞬断的情况，而且哨兵模式只有一个主节点对外提供服务，没法支持很高的并发，且单个主节点内存也不宜设置得过大，否则会导致持久化文件过大，影响数据恢复或主从同步的效率

### 2.1.2 高可用集群模式

![图片](https://uploader.shimo.im/f/wsBKl9fbfz6gm0jH.png!thumbnail?fileGuid=9c6PyX99KDryjwdh)

redis 集群是一个由多个主从节点群组成的分布式服务器群，它具有复制、高可用和分片特性。Redis 集群不需要 sentinel 哨兵也能完成节点移除和故障转移的功能。需要将每个节点设置成集群模式，这种集群模式没有中心节点，可水平扩展，据官方文档称可以线性扩展到上万个节点(官方推荐不超过 1000 个节点)。redis 集群的性能和高可用性均优于之前版本的哨兵模式，且集群配置非常简单

## 2.2 版本(高可用集群模式搭建)

|**组件**|**版本**|**备注**|
|:----:|:----|:----:|:----|:----:|:----|
|centos|64 位|7.x 以上|
|redis|5.0||
|gcc|xxx|    |

## 2.2 主机规划

|**ip**|**host**|**安装软件**|
|:----|:----|:----|:----|:----|:----|
|127.0.0.1|hadoop101|redis    gcc|

## 2.3 下载

```plain
http://download.redis.io/releases/redis-5.0.2.tar.gz tar xzf redis-5.0.2.tar.gz
```
## 2.4 配置环境

### 2.4.1redis 安装

进入到解压好的 redis-5.0.2 目录下，进行编译与安装 make & make install

启动并指定配置文件 src/redis-server redis.conf（注意要使用后台启动，所以修改 redis.conf 里的daemonize 改为 yes)

验证启动是否成功  ps -ef | grep redis

进入 redis 客户端    /usr/local/redis/bin/redis-cli

退出客户端 quit

退出 redis 服务：

（1）pkill redis-server

（2）kill 进程号

（3）src/redis-cli shutdown

### 2.4.2redis 集群搭建

redis 集群需要至少要三个 master 节点，我们这里搭建三个 master 节点，并且给每个 master 再搭建一个 slave 节点，总共 6 个 redis 节点，这里用三台机器部署 6 个 redis 实例，每台机器一主一从，搭建集群的步骤如下：

第一步：在第一台机器的/usr/local 下创建文件夹 redis-cluster，然后在其下面分别创建 2 个文件夾如下:

（1）mkdir -p /usr/local/redis-cluster

（2）mkdir 8001、mkdir 8004

第二步：把之前的 redis.conf 配置文件 copy 到 8001 下，修改如下内容：

（1）daemonize yes

（2）port 8001（分别对每个机器的端口号进行设置）

（3）dir /usr/local/redis-cluster/8001/（指定数据文件存放位置，必须要指定不同的目录位置，不然会丢失数据）

（4）cluster-enabled yes（启动集群模式）

（5）cluster-config-file nodes-8001.conf（集群节点信息文件，这里 800x 最好和 port 对应上） （6）cluster-node-timeout 5000

(7)  # bind 127.0.0.1（去掉 bind 绑定访问 ip 信息）

(8)  protected-mode  no   （关闭保护模式）  （9）appendonly yes

如果要设置密码需要增加如下配置：

（10）requirepass zhuge     (设置 redis 访问密码)

（11）masterauth zhuge      (设置集群节点间访问密码，跟上面一致)

第三步：把修改后的配置文件，copy 到 8002，修改第 2、3、5 项里的端口号，可以用批量替换：

:%s/源字符串/目的字符串/g

第四步：另外两台机器也需要做上面几步操作，第二台机器用 8002 和 8005，第三台机器用 8003 和 8006

第五步：分别启动 6 个 redis 实例，然后检查是否启动成功

（1）/usr/local/redis-5.0.2/src/redis-server /usr/local/redis-cluster/800*/redis.conf

（2）ps -ef | grep redis 查看是否启动成功

第六步：用 redis-cli 创建整个 redis 集群(redis5 以前的版本集群是依靠 ruby 脚本 redis-trib.rb 实现)（1）/usr/local/redis-5.0.2/src/redis-cli -a zhuge --cluster create --cluster-replicas 1 192.168.0.61:8001 192.168.0.62:8002 192.168.0.63:8003 192.168.0.61:8004 192.168.0.62:8005 192.168.0.63:8006 代表为每个创建的主服务器节点创建一个从服务器节点第七步：验证集群：

（1）连接任意一个客户端即可：./redis-cli -c -h -p (-a 访问服务端密码，-c 表示集群模式，指定 ip 地址和端口号）如：/usr/local/redis-5.0.2/src/redis-cli -a zhuge -c -h 192.168.0.61 -p 800* （2）进行验证：cluster info（查看集群信息）、cluster nodes（查看节点列表）

（3）进行数据操作验证

（4）关闭集群则需要逐个进行关闭，使用命令： /usr/local/redis/bin/redis-cli -a zhuge -c -h 192.168.0.60 -p 800* shutdown

# 3.快速入门

## 3.1Redis 基础数据结构

Redis 有 5 种基础数据结构，分别为：string (字符串)、list (列表)、set (集合)、hash (哈希) 和 zset (有序集合)。

## 3.2Java 操作 redis 集群

借助 redis 的 java 客户端 jedis 可以操作以上集群，引用 jedis 版本的 maven 坐标如下：

```plain
<dependency>
     <groupId>redis.clients</groupId>
     <artifactId>jedis</artifactId>
     <version>2.9.0</version>
</dependency>
```
Java 编写访问 redis 集群的代码非常简单，如下所示：
```java
import java.io.IOException;
import java.util.HashSet;
import java.util.Set;
import redis.clients.jedis.HostAndPort;
import redis.clients.jedis.JedisCluster;
import redis.clients.jedis.JedisPoolConfig;
 
/**
 * 访问 redis 集群
 */
public class RedisCluster {
    public static void main(String[] args) throws IOException{
        Set<HostAndPort> jedisClusterNode = new HashSet<HostAndPort>();
        jedisClusterNode.add(new HostAndPort("192.168.0.61", 8001));
        jedisClusterNode.add(new HostAndPort("192.168.0.62", 8002));
        jedisClusterNode.add(new HostAndPort("192.168.0.63", 8003));
        jedisClusterNode.add(new HostAndPort("192.168.0.61", 8004));
        jedisClusterNode.add(new HostAndPort("192.168.0.62", 8005));
        jedisClusterNode.add(new HostAndPort("192.168.0.63", 8006));
        JedisPoolConfig config = new JedisPoolConfig();
        config.setMaxTotal(100);
        config.setMaxIdle(10);
        config.setTestOnBorrow(true);
        //connectionTimeout：指的是连接一个 url 的连接等待时间
        //soTimeout：指的是连接上一个 url，获取 response 的返回等待时间
        JedisCluster jedisCluster = new JedisCluster(jedisClusterNode, 6000, 5000, 10, "zhuge", config);
        System.out.println(jedisCluster.set("student", "zhuge"));
        System.out.println(jedisCluster.set("age", "19"));
        System.out.println(jedisCluster.get("student"));
        System.out.println(jedisCluster.get("age"));
        jedisCluster.close();
    }
}
```
## 3.3

# 4.深入了解

## 4.1Redis 的单线程和高性能

Redis 单线程为什么还能这么快？

因为它所有的数据都在内存中，所有的运算都是内存级别的运算，而且单线程避免了多线程的切换性能损耗问题。正因为 Redis 是单线程，所以要小心使用 Redis 指令，对于那些耗时的指令(比如 keys)，一定要谨慎使用，一不小心就可能会导致 Redis 卡顿。

Redis 单线程如何处理那么多的并发客户端连接？

Redis 的 IO 多路复用：redis 利用 epoll 来实现 IO 多路复用，将连接信息和事件放到队列中，依次放到文件事件分派器，事件分派器将事件分发给事件处理器。

Nginx 也是采用 IO 多路复用原理解决 C10K 问题

## 4.2 持久化

### 4.2.1RDB 快照（snapshot）

在默认情况下，Redis 将内存数据库快照保存在名字为 dump.rdb 的二进制文件中。

你可以对 Redis 进行设置， 让它在“ N 秒内数据集至少有 M 个改动”这一条件被满足时， 自动保存一次数据集。

比如说，以下设置会让 Redis 在满足“ 60 秒内有至少有 1000 个键被改动”这一条件时， 自动保存一次数据集：

# save 60 1000

### 4.2.2AOF（append-only file）

快照功能并不是非常耐久（durable）：如果 Redis 因为某些原因而造成故障停机， 那么服务器将丢失最近写入、且仍未保存到快照中的那些数据。从 1.1 版本开始，Redis 增加了一种完全耐久的持久化方式：AOF 持久化，将修改的每一条指令记录进文件

你可以通过修改配置文件来打开 AOF 功能：

# appendonly yes

从现在开始， 每当 Redis 执行一个改变数据集的命令时（比如 SET）， 这个命令就会被追加到 AOF 文件的末尾。

这样的话， 当 Redis 重新启时， 程序就可以通过重新执行 AOF 文件中的命令来达到重建数据集的目的。

你可以配置 Redis 多久才将数据 fsync 到磁盘一次。

有三个选项：

	每次有新命令追加到 AOF 文件时就执行一次 fsync ：非常慢，也非常安全。

	每秒 fsync 一次：足够快（和使用 RDB 持久化差不多），并且在故障时只会丢失 1 秒钟的数据。

	从不 fsync ：将数据交给操作系统来处理。更快，也更不安全的选择。

推荐（并且也是默认）的措施为每秒 fsync 一次， 这种 fsync 策略可以兼顾速度和安全性。

### 4.2.3RDB 和 AOF，我应该用哪一个？

如果你非常关心你的数据， 但仍然可以承受数分钟以内的数据丢失， 那么你可以只使用 RDB 持久化。

有很多用户都只使用 AOF 持久化， 但我们并不推荐这种方式： 因为定时生成 RDB 快照（snapshot）非常便于进行数据库备份， 并且 RDB 恢复数据集的速度也要比 AOF 恢复的速度要快。

### 4.2.4Redis 4.0 混合持久化

重启 Redis 时，我们很少使用 rdb 来恢复内存状态，因为会丢失大量数据。我们通常使用 AOF 日志重放，但是重放 AOF 日志性能相对 rdb 来说要慢很多，这样在 Redis 实例很大的情况下，启动需要花费很长的时间。Redis 4.0 为了解决这个问题，带来了一个新的持久化选项——混合持久化。AOF在重写(aof文件里可能有太多没用指令，所以aof会定期根据内存的最新数据生成aof文件)时将重写这一刻之前的内存rdb快照文件的内容和增量的 AOF修改内存数据的命令日志文件存在一起，都写入新的aof文件，新的文件一开始不叫appendonly.aof，等到重写完新的AOF文件才会进行改名，原子的覆盖原有的AOF文件，完成新旧两个AOF文件的替换；

AOF 根据配置规则在后台自动重写，也可以人为执行命令 bgrewriteaof 重写 AOF。 于是在 Redis 重启的时候，可以先加载 rdb 的内容，然后再重放增量 AOF 日志就可以完全替代之前的 AOF 全量文件重放，重启效率因此大幅得到提升。

开启混合持久化：

# aof-use-rdb-preamble yes

混合持久化 aof 文件结构

## 4.3 六种缓存淘汰策略

当 Redis 内存超出物理内存限制时，内存的数据会开始和磁盘产生频繁的交换 (swap)。交换会让 Redis 的性能急剧下降，对于访问量比较频繁的 Redis 来说，这样龟速的存取效率基本上等于不可用。

在生产环境中我们是不允许 Redis 出现交换行为的，为了限制最大使用内存，Redis 提供了配置参数 maxmemory 来限制内存超出期望大小。

当实际内存超出 maxmemory 时，Redis 提供了几种可选策略 (maxmemory-policy) 来让用户自己决定该如何腾出新的空间以继续提供读写服务。

noeviction不会继续服务写请求 (DEL 请求可以继续服务)，读请求可以继续进行。这样可以保证不会丢失数据，但是会让线上的业务不能持续进行。这是默认的淘汰策略。

volatile-lru尝试淘汰设置了过期时间的 key，最少使用的 key 优先被淘汰。没有设置过期时间的 key 不会被淘汰，这样可以保证需要持久化的数据不会突然丢失。

volatile-ttl跟上面一样，除了淘汰的策略不是 LRU，而是 key 的剩余寿命 ttl 的值，ttl 越小越优先被淘汰。

volatile-random跟上面一样，不过淘汰的 key 是过期 key 集合中随机的 key。

allkeys-lru区别于 volatile-lru，这个策略要淘汰的 key 对象是全体的 key 集合，而不只是过期的 key 集合。这意味着没有设置过期时间的 key 也会被淘汰。

allkeys-random跟上面一样，不过淘汰的策略是随机的 key。

volatile-xxx策略只会针对带过期时间的 key 进行淘汰，allkeys-xxx 策略会对所有的 key 进行淘汰。如果你只是拿 Redis 做缓存，那应该使用 allkeys-xxx，客户端写缓存时不必携带过期时间。如果你还想同时使用 Redis 的持久化功能，那就使用 volatile-xxx 策略，这样可以保留没有设置过期时间的 key，它们是永久的 key 不会被 LRU 算法淘汰。

## 4.4Redis 集群原理分析

Redis Cluster 将所有数据划分为16384 的 slots(槽位)，每个节点负责其中一部分槽位。槽位的信息存储于每个节点中，。

当 Redis Cluster 的客户端来连接集群时，它也会得到一份集群的槽位配置信息并将其缓存在客户端本地。这样当客户端要查找某个 key 时，可以直接定位到目标节点。同时因为槽位的信息可能会存在客户端与服务器不一致的情况，还需要纠正机制来实现槽位信息的校验调整。

### 4.4.1 槽位定位算法

Cluster 默认会对 key 值使用 crc16 算法进行 hash 得到一个整数值，然后用这个整数值对 16384 进行取模来得到具体槽位。

HASH_SLOT = CRC16(key) mod 16384

### 4.4.2 跳转重定位

当客户端向一个错误的节点发出了指令，该节点会发现指令的 key 所在的槽位并不归自己管理，这时它会向客户端发送一个特殊的跳转指令携带目标操作的节点地址，告诉客户端去连这个节点去获取数据。客户端收到指令后除了跳转到正确的节点上去操作，还会同步更新纠正本地的槽位映射表缓存，后续所有 key 将使用新的槽位映射表。

![图片](https://uploader.shimo.im/f/ywh6m8TFVEWpgFlE.png!thumbnail?fileGuid=9c6PyX99KDryjwdh)

### 4.4.3 网络抖动

真实世界的机房网络往往并不是风平浪静的，它们经常会发生各种各样的小问题。比如网络抖动就是非常常见的一种现象，突然之间部分连接变得不可访问，然后很快又恢复正常。

为解决这种问题，Redis Cluster 提供了一种选项 cluster-node-timeout，表示当某个节点持续 timeout 的时间失联时，才可以认定该节点出现故障，需要进行主从切换。如果没有这个选项，网络抖动会导致主从频繁切换 (数据的重新复制)。

## 4.5Redis集群选举原理分析

当slave发现自己的master变为FAIL状态时，便尝试进行Failover，以期成为新的master。由于挂掉的master可能会有多个slave，从而存在多个slave竞争成为master节点的过程， 其过程如下：

1.slave发现自己的master变为FAIL

2.将自己记录的集群currentEpoch加1，并广播FAILOVER_AUTH_REQUEST 信息

3.其他节点收到该信息，只有master响应，判断请求者的合法性，并发送FAILOVER_AUTH_ACK，对每一个epoch只发送一次ack

4.尝试failover的slave收集FAILOVER_AUTH_ACK

5.超过半数后变成新Master

6.广播Pong通知其他集群节点。

从节点并不是在主节点一进入 FAIL 状态就马上尝试发起选举，而是有一定延迟，一定的延迟确保我们等待FAIL状态在集群中传播，slave如果立即尝试选举，其它masters或许尚未意识到FAIL状态，可能会拒绝投票

•延迟计算公式：

DELAY = 500ms + random(0 ~ 500ms) + SLAVE_RANK * 1000ms

•SLAVE_RANK表示此slave已经从master复制数据的总量的rank。Rank越小代表已复制的数据越新。这种方式下，持有最新数据的slave将会首先发起选举（理论上）。

# 5.优化

## 5.1

## 5.2

## 5.3


