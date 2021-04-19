# 1.Mysql架构介绍

## 1.1Mysql存储引擎

* InnoDB 支持行锁,事务
* MyISAM 支持表锁,不支持事务
* Memory 默认使用hash索引,比b+tree效力高

可通过show engines;命令来查看

![图片](https://uploader.shimo.im/f/3OP5cW2N0Yn82D0j.png!thumbnail?fileGuid=YXxCjPwcrdWkr9dt)

## 1.2Mysql逻辑架构

![图片](https://uploader.shimo.im/f/q69tQqiPQIBts4yE.png!thumbnail?fileGuid=YXxCjPwcrdWkr9dt)

## 1.3Mysql物理文件体系结构

![图片](https://uploader.shimo.im/f/XY01N77sA6c0ZjnN.png!thumbnail?fileGuid=YXxCjPwcrdWkr9dt)

### （1）binlog二进制日志文件

不管使用的是哪一种存储引擎，都会产生binlog。如果开启了binlog二进制日志，就会有若干个二进制日志文件，如mysql-bin.000001、mysql-bin.000002、mysql-bin.00003等。binlog记录了MySQL对数据库执行更改的所有操作。查看当前binlog文件列表，如图1-7所示。

```sql
show master logs ;
```
。从MySQL 5.1版本开始，MySQL引入了binlog_format参数。这个参数有可选值statement和row：statement就是之前的格式，基于SQL语句来记录；row记录的则是行的更改情况，可以避免之前提到的数据不一致的问题。做MySQL主从复制，statement格式的binlog可能会导致主备不一致，所以要使用row格式。我们还需要借助mysqlbinlog工具来解析和查看binlog中的内容。如果需要用binlog来恢复数据，标准做法是用mysqlbinlog工具把binlog中的内容解析出来，然后把解析结果整个发给MySQL执行。

### （2）redo重做日志文件

ib_logfile0、ib_logfile1是InnoDB引擎特有的、用于记录InnoDB引擎下事务的日志，它记录每页更改的物理情况。首先要搞明白的是已经有binlog了为什么还需要redo log，因为两者分工不同。binlog主要用来做数据归档，但是并不具备崩溃恢复的能力，也就是说如果系统突然崩溃，重启后可能会有部分数据丢失。Innodb将所有对页面的修改操作写入一个专门的文件，并在数据库启动时从此文件进行恢复操作，这个文件就是redo log file。redo log的存在可以完美解决这个问题。默认情况下，每个InnoDB引擎至少有一个重做日志组，每组下至少有两个重做日志文件，例如前面提到的ib_logfile0和ib_logfile1。重做日志组中的每个日志文件大小一致且循环写入，也就是说先写iblogfile0，写满了之后写iblogfile1，一旦iblogfile1写满了，就继续写iblogfile0。

### （3）共享表空间和独立表空间

在MySQL 5.6.6之前的版本中，InnoDB默认会将所有的数据库InnoDB引擎的表数据存储在一个共享表空间ibdata1中，这样管理起来很困难，增删数据库的时候，ibdata1文件不会自动收缩，单个数据库的备份也将成为问题。为了优化上述问题，在MySQL 5.6.6之后的版本中，独立表空间innodb_file_per_table参数默认开启，每个数据库的每个表都有自已独立的表空间，每个表的数据和索引都会存在自己的表空间中。即便是innnodb表指定为独立表空间，用户自定义数据库中的某些元数据信息、回滚（undo）信息、插入缓冲（change buffer）、二次写缓冲（double write buffer）等还是存放在共享表空间，所以又称为系统表空间。

```sql
show variables like 'innodb_data%';
```
![图片](https://uploader.shimo.im/f/u7Hyw2DUgNZrb2iJ.png!thumbnail?fileGuid=YXxCjPwcrdWkr9dt)

### （4）undo log

undo log是回滚日志，如果事务回滚，则需要依赖undo日志进行回滚操作。MySQL在进行事务操作的同时，会记录事务性操作修改数据之前的信息，就是undo日志，确保可以回滚到事务发生之前的状态。innodb的undo log存放在ibdata1共享表空间中，当开启事务后，事务所使用的undo log会存放在ibdata1中，即使这个事务被关闭，undo log也会一直占用空间。

```sql
show variables like '%undo%';
```
![图片](https://uploader.shimo.im/f/DGkHNWu4YhFdamkn.png!thumbnail?fileGuid=YXxCjPwcrdWkr9dt)


### （5）临时表空间

存储临时对象的空间，比如临时表对象等。如图1-10所示，参数innodb_temp_data_file_path可以看到临时表空间的信息，上限设置为5GB。

```sql
show variables like 'innodb_temp_data_file_path%';
```
![图片](https://uploader.shimo.im/f/LdGUH6FhrBX7riVD.png!thumbnail?fileGuid=YXxCjPwcrdWkr9dt)

### （6）error log

错误日志记录了MySQL Server每次启动和关闭的详细信息，以及运行过程中所有较为严重的警告和错误信息。

```plain
show variables like 'log_error%';
```
![图片](https://uploader.shimo.im/f/9LSSWqjnPsANgR1E.png!thumbnail?fileGuid=YXxCjPwcrdWkr9dt)

### （7）slow.log

如果配置了MySQL的慢查询日志，MySQL就会将运行过程中的慢查询日志记录到slow_log文件中。MySQL的慢查询日志是MySQL提供的一种日志记录，用来记录在MySQL中响应时间超过阀值的语句，具体指运行时间超过long_query_time值的SQL。

```plain
show variables like '%slow_query_log%';
```
![图片](https://uploader.shimo.im/f/1NAenI9sjq9Kzjlw.png!thumbnail?fileGuid=YXxCjPwcrdWkr9dt)

例如，mysqldumpslow -s r -t 20 /database/mysql/slow-log表示为得到返回记录集最多的20个查询；mysqldumpslow -s t -t 20 -g "left join" /database/mysql/slow-log表示得到按照时间排序的前20条里面含有左连接的查询语句。

# 2.InnoDB存储引擎体系结构

![图片](https://uploader.shimo.im/f/J6DLDEC81lyEwYKx.png!thumbnail?fileGuid=YXxCjPwcrdWkr9dt)

## 2.1缓冲池

InnoDB存储引擎是基于磁盘存储的。由于CPU速度和磁盘速度之间的鸿沟，InnoDB引擎使用缓冲池技术来提高数据库的整体性能。

```sql
show variables like 'innodb_buffer_pool_size%';
```
![图片](https://uploader.shimo.im/f/4PVQJgcMmnOFiucj.png!thumbnail?fileGuid=YXxCjPwcrdWkr9dt)

，这个对InnoDB整体性能影响也最大，一般可以设置为50%到80%的内存大小。在专用数据库服务器上，可以将缓冲池大小设置得大些，多次访问相同的数据表数据所需要的磁盘I/O就更少

InnoDB存储引擎的缓存机制和MyISAM的最大区别就在于InnoDB缓冲池不仅仅缓存索引，还会缓存实际的数据。

InnoDB存储引擎的LRU列表中还加入了midpoint位置，即新读取的页并不是直接放到LRU列表首部，而是放到LRU列表的midpoint位置。这个算法在InnoDB存储引擎下称为midpoint insertion strategy，默认该位置在LRU列表长度的5/8处。midpoint的位置可由参数innodb_old_blocks_pct控制

```sql
show variables like 'innodb_old_blocks_pct%';
```
![图片](https://uploader.shimo.im/f/C8aF6EUfrToCIBOU.png!thumbnail?fileGuid=YXxCjPwcrdWkr9dt)

在图中，innodb_old_blocks_pct默认值为37，表示新读取的页插入到LRU列表尾端37%的位置

MySQL 5.6之后的版本提供了一个新特性来快速预热buffer_pool缓冲池，如图2-4所示。参数innodb_buffer_pool_dump_at_shutdown=ON表示在关闭MySQL时会把内存中的热数据保存在磁盘里ib_buffer_pool文件中，其保存比率由参数innodb_buffer_pool_dump_pct控制，默认为25%。

## 2.2change buffer

change buffer的主要目的是将对二级索引的数据操作缓存下来，以减少二级索引的随机I/O，并达到操作合并的效果。

## 2.3自适应哈希索引

InnoDB存储引擎会监控对表上二级索引的查找。如果发现某二级索引被频繁访问，二级索引就成为热数据；如果观察到建立哈希索引可以带来速度的提升，则建立哈希索引，所以称之为自适应（adaptive）的，即自适应哈希索引（Adaptive Hash Index，AHI）。

## 2.4redo log buffer

redo log buffer是一块内存区域，存放将要写入redo log文件的数据

## 2.5double write

double write（两次写）技术的引入是为了提高数据写入的可靠性

## 2.6InnoDB后台线程

### 2.6.1InnoDB后台主线程

master thread是InnoD存储引擎非常核心的一个在后台运行的主线程，相当重要。它做的主要工作包括但不限于：将缓冲池中的数据异步刷新到磁盘，保证数据的一致性，包括脏页的刷新、合并插入缓冲等。

### 2.6.2InnoDB后台I/O线程

在InnoDB存储引擎中大量使用了AIO（Async I/O）来处理写I/O请求，这样可以极大地提高数据库的性能。I/O线程的工作主要是负责这些I/O请求的回调（call back）处理。InnoDB 1.0版本之前共有4个I/O线程，分别是write、read、insert buffer和log I/O thread。在Linux平台下，I/O线程的数量不能进行调整，但是在Windows平台下可以通过参数innodb_file_io_threads来增大I/O线程。从InnoDB 1.0.x版本开始，read thread和write thread分别增大到了4个，并且不再使用innodb_file_io_threads参数，而是分别使用innodb_read_io_threads和innodb_write_io_threads参数进行设置，如此调整后，在Linux平台上就可以根据CPU核数来更改相应的参数值了。

### 2.6.3InnoDB脏页刷新线程

MySQL 5.6版本以前，脏页的清理工作交由master线程处理。page cleaner thread是5.6.2版本引入的一个新线程，实现从master线程中卸下缓冲池刷脏页的工作。为了进一步提升扩展性和刷脏效率，在5.7.4版本里引入了多个page cleaner线程，从而达到并行刷脏的效果。

### 2.6.4InnoDB purge线程

purge thread负责回收已经使用并分配的undo页

## 2.7redo log

明确一下InnoDB修改数据的基本流程。当我们想要修改DB上某一行数据的时候，InnoDB是把数据从磁盘读取到内存的缓冲池上进行修改。这个时候数据在内存中被修改，与磁盘中相比就存在了差异，我们称这种有差异的数据为脏页。InnoDB对脏页的处理不是每次生成脏页就将脏页刷新回磁盘，因为这样会产生海量的I/O操作，严重影响InnoDB的处理性能。既然脏页与磁盘中的数据存在差异，那么如果在此期间DB出现故障就会造成数据丢失。为了解决这个问题，redo log就应运而生了。

## 2.8undo log

undo log是InnoDB MVCC事务特性的重要组成部分。当我们对记录做了变更操作时就会产生undo记录，undo记录默认被记录到系统表空间（ibdata）中，但从MySQL 5.6开始，也可以使用独立的undo表空间。

## 2.9Query Cache

# 3.Mysql事务和锁

## 3.1事务隔离级别

数据库提供了4种隔离级别，由低到高依次为read uncommitted（读未提交）、read committed（读已提交）、repeatable read（可重复读取）、serializable（序列化）。

## 3.2InoDB锁机制介绍

锁机制是事务型数据库中为了保证数据的一致性手段

InnoDB主要使用两种级别的锁机制： 行级锁和表级锁。

InnoDB的行级锁类型主要有共享（S）锁（又称读锁）、排他（X）锁（又称写锁）。共享（S）锁允许持有该锁的事务读取行；排他（X）锁允许持有该锁的事务更新或删除行。

## 3.3锁等待和死锁

### 3.3.1锁等待

锁等待是指一个事务过程中产生的锁，其他事务需要等待上一个事务释放它的锁才能占用该资源。如果该事务一直不释放，就需要持续等待下去，直到超过了锁等待时间，会报一个等待超时的错误。在MySQL中通过innodb_lock_wait_timeout参数来控制锁等待时间，单位是秒。如图3-9所示，可以通过语句show variables like '%innodb_lock_wait%'来查看锁等待超时时间

![图片](https://uploader.shimo.im/f/9TZNkRWsH5W5DP7Z.png!thumbnail?fileGuid=YXxCjPwcrdWkr9dt)

当MySQL发生锁等待情况时，可以通过语句select * from sys.innodb_lock_waits来在线查看，

![图片](https://uploader.shimo.im/f/SxVeG6v1T54pHJLJ.png!thumbnail?fileGuid=YXxCjPwcrdWkr9dt)

### 3.3.2死锁

在MySQL中，两个或两个以上的事务相互持有和请求锁，并形成一个循环的依赖关系，就会产生死锁，也就是锁资源请求产生了死循环现象。InnoDB会自动检测事务死锁，立即回滚其中某个事务，并且返回一个错误。它根据某种机制来选择那个最简单（代价最小）的事务来进行回滚。常见的死锁会报错“ERROR 1213 (40001):deadlock found when trying to get lock; try restarting transaction.”。偶然发生的死锁不必担心，InnoDB存储引擎有一个后台的锁监控线程，该线程负责查看可能的死锁问题，并自动告知用户。

## 3.5锁问题的监控

我们通过show full processlist是为了查看当前MySQL是否有压力、都在跑什么语句、当前语句耗时多久了，从中可以看到总共有多少链接数、哪些线程有问题，然后把有问题的线程kill掉，临时解决一些突发性的问题。

![图片](https://uploader.shimo.im/f/MNGXYWroH3oCL8ha.png!thumbnail?fileGuid=YXxCjPwcrdWkr9dt)

通过执行show engine innodb status命令来查看是否存在锁表情况，如

![图片](https://uploader.shimo.im/f/SK2aHBjdUpClkRgX.png!thumbnail?fileGuid=YXxCjPwcrdWkr9dt)


MySQL将事务和锁信息记录在了information_schema数据库中，我们只需要查询即可。涉及的表主要有3个，即innodb_trx、innodb_locks、innodb_lock_waits，

```sql
select *
from information_schema.INNODB_TRX;

select *
from information_schema.INNODB_LOCKS;
select *
from information_schema.INNODB_LOCK_WAITS;
```
# 4.Mysql服务器全面优化

![图片](https://uploader.shimo.im/f/62gay6IPGnVzTPkD.png!thumbnail?fileGuid=YXxCjPwcrdWkr9dt)

## 4.1Mysql 5.7 InnoDB存储引擎增强特性

* Online Alter Table以及索引
* innodb_buffer_pool online change
* innodb_buffer_pool_dump和load的增强
* InnoDB临时表优化
* page clean的效率提升
* undo log自动清除
## 4.2硬件层面优化

（1）使用SSD或者PCIe SSD设备，至少获得数百倍甚至万倍的IOPS提升。

## 4.3Linux操作系统层面优化

文件系统选择推荐使用xfs

## 4.4Mysql配置参数优化

## 4.5设计规范

* 库表的设计规范
* 索引设计规范
* sql语句的规范
