框架高级课程系列之Redis6

尚硅谷JavaEE教研组

# 1.NoSQL数据库简介

## 1.1技术发展

技术的分类

1、解决功能性的问题：Java、Jsp、RDBMS、Tomcat、HTML、Linux、JDBC、SVN

2、解决扩展性的问题：Struts、Spring、SpringMVC、Hibernate、Mybatis

3、解决性能的问题：NoSQL、Java线程、Hadoop、Nginx、MQ、ElasticSearch

### 1.1.1Web1.0时代

Web1.0的时代，数据访问量很有限，用一夫当关的高性能的单点服务器可以解决大部分问题。

![图片](https://uploader.shimo.im/f/nsZCph8AcRp4AFjl.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

### 1.1.2Web2.0时代

随着Web2.0的时代的到来，用户访问量大幅度提升，同时产生了大量的用户数据。加上后来的智能移动设备的普及，所有的互联网平台都面临了巨大的性能挑战。

![图片](https://uploader.shimo.im/f/AcxxhVM12EPG7GB1.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

### 1.1.3解决CPU及内存压力

![图片](https://uploader.shimo.im/f/JuFOHmsC8LAvuBro.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

### 1.1.4解决IO压力

![图片](https://uploader.shimo.im/f/dVJPNpZUEifp8Dsw.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

## 1.2NoSQL数据库

### 1.2.1NoSQL数据库概述

NoSQL(NoSQL = Not Only SQL )，意即“不仅仅是SQL”，泛指非关系型的数据库。

NoSQL 不依赖业务逻辑方式存储，而以简单的key-value模式存储。因此大大的增加了数据库的扩展能力。

不遵循SQL标准。

不支持ACID。

远超于SQL的性能。

### 1.2.2NoSQL适用场景

对数据高并发的读写

海量数据的读写

对数据高可扩展性的

### 1.2.3NoSQL不适用场景

需要事务支持

基于sql的结构化查询存储，处理复杂的关系,需要即席查询。

（用不着sql的和用了sql也不行的情况，请考虑用NoSql）

### 1.2.4Memcache

|![图片](https://uploader.shimo.im/f/QbOKvP6mP5IXJXBj.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)|很早出现的NoSql数据库<br>数据都在内存中，一般不持久化<br>支持简单的key-value模式，支持类型单一<br>一般是作为缓存数据库辅助持久化的数据库|
|:----|:----|

### 1.2.5Redis

|![图片](https://uploader.shimo.im/f/AstrRcGoqnl2EbdS.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)|几乎覆盖了Memcached的绝大部分功能<br>数据都在内存中，支持持久化，主要用作备份恢复<br>除了支持简单的key-value模式，还支持多种数据结构的存储，比如list、set、hash、zset等。<br>一般是作为缓存数据库辅助持久化的数据库|
|:----|:----|

### 1.2.6MongoDB

|![图片](https://uploader.shimo.im/f/eQqgaml7pw8EmKch.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)<br>|高性能、开源、模式自由(schema  free)的文档型数据库<br>数据都在内存中，如果内存不足，把不常用的数据保存到硬盘<br>虽然是key-value模式，但是对value（尤其是json）提供了丰富的查询功能<br>支持二进制数据及大型对象<br>可以根据数据的特点替代RDBMS，成为独立的数据库。或者配合RDBMS，存储特定的数据。|
|:----|:----|

## 1.3行式存储数据库（大数据时代）

### 1.3.1行式数据库

![图片](https://uploader.shimo.im/f/cx4niw48fOaJiQrx.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

### 1.3.2列式数据库

![图片](https://uploader.shimo.im/f/QMXwEOV6sM1lX0Mb.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

Hbase

![图片](https://uploader.shimo.im/f/ZYgUn98OYJwaQ3WU.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

HBase是Hadoop项目中的数据库。它用于需要对大量的数据进行随机、实时的读写操作的场景中。

HBase的目标就是处理数据量非常庞大的表，可以用普通的计算机处理超过10亿行数据，还可处理有数百万列元素的数据表。

Cassandra[kəˈsændrə]

![图片](https://uploader.shimo.im/f/ZivEwMZkYE8aQpvM.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

Apache Cassandra是一款免费的开源NoSQL数据库，其设计目的在于管理由大量商用服务器构建起来的庞大集群上的海量数据集(数据量通常达到PB级别)。在众多显著特性当中，Cassandra最为卓越的长处是对写入及读取操作进行规模调整，而且其不强调主集群的设计思路能够以相对直观的方式简化各集群的创建与扩展流程。

|计算机存储单位计算机存储单位一般用B，KB，MB，GB，TB，EB，ZB，YB，BB来表示，它们之间的关系是：<br>位bit (比特)(Binary Digits)：存放一位二进制数，即0或1，最小的存储单位。<br>字节byte：8个二进制位为一个字节(B)，最常用的单位。<br>1KB (Kilobyte千字节)=1024B，<br>1MB (Megabyte兆字节简称“兆”)=1024KB，<br>1GB (Gigabyte吉字节又称“千兆”)=1024MB，<br>1TB (Trillionbyte万亿字节太字节)=1024GB，其中1024=2^10 ( 2的10次方)，<br>1PB（Petabyte千万亿字节拍字节）=1024TB，<br>1EB（Exabyte百亿亿字节艾字节）=1024PB，<br>1ZB (Zettabyte十万亿亿字节泽字节)= 1024 EB,<br>1YB (Jottabyte一亿亿亿字节尧字节)= 1024 ZB,<br>1BB (Brontobyte一千亿亿亿字节)= 1024 YB.<br>注：“兆”为百万级数量单位。|
|:----|

## 1.4图关系型数据库

![图片](https://uploader.shimo.im/f/iYjEcpGDiUHZYJfj.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

主要应用：社会关系，公共交通网络，地图及网络拓谱(n*(n-1)/2)

![图片](https://uploader.shimo.im/f/ineC4HdKC01wPkGo.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

## 1.5DB-Engines 数据库排名

[http://db-engines.com/en/ranking](http://db-engines.com/en/ranking?fileGuid=yBtmdjz5Ja42cjcb)

![图片](https://uploader.shimo.im/f/FUCeSk9mejwMo8QQ.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

# 2.Redis概述安装

Redis是一个开源的key-value存储系统。

和Memcached类似，它支持存储的value类型相对更多，包括string(字符串)、list(链表)、set(集合)、zset(sorted set --有序集合)和hash（哈希类型）。

这些数据类型都支持push/pop、add/remove及取交集并集和差集及更丰富的操作，而且这些操作都是原子性的。

在此基础上，Redis支持各种不同方式的排序。

与memcached一样，为了保证效率，数据都是缓存在内存中。

区别的是Redis会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件。

并且在此基础上实现了master-slave(主从)同步。

## 2.1应用场景

配合关系型数据库做高速缓存

高频次，热门访问的数据，降低数据库IO

分布式架构，做session共享

![图片](https://uploader.shimo.im/f/GxZ1qxmJETKjACOj.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

多样的数据结构存储持久化数据

![图片](https://uploader.shimo.im/f/8Q4NS5XIKI8Mv3DM.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

## 2.2Redis安装

|Redis官方网站|Redis中文官方网站|
|:----|:----|
|[http://redis.io](http://redis.io?fileGuid=yBtmdjz5Ja42cjcb)|http://redis.cn/|

![图片](https://uploader.shimo.im/f/rahsMeKnwuyP6Psx.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

![图片](https://uploader.shimo.im/f/J1zWFgPtEoCdw7cD.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

### 2.2.1安装版本

6.2.1 for Linux（redis-6.2.1.tar.gz）

不用考虑在windows环境下对Redis的支持

![图片](https://uploader.shimo.im/f/8b9VhYQb0B0WN84H.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

### 2.2.2安装步骤

准备工作：下载安装最新版的gcc编译器

安装C 语言的编译环境

yum install centos-release-scl scl-utils-build

yum install -y devtoolset-8-toolchain

scl enable devtoolset-8 bash

测试 gcc版本

gcc --version

![图片](https://uploader.shimo.im/f/8ZHUPlXMlyG1N81y.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

下载redis-6.2.1.tar.gz放/opt目录

解压命令：tar -zxvf redis-6.2.1.tar.gz

解压完成后进入目录：cd redis-6.2.1

在redis-6.2.1目录下再次执行make命令（只是编译好）

如果没有准备好C语言编译环境，make 会报错—Jemalloc/jemalloc.h：没有那个文件

![图片](https://uploader.shimo.im/f/1Zn9YXNp9Vh5K8HC.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

解决方案：运行make distclean

![图片](https://uploader.shimo.im/f/4OqTJPMuoStJ4uGX.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

在redis-6.2.1目录下再次执行make命令（只是编译好）

![图片](https://uploader.shimo.im/f/aW9gyjd2kNk3fdvF.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

跳过make test 继续执行: make install

![图片](https://uploader.shimo.im/f/kxgW5EhjFeKGdrPa.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

### 2.2.3安装目录：/usr/local/bin

查看默认安装目录：

redis-benchmark:性能测试工具，可以在自己本子运行，看看自己本子性能如何

redis-check-aof：修复有问题的AOF文件，rdb和aof后面讲

redis-check-dump：修复有问题的dump.rdb文件

redis-sentinel：Redis集群使用

redis-server：Redis服务器启动命令

redis-cli：客户端，操作入口

### 2.2.4前台启动（不推荐）

前台启动，命令行窗口不能关闭，否则服务器停止

![图片](https://uploader.shimo.im/f/lby1jiKrhJ1Eh9ZE.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

### 2.2.5后台启动（推荐）

备份redis.conf

拷贝一份redis.conf到其他目录

cp  /opt/redis-3.2.5/redis.conf  /myredis

后台启动设置daemonize no改成yes

修改redis.conf(128行)文件将里面的daemonize no 改成 yes，让服务在后台启动

Redis启动

redis-server/myredis/redis.conf

![图片](https://uploader.shimo.im/f/FMrBXQvKnBosfqnk.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

用客户端访问：redis-cli

![图片](https://uploader.shimo.im/f/FlA0n4RN62oH09j2.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

多个端口可以：redis-cli -p6379

测试验证： ping

![图片](https://uploader.shimo.im/f/yc9qLsP6DDoigCGZ.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

Redis关闭

单实例关闭：redis-cli shutdown

![图片](https://uploader.shimo.im/f/C2gZkUEEjr6GvK5Y.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

也可以进入终端后再关闭

![图片](https://uploader.shimo.im/f/fBJBjMpGy3SJVgof.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

多实例关闭，指定端口关闭：redis-cli -p 6379 shutdown

Redis介绍相关知识

|端口6379从何而来<br>Alessia  Merz<br>![图片](https://uploader.shimo.im/f/CaISTOgTtteHukic.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)|默认16个数据库，类似数组下标从0开始，初始默认使用0号库<br>使用命令select   <dbid>来切换数据库。如: select 8<br>统一密码管理，所有库同样密码。<br>dbsize查看当前数据库的key的数量<br>flushdb清空当前库<br>flushall通杀全部库|
|:----|:----|
Redis是单线程+多路IO复用技术

多路复用是指使用一个线程来检查多个文件描述符（Socket）的就绪状态，比如调用select和poll函数，传入多个文件描述符，如果有一个文件描述符就绪，则返回，否则阻塞直到超时。得到就绪状态后进行真正的操作可以在同一个线程里执行，也可以启动线程执行（比如使用线程池）

串行   vs   多线程+锁（memcached） vs   单线程+多路IO复用(Redis)

（与Memcache三点不同: 支持多数据类型，支持持久化，单线程+多路IO复用）

![图片](https://uploader.shimo.im/f/EW1NSUEDOwaLHtFp.gif?fileGuid=yBtmdjz5Ja42cjcb)

# 3.常用五大数据类型

哪里去获得redis常见数据类型操作命令http://www.redis.cn/commands.html

## 3.1Redis键(key)

keys *查看当前库所有key    (匹配：keys *1)

exists key判断某个key是否存在

type key 查看你的key是什么类型

del key       删除指定的key数据

unlink key   根据value选择非阻塞删除

仅将keys从keyspace元数据中删除，真正的删除会在后续异步操作。

expire key 10   10秒钟：为给定的key设置过期时间

ttl key 查看还有多少秒过期，-1表示永不过期，-2表示已过期

select命令切换数据库

dbsize查看当前数据库的key的数量

flushdb清空当前库

flushall通杀全部库

## 3.2Redis字符串(String)

简介

String是Redis最基本的类型，你可以理解成与Memcached一模一样的类型，一个key对应一个value。

String类型是二进制安全的。意味着Redis的string可以包含任何数据。比如jpg图片或者序列化的对象。

String类型是Redis最基本的数据类型，一个Redis中字符串value最多可以是512M

常用命令

set   <key><value>添加键值对

![图片](https://uploader.shimo.im/f/s3Bb4tBvIWs8dOA3.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

*NX：当数据库中key不存在时，可以将key-value添加数据库

*XX：当数据库中key存在时，可以将key-value添加数据库，与NX参数互斥

*EX：key的超时秒数

*PX：key的超时毫秒数，与EX互斥

get   <key>查询对应键值

append  <key><value>将给定的<value> 追加到原值的末尾

strlen  <key>获得值的长度

setnx  <key><value>只有在 key 不存在时    设置 key 的值

incr  <key>

将 key 中储存的数字值增1

只能对数字值操作，如果为空，新增值为1

decr  <key>

将 key 中储存的数字值减1

只能对数字值操作，如果为空，新增值为-1

incrby / decrby  <key><步长>将 key 中储存的数字值增减。自定义步长。

|原子性<br>![图片](https://uploader.shimo.im/f/n0iGtcFEa2eg7EGm.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)<br>所谓原子操作是指不会被线程调度机制打断的操作；<br>这种操作一旦开始，就一直运行到结束，中间不会有任何 context switch （切换到另一个线程）。<br>（1）在单线程中， 能够在单条指令中完成的操作都可以认为是"原子操作"，因为中断只能发生于指令之间。<br>（2）在多线程中，不能被其它进程（线程）打断的操作就叫原子操作。<br>Redis单命令的原子性主要得益于Redis的单线程。<br>案例：<br>java中的i++是否是原子操作？不是<br>i=0;两个线程分别对i进行++100次,值是多少？ 2~200<br>|
|:----|
mset  <key1><value1><key2><value2>  .....

同时设置一个或多个 key-value对

mget  <key1><key2><key3> .....

同时获取一个或多个 value

msetnx <key1><value1><key2><value2>  .....

同时设置一个或多个 key-value 对，当且仅当所有给定 key 都不存在。

原子性，有一个失败则都失败

getrange  <key><起始位置><结束位置>

获得值的范围，类似java中的substring，前包，后包

setrange  <key><起始位置><value>

用 <value>  覆写<key>所储存的字符串值，从<起始位置>开始(索引从0开始)。

setex  <key><过期时间><value>

设置键值的同时，设置过期时间，单位秒。

getset <key><value>

以新换旧，设置了新值同时获得旧值。

数据结构

String的数据结构为简单动态字符串(Simple Dynamic String,缩写SDS)。是可以修改的字符串，内部结构实现上类似于Java的ArrayList，采用预分配冗余空间的方式来减少内存的频繁分配.

![图片](https://uploader.shimo.im/f/ZzTbKMrfxwQmVCYg.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

如图中所示，内部为当前字符串实际分配的空间capacity一般要高于实际字符串长度len。当字符串长度小于1M时，扩容都是加倍现有的空间，如果超过1M，扩容时一次只会多扩1M的空间。需要注意的是字符串最大长度为512M。

## 3.3Redis列表(List)

简介

单键多值

Redis 列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部（左边）或者尾部（右边）。

它的底层实际是个双向链表，对两端的操作性能很高，通过索引下标的操作中间的节点性能会较差。

![图片](https://uploader.shimo.im/f/UGBfyW30qEjTj9eJ.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

常用命令

lpush/rpush  <key><value1><value2><value3> .... 从左边/右边插入一个或多个值。

lpop/rpop  <key>从左边/右边吐出一个值。值在键在，值光键亡。

rpoplpush  <key1><key2>从<key1>列表右边吐出一个值，插到<key2>列表左边。

lrange <key><start><stop>

按照索引下标获得元素(从左到右)

lrange mylist 0 -1   0左边第一个，-1右边第一个，（0-1表示获取所有）

lindex <key><index>按照索引下标获得元素(从左到右)

llen <key>获得列表长度

linsert <key>  before <value><newvalue>在<value>的后面插入<newvalue>插入值

lrem <key><n><value>从左边删除n个value(从左到右)

lset<key><index><value>将列表key下标为index的值替换成value

数据结构

List的数据结构为快速链表quickList。

首先在列表元素较少的情况下会使用一块连续的内存存储，这个结构是ziplist，也即是压缩列表。

它将所有的元素紧挨着一起存储，分配的是一块连续的内存。

当数据量比较多的时候才会改成quicklist。

因为普通的链表需要的附加指针空间太大，会比较浪费空间。比如这个列表里存的只是int类型的数据，结构上还需要两个额外的指针prev和next。

![图片](https://uploader.shimo.im/f/kvkbHVdQ9V3pdHYm.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

Redis将链表和ziplist结合起来组成了quicklist。也就是将多个ziplist使用双向指针串起来使用。这样既满足了快速的插入删除性能，又不会出现太大的空间冗余。

## 3.4Redis集合(Set)

简介

Redis set对外提供的功能与list类似是一个列表的功能，特殊之处在于set是可以自动排重的，当你需要存储一个列表数据，又不希望出现重复数据时，set是一个很好的选择，并且set提供了判断某个成员是否在一个set集合内的重要接口，这个也是list所不能提供的。

Redis的Set是string类型的无序集合。它底层其实是一个value为null的hash表，所以添加，删除，查找的复杂度都是O(1)。

一个算法，随着数据的增加，执行时间的长短，如果是O(1)，数据增加，查找数据的时间不变

常用命令

sadd <key><value1><value2> .....

将一个或多个 member 元素加入到集合 key 中，已经存在的 member 元素将被忽略

smembers <key>取出该集合的所有值。

sismember <key><value>判断集合<key>是否为含有该<value>值，有1，没有0

scard<key>返回该集合的元素个数。

srem <key><value1><value2> .... 删除集合中的某个元素。

spop <key>随机从该集合中吐出一个值。

srandmember <key><n>随机从该集合中取出n个值。不会从集合中删除 。

smove <source><destination>value把集合中一个值从一个集合移动到另一个集合

sinter <key1><key2>返回两个集合的交集元素。

sunion <key1><key2>返回两个集合的并集元素。

sdiff <key1><key2>返回两个集合的差集元素(key1中的，不包含key2中的)

数据结构

Set数据结构是dict字典，字典是用哈希表实现的。

Java中HashSet的内部实现使用的是HashMap，只不过所有的value都指向同一个对象。Redis的set结构也是一样，它的内部也使用hash结构，所有的value都指向同一个内部值。

## 3.5Redis哈希(Hash)

简介

Redis hash 是一个键值对集合。

Redis hash是一个string类型的field和value的映射表，hash特别适合用于存储对象。

类似Java里面的Map<String,Object>

用户ID为查找的key，存储的value用户对象包含姓名，年龄，生日等信息，如果用普通的key/value结构来存储

主要有以下2种存储方式：

|![图片](https://uploader.shimo.im/f/AeKIMoQlfh9TvSPS.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)<br>每次修改用户的某个属性需要，先反序列化改好后再序列化回去。开销较大。|![图片](https://uploader.shimo.im/f/4i8lsmerI9RHCFVZ.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)<br>用户ID数据冗余|
|:----|:----|
|![图片](https://uploader.shimo.im/f/9ciLxE7vhw1GHL4S.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)<br>通过 key(用户ID) + field(属性标签) 就可以操作对应属性数据了，既不需要重复存储数据，也不会带来序列化和并发修改控制的问题|
|:----|

常用命令

hset <key><field><value>给<key>集合中的  <field>键赋值<value>

hget <key1><field>从<key1>集合<field>取出 value

hmset <key1><field1><value1><field2><value2>... 批量设置hash的值

hexists<key1><field>查看哈希表 key 中，给定域 field 是否存在。

hkeys <key>列出该hash集合的所有field

hvals <key>列出该hash集合的所有value

hincrby <key><field><increment>为哈希表 key 中的域 field 的值加上增量 1   -1

hsetnx <key><field><value>将哈希表 key 中的域 field 的值设置为 value ，当且仅当域 field 不存在 .

数据结构

Hash类型对应的数据结构是两种：ziplist（压缩列表），hashtable（哈希表）。当field-value长度较短且个数较少时，使用ziplist，否则使用hashtable。

## 3.6Redis有序集合Zset(sorted set)

简介

Redis有序集合zset与普通集合set非常相似，是一个没有重复元素的字符串集合。

不同之处是有序集合的每个成员都关联了一个评分（score）,这个评分（score）被用来按照从最低分到最高分的方式排序集合中的成员。集合的成员是唯一的，但是评分可以是重复了 。

因为元素是有序的, 所以你也可以很快的根据评分（score）或者次序（position）来获取一个范围的元素。

访问有序集合的中间元素也是非常快的,因此你能够使用有序集合作为一个没有重复成员的智能列表。

常用命令

zadd  <key><score1><value1><score2><value2>…

将一个或多个 member 元素及其 score 值加入到有序集 key 当中。

zrange <key><start><stop>  [WITHSCORES]

返回有序集 key 中，下标在<start><stop>之间的元素

带WITHSCORES，可以让分数一起和值返回到结果集。

zrangebyscore key minmax [withscores] [limit offset count]

返回有序集 key 中，所有 score 值介于 min 和 max 之间(包括等于 min 或 max )的成员。有序集成员按 score 值递增(从小到大)次序排列。

zrevrangebyscore key maxmin [withscores] [limit offset count]

同上，改为从大到小排列。

zincrby <key><increment><value>      为元素的score加上增量

zrem  <key><value>删除该集合下，指定值的元素

zcount <key><min><max>统计该集合，分数区间内的元素个数

zrank <key><value>返回该值在集合中的排名，从0开始。

案例：如何利用zset实现一个文章访问量的排行榜？

![图片](https://uploader.shimo.im/f/MxnxuIg8UXTKGpjl.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

数据结构

SortedSet(zset)是Redis提供的一个非常特别的数据结构，一方面它等价于Java的数据结构Map<String, Double>，可以给每一个元素value赋予一个权重score，另一方面它又类似于TreeSet，内部的元素会按照权重score进行排序，可以得到每个元素的名次，还可以通过score的范围来获取元素的列表。

zset底层使用了两个数据结构

（1）hash，hash的作用就是关联元素value和权重score，保障元素value的唯一性，可以通过元素value找到相应的score值。

（2）跳跃表，跳跃表的目的在于给元素value排序，根据score的范围获取元素列表。

跳跃表（跳表）

1、简介

有序集合在生活中比较常见，例如根据成绩对学生排名，根据得分对玩家排名等。对于有序集合的底层实现，可以用数组、平衡树、链表等。数组不便元素的插入、删除；平衡树或红黑树虽然效率高但结构复杂；链表查询需要遍历所有效率低。Redis采用的是跳跃表。跳跃表效率堪比红黑树，实现远比红黑树简单。

2、实例

对比有序链表和跳跃表，从链表中查询出51

有序链表

![图片](https://uploader.shimo.im/f/HZFShRUHLxzwjVvO.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

要查找值为51的元素，需要从第一个元素开始依次查找、比较才能找到。共需要6次比较。

跳跃表

![图片](https://uploader.shimo.im/f/jlLGDiINl9qxeSiX.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

从第2层开始，1节点比51节点小，向后比较。

21节点比51节点小，继续向后比较，后面就是NULL了，所以从21节点向下到第1层

在第1层，41节点比51节点小，继续向后，61节点比51节点大，所以从41向下

在第0层，51节点为要查找的节点，节点被找到，共查找4次。

从此可以看出跳跃表比有序链表效率要高

# 4.Redis配置文件介绍

自定义目录：/myredis/redis.conf

###Units单位###

配置大小单位,开头定义了一些基本的度量单位，只支持bytes，不支持bit

大小写不敏感

![图片](https://uploader.shimo.im/f/GC8OtsSTEsn7iuEC.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

###INCLUDES包含###

![图片](https://uploader.shimo.im/f/DrR3w3apjdhgugDD.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

类似jsp中的include，多实例的情况可以把公用的配置文件提取出来

###网络相关配置 ###

bind

默认情况bind=127.0.0.1只能接受本机的访问请求

不写的情况下，无限制接受任何ip地址的访问

生产环境肯定要写你应用服务器的地址；服务器是需要远程访问的，所以需要将其注释掉

如果开启了protected-mode，那么在没有设定bind ip且没有设密码的情况下，Redis只允许接受本机的响应

![图片](https://uploader.shimo.im/f/GePGQ7B9gklJNFlT.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

保存配置，停止服务，重启启动查看进程，不再是本机访问了。

![图片](https://uploader.shimo.im/f/P26fgfjcEIpc3CxL.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

protected-mode

将本机访问保护模式设置no

![图片](https://uploader.shimo.im/f/dO0SAbrAjJHIghfD.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

Port

端口号，默认 6379

![图片](https://uploader.shimo.im/f/arwGWdtEz9sdmQve.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

tcp-backlog

设置tcp的backlog，backlog其实是一个连接队列，backlog队列总和=未完成三次握手队列 + 已经完成三次握手队列。

在高并发环境下你需要一个高backlog值来避免慢客户端连接问题。

注意Linux内核会将这个值减小到/proc/sys/net/core/somaxconn的值（128），所以需要确认增大/proc/sys/net/core/somaxconn和/proc/sys/net/ipv4/tcp_max_syn_backlog（128）两个值来达到想要的效果

![图片](https://uploader.shimo.im/f/rl7wesVxuHiDFxZt.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

timeout

一个空闲的客户端维持多少秒会关闭，0表示关闭该功能。即永不关闭。

![图片](https://uploader.shimo.im/f/1oAwWsDqi05NiFGv.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

tcp-keepalive

对访问客户端的一种心跳检测，每个n秒检测一次。

单位为秒，如果设置为0，则不会进行Keepalive检测，建议设置成60

![图片](https://uploader.shimo.im/f/euOSduXnJ9Vn7hz6.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

###GENERAL通用###

daemonize

是否为后台进程，设置为yes

守护进程，后台启动

![图片](https://uploader.shimo.im/f/v2NRk4YGwvzWZ49J.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

pidfile

存放pid文件的位置，每个实例会产生一个不同的pid文件

![图片](https://uploader.shimo.im/f/5urmOEsECjvNatZL.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

loglevel

指定日志记录级别，Redis总共支持四个级别：debug、verbose、notice、warning，默认为notice

四个级别根据使用阶段来选择，生产环境选择notice 或者warning

![图片](https://uploader.shimo.im/f/QvfsddiqNfDHIgSo.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

logfile

日志文件名称

![图片](https://uploader.shimo.im/f/flludylyV1EQT7LC.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

databases 16

设定库的数量 默认16，默认数据库为0，可以使用SELECT <dbid>命令在连接上指定数据库id

![图片](https://uploader.shimo.im/f/ep0CX3JsQZOSa30A.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

###SECURITY安全###

设置密码

![图片](https://uploader.shimo.im/f/iKnzoxKM5UYUkHXd.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

访问密码的查看、设置和取消

在命令中设置密码，只是临时的。重启redis服务器，密码就还原了。

永久设置，需要再配置文件中进行设置。

![图片](https://uploader.shimo.im/f/gSlLrtEGl3wJhiY3.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

####  ###

LIMITS限制

maxclients

设置redis同时可以与多少个客户端进行连接。

默认情况下为10000个客户端。

如果达到了此限制，redis则会拒绝新的连接请求，并且向这些连接请求方发出“max number of clients reached”以作回应。

![图片](https://uploader.shimo.im/f/BjU8f1XBeCJbJB9w.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

maxmemory

建议必须设置，否则，将内存占满，造成服务器宕机

设置redis可以使用的内存量。一旦到达内存使用上限，redis将会试图移除内部数据，移除规则可以通过maxmemory-policy来指定。

如果redis无法根据移除规则来移除内存中的数据，或者设置了“不允许移除”，那么redis则会针对那些需要申请内存的指令返回错误信息，比如SET、LPUSH等。

但是对于无内存申请的指令，仍然会正常响应，比如GET等。如果你的redis是主redis（说明你的redis有从redis），那么在设置内存使用上限时，需要在系统中留出一些内存空间给同步队列缓存，只有在你设置的是“不移除”的情况下，才不用考虑这个因素。

![图片](https://uploader.shimo.im/f/Rtmayho8x1J0LmqH.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

maxmemory-policy

volatile-lru：使用LRU算法移除key，只对设置了过期时间的键；（最近最少使用）

allkeys-lru：在所有集合key中，使用LRU算法移除key

volatile-random：在过期集合中移除随机的key，只对设置了过期时间的键

allkeys-random：在所有集合key中，移除随机的key

volatile-ttl：移除那些TTL值最小的key，即那些最近要过期的key

noeviction：不进行移除。针对写操作，只是返回错误信息

![图片](https://uploader.shimo.im/f/JxauDb0ivfaVsLoC.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

maxmemory-samples

设置样本数量，LRU算法和最小TTL算法都并非是精确的算法，而是估算值，所以你可以设置样本的大小，redis默认会检查这么多个key并选择其中LRU的那个。

一般设置3到7的数字，数值越小样本越不准确，但性能消耗越小。

![图片](https://uploader.shimo.im/f/hFA4B2hPEeUQG4xo.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

# 5.Redis的发布和订阅

什么是发布和订阅

Redis 发布订阅 (pub/sub) 是一种消息通信模式：发送者 (pub) 发送消息，订阅者 (sub) 接收消息。

Redis 客户端可以订阅任意数量的频道。

Redis的发布和订阅

1、客户端可以订阅频道如下图

![图片](https://uploader.shimo.im/f/QTxGRNgcvUyzf6LE.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

2、当给这个频道发布消息后，消息就会发送给订阅的客户端

![图片](https://uploader.shimo.im/f/eAhHs8uqdiMXUYxq.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)


发布订阅命令行实现

打开一个客户端订阅channel1

SUBSCRIBE channel1

![图片](https://uploader.shimo.im/f/U18KR07n0q8fRvZE.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

2、打开另一个客户端，给channel1发布消息hello

publish channel1 hello

![图片](https://uploader.shimo.im/f/2ZWteVUqZP5pVG4p.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

返回的1是订阅者数量

3、打开第一个客户端可以看到发送的消息

![图片](https://uploader.shimo.im/f/x49CA4QHtvXKDLFP.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

注：发布的消息没有持久化，如果在订阅的客户端收不到hello，只能收到订阅后发布的消息

# 6.Redis新数据类型

Bitmaps

简介

现代计算机用二进制（位） 作为信息的基础单位， 1个字节等于8位， 例如“abc”字符串是由3个字节组成， 但实际在计算机存储时将其用二进制表示， “abc”分别对应的ASCII码分别是97、 98、 99， 对应的二进制分别是01100001、 01100010和01100011，如下图

![图片](https://uploader.shimo.im/f/AcTEDCLcsTgJgzZF.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

合理地使用操作位能够有效地提高内存使用率和开发效率。

Redis提供了Bitmaps这个“数据类型”可以实现对位的操作：

Bitmaps本身不是一种数据类型， 实际上它就是字符串（key-value） ， 但是它可以对字符串的位进行操作。

Bitmaps单独提供了一套命令， 所以在Redis中使用Bitmaps和使用字符串的方法不太相同。 可以把Bitmaps想象成一个以位为单位的数组， 数组的每个单元只能存储0和1， 数组的下标在Bitmaps中叫做偏移量。

![图片](https://uploader.shimo.im/f/11kumXOcFBEOtYz3.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

命令

1、setbit

（1）格式

setbit<key><offset><value>设置Bitmaps中某个偏移量的值（0或1）

![图片](https://uploader.shimo.im/f/J1LHqvkFeUc8Blfc.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

*offset:偏移量从0开始

（2）实例

每个独立用户是否访问过网站存放在Bitmaps中， 将访问的用户记做1， 没有访问的用户记做0， 用偏移量作为用户的id。

设置键的第offset个位的值（从0算起） ， 假设现在有20个用户，userid=1， 6， 11， 15， 19的用户对网站进行了访问， 那么当前Bitmaps初始化结果如图

![图片](https://uploader.shimo.im/f/eM22d4oTQIkOY7NO.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

unique:users:20201106代表2020-11-06这天的独立访问用户的Bitmaps

![图片](https://uploader.shimo.im/f/GCHaVutcGK04NuXd.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

注：

很多应用的用户id以一个指定数字（例如10000） 开头， 直接将用户id和Bitmaps的偏移量对应势必会造成一定的浪费， 通常的做法是每次做setbit操作时将用户id减去这个指定数字。

在第一次初始化Bitmaps时， 假如偏移量非常大， 那么整个初始化过程执行会比较慢， 可能会造成Redis的阻塞。

2、getbit

（1）格式

getbit<key><offset>获取Bitmaps中某个偏移量的值

![图片](https://uploader.shimo.im/f/rIyzk1R0HRbwgPmT.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

获取键的第offset位的值（从0开始算）

（2）实例

获取id=8的用户是否在2020-11-06这天访问过， 返回0说明没有访问过：

![图片](https://uploader.shimo.im/f/K5RLJ1A4NAwejJKs.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

注：因为100根本不存在，所以也是返回0

3、bitcount

统计字符串被设置为1的bit数。一般情况下，给定的整个字符串都会被进行计数，通过指定额外的 start 或 end 参数，可以让计数只在特定的位上进行。start 和 end 参数的设置，都可以使用负数值：比如 -1 表示最后一个位，而 -2 表示倒数第二个位，start、end 是指bit组的字节的下标数，二者皆包含。

（1）格式

bitcount<key>[start end] 统计字符串从start字节到end字节比特值为1的数量

![图片](https://uploader.shimo.im/f/1SSb1yGxRu06ZySt.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

（2）实例

计算2022-11-06这天的独立访问用户数量

![图片](https://uploader.shimo.im/f/LJBSdiVLthU77XzY.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

start和end代表起始和结束字节数， 下面操作计算用户id在第1个字节到第3个字节之间的独立访问用户数， 对应的用户id是11， 15， 19。

![图片](https://uploader.shimo.im/f/yMFS0s5gVt24Eg9o.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

举例： K1 【01000001 01000000  00000000 00100001】，对应【0，1，2，3】

bitcount K1 1 2  ： 统计下标1、2字节组中bit=1的个数，即01000000  00000000

--》bitcount K1 1 2 　　--》1

bitcount K1 1 3  ： 统计下标1、2字节组中bit=1的个数，即01000000  00000000 00100001

--》bitcount K1 1 3　　--》3

bitcount K1 0 -2  ： 统计下标0到下标倒数第2，字节组中bit=1的个数，即01000001  01000000   00000000

--》bitcount K1 0 -2　　--》3

注意：redis的setbit设置或清除的是bit位置，而bitcount计算的是byte位置。

4、bitop

(1)格式

bitop  and(or/not/xor) <destkey> [key…]

![图片](https://uploader.shimo.im/f/JLSn1JGAZ0NUpwkC.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

bitop是一个复合操作， 它可以做多个Bitmaps的and（交集） 、 or（并集） 、 not（非） 、 xor（异或） 操作并将结果保存在destkey中。

(2)实例

2020-11-04 日访问网站的userid=1,2,5,9。

setbit unique:users:20201104 1 1

setbit unique:users:20201104 2 1

setbit unique:users:20201104 5 1

setbit unique:users:20201104 9 1



2020-11-03 日访问网站的userid=0,1,4,9。

setbit unique:users:20201103 0 1

setbit unique:users:20201103 1 1

setbit unique:users:20201103 4 1

setbit unique:users:20201103 9 1

计算出两天都访问过网站的用户数量

bitop and unique:users:and:20201104_03

unique:users:20201103unique:users:20201104

![图片](https://uploader.shimo.im/f/5SlhepiBkPZ8ItPx.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

![图片](https://uploader.shimo.im/f/NcQ0BlX5zcH9McKX.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

计算出任意一天都访问过网站的用户数量（例如月活跃就是类似这种） ， 可以使用or求并集

![图片](https://uploader.shimo.im/f/L6LZJuP7v4dAgOOJ.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

Bitmaps与set对比

假设网站有1亿用户， 每天独立访问的用户有5千万， 如果每天用集合类型和Bitmaps分别存储活跃用户可以得到表

|set和Bitmaps存储一天活跃用户对比|    |    |    |
|:----|:----|:----|:----|:----|
|数据<br>类型|每个用户id占用空间|需要存储的用户量|全部内存量|
|集合<br>类型|64位|50000000|64位*50000000 = 400MB|
|Bitmaps|1位|100000000|1位*100000000 = 12.5MB|


很明显， 这种情况下使用Bitmaps能节省很多的内存空间， 尤其是随着时间推移节省的内存还是非常可观的

|set和Bitmaps存储独立用户空间对比|    |    |    |
|:----|:----|:----|:----|
|数据类型|一天|一个月|一年|
|集合类型|400MB|12GB|144GB|
|Bitmaps|12.5MB|375MB|4.5GB|


但Bitmaps并不是万金油， 假如该网站每天的独立访问用户很少， 例如只有10万（大量的僵尸用户） ， 那么两者的对比如下表所示， 很显然， 这时候使用Bitmaps就不太合适了， 因为基本上大部分位都是0。

|set和Bitmaps存储一天活跃用户对比（独立用户比较少）|    |    |    |
|:----|:----|:----|:----|
|数据类型|每个userid占用空间|需要存储的用户量|全部内存量|
|集合类型|64位|100000|64位*100000 = 800KB|
|Bitmaps|1位|100000000|1位*100000000 = 12.5MB|


HyperLogLog

简介

在工作当中，我们经常会遇到与统计相关的功能需求，比如统计网站PV（PageView页面访问量）,可以使用Redis的incr、incrby轻松实现。

但像UV（UniqueVisitor，独立访客）、独立IP数、搜索记录数等需要去重和计数的问题如何解决？这种求集合中不重复元素个数的问题称为基数问题。

解决基数问题有很多种方案：

（1）数据存储在MySQL表中，使用distinct count计算不重复个数

（2）使用Redis提供的hash、set、bitmaps等数据结构来处理

以上的方案结果精确，但随着数据不断增加，导致占用空间越来越大，对于非常大的数据集是不切实际的。

能否能够降低一定的精度来平衡存储空间？Redis推出了HyperLogLog

Redis HyperLogLog 是用来做基数统计的算法，HyperLogLog 的优点是，在输入元素的数量或者体积非常非常大时，计算基数所需的空间总是固定的、并且是很小的。

在 Redis 里面，每个 HyperLogLog 键只需要花费 12 KB 内存，就可以计算接近 2^64 个不同元素的基数。这和计算基数时，元素越多耗费内存就越多的集合形成鲜明对比。

但是，因为 HyperLogLog 只会根据输入元素来计算基数，而不会储存输入元素本身，所以 HyperLogLog 不能像集合那样，返回输入的各个元素。

什么是基数?

比如数据集 {1, 3, 5, 7, 5, 7, 8}， 那么这个数据集的基数集为 {1, 3, 5 ,7, 8}, 基数(不重复元素)为5。 基数估计就是在误差可接受的范围内，快速计算基数。

命令

1、pfadd

（1）格式

pfadd <key>< element> [element ...]   添加指定元素到 HyperLogLog 中

![图片](https://uploader.shimo.im/f/6fUXRJxoYP6r4iPr.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

（2）实例

![图片](https://uploader.shimo.im/f/hX8k5YjxtmeZcdFm.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

将所有元素添加到指定HyperLogLog数据结构中。如果执行命令后HLL估计的近似基数发生变化，则返回1，否则返回0。

2、pfcount

（1）格式

pfcount<key> [key ...] 计算HLL的近似基数，可以计算多个HLL，比如用HLL存储每天的UV，计算一周的UV可以使用7天的UV合并计算即可

![图片](https://uploader.shimo.im/f/iR7wbS3eIVw0t1I4.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

（2）实例

![图片](https://uploader.shimo.im/f/Iuw47XU8wuvfPLXQ.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

3、pfmerge

（1）格式

pfmerge<destkey><sourcekey> [sourcekey ...]  将一个或多个HLL合并后的结果存储在另一个HLL中，比如每月活跃用户可以使用每天的活跃用户来合并计算可得

![图片](https://uploader.shimo.im/f/yZ1F1iSM1bZmYlJQ.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)


（2）实例

![图片](https://uploader.shimo.im/f/jqPds4oE5BxXLwGL.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)


Geospatial

简介

Redis 3.2 中增加了对GEO类型的支持。GEO，Geographic，地理信息的缩写。该类型，就是元素的2维坐标，在地图上就是经纬度。redis基于该类型，提供了经纬度设置，查询，范围查询，距离查询，经纬度Hash等常见操作。

命令

1、geoadd

（1）格式

geoadd<key>< longitude><latitude><member> [longitude latitude member...]   添加地理位置（经度，纬度，名称）

![图片](https://uploader.shimo.im/f/AdtTa7WWDdBcpY3j.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)


（2）实例

geoadd china:city121.47 31.23 shanghai

geoadd china:city 106.50 29.53 chongqing 114.05 22.52 shenzhen 116.38 39.90 beijing


![图片](https://uploader.shimo.im/f/qrCacDHaygQRNKVs.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

两极无法直接添加，一般会下载城市数据，直接通过 Java 程序一次性导入。

有效的经度从 -180 度到 180 度。有效的纬度从 -85.05112878 度到 85.05112878 度。

当坐标位置超出指定范围时，该命令将会返回一个错误。

已经添加的数据，是无法再次往里面添加的。

2、geopos

（1）格式

geopos  <key><member> [member...]  获得指定地区的坐标值

![图片](https://uploader.shimo.im/f/XCpkctZZmHSgtaZt.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)



（2）实例

![图片](https://uploader.shimo.im/f/yyth4rmrbwLwvMh3.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)


3、geodist

（1）格式

geodist<key><member1><member2>  [m|km|ft|mi ]  获取两个位置之间的直线距离

![图片](https://uploader.shimo.im/f/6REtT8VBmdmXvguq.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)


（2）实例

获取两个位置之间的直线距离

![图片](https://uploader.shimo.im/f/Db0lv0t75mUQqNcU.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

单位：

m 表示单位为米[默认值]。

km 表示单位为千米。

mi 表示单位为英里。

ft 表示单位为英尺。

如果用户没有显式地指定单位参数， 那么 GEODIST 默认使用米作为单位

4、georadius

（1）格式

georadius<key>< longitude><latitude>radius  m|km|ft|mi   以给定的经纬度为中心，找出某一半径内的元素

![图片](https://uploader.shimo.im/f/GQ4AnXuEp9UZLfLA.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

经度 纬度 距离 单位

（2）实例

![图片](https://uploader.shimo.im/f/zuSmPCS4LTMYHnhn.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

# 7.Redis_Jedis_测试

Jedis所需要的jar包

|<dependency><br/><groupId>redis.clients</groupId><br/><artifactId>jedis</artifactId><br/><version>3.2.0</version><br/></dependency>|
|:----|
连接Redis注意事项

禁用Linux的防火墙：Linux(CentOS7)里执行命令

systemctl stop/disable firewalld.service

redis.conf中注释掉bind 127.0.0.1 ,然后 protected-mode no

Jedis常用操作

创建动态的工程

创建测试程序

|package com.atguigu.jedis;<br>import redis.clients.jedis.Jedis;<br>public class Demo01 {<br>public static void main(String[] args) {<br>Jedis jedis = new Jedis("192.168.137.3",6379);<br>String pong = jedis.ping();<br>System.out.println("连接成功："+pong);<br>jedis.close();<br>}<br>}|
|:----|
测试相关数据类型

Jedis-API:    Key

|jedis.set("k1", "v1");<br>jedis.set("k2", "v2");<br>jedis.set("k3", "v3");<br>Set<String> keys = jedis.keys("*");<br>System.out.println(keys.size());<br>for (String key : keys) {<br>System.out.println(key);<br>}<br>System.out.println(jedis.exists("k1"));<br>System.out.println(jedis.ttl("k1"));<br>System.out.println(jedis.get("k1"));|
|:----|
Jedis-API:    String

|jedis.mset("str1","v1","str2","v2","str3","v3");<br>System.out.println(jedis.mget("str1","str2","str3"));|
|:----|
Jedis-API:    List

|List<String> list = jedis.lrange("mylist",0,-1);<br>for (String element : list) {<br>System.out.println(element);<br>}|
|:----|
Jedis-API:    set

|jedis.sadd("orders", "order01");<br>jedis.sadd("orders", "order02");<br>jedis.sadd("orders", "order03");<br>jedis.sadd("orders", "order04");<br>Set<String> smembers = jedis.smembers("orders");<br>for (String order : smembers) {<br>System.out.println(order);<br>}<br>jedis.srem("orders", "order02");|
|:----|
Jedis-API:    hash

|jedis.hset("hash1","userName","lisi");<br>System.out.println(jedis.hget("hash1","userName"));<br>Map<String,String> map = new HashMap<String,String>();<br>map.put("telphone","13810169999");<br>map.put("address","atguigu");<br>map.put("email","abc@163.com");<br>jedis.hmset("hash2",map);<br>List<String> result = jedis.hmget("hash2", "telphone","email");<br>for (String element : result) {<br>System.out.println(element);<br>}|
|:----|
Jedis-API:    zset

|jedis.zadd("zset01", 100d, "z3");<br>jedis.zadd("zset01", 90d, "l4");<br>jedis.zadd("zset01", 80d, "w5");<br>jedis.zadd("zset01", 70d, "z6");<br><br>Set<String> zrange = jedis.zrange("zset01", 0, -1);<br>for (String e : zrange) {<br>System.out.println(e);<br>}|
|:----|

# 8.Redis_Jedis_实例

完成一个手机验证码功能

要求：

1、输入手机号，点击发送后随机生成6位数字码，2分钟有效

2、输入验证码，点击验证，返回成功或失败

3、每个手机号每天只能输入3次

![图片](https://uploader.shimo.im/f/SIQizd5buOHWLZxl.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

# 9.Redis与Spring Boot整合

Spring Boot整合Redis非常简单，只需要按如下步骤整合即可

整合步骤

在pom.xml文件中引入redis相关依赖

|<!-- redis --><br/><dependency><br/><groupId>org.springframework.boot</groupId><br/><artifactId>spring-boot-starter-data-redis</artifactId><br/></dependency><br/><br/><!-- spring2.X集成redis所需common-pool2--><br/><dependency><br/><groupId>org.apache.commons</groupId><br/><artifactId>commons-pool2</artifactId><br/><version>2.6.0</version><br/></dependency>|
|:----|

application.properties配置redis配置

|#Redis服务器地址<br/>spring.redis.host=192.168.140.136<br/>#Redis服务器连接端口<br/>spring.redis.port=6379<br/>#Redis数据库索引（默认为0）<br/>spring.redis.database= 0<br/>#连接超时时间（毫秒）<br/>spring.redis.timeout=1800000<br/>#连接池最大连接数（使用负值表示没有限制）<br/>spring.redis.lettuce.pool.max-active=20<br/>#最大阻塞等待时间(负数表示没限制)<br/>spring.redis.lettuce.pool.max-wait=-1<br/>#连接池中的最大空闲连接<br/>spring.redis.lettuce.pool.max-idle=5<br/>#连接池中的最小空闲连接<br/>spring.redis.lettuce.pool.min-idle=0|
|:----|

添加redis配置类

|@EnableCaching<br/>@Configuration<br/>public class RedisConfig extends CachingConfigurerSupport {<br/><br/>@Bean<br/>public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory) {<br/>RedisTemplate<String, Object> template = new RedisTemplate<>();<br/>RedisSerializer<String> redisSerializer = new StringRedisSerializer();<br/>Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);<br/>ObjectMapper om = new ObjectMapper();<br/>om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);<br/>om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);<br/>jackson2JsonRedisSerializer.setObjectMapper(om);<br/>template.setConnectionFactory(factory);<br/>//key序列化方式<br/>template.setKeySerializer(redisSerializer);<br/>//value序列化<br/>template.setValueSerializer(jackson2JsonRedisSerializer);<br/>//value hashmap序列化<br/>template.setHashValueSerializer(jackson2JsonRedisSerializer);<br/>return template;<br/>}<br/><br/>@Bean<br/>public CacheManager cacheManager(RedisConnectionFactory factory) {<br/>RedisSerializer<String> redisSerializer = new StringRedisSerializer();<br/>Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);<br/>//解决查询缓存转换异常的问题<br/>ObjectMapper om = new ObjectMapper();<br/>om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);<br/>om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);<br/>jackson2JsonRedisSerializer.setObjectMapper(om);<br/>//配置序列化（解决乱码的问题）,过期时间600秒<br/>RedisCacheConfiguration config = RedisCacheConfiguration.defaultCacheConfig()<br/>.entryTtl(Duration.ofSeconds(600))<br/>.serializeKeysWith(RedisSerializationContext.SerializationPair.fromSerializer(redisSerializer))<br/>.serializeValuesWith(RedisSerializationContext.SerializationPair.fromSerializer(jackson2JsonRedisSerializer))<br/>.disableCachingNullValues();<br/>RedisCacheManager cacheManager = RedisCacheManager.builder(factory)<br/>.cacheDefaults(config)<br/>.build();<br/>return cacheManager;<br/>}<br/>}|
|:----|

4、测试一下

RedisTestController中添加测试方法

|@RestController<br/>@RequestMapping("/redisTest")<br/>public class RedisTestController {<br/>@Autowired<br/>private RedisTemplate redisTemplate;<br>null<br/>@GetMapping<br/>public String testRedis() {<br/>//设置值到redis<br/>redisTemplate.opsForValue().set("name","lucy");<br/>//从redis获取值<br/>String name = (String)redisTemplate.opsForValue().get("name");<br/>return name;<br/>}<br/>}|
|:----|

# 10.Redis_事务_锁机制_秒杀

Redis的事务定义

![图片](https://uploader.shimo.im/f/IXxxwGzyD76VjdR3.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

Redis事务是一个单独的隔离操作：事务中的所有命令都会序列化、按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断。

Redis事务的主要作用就是串联多个命令防止别的命令插队。

Multi、Exec、discard

从输入Multi命令开始，输入的命令都会依次进入命令队列中，但不会执行，直到输入Exec后，Redis会将之前的命令队列中的命令依次执行。

组队的过程中可以通过discard来放弃组队。

![图片](https://uploader.shimo.im/f/Kr0mawmZfTvwUysP.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

案例：

|![图片](https://uploader.shimo.im/f/GN9YK873VuZDQ7GX.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)<br>组队成功，提交成功<br>|
|:----|:----|
|![图片](https://uploader.shimo.im/f/grYwWJrFh6LhYUps.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)<br>组队阶段报错，提交失败|
|![图片](https://uploader.shimo.im/f/Sra394r1q3bIb6Yz.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)<br>组队成功，提交有成功有失败情况|

事务的错误处理

组队中某个命令出现了报告错误，执行时整个的所有队列都会被取消。

![图片](https://uploader.shimo.im/f/NDImbuuXrE31VKsJ.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

如果执行阶段某个命令报出了错误，则只有报错的命令不会被执行，而其他的命令都会执行，不会回滚。

![图片](https://uploader.shimo.im/f/eSxelWgb4FixqKns.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

为什么要做成事务

想想一个场景：有很多人有你的账户,同时去参加双十一抢购

事务冲突的问题

例子

一个请求想给金额减8000

一个请求想给金额减5000

一个请求想给金额减1000

![图片](https://uploader.shimo.im/f/XVFDz5imsF0rwJAC.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

悲观锁

![图片](https://uploader.shimo.im/f/597HeBh2EPaqZwU5.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

悲观锁(Pessimistic Lock), 顾名思义，就是很悲观，每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会block直到它拿到锁。传统的关系型数据库里边就用到了很多这种锁机制，比如行锁，表锁等，读锁，写锁等，都是在做操作之前先上锁。

乐观锁

![图片](https://uploader.shimo.im/f/7uTRUTQh1U1qOwr8.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

乐观锁(Optimistic Lock), 顾名思义，就是很乐观，每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，可以使用版本号等机制。乐观锁适用于多读的应用类型，这样可以提高吞吐量。Redis就是利用这种check-and-set机制实现事务的。

WATCH key [key ...]

在执行multi之前，先执行watch key1 [key2],可以监视一个(或多个) key ，如果在事务执行之前这个(或这些) key 被其他命令所改动，那么事务将被打断。

![图片](https://uploader.shimo.im/f/wu4cSNAho63y0YVH.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

unwatch

取消 WATCH 命令对所有 key 的监视。

如果在执行 WATCH 命令之后，EXEC 命令或DISCARD 命令先被执行了的话，那么就不需要再执行UNWATCH 了。

[http://doc.redisfans.com/transaction/exec.html](http://doc.redisfans.com/transaction/exec.html?fileGuid=yBtmdjz5Ja42cjcb)

Redis事务三特性

单独的隔离操作

事务中的所有命令都会序列化、按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断。

没有隔离级别的概念

队列中的命令没有提交之前都不会实际被执行，因为事务提交前任何指令都不会被实际执行

不保证原子性

事务中如果有一条命令执行失败，其后的命令仍然会被执行，没有回滚

# 11.Redis_事务_秒杀案例

解决计数器和人员记录的事务操作

![图片](https://uploader.shimo.im/f/LeqfGbH84qm1hDEa.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

Redis事务--秒杀并发模拟

使用工具ab模拟测试

CentOS6 默认安装

CentOS7需要手动安装

联网：yum install httpd-tools

无网络

（1） 进入cd  /run/media/root/CentOS 7 x86_64/Packages（路径跟centos6不同）

（2） 顺序安装

apr-1.4.8-3.el7.x86_64.rpm

apr-util-1.5.2-6.el7.x86_64.rpm

httpd-tools-2.4.6-67.el7.centos.x86_64.rpm

测试及结果

通过ab测试

vim postfile 模拟表单提交参数,以&符号结尾;存放当前目录。

内容：prodid=0101&

ab -n 2000 -c 200 -k -p ~/postfile -T application/x-www-form-urlencoded http://192.168.2.115:8081/Seckill/doseckill

超卖

|![图片](https://uploader.shimo.im/f/qfFuB3aK27Bnrn71.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)|![图片](https://uploader.shimo.im/f/mnSS4TWJpR1Xx0PU.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)<br>|
|:----|:----|
超卖问题

![图片](https://uploader.shimo.im/f/kr4NkCcSpILZ31we.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

利用乐观锁淘汰用户，解决超卖问题。

![图片](https://uploader.shimo.im/f/dF0N2Lo6JNSWeMPi.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

|//增加乐观锁<br>jedis.watch(qtkey);<br><br>//3.判断库存<br>String qtkeystr = jedis.get(qtkey);<br>if(qtkeystr==null \|\| "".equals(qtkeystr.trim())) {<br>System.out.println("未初始化库存");<br>jedis.close();<br>return false ;<br>}<br><br>int qt = Integer.parseInt(qtkeystr);<br>if(qt<=0) {<br>System.err.println("已经秒光");<br>jedis.close();<br>return false;<br>}<br><br>//增加事务<br>Transaction multi = jedis.multi();<br><br>//4.减少库存<br>//jedis.decr(qtkey);<br>multi.decr(qtkey);<br><br>//5.加人<br>//jedis.sadd(usrkey, uid);<br>multi.sadd(usrkey, uid);<br><br>//执行事务<br>List<Object> list = multi.exec();<br><br>//判断事务提交是否失败<br>if(list==null \|\| list.size()==0) {<br>System.out.println("秒杀失败");<br>jedis.close();<br>return false;<br>}<br>System.err.println("秒杀成功");<br>jedis.close();|![图片](https://uploader.shimo.im/f/Pbs8E3ffL7QHWPdn.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)<br>![图片](https://uploader.shimo.im/f/EJBjypSbn9pqUQx9.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)|
|:----|:----|
继续增加并发测试

连接有限制

ab -n 2000 -c 200 -k -p postfile -T 'application/x-www-form-urlencoded'[http://192.168.140.1:8080/seckill/doseckill](http://192.168.140.1:8080/seckill/doseckill?fileGuid=yBtmdjz5Ja42cjcb)

![图片](https://uploader.shimo.im/f/oojTEAm3NhkVqbZz.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

增加-r参数，-r   Don't exit on socket receive errors.

ab -n 2000 -c 100 -r -p postfile -T 'application/x-www-form-urlencoded'[http://192.168.140.1:8080/seckill/doseckill](http://192.168.140.1:8080/seckill/doseckill?fileGuid=yBtmdjz5Ja42cjcb)

已经秒光，可是还有库存

ab -n 2000 -c 100 -p postfile -T 'application/x-www-form-urlencoded'[http://192.168.137.1:8080/seckill/doseckill](http://192.168.137.1:8080/seckill/doseckill?fileGuid=yBtmdjz5Ja42cjcb)

已经秒光，可是还有库存。原因，就是乐观锁导致很多请求都失败。先点的没秒到，后点的可能秒到了。

![图片](https://uploader.shimo.im/f/E6NN8z2NM4QbtQ2K.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

连接超时，通过连接池解决

![图片](https://uploader.shimo.im/f/YXlQdQImb0drpJFY.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

连接池

节省每次连接redis服务带来的消耗，把连接好的实例反复利用。

通过参数管理连接的行为

代码见项目中

链接池参数

MaxTotal：控制一个pool可分配多少个jedis实例，通过pool.getResource()来获取；如果赋值为-1，则表示不限制；如果pool已经分配了MaxTotal个jedis实例，则此时pool的状态为exhausted。

maxIdle：控制一个pool最多有多少个状态为idle(空闲)的jedis实例；

MaxWaitMillis：表示当borrow一个jedis实例时，最大的等待毫秒数，如果超过等待时间，则直接抛JedisConnectionException；

testOnBorrow：获得一个jedis实例的时候是否检查连接可用性（ping()）；如果为true，则得到的jedis实例均是可用的；

解决库存遗留问题

LUA脚本

![图片](https://uploader.shimo.im/f/KoDtlymdKYSDsI5p.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

Lua 是一个小巧的[脚本语言](http://baike.baidu.com/item/%E8%84%9A%E6%9C%AC%E8%AF%AD%E8%A8%80?fileGuid=yBtmdjz5Ja42cjcb)，Lua脚本可以很容易的被C/C++ 代码调用，也可以反过来调用C/C++的函数，Lua并没有提供强大的库，一个完整的Lua解释器不过200k，所以Lua不适合作为开发独立应用程序的语言，而是作为嵌入式脚本语言。

很多应用程序、游戏使用LUA作为自己的嵌入式脚本语言，以此来实现可配置性、可扩展性。

这其中包括魔兽争霸地图、魔兽世界、博德之门、愤怒的小鸟等众多游戏插件或外挂。

[https://www.w3cschool.cn/lua/](https://www.w3cschool.cn/lua/?fileGuid=yBtmdjz5Ja42cjcb)

LUA脚本在Redis中的优势

将复杂的或者多步的redis操作，写为一个脚本，一次提交给redis执行，减少反复连接redis的次数。提升性能。

LUA脚本是类似redis事务，有一定的原子性，不会被其他命令插队，可以完成一些redis事务性的操作。

但是注意redis的lua脚本功能，只有在Redis 2.6以上的版本才可以使用。

利用lua脚本淘汰用户，解决超卖问题。

redis 2.6版本以后，通过lua脚本解决争抢问题，实际上是redis 利用其单线程的特性，用任务队列的方式解决多任务并发问题。

![图片](https://uploader.shimo.im/f/Sx7IM3DxrO39LRqW.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

Redis_事务_秒杀案例_代码

项目结构

![图片](https://uploader.shimo.im/f/QDuphfuKGyof7zZA.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

第一版：简单版

老师点10次，正常秒杀

同学一起点试一试，秒杀也是正常的。这是因为还达不到并发的效果。

使用工具ab模拟并发测试，会出现超卖情况。查看库存会出现负数。

第二版：加事务-乐观锁(解决超卖),但出现遗留库存和连接超时

第三版：连接池解决超时问题

第四版：解决库存依赖问题，LUA脚本

|local userid=KEYS[1];<br>local prodid=KEYS[2];<br>localqtkey="sk:"..prodid..":qt";<br>localusersKey="sk:"..prodid.":usr';<br>local userExists=redis.call("sismember",usersKey,userid);<br>if tonumber(userExists)==1 then<br>return 2;<br>end<br>local num= redis.call("get" ,qtkey);<br>if tonumber(num)<=0 then<br>return 0;<br>else<br>redis.call("decr",qtkey);<br>redis.call("sadd",usersKey,userid);<br>end<br>return 1;|
|:----|

# 12.Redis持久化之RDB

总体介绍

官网介绍：http://www.redis.io

![图片](https://uploader.shimo.im/f/zbSTcwAtKCyHrcUh.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

Redis 提供了2个不同形式的持久化方式。

RDB（Redis DataBase）

AOF（Append Of File）

RDB（Redis DataBase）

官网介绍

![图片](https://uploader.shimo.im/f/3qMiEO4PWvW90J2X.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

是什么

在指定的时间间隔内将内存中的数据集快照写入磁盘， 也就是行话讲的Snapshot快照，它恢复时是将快照文件直接读到内存里

备份是如何执行的

Redis会单独创建（fork）一个子进程来进行持久化，会先将数据写入到 一个临时文件中，待持久化过程都结束了，再用这个临时文件替换上次持久化好的文件。 整个过程中，主进程是不进行任何IO操作的，这就确保了极高的性能 如果需要进行大规模数据的恢复，且对于数据恢复的完整性不是非常敏感，那RDB方式要比AOF方式更加的高效。RDB的缺点是最后一次持久化后的数据可能丢失。

Fork

Fork的作用是复制一个与当前进程一样的进程。新进程的所有数据（变量、环境变量、程序计数器等） 数值都和原进程一致，但是是一个全新的进程，并作为原进程的子进程

在Linux程序中，fork()会产生一个和父进程完全相同的子进程，但子进程在此后多会exec系统调用，出于效率考虑，Linux中引入了“写时复制技术”

一般情况父进程和子进程会共用同一段物理内存，只有进程空间的各段的内容要发生变化时，才会将父进程的内容复制一份给子进程。

RDB持久化流程

![图片](https://uploader.shimo.im/f/SSlDmVkra8AIdrmA.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

dump.rdb文件

在redis.conf中配置文件名称，默认为dump.rdb

![图片](https://uploader.shimo.im/f/2owP6gPgEckz9V8B.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

配置位置

rdb文件的保存路径，也可以修改。默认为Redis启动时命令行所在的目录下

dir "/myredis/"

![图片](https://uploader.shimo.im/f/M56xUQPO2rHeuYFD.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

如何触发RDB快照；保持策略

配置文件中默认的快照配置

![图片](https://uploader.shimo.im/f/sGvcNCSFyPhwum5g.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

命令save VS bgsave

save ：save时只管保存，其它不管，全部阻塞。手动保存。不建议。

bgsave：Redis会在后台异步进行快照操作， 快照同时还可以响应客户端请求。

可以通过lastsave 命令获取最后一次成功执行快照的时间

flushall命令

执行flushall命令，也会产生dump.rdb文件，但里面是空的，无意义

###SNAPSHOTTING快照###

Save

格式：save 秒钟 写操作次数

RDB是整个内存的压缩过的Snapshot，RDB的数据结构，可以配置复合的快照触发条件，

默认是1分钟内改了1万次，或5分钟内改了10次，或15分钟内改了1次。

禁用

不设置save指令，或者给save传入空字符串

stop-writes-on-bgsave-error

![图片](https://uploader.shimo.im/f/LHm2WDpcmuS8sDtw.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

当Redis无法写入磁盘的话，直接关掉Redis的写操作。推荐yes.

rdbcompression 压缩文件

![图片](https://uploader.shimo.im/f/w2U62etvjGLJrzGd.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

对于存储到磁盘中的快照，可以设置是否进行压缩存储。如果是的话，redis会采用LZF算法进行压缩。

如果你不想消耗CPU来进行压缩的话，可以设置为关闭此功能。推荐yes.

rdbchecksum 检查完整性

![图片](https://uploader.shimo.im/f/MjZduV6xJilCpedx.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

在存储快照后，还可以让redis使用CRC64算法来进行数据校验，

但是这样做会增加大约10%的性能消耗，如果希望获取到最大的性能提升，可以关闭此功能

推荐yes.

rdb的备份

先通过config get dir  查询rdb文件的目录

将*.rdb的文件拷贝到别的地方

rdb的恢复

关闭Redis

先把备份的文件拷贝到工作目录下 cp dump2.rdb dump.rdb

启动Redis, 备份数据会直接加载

优势

适合大规模的数据恢复

对数据完整性和一致性要求不高更适合使用

节省磁盘空间

恢复速度快

![图片](https://uploader.shimo.im/f/ZGOtIO4iQKi5XaWX.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

劣势

Fork的时候，内存中的数据被克隆了一份，大致2倍的膨胀性需要考虑

虽然Redis在fork时使用了写时拷贝技术,但是如果数据庞大时还是比较消耗性能。

在备份周期在一定间隔时间做一次备份，所以如果Redis意外down掉的话，就会丢失最后一次快照后的所有修改。

如何停止

动态停止RDB：redis-cli config set save ""#save后给空值，表示禁用保存策略

小总结

![图片](https://uploader.shimo.im/f/aAS7hU3hCvOseNIU.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

# 13.Redis持久化之AOF

AOF（Append Only File）

是什么

以日志的形式来记录每个写操作（增量保存），将Redis执行过的所有写指令记录下来(读操作不记录)， 只许追加文件但不可以改写文件，redis启动之初会读取该文件重新构建数据，换言之，redis 重启的话就根据日志文件的内容将写指令从前到后执行一次以完成数据的恢复工作

AOF持久化流程

（1）客户端的请求写命令会被append追加到AOF缓冲区内；

（2）AOF缓冲区根据AOF持久化策略[always,everysec,no]将操作sync同步到磁盘的AOF文件中；

（3）AOF文件大小超过重写策略或手动重写时，会对AOF文件rewrite重写，压缩AOF文件容量；

（4）Redis服务重启时，会重新load加载AOF文件中的写操作达到数据恢复的目的；

![图片](https://uploader.shimo.im/f/5ko3knKkHxjSfVTw.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

AOF默认不开启

可以在redis.conf中配置文件名称，默认为 appendonly.aof

AOF文件的保存路径，同RDB的路径一致。

AOF和RDB同时开启，redis听谁的？

AOF和RDB同时开启，系统默认取AOF的数据（数据不会存在丢失）

AOF启动/修复/恢复

AOF的备份机制和性能虽然和RDB不同, 但是备份和恢复的操作同RDB一样，都是拷贝备份文件，需要恢复时再拷贝到Redis工作目录下，启动系统即加载。

正常恢复

修改默认的appendonly no，改为yes

将有数据的aof文件复制一份保存到对应目录(查看目录：config get dir)

恢复：重启redis然后重新加载

异常恢复

修改默认的appendonly no，改为yes

如遇到AOF文件损坏，通过/usr/local/bin/redis-check-aof--fix appendonly.aof进行恢复

备份被写坏的AOF文件

恢复：重启redis，然后重新加载

AOF同步频率设置

appendfsync always

始终同步，每次Redis的写入都会立刻记入日志；性能较差但数据完整性比较好

appendfsync everysec

每秒同步，每秒记入日志一次，如果宕机，本秒的数据可能丢失。

appendfsync no

redis不主动进行同步，把同步时机交给操作系统。

Rewrite压缩

1是什么：

AOF采用文件追加方式，文件会越来越大为避免出现此种情况，新增了重写机制, 当AOF文件的大小超过所设定的阈值时，Redis就会启动AOF文件的内容压缩， 只保留可以恢复数据的最小指令集.可以使用命令bgrewriteaof

2重写原理，如何实现重写

AOF文件持续增长而过大时，会fork出一条新进程来将文件重写(也是先写临时文件最后再rename)，redis4.0版本后的重写，是指上就是把rdb 的快照，以二级制的形式附在新的aof头部，作为已有的历史数据，替换掉原来的流水账操作。

no-appendfsync-on-rewrite：

如果 no-appendfsync-on-rewrite=yes ,不写入aof文件只写入缓存，用户请求不会阻塞，但是在这段时间如果宕机会丢失这段时间的缓存数据。（降低数据安全性，提高性能）

如果 no-appendfsync-on-rewrite=no,  还是会把数据往磁盘里刷，但是遇到重写操作，可能会发生阻塞。（数据安全，但是性能降低）

触发机制，何时重写

Redis会记录上次重写时的AOF大小，默认配置是当AOF文件大小是上次rewrite后大小的一倍且文件大于64M时触发

重写虽然可以节约大量磁盘空间，减少恢复时间。但是每次重写还是有一定的负担的，因此设定Redis要满足一定条件才会进行重写。

auto-aof-rewrite-percentage：设置重写的基准值，文件达到100%时开始重写（文件是原来重写后文件的2倍时触发）

auto-aof-rewrite-min-size：设置重写的基准值，最小文件64MB。达到这个值开始重写。

例如：文件达到70MB开始重写，降到50MB，下次什么时候开始重写？100MB

系统载入时或者上次重写完毕时，Redis会记录此时AOF大小，设为base_size,

如果Redis的AOF当前大小>= base_size +base_size*100% (默认)且当前大小>=64mb(默认)的情况下，Redis会对AOF进行重写。

3、重写流程

（1）bgrewriteaof触发重写，判断是否当前有bgsave或bgrewriteaof在运行，如果有，则等待该命令结束后再继续执行。

（2）主进程fork出子进程执行重写操作，保证主进程不会阻塞。

（3）子进程遍历redis内存中数据到临时文件，客户端的写请求同时写入aof_buf缓冲区和aof_rewrite_buf重写缓冲区保证原AOF文件完整以及新AOF文件生成期间的新的数据修改动作不会丢失。

（4）1).子进程写完新的AOF文件后，向主进程发信号，父进程更新统计信息。2).主进程把aof_rewrite_buf中的数据写入到新的AOF文件。

（5）使用新的AOF文件覆盖旧的AOF文件，完成AOF重写。

![图片](https://uploader.shimo.im/f/kT9hEEkzEFHmpqwI.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

优势

![图片](https://uploader.shimo.im/f/DWwT59SzXyrd1Ack.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

备份机制更稳健，丢失数据概率更低。

可读的日志文本，通过操作AOF稳健，可以处理误操作。

劣势

比起RDB占用更多的磁盘空间。

恢复备份速度要慢。

每次读写都同步的话，有一定的性能压力。

存在个别Bug，造成恢复不能。

小总结

![图片](https://uploader.shimo.im/f/IlhvLHSiPiY8ASsZ.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

总结(Which one)

用哪个好

官方推荐两个都启用。

如果对数据不敏感，可以选单独用RDB。

不建议单独用 AOF，因为可能会出现Bug。

如果只是做纯内存缓存，可以都不用。

官网建议

![图片](https://uploader.shimo.im/f/O6Qw6zgMcZicFV7d.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

RDB持久化方式能够在指定的时间间隔能对你的数据进行快照存储

AOF持久化方式记录每次对服务器写的操作,当服务器重启的时候会重新执行这些命令来恢复原始的数据,AOF命令以redis协议追加保存每次写的操作到文件末尾.

Redis还能对AOF文件进行后台重写,使得AOF文件的体积不至于过大

只做缓存：如果你只希望你的数据在服务器运行的时候存在,你也可以不使用任何持久化方式.

同时开启两种持久化方式

在这种情况下,当redis重启的时候会优先载入AOF文件来恢复原始的数据, 因为在通常情况下AOF文件保存的数据集要比RDB文件保存的数据集要完整.

RDB的数据不实时，同时使用两者时服务器重启也只会找AOF文件。那要不要只使用AOF呢？

建议不要，因为RDB更适合用于备份数据库(AOF在不断变化不好备份)， 快速重启，而且不会有AOF可能潜在的bug，留着作为一个万一的手段。

性能建议

|因为RDB文件只用作后备用途，建议只在Slave上持久化RDB文件，而且只要15分钟备份一次就够了，只保留save 900 1这条规则。<br><br>如果使用AOF，好处是在最恶劣情况下也只会丢失不超过两秒数据，启动脚本较简单只load自己的AOF文件就可以了。<br>代价,一是带来了持续的IO，二是AOF rewrite的最后将rewrite过程中产生的新数据写到新文件造成的阻塞几乎是不可避免的。<br>只要硬盘许可，应该尽量减少AOF rewrite的频率，AOF重写的基础大小默认值64M太小了，可以设到5G以上。<br>默认超过原大小100%大小时重写可以改到适当的数值。|
|:----|

# 14.Redis_主从复制

是什么

主机数据更新后根据配置和策略， 自动同步到备机的master/slaver机制，Master以写为主，Slave以读为主

能干嘛

读写分离，性能扩展

容灾快速恢复

![图片](https://uploader.shimo.im/f/NmgWTW44PGduYQgO.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

怎么玩：主从复制

拷贝多个redis.conf文件include(写绝对路径)

开启daemonize yes

Pid文件名字pidfile

指定端口port

Log文件名字

dump.rdb名字dbfilename

Appendonly 关掉或者换名字

新建redis6379.conf，填写以下内容

include /myredis/redis.conf

pidfile /var/run/redis_6379.pid

port 6379

dbfilename dump6379.rdb

![图片](https://uploader.shimo.im/f/HG0fXHo87f6CJ1Vm.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

新建redis6380.conf，填写以下内容

![图片](https://uploader.shimo.im/f/nh6AGyJs5FyC4hA4.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

新建redis6381.conf，填写以下内容

![图片](https://uploader.shimo.im/f/qfwRTOasVjnpjibz.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

slave-priority 10

设置从机的优先级，值越小，优先级越高，用于选举主机时使用。默认100

启动三台redis服务器

![图片](https://uploader.shimo.im/f/3RdVLmsP5O53kt03.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

查看系统进程，看看三台服务器是否启动

![图片](https://uploader.shimo.im/f/xbJFOMUAn6rzyFG1.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

查看三台主机运行情况

info replication

打印主从复制的相关信息

![图片](https://uploader.shimo.im/f/5uwQ2chrH2kHcNNr.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

配从(库)不配主(库)

slaveof  <ip><port>

成为某个实例的从服务器

1、在6380和6381上执行: slaveof 127.0.0.1 6379

![图片](https://uploader.shimo.im/f/i3I0LTSOL7NlCqgS.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

2、在主机上写，在从机上可以读取数据

在从机上写数据报错

![图片](https://uploader.shimo.im/f/w4DY3nRZ3i9zDyq9.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

3、主机挂掉，重启就行，一切如初

4、从机重启需重设：slaveof 127.0.0.1 6379

可以将配置增加到文件中。永久生效。

![图片](https://uploader.shimo.im/f/BQ5jGxOHbvYUwpJD.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

常用3招

一主二仆

切入点问题？slave1、slave2是从头开始复制还是从切入点开始复制?比如从k4进来，那之前的k1,k2,k3是否也可以复制？

从机是否可以写？set可否？

主机shutdown后情况如何？从机是上位还是原地待命？

主机又回来了后，主机新增记录，从机还能否顺利复制？

其中一台从机down后情况如何？依照原有它能跟上大部队吗？

![图片](https://uploader.shimo.im/f/yPgQxlWIj5jopWn6.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)


薪火相传

上一个Slave可以是下一个slave的Master，Slave同样可以接收其他 slaves的连接和同步请求，那么该slave作为了链条中下一个的master, 可以有效减轻master的写压力,去中心化降低风险。

用 slaveof  <ip><port>

中途变更转向:会清除之前的数据，重新建立拷贝最新的

风险是一旦某个slave宕机，后面的slave都没法备份

主机挂了，从机还是从机，无法写数据了

![图片](https://uploader.shimo.im/f/CVyPEfNbAHHe97ls.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

![图片](https://uploader.shimo.im/f/jYpVAT3r3FkvLWfB.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

反客为主

当一个master宕机后，后面的slave可以立刻升为master，其后面的slave不用做任何修改。

用 slaveof  no one  将从机变为主机。

![图片](https://uploader.shimo.im/f/VQrAs9Lzn7lA0csK.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

复制原理

Slave启动成功连接到master后会发送一个sync命令

Master接到命令启动后台的存盘进程，同时收集所有接收到的用于修改数据集命令， 在后台进程执行完毕之后，master将传送整个数据文件到slave,以完成一次完全同步

全量复制：而slave服务在接收到数据库文件数据后，将其存盘并加载到内存中。

增量复制：Master继续将新的所有收集到的修改命令依次传给slave,完成同步

但是只要是重新连接master,一次完全同步（全量复制)将被自动执行

![图片](https://uploader.shimo.im/f/Ka7tRlfmfmhQeQSl.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

哨兵模式(sentinel)

是什么

反客为主的自动版，能够后台监控主机是否故障，如果故障了根据投票数自动将从库转换为主库

![图片](https://uploader.shimo.im/f/O0yZ6KUju6yZ1Dz2.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

怎么玩(使用步骤)

调整为一主二仆模式，6379带着6380、6381

![图片](https://uploader.shimo.im/f/QAqTlurqaE6mLEtk.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

自定义的/myredis目录下新建sentinel.conf文件，名字绝不能错

配置哨兵,填写内容

sentinel monitor mymaster 127.0.0.1 6379 1

其中mymaster为监控对象起的服务器名称， 1 为至少有多少个哨兵同意迁移的数量。

启动哨兵

/usr/local/bin

redis做压测可以用自带的redis-benchmark工具

执行redis-sentinel  /myredis/sentinel.conf

![图片](https://uploader.shimo.im/f/lmh2AadIER69RTMy.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

当主机挂掉，从机选举中产生新的主机

(大概10秒左右可以看到哨兵窗口日志，切换了新的主机)

哪个从机会被选举为主机呢？根据优先级别：slave-priority

原主机重启后会变为从机。

![图片](https://uploader.shimo.im/f/BdYRKop0J16DSpgx.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

复制延时

由于所有的写操作都是先在Master上操作，然后同步更新到Slave上，所以从Master同步到Slave机器有一定的延迟，当系统很繁忙的时候，延迟问题会更加严重，Slave机器数量的增加也会使这个问题更加严重。

故障恢复

![图片](https://uploader.shimo.im/f/4K5MkFLGsu7Oaxz6.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

优先级在redis.conf中默认：slave-priority 100，值越小优先级越高

偏移量是指获得原主机数据最全的

每个redis实例启动后都会随机生成一个40位的runid

主从复制

|private static JedisSentinelPool jedisSentinelPool=null;<br/><br/>public static  Jedis getJedisFromSentinel(){<br/>if(jedisSentinelPool==null){<br/>Set<String> sentinelSet=new HashSet<>();<br/>sentinelSet.add("192.168.11.103:26379");<br/><br/>JedisPoolConfig jedisPoolConfig =new JedisPoolConfig();<br/>jedisPoolConfig.setMaxTotal(10); //最大可用连接数<br/>jedisPoolConfig.setMaxIdle(5); //最大闲置连接数<br/>jedisPoolConfig.setMinIdle(5); //最小闲置连接数<br/>jedisPoolConfig.setBlockWhenExhausted(true); //连接耗尽是否等待<br/>jedisPoolConfig.setMaxWaitMillis(2000); //等待时间<br/>jedisPoolConfig.setTestOnBorrow(true); //取连接的时候进行一下测试ping pong<br/><br/>jedisSentinelPool=new JedisSentinelPool("mymaster",sentinelSet,jedisPoolConfig);<br/>return jedisSentinelPool.getResource();<br/>}else{<br/>return jedisSentinelPool.getResource();<br/>}<br/>}|
|:----|

# 15.Redis集群

问题

容量不够，redis如何进行扩容？

并发写操作， redis如何分摊？

另外，主从模式，薪火相传模式，主机宕机，导致ip地址发生变化，应用程序中配置需要修改对应的主机地址、端口等信息。

之前通过代理主机来解决，但是redis3.0中提供了解决方案。就是无中心化集群配置。

什么是集群

Redis 集群实现了对Redis的水平扩容，即启动N个redis节点，将整个数据库分布存储在这N个节点中，每个节点存储总数据的1/N。

Redis 集群通过分区（partition）来提供一定程度的可用性（availability）： 即使集群中有一部分节点失效或者无法进行通讯， 集群也可以继续处理命令请求。

删除持久化数据

将rdb,aof文件都删除掉。

制作6个实例，6379,6380,6381,6389,6390,6391

配置基本信息

开启daemonize yes

Pid文件名字

指定端口

Log文件名字

Dump.rdb名字

Appendonly 关掉或者换名字

redis cluster配置修改

cluster-enabled yes    打开集群模式

cluster-config-file nodes-6379.conf  设定节点配置文件名

cluster-node-timeout 15000   设定节点失联时间，超过该时间（毫秒），集群自动进行主从切换。

|include /home/bigdata/redis.conf<br>port 6379<br>pidfile "/var/run/redis_6379.pid"<br>dbfilename "dump6379.rdb"<br>dir "/home/bigdata/redis_cluster"<br>logfile "/home/bigdata/redis_cluster/redis_err_6379.log"<br>cluster-enabled yes<br>cluster-config-file nodes-6379.conf<br>cluster-node-timeout 15000|
|:----|

修改好redis6379.conf文件，拷贝多个redis.conf文件

![图片](https://uploader.shimo.im/f/E4GPzB5zIcouKRO0.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

使用查找替换修改另外5个文件

例如：:%s/6379/6380

启动6个redis服务

![图片](https://uploader.shimo.im/f/g0NurjelRg6iBUZ3.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

将六个节点合成一个集群

组合之前，请确保所有redis实例启动后，nodes-xxxx.conf文件都生成正常。

![图片](https://uploader.shimo.im/f/GgnhDPwdwSf52hxv.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

合体：

cd  /opt/redis-6.2.1/src

|redis-cli --cluster create --cluster-replicas 1 192.168.11.101:6379192.168.11.101:6380 192.168.11.101:6381 192.168.11.101:6389 192.168.11.101:6390 192.168.11.101:6391|
|:----|

此处不要用127.0.0.1， 请用真实IP地址

--replicas 1 采用最简单的方式配置集群，一台主机，一台从机，正好三组。

![图片](https://uploader.shimo.im/f/71NGqb8bNBAfmySz.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

![图片](https://uploader.shimo.im/f/RDPc71rXGLnCXtRU.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

普通方式登录

可能直接进入读主机，存储数据时，会出现MOVED重定向操作。所以，应该以集群方式登录。

![图片](https://uploader.shimo.im/f/0r1RT3WWchvzTYZK.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

-c 采用集群策略连接，设置数据会自动切换到相应的写主机

![图片](https://uploader.shimo.im/f/P7eYWME6gk0N0nKc.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

通过 cluster nodes 命令查看集群信息

![图片](https://uploader.shimo.im/f/Dh8wUYN06WgnKgjg.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

redis cluster 如何分配这六个节点?

一个集群至少要有三个主节点。

选项 --cluster-replicas 1 表示我们希望为集群中的每个主节点创建一个从节点。

分配原则尽量保证每个主数据库运行在不同的IP地址，每个从库和主库不在一个IP地址上。

什么是slots

[OK] All nodes agree about slots configuration.

>>> Check for open slots...

>>> Check slots coverage...

[OK] All16384slots covered.

一个 Redis 集群包含 16384 个插槽（hash slot）， 数据库中的每个键都属于这 16384 个插槽的其中一个，

集群使用公式 CRC16(key) % 16384 来计算键 key 属于哪个槽， 其中 CRC16(key) 语句用于计算键 key 的 CRC16 校验和 。

集群中的每个节点负责处理一部分插槽。 举个例子， 如果一个集群可以有主节点， 其中：

节点 A 负责处理 0 号至 5460 号插槽。

节点 B 负责处理 5461 号至 10922 号插槽。

节点 C 负责处理 10923 号至 16383 号插槽。

在集群中录入值

在redis-cli每次录入、查询键值，redis都会计算出该key应该送往的插槽，如果不是该客户端对应服务器的插槽，redis会报错，并告知应前往的redis实例地址和端口。

redis-cli客户端提供了 –c 参数实现自动重定向。

如 redis-cli-c–p 6379 登入后，再录入、查询键值对可以自动重定向。

不在一个slot下的键值，是不能使用mget,mset等多键操作。

![图片](https://uploader.shimo.im/f/C7EL1dSeiRGPiJ4c.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

可以通过{}来定义组的概念，从而使key中{}内相同内容的键值对放到一个slot中去。

![图片](https://uploader.shimo.im/f/ZHULQqobRjc0nYge.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

查询集群中的值

CLUSTER GETKEYSINSLOT <slot><count> 返回 count 个 slot 槽中的键。

![图片](https://uploader.shimo.im/f/LYCdRBEQhx1q8Jhs.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

故障恢复

如果主节点下线？从节点能否自动升为主节点？注意：15秒超时

![图片](https://uploader.shimo.im/f/7DHI8UE9qGuTY4tH.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

主节点恢复后，主从关系会如何？主节点回来变成从机。

![图片](https://uploader.shimo.im/f/JO1eC4aEQRZw38MP.jpeg!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

如果所有某一段插槽的主从节点都宕掉，redis服务是否还能继续?

如果某一段插槽的主从都挂掉，而cluster-require-full-coverage 为yes ，那么 ，整个集群都挂掉

如果某一段插槽的主从都挂掉，而cluster-require-full-coverage 为no ，那么，该插槽数据全都不能使用，也无法存储。

redis.conf中的参数  cluster-require-full-coverage

集群的Jedis开发

即使连接的不是主机，集群会自动切换主机存储。主机写，从机读。

无中心化主从集群。无论从哪台主机写的数据，其他主机上都能读到数据。

|public class JedisClusterTest {<br>public static void main(String[] args) {<br>Set<HostAndPort>set =new HashSet<HostAndPort>();<br>set.add(new HostAndPort("192.168.31.211",6379));<br>JedisCluster jedisCluster=new JedisCluster(set);<br>jedisCluster.set("k1", "v1");<br>System.out.println(jedisCluster.get("k1"));<br>}<br>}|
|:----|

Redis 集群提供了以下好处

实现扩容

分摊压力

无中心配置相对简单

Redis 集群的不足

多键操作是不被支持的

多键的Redis事务是不被支持的。lua脚本不被支持

由于集群方案出现较晚，很多公司已经采用了其他的集群方案，而代理或者客户端分片的方案想要迁移至redis cluster，需要整体迁移而不是逐步过渡，复杂度较大。

# 16.Redis应用问题解决

## 16.1缓存穿透

问题描述

key对应的数据在数据源并不存在，每次针对此key的请求从缓存获取不到，请求都会压到数据源，从而可能压垮数据源。比如用一个不存在的用户id获取用户信息，不论缓存还是数据库都没有，若黑客利用此漏洞进行攻击可能压垮数据库。

![图片](https://uploader.shimo.im/f/imtQcrgNeAerHnGx.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

解决方案

一个一定不存在缓存及查询不到的数据，由于缓存是不命中时被动写的，并且出于容错考虑，如果从存储层查不到数据则不写入缓存，这将导致这个不存在的数据每次请求都要到存储层去查询，失去了缓存的意义。

解决方案：

对空值缓存：如果一个查询返回的数据为空（不管是数据是否不存在），我们仍然把这个空结果（null）进行缓存，设置空结果的过期时间会很短，最长不超过五分钟

设置可访问的名单（白名单）：

使用bitmaps类型定义一个可以访问的名单，名单id作为bitmaps的偏移量，每次访问和bitmap里面的id进行比较，如果访问id不在bitmaps里面，进行拦截，不允许访问。

采用布隆过滤器：(布隆过滤器（Bloom Filter）是1970年由布隆提出的。它实际上是一个很长的二进制向量(位图)和一系列随机映射函数（哈希函数）。

布隆过滤器可以用于检索一个元素是否在一个集合中。它的优点是空间效率和查询时间都远远超过一般的算法，缺点是有一定的误识别率和删除困难。)

将所有可能存在的数据哈希到一个足够大的bitmaps中，一个一定不存在的数据会被 这个bitmaps拦截掉，从而避免了对底层存储系统的查询压力。

进行实时监控：当发现Redis的命中率开始急速降低，需要排查访问对象和访问的数据，和运维人员配合，可以设置黑名单限制服务

## 17.2缓存击穿

问题描述

key对应的数据存在，但在redis中过期，此时若有大量并发请求过来，这些请求发现缓存过期一般都会从后端DB加载数据并回设到缓存，这个时候大并发的请求可能会瞬间把后端DB压垮。

![图片](https://uploader.shimo.im/f/J1DqEEO12XjtCDNd.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

解决方案

key可能会在某些时间点被超高并发地访问，是一种非常“热点”的数据。这个时候，需要考虑一个问题：缓存被“击穿”的问题。

解决问题：

（1）预先设置热门数据：在redis高峰访问之前，把一些热门数据提前存入到redis里面，加大这些热门数据key的时长

（2）实时调整：现场监控哪些数据热门，实时调整key的过期时长

（3）使用锁：

就是在缓存失效的时候（判断拿出来的值为空），不是立即去load db。

先使用缓存工具的某些带成功操作返回值的操作（比如Redis的SETNX）去set一个mutex key

当操作返回成功时，再进行load db的操作，并回设缓存,最后删除mutex key；

当操作返回失败，证明有线程在load db，当前线程睡眠一段时间再重试整个get缓存的方法。

![图片](https://uploader.shimo.im/f/acu2KEunLBabgC51.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

## 17.3缓存雪崩

问题描述

key对应的数据存在，但在redis中过期，此时若有大量并发请求过来，这些请求发现缓存过期一般都会从后端DB加载数据并回设到缓存，这个时候大并发的请求可能会瞬间把后端DB压垮。

缓存雪崩与缓存击穿的区别在于这里针对很多key缓存，前者则是某一个key

正常访问

![图片](https://uploader.shimo.im/f/4gYK6mEGs45Nn7PJ.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

缓存失效瞬间

![图片](https://uploader.shimo.im/f/YKuIF0imBjXvLBjC.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

解决方案

缓存失效时的雪崩效应对底层系统的冲击非常可怕！

解决方案：

构建多级缓存架构：nginx缓存 + redis缓存 +其他缓存（ehcache等）

使用锁或队列：

用加锁或者队列的方式保证来保证不会有大量的线程对数据库一次性进行读写，从而避免失效时大量的并发请求落到底层存储系统上。不适用高并发情况

设置过期标志更新缓存：

记录缓存数据是否过期（设置提前量），如果过期会触发通知另外的线程在后台去更新实际key的缓存。

将缓存失效时间分散开：

比如我们可以在原有的失效时间基础上增加一个随机值，比如1-5分钟随机，这样每一个缓存的过期时间的重复率就会降低，就很难引发集体失效的事件。

## 17.4分布式锁

问题描述

随着业务发展的需要，原单体单机部署的系统被演化成分布式集群系统后，由于分布式系统多线程、多进程并且分布在不同机器上，这将使原单机部署情况下的并发控制锁策略失效，单纯的Java API并不能提供分布式锁的能力。为了解决这个问题就需要一种跨JVM的互斥机制来控制共享资源的访问，这就是分布式锁要解决的问题！

分布式锁主流的实现方案：

1. 基于数据库实现分布式锁

2. 基于缓存（Redis等）

3. 基于Zookeeper

每一种分布式锁解决方案都有各自的优缺点：

1. 性能：redis最高

2. 可靠性：zookeeper最高

这里，我们就基于redis实现分布式锁。

解决方案：使用redis实现分布式锁

redis:命令

set sku:1:info “OK” NX PX 10000

EX second ：设置键的过期时间为 second 秒。 SET key value EX second 效果等同于 SETEX key second value 。

PX millisecond ：设置键的过期时间为 millisecond 毫秒。 SET key value PX millisecond 效果等同于 PSETEX key millisecond value 。

NX ：只在键不存在时，才对键进行设置操作。 SET key value NX 效果等同于 SETNX key value 。

XX ：只在键已经存在时，才对键进行设置操作。

![图片](https://uploader.shimo.im/f/gcTFm8s6BXDbkIZi.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

1. 多个客户端同时获取锁（setnx）

2. 获取成功，执行业务逻辑{从db获取数据，放入缓存}，执行完成释放锁（del）

3. 其他客户端等待重试

编写代码

Redis: set num 0

|@GetMapping("testLock")<br/>public void testLock(){<br/>//1获取锁，setne<br/>Boolean lock = redisTemplate.opsForValue().setIfAbsent("lock", "111");<br/>//2获取锁成功、查询num的值<br/>if(lock){<br/>Object value = redisTemplate.opsForValue().get("num");<br/>//2.1判断num为空return<br/>if(StringUtils.isEmpty(value)){<br/>return;<br/>}<br/>//2.2有值就转成成int<br/>int num = Integer.parseInt(value+"");<br/>//2.3把redis的num加1<br/>redisTemplate.opsForValue().set("num", ++num);<br/>//2.4释放锁，del<br/>redisTemplate.delete("lock");<br/><br/>}else{<br/>//3获取锁失败、每隔0.1秒再获取<br/>try {<br/>Thread.sleep(100);<br/>testLock();<br/>} catch (InterruptedException e) {<br/>e.printStackTrace();<br/>}<br/>}<br/>}|
|:----|
重启，服务集群，通过网关压力测试：

ab -n 1000 -c 100 http://192.168.140.1:8080/test/testLock

![图片](https://uploader.shimo.im/f/XqUEIyRgOh1q1xAq.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

查看redis中num的值：

![图片](https://uploader.shimo.im/f/rEzGTWFBE11WnOfp.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

基本实现。

问题：setnx刚好获取到锁，业务逻辑出现异常，导致锁无法释放

解决：设置过期时间，自动释放锁。

优化之设置锁的过期时间

设置过期时间有两种方式：

1. 首先想到通过expire设置过期时间（缺乏原子性：如果在setnx和expire之间出现异常，锁也无法释放）

2. 在set时指定过期时间（推荐）

![图片](https://uploader.shimo.im/f/iFlbvNZkGN1YAmtz.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

设置过期时间：

![图片](https://uploader.shimo.im/f/4Pl313juwd7TwN8t.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

压力测试肯定也没有问题。自行测试

问题：可能会释放其他服务器的锁。

场景：如果业务逻辑的执行时间是7s。执行流程如下

index1业务逻辑没执行完，3秒后锁被自动释放。

index2获取到锁，执行业务逻辑，3秒后锁被自动释放。

index3获取到锁，执行业务逻辑

index1业务逻辑执行完成，开始调用del释放锁，这时释放的是index3的锁，导致index3的业务只执行1s就被别人释放。

最终等于没锁的情况。

解决：setnx获取锁时，设置一个指定的唯一值（例如：uuid）；释放前获取这个值，判断是否自己的锁

优化之UUID防误删

![图片](https://uploader.shimo.im/f/PwMFGgl4omMcDtnV.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

![图片](https://uploader.shimo.im/f/RAiITTMs5cvlf3DV.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

问题：删除操作缺乏原子性。

场景：

index1执行删除时，查询到的lock值确实和uuid相等

uuid=v1

set(lock,uuid)；

![图片](https://uploader.shimo.im/f/4At7lvlCsyvvHm8F.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

index1执行删除前，lock刚好过期时间已到，被redis自动释放

在redis中没有了lock，没有了锁。

![图片](https://uploader.shimo.im/f/lg4TZCgQbHkjER3G.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

index2获取了lock

index2线程获取到了cpu的资源，开始执行方法

uuid=v2

set(lock,uuid)；

index1执行删除，此时会把index2的lock删除

index1 因为已经在方法中了，所以不需要重新上锁。index1有执行的权限。index1已经比较完成了，这个时候，开始执行

![图片](https://uploader.shimo.im/f/lg4TZCgQbHkjER3G.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

删除的index2的锁！

优化之LUA脚本保证删除的原子性

|@GetMapping("testLockLua")<br/>public void testLockLua() {<br/>//1声明一个uuid ,将做为一个value放入我们的key所对应的值中<br/>String uuid = UUID.randomUUID().toString();<br/>//2定义一个锁：lua脚本可以使用同一把锁，来实现删除！<br/>String skuId = "25"; //访问skuId为25号的商品100008348542<br/>String locKey = "lock:" + skuId; //锁住的是每个商品的数据<br/><br/>// 3获取锁<br/>Boolean lock = redisTemplate.opsForValue().setIfAbsent(locKey, uuid, 3, TimeUnit.SECONDS);<br/><br/>//第一种：lock与过期时间中间不写任何的代码。<br/>// redisTemplate.expire("lock",10, TimeUnit.SECONDS);//设置过期时间<br/>//如果true<br/>if (lock) {<br/>//执行的业务逻辑开始<br/>//获取缓存中的num数据<br/>Object value = redisTemplate.opsForValue().get("num");<br/>//如果是空直接返回<br/>if (StringUtils.isEmpty(value)) {<br/>return;<br/>}<br/>//不是空如果说在这出现了异常！那么delete就删除失败！也就是说锁永远存在！<br/>int num = Integer.parseInt(value + "");<br/>//使num每次+1放入缓存<br/>redisTemplate.opsForValue().set("num", String.valueOf(++num));<br/>/*使用lua脚本来锁*/<br/>//定义lua脚本<br/>String script = "if redis.call('get', KEYS[1]) == ARGV[1] then returnredis.call('del', KEYS[1]) else return 0 end";<br/>//使用redis执行lua执行<br/>DefaultRedisScript<Long> redisScript = new DefaultRedisScript<>();<br/>redisScript.setScriptText(script);<br/>//设置一下返回值类型为Long<br/>//因为删除判断的时候，返回的0,给其封装为数据类型。如果不封装那么默认返回String类型，<br/>//那么返回字符串与0会有发生错误。<br/>redisScript.setResultType(Long.class);<br/>//第一个要是script脚本，第二个需要判断的key，第三个就是key所对应的值。<br/>redisTemplate.execute(redisScript, Arrays.asList(locKey), uuid);<br/>} else {<br/>//其他线程等待<br/>try {<br/>//睡眠<br/>Thread.sleep(1000);<br/>//睡醒了之后，调用方法。<br/>testLockLua();<br/>} catch (InterruptedException e) {<br/>e.printStackTrace();<br/>}<br/>}<br/>}|
|:----|
Lua 脚本详解：

![图片](https://uploader.shimo.im/f/p9iFrNBZptt9CaLZ.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

项目中正确使用：

|定义key，key应该是为每个sku定义的，也就是每个sku有一把锁。<br>String locKey ="lock:"+skuId; //锁住的是每个商品的数据<br>Boolean lock = redisTemplate.opsForValue().setIfAbsent(locKey, uuid,3,TimeUnit.SECONDS);<br>|
|:----|
|![图片](https://uploader.shimo.im/f/NHt1trbPbh32DzCI.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)|

总结

1、加锁

|// 1.从redis中获取锁,set k1 v1 px 20000 nx<br/>String uuid = UUID.randomUUID().toString();<br/>Boolean lock = this.redisTemplate.opsForValue()<br/>.setIfAbsent("lock", uuid, 2, TimeUnit.SECONDS);|
|:----|
使用lua释放锁

|// 2.释放锁del<br/>String script = "if redis.call('get', KEYS[1]) == ARGV[1] then return redis.call('del', KEYS[1]) else return 0 end";<br/>//设置lua脚本返回的数据类型<br/>DefaultRedisScript<Long> redisScript = new DefaultRedisScript<>();<br/>//设置lua脚本返回类型为Long<br/>redisScript.setResultType(Long.class);<br/>redisScript.setScriptText(script);<br/>redisTemplate.execute(redisScript, Arrays.asList("lock"),uuid);|
|:----|
重试

|Thread.sleep(500);<br/>testLock();|
|:----|
为了确保分布式锁可用，我们至少要确保锁的实现同时满足以下四个条件：

- 互斥性。在任意时刻，只有一个客户端能持有锁。

- 不会发生死锁。即使有一个客户端在持有锁的期间崩溃而没有主动解锁，也能保证后续其他客户端能加锁。

- 解铃还须系铃人。加锁和解锁必须是同一个客户端，客户端自己不能把别人加的锁给解了。

- 加锁和解锁必须具有原子性。

# 17.Redis6.0新功能

## 17.1ACL

简介

Redis ACL是Access Control List（访问控制列表）的缩写，该功能允许根据可以执行的命令和可以访问的键来限制某些连接。

在Redis 5版本之前，Redis 安全规则只有密码控制 还有通过rename 来调整高危命令比如 flushdb ， KEYS* ， shutdown 等。Redis 6 则提供ACL的功能对用户进行更细粒度的权限控制 ：

（1）接入权限:用户名和密码

（2）可以执行的命令

（3）可以操作的 KEY

参考官网：https://redis.io/topics/acl

命令

1、使用acl list命令展现用户权限列表

（1）数据说明

![图片](https://uploader.shimo.im/f/00QQS1qALG79l8So.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

2、使用acl cat命令

（1）查看添加权限指令类别

![图片](https://uploader.shimo.im/f/hNynKqtrmtUtkHMQ.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

（2）加参数类型名可以查看类型下具体命令

![图片](https://uploader.shimo.im/f/r6ClyQMfUqWiPtfW.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

3、使用acl whoami命令查看当前用户

![图片](https://uploader.shimo.im/f/Jy984sgUV9YfDfV0.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

4、使用aclsetuser命令创建和编辑用户ACL

（1）ACL规则

下面是有效ACL规则的列表。某些规则只是用于激活或删除标志，或对用户ACL执行给定更改的单个单词。其他规则是字符前缀，它们与命令或类别名称、键模式等连接在一起。

|ACL规则|    |    |
|:----|:----|:----|
|类型|参数|说明|
|启动和禁用用户|on|激活某用户账号|
|    |off|禁用某用户账号。注意，已验证的连接仍然可以工作。如果默认用户被标记为off，则新连接将在未进行身份验证的情况下启动，并要求用户使用AUTH选项发送AUTH或HELLO，以便以某种方式进行身份验证。|
|权限的添加删除|+<command>|将指令添加到用户可以调用的指令列表中|
|    |-<command>|从用户可执行指令列表移除指令|
|    |+@<category>|添加该类别中用户要调用的所有指令，有效类别为@admin、@set、@sortedset…等，通过调用ACL CAT命令查看完整列表。特殊类别@all表示所有命令，包括当前存在于服务器中的命令，以及将来将通过模块加载的命令。|
|    |-@<actegory>|从用户可调用指令中移除类别|
|    |allcommands|+@all的别名|
|    |nocommand|-@all的别名|
|可操作键的添加或删除|~<pattern>|添加可作为用户可操作的键的模式。例如~*允许所有的键|

（2）通过命令创建新用户默认权限

acl setuser user1

![图片](https://uploader.shimo.im/f/hL8XFpYjwzlbfQOs.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

在上面的示例中，我根本没有指定任何规则。如果用户不存在，这将使用just created的默认属性来创建用户。如果用户已经存在，则上面的命令将不执行任何操作。

（3）设置有用户名、密码、ACL权限、并启用的用户

acl setuser user2 on >password ~cached:* +get

![图片](https://uploader.shimo.im/f/YYqII6LkwwCh0wz7.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

(4)切换用户，验证权限

![图片](https://uploader.shimo.im/f/W631IQ1JBKiXceLr.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

## 17.2IO多线程

简介

Redis6终于支撑多线程了，告别单线程了吗？

IO多线程其实指客户端交互部分的网络IO交互处理模块多线程，而非执行命令多线程。Redis6执行命令依然是单线程。

原理架构

Redis 6 加入多线程,但跟 Memcached 这种从 IO处理到数据访问多线程的实现模式有些差异。Redis 的多线程部分只是用来处理网络数据的读写和协议解析，执行命令仍然是单线程。之所以这么设计是不想因为多线程而变得复杂，需要去控制 key、lua、事务，LPUSH/LPOP 等等的并发问题。整体的设计大体如下:

![图片](https://uploader.shimo.im/f/5Hbd0uNkaAMwC6W7.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

另外，多线程IO默认也是不开启的，需要再配置文件中配置

io-threads-do-reads  yes

io-threads 4

工具支持 Cluster

之前老版Redis想要搭集群需要单独安装ruby环境，Redis 5 将 redis-trib.rb 的功能集成到 redis-cli 。另外官方 redis-benchmark 工具开始支持 cluster 模式了，通过多线程的方式对多个分片进行压测。

![图片](https://uploader.shimo.im/f/hAZIdX2JJIoQhyS6.png!thumbnail?fileGuid=yBtmdjz5Ja42cjcb)

Redis新功能持续关注

Redis6新功能还有：

1、RESP3新的 Redis 通信协议：优化服务端与客户端之间通信

2、Client side caching客户端缓存：基于 RESP3 协议实现的客户端缓存功能。为了进一步提升缓存的性能，将客户端经常访问的数据cache到客户端。减少TCP网络交互。

3、Proxy集群代理模式：Proxy 功能，让 Cluster 拥有像单实例一样的接入方式，降低大家使用cluster的门槛。不过需要注意的是代理不改变 Cluster 的功能限制，不支持的命令还是不会支持，比如跨 slot 的多Key操作。

4、Modules API

Redis 6中模块API开发进展非常大，因为Redis Labs为了开发复杂的功能，从一开始就用上Redis模块。Redis可以变成一个框架，利用Modules来构建不同系统，而不需要从头开始写然后还要BSD许可。Redis一开始就是一个向编写各种系统开放的平台。

