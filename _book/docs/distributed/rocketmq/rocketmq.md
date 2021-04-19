# 1.概述

## 1.1 简介

## 1.2 特点

## 1.3 场景

1. 解耦
2. 异步
3. 肖锋
## 1.4 架构

![图片](https://uploader.shimo.im/f/clfb0A6WUWCtYOvb.png!thumbnail?fileGuid=dvytdvPPr9YTkRdX)

## 1.5 工作原理

![图片](https://uploader.shimo.im/f/078TrizMMfMK3OSu.png!thumbnail?fileGuid=dvytdvPPr9YTkRdX)

# 2.环境搭建

## 2.1 版本

|**组件**|**版本**|**备注**|
|:----:|:----|:----:|:----|:----:|:----|
|centos|64 位|7.x 以上|
|jdk|1.8|    |
|rocketmq|4.x||

## 2.2 主机规划

|**ip**|**host**|**安装软件**|
|:----|:----|:----|:----|:----|:----|
|192.168.62.130|hadoop102|rocketmq  jdk|
|192.168.62.131|hadoop103|rocketmq  jdk|

## 2.3 下载

```plain
https://github.com/apache/rocketmq/
```
## 2.4 单机启动

### 2.4.1启动NameServer

```plain
# 1.启动NameServer
nohup sh bin/mqnamesrv &
# 2.查看启动日志
tail -f ~/logs/rocketmqlogs/namesrv.log
```
### 2.4.2启动Broker

```plain
# 1.启动Broker
nohup sh bin/mqbroker -n localhost:9876 &
# 2.查看启动日志
tail -f ~/logs/rocketmqlogs/broker.log 
```
问题描述：
* RocketMQ默认的虚拟机内存较大，启动Broker如果因为内存不足失败，需要编辑如下两个配置文件，修改JVM内存大小
```plain
# 编辑runbroker.sh和runserver.sh修改默认JVM大小
vi runbroker.sh
vi runserver.sh
```
* 参考设置：

`JAVA_OPT="${JAVA_OPT} -server -Xms256m -Xmx256m -Xmn128m -XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=320m"`

### 2.4.3测试RocketMQ

发送消息

```plain
# 1.设置环境变量
export NAMESRV_ADDR=localhost:9876
# 2.使用安装包的Demo发送消息
sh bin/tools.sh org.apache.rocketmq.example.quickstart.Producer
```
接收消息
```plain
# 1.设置环境变量
export NAMESRV_ADDR=localhost:9876
# 2.接收消息
sh bin/tools.sh org.apache.rocketmq.example.quickstart.Consumer
```
关闭RocketMQ
```plain
# 1.关闭NameServer
sh bin/mqshutdown namesrv
# 2.关闭Broker
sh bin/mqshutdown broker
```
## 2.5集群部署

### 2.5.1服务器环境

|序号|IP|角色|架构模式|
|:----|:----|:----|:----|
|1|192.168.62.130|nameserver、brokerserver|Master1、Slave2|
|2|192.168.62.131|nameserver、brokerserver|Master2、Slave1|

### 2.5.2 Host添加信息

```plain
vim /etc/hosts
```
配置如下:
```plain
# nameserver
192.168.62.130 rocketmq-nameserver1
192.168.62.131 rocketmq-nameserver2
# broker
192.168.62.130 rocketmq-master1
192.168.62.130 rocketmq-slave2
192.168.62.131 rocketmq-master2
192.168.62.131 rocketmq-slave1
```
```plain
# nameserver
sudo echo '192.168.62.130 rocketmq-nameserver1' >>/etc/hosts
echo '192.168.62.131 rocketmq-nameserver2' >>/etc/hosts
# broker
echo '192.168.62.130 rocketmq-master1' >>/etc/hosts
echo '192.168.62.130 rocketmq-slave2' >>/etc/hosts
echo '192.168.62.131 rocketmq-master2' >>/etc/hosts
echo '192.168.62.131 rocketmq-slave1' >>/etc/hosts
```
配置完成后, 重启网卡
```plain
systemctl restart network
```
### 2.5.3 防火墙配置

宿主机需要远程访问虚拟机的rocketmq服务和web服务，需要开放相关的端口号，简单粗暴的方式是直接关闭防火墙

```plain
# 关闭防火墙
systemctl stop firewalld.service 
# 查看防火墙的状态
firewall-cmd --state 
# 禁止firewall开机启动
systemctl disable firewalld.service
```
或者为了安全，只开放特定的端口号，RocketMQ默认使用3个端口：9876 、10911 、11011 。如果防火墙没有关闭的话，那么防火墙就必须开放这些端口：
* `nameserver`默认使用 9876 端口
* `master`默认使用 10911 端口
* `slave`默认使用11011 端口

执行以下命令：

```plain
# 开放name server默认端口
firewall-cmd --remove-port=9876/tcp --permanent
# 开放master默认端口
firewall-cmd --remove-port=10911/tcp --permanent
# 开放slave默认端口 (当前集群模式可不开启)
firewall-cmd --remove-port=11011/tcp --permanent 
# 重启防火墙
firewall-cmd --reload
```
### 2.5.4环境变量配置

```plain
vim /etc/profile
```
在profile文件的末尾加入如下命令
```plain
#set rocketmq
ROCKETMQ_HOME=/usr/local/software/rocketmq
PATH=$PATH:$ROCKETMQ_HOME/bin
export ROCKETMQ_HOME PATH
```
```plain
#set rocketmq
echo 'ROCKETMQ_HOME=/usr/local/rocketmq/rocketmq-all-4.4.0-bin-release' >>/etc/profile
echo 'PATH=$PATH:$ROCKETMQ_HOME/bin' >>/etc/profile
echo 'export ROCKETMQ_HOME PATH' >>/etc/profile
```
输入:wq! 保存并退出， 并使得配置立刻生效：
```plain
source /etc/profile
```
### 2.5.5 创建消息存储路径

```plain
sudo mkdir -p /usr/local/rocketmq/store
sudo mkdir -p /usr/local/rocketmq/store/commitlog
sudo mkdir -p /usr/local/rocketmq/store/consumequeue
sudo mkdir -p /usr/local/rocketmq/store/index
sudo mkdir -p /usr/local/rocketmqs/store
sudo mkdir -p /usr/local/rocketmqs/store/commitlog
sudo mkdir -p /usr/local/rocketmqs/store/consumequeue
sudo mkdir -p /usr/local/rocketmqs/store/index
```
### 2.5.6broker配置文件

#### 1）master1

服务器：192.168.62.130

```plain
vim /usr/local/software/rocketmq/conf/2m-2s-sync/broker-a.properties
```
修改配置如下：
```plain
#所属集群名字
brokerClusterName=rocketmq-cluster
#broker名字，注意此处不同的配置文件填写的不一样
brokerName=broker-a
#0 表示 Master，>0 表示 Slave
brokerId=0
#nameServer地址，分号分割
namesrvAddr=rocketmq-nameserver1:9876;rocketmq-nameserver2:9876
#在发送消息时，自动创建服务器不存在的topic，默认创建的队列数
defaultTopicQueueNums=4
#是否允许 Broker 自动创建Topic，建议线下开启，线上关闭
autoCreateTopicEnable=true
#是否允许 Broker 自动创建订阅组，建议线下开启，线上关闭
autoCreateSubscriptionGroup=true
#Broker 对外服务的监听端口
listenPort=10911
#删除文件时间点，默认凌晨 4点
deleteWhen=04
#文件保留时间，默认 48 小时
fileReservedTime=120
#commitLog每个文件的大小默认1G
mapedFileSizeCommitLog=1073741824
#ConsumeQueue每个文件默认存30W条，根据业务情况调整
mapedFileSizeConsumeQueue=300000
#destroyMapedFileIntervalForcibly=120000
#redeleteHangedFileInterval=120000
#检测物理文件磁盘空间
diskMaxUsedSpaceRatio=88
#存储路径
storePathRootDir=/usr/local/rocketmq/store
#commitLog 存储路径
storePathCommitLog=/usr/local/rocketmq/store/commitlog
#消费队列存储路径存储路径
storePathConsumeQueue=/usr/local/rocketmq/store/consumequeue
#消息索引存储路径
storePathIndex=/usr/local/rocketmq/store/index
#checkpoint 文件存储路径
storeCheckpoint=/usr/local/rocketmq/store/checkpoint
#abort 文件存储路径
abortFile=/usr/local/rocketmq/store/abort
#限制的消息大小
maxMessageSize=65536
#flushCommitLogLeastPages=4
#flushConsumeQueueLeastPages=2
#flushCommitLogThoroughInterval=10000
#flushConsumeQueueThoroughInterval=60000
#Broker 的角色
#- ASYNC_MASTER 异步复制Master
#- SYNC_MASTER 同步双写Master
#- SLAVE
brokerRole=SYNC_MASTER
#刷盘方式
#- ASYNC_FLUSH 异步刷盘
#- SYNC_FLUSH 同步刷盘
flushDiskType=SYNC_FLUSH
#checkTransactionMessageEnable=false
#发消息线程池数量
#sendMessageThreadPoolNums=128
#拉消息线程池数量
#pullMessageThreadPoolNums=128
```
#### 
#### 2）slave2

服务器：192.168.62.130

```plain
vim /usr/local/software/rocketmq/conf/2m-2s-sync/broker-b-s.properties
```
修改配置如下：
```plain
#所属集群名字
brokerClusterName=rocketmq-cluster
#broker名字，注意此处不同的配置文件填写的不一样
brokerName=broker-b
#0 表示 Master，>0 表示 Slave
brokerId=1
#nameServer地址，分号分割
namesrvAddr=rocketmq-nameserver1:9876;rocketmq-nameserver2:9876
#在发送消息时，自动创建服务器不存在的topic，默认创建的队列数
defaultTopicQueueNums=4
#是否允许 Broker 自动创建Topic，建议线下开启，线上关闭
autoCreateTopicEnable=true
#是否允许 Broker 自动创建订阅组，建议线下开启，线上关闭
autoCreateSubscriptionGroup=true
#Broker 对外服务的监听端口
listenPort=11011
#删除文件时间点，默认凌晨 4点
deleteWhen=04
#文件保留时间，默认 48 小时
fileReservedTime=120
#commitLog每个文件的大小默认1G
mapedFileSizeCommitLog=1073741824
#ConsumeQueue每个文件默认存30W条，根据业务情况调整
mapedFileSizeConsumeQueue=300000
#destroyMapedFileIntervalForcibly=120000
#redeleteHangedFileInterval=120000
#检测物理文件磁盘空间
diskMaxUsedSpaceRatio=88
#存储路径
storePathRootDir=/usr/local/rocketmqs/store
#commitLog 存储路径
storePathCommitLog=/usr/local/rocketmqs/store/commitlog
#消费队列存储路径存储路径
storePathConsumeQueue=/usr/local/rocketmqs/store/consumequeue
#消息索引存储路径
storePathIndex=/usr/local/rocketmqs/store/index
#checkpoint 文件存储路径
storeCheckpoint=/usr/local/rocketmqs/store/checkpoint
#abort 文件存储路径
abortFile=/usr/local/rocketmqs/store/abort
#限制的消息大小
maxMessageSize=65536
#flushCommitLogLeastPages=4
#flushConsumeQueueLeastPages=2
#flushCommitLogThoroughInterval=10000
#flushConsumeQueueThoroughInterval=60000
#Broker 的角色
#- ASYNC_MASTER 异步复制Master
#- SYNC_MASTER 同步双写Master
#- SLAVE
brokerRole=SLAVE
#刷盘方式
#- ASYNC_FLUSH 异步刷盘
#- SYNC_FLUSH 同步刷盘
flushDiskType=ASYNC_FLUSH
#checkTransactionMessageEnable=false
#发消息线程池数量
#sendMessageThreadPoolNums=128
#拉消息线程池数量
#pullMessageThreadPoolNums=128
```
#### 
#### 3）master2

服务器：192.168.62.131

```plain
vim /usr/local/software/rocketmq/conf/2m-2s-sync/broker-b.properties
```
修改配置如下：
```plain
#所属集群名字
brokerClusterName=rocketmq-cluster
#broker名字，注意此处不同的配置文件填写的不一样
brokerName=broker-b
#0 表示 Master，>0 表示 Slave
brokerId=0
#nameServer地址，分号分割
namesrvAddr=rocketmq-nameserver1:9876;rocketmq-nameserver2:9876
#在发送消息时，自动创建服务器不存在的topic，默认创建的队列数
defaultTopicQueueNums=4
#是否允许 Broker 自动创建Topic，建议线下开启，线上关闭
autoCreateTopicEnable=true
#是否允许 Broker 自动创建订阅组，建议线下开启，线上关闭
autoCreateSubscriptionGroup=true
#Broker 对外服务的监听端口
listenPort=10911
#删除文件时间点，默认凌晨 4点
deleteWhen=04
#文件保留时间，默认 48 小时
fileReservedTime=120
#commitLog每个文件的大小默认1G
mapedFileSizeCommitLog=1073741824
#ConsumeQueue每个文件默认存30W条，根据业务情况调整
mapedFileSizeConsumeQueue=300000
#destroyMapedFileIntervalForcibly=120000
#redeleteHangedFileInterval=120000
#检测物理文件磁盘空间
diskMaxUsedSpaceRatio=88
#存储路径
storePathRootDir=/usr/local/rocketmq/store
#commitLog 存储路径
storePathCommitLog=/usr/local/rocketmq/store/commitlog
#消费队列存储路径存储路径
storePathConsumeQueue=/usr/local/rocketmq/store/consumequeue
#消息索引存储路径
storePathIndex=/usr/local/rocketmq/store/index
#checkpoint 文件存储路径
storeCheckpoint=/usr/local/rocketmq/store/checkpoint
#abort 文件存储路径
abortFile=/usr/local/rocketmq/store/abort
#限制的消息大小
maxMessageSize=65536
#flushCommitLogLeastPages=4
#flushConsumeQueueLeastPages=2
#flushCommitLogThoroughInterval=10000
#flushConsumeQueueThoroughInterval=60000
#Broker 的角色
#- ASYNC_MASTER 异步复制Master
#- SYNC_MASTER 同步双写Master
#- SLAVE
brokerRole=SYNC_MASTER
#刷盘方式
#- ASYNC_FLUSH 异步刷盘
#- SYNC_FLUSH 同步刷盘
flushDiskType=SYNC_FLUSH
#checkTransactionMessageEnable=false
#发消息线程池数量
#sendMessageThreadPoolNums=128
#拉消息线程池数量
#pullMessageThreadPoolNums=128
```
#### 4）slave1

服务器：192.168.62.131

```plain
vim /usr/local/software/rocketmq/conf/2m-2s-sync/broker-a-s.properties
```
修改配置如下：
```plain
#所属集群名字
brokerClusterName=rocketmq-cluster
#broker名字，注意此处不同的配置文件填写的不一样
brokerName=broker-a
#0 表示 Master，>0 表示 Slave
brokerId=1
#nameServer地址，分号分割
namesrvAddr=rocketmq-nameserver1:9876;rocketmq-nameserver2:9876
#在发送消息时，自动创建服务器不存在的topic，默认创建的队列数
defaultTopicQueueNums=4
#是否允许 Broker 自动创建Topic，建议线下开启，线上关闭
autoCreateTopicEnable=true
#是否允许 Broker 自动创建订阅组，建议线下开启，线上关闭
autoCreateSubscriptionGroup=true
#Broker 对外服务的监听端口
listenPort=11011
#删除文件时间点，默认凌晨 4点
deleteWhen=04
#文件保留时间，默认 48 小时
fileReservedTime=120
#commitLog每个文件的大小默认1G
mapedFileSizeCommitLog=1073741824
#ConsumeQueue每个文件默认存30W条，根据业务情况调整
mapedFileSizeConsumeQueue=300000
#destroyMapedFileIntervalForcibly=120000
#redeleteHangedFileInterval=120000
#检测物理文件磁盘空间
diskMaxUsedSpaceRatio=88
#存储路径
storePathRootDir=/usr/local/rocketmqs/store
#commitLog 存储路径
storePathCommitLog=/usr/local/rocketmqs/store/commitlog
#消费队列存储路径存储路径
storePathConsumeQueue=/usr/local/rocketmqs/store/consumequeue
#消息索引存储路径
storePathIndex=/usr/local/rocketmqs/store/index
#checkpoint 文件存储路径
storeCheckpoint=/usr/local/rocketmqs/store/checkpoint
#abort 文件存储路径
abortFile=/usr/local/rocketmqs/store/abort
#限制的消息大小
maxMessageSize=65536
#flushCommitLogLeastPages=4
#flushConsumeQueueLeastPages=2
#flushCommitLogThoroughInterval=10000
#flushConsumeQueueThoroughInterval=60000
#Broker 的角色
#- ASYNC_MASTER 异步复制Master
#- SYNC_MASTER 同步双写Master
#- SLAVE
brokerRole=SLAVE
#刷盘方式
#- ASYNC_FLUSH 异步刷盘
#- SYNC_FLUSH 同步刷盘
flushDiskType=ASYNC_FLUSH
#checkTransactionMessageEnable=false
#发消息线程池数量
#sendMessageThreadPoolNums=128
#拉消息线程池数量
#pullMessageThreadPoolNums=128
```
### 
### 2.5.7 修改启动脚本文件

#### 1）runbroker.sh

```plain
vim /usr/local/software/rocketmq/bin/runbroker.sh
```
需要根据内存大小进行适当的对JVM参数进行调整：
```plain
#===================================================
# 开发环境配置 JVM Configuration
JAVA_OPT="${JAVA_OPT} -server -Xms256m -Xmx256m -Xmn128m"
```
####2）runserver.sh
```plain
vim /usr/local/software/rocketmq/bin/runserver.sh
JAVA_OPT="${JAVA_OPT} -server -Xms256m -Xmx256m -Xmn128m -XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=320m"
```
### 2.5.8 服务启动

#### 1）启动NameServe集群

分别在192.168.25.135和192.168.25.138启动NameServer

```plain
cd /usr/local/software/rocketmq/bin
nohup sh mqnamesrv &
```
#### 2）启动Broker集群

* 在192.168.62.130上启动master1和slave2

master1：

```plain
cd /usr/local/software/rocketmq/bin
nohup sh mqbroker -c /usr/local/software/rocketmq/conf/2m-2s-sync/broker-a.properties &
```
slave2：
```plain
cd /usr/local/software/rocketmq/bin
nohup sh mqbroker -c /usr/local/software/rocketmq/conf/2m-2s-sync/broker-b-s.properties &
```
* 在192.168.62.131上启动master2和slave2

master2

```plain
cd /usr/local/software/rocketmq/bin
nohup sh mqbroker -c /usr/local/software/rocketmq/conf/2m-2s-sync/broker-b.properties &
```
slave1
```plain
/usr/local/software/rocketmq/bin
nohup sh mqbroker -c /usr/local/software/rocketmq/conf/2m-2s-sync/broker-a-s.properties &
```
### 
### 2.5.9 查看进程状态

启动后通过JPS查看启动进程

![图片](https://uploader.shimo.im/f/hbKFvOLlYPY4tboG.png!thumbnail?fileGuid=dvytdvPPr9YTkRdX)

### 2.5.10 查看日志

```plain
# 查看nameServer日志
tail -500f ~/logs/rocketmqlogs/namesrv.log
# 查看broker日志
tail -500f ~/logs/rocketmqlogs/broker.log
```

# 3.快速入门

## 3.1 普通消息

![图片](https://uploader.shimo.im/f/yqRowCKXd7HlBq0E.png!thumbnail?fileGuid=dvytdvPPr9YTkRdX)

![图片](https://uploader.shimo.im/f/qUF6O8oOiS8qioov.png!thumbnail?fileGuid=dvytdvPPr9YTkRdX)

## 3.2 定时消息

![图片](https://uploader.shimo.im/f/AE15kw9mQ1FVisBQ.png!thumbnail?fileGuid=dvytdvPPr9YTkRdX)

## 3.3 顺序消息

![图片](https://uploader.shimo.im/f/NXCUwrJ7jSPgx9g2.png!thumbnail?fileGuid=dvytdvPPr9YTkRdX)

## 3.4 事务消息

![图片](https://uploader.shimo.im/f/9OokIdKAdrjCQHg4.png!thumbnail?fileGuid=dvytdvPPr9YTkRdX)

# 4.深入了解

## 4.1Consumer端

RocketMQ提供了两种消费模式：PUSH（pull进行监听）和PULL（长轮训）

1. Push 方式：rocketmq 已经提供了很全面的实现， consumer 通过长轮询拉取消息后回调

MessageListener 接口实现完成消费， 应用系统只要 MessageListener 完成业务逻辑即可

2. Pull 方式：完全由业务系统去控制，定时拉取消息，指定队列消费等等， 当然这里需要业务系统

根据自己的业务需求去实现。

这两种模式分别对应的是DefaultMQPushConsumer类和DefaultMQPullConsumer类

![图片](https://uploader.shimo.im/f/1Qnehar83j8Ln6yY.png!thumbnail?fileGuid=dvytdvPPr9YTkRdX)

org.apache.rocketmq.client.impl.consumer.PullMessageService#run

### 4.1.1消费模型

org.apache.rocketmq.common.protocol.heartbeat.MessageModel#BROADCASTING

org.apache.rocketmq.common.protocol.heartbeat.MessageModel#CLUSTERING

### 4.1.2消费选择

org.apache.rocketmq.common.consumer.ConsumeFromWhere#CONSUME_FROM_LAST_OFFSET

第一次启动从队列最后位置消费，后续再启动接着上次消费的进度开始消费

org.apache.rocketmq.common.consumer.ConsumeFromWhere#CONSUME_FROM_FIRST_OFFSET

第一次启动从队列初始位置消费，后续再启动接着上次消费的进度开始消费

org.apache.rocketmq.common.consumer.ConsumeFromWhere#CONSUME_FROM_TIMESTAMP

第一次启动从指定时间点位置消费，后续再启动接着上次消费的进度开始消费

以上所说的第一次启动是指从来没有消费过的消费者，如果该消费者消费过，那么会在broker端记录该消费者的消费位置，如果该消费者挂了再启动，那么自动从上次消费的进度开始

### 4.1.3消息重复幂等：

RocketMQ无法避免消息重复，所以如果业务对消费重复非常敏感，务必要在业务层面去重

Ps：见开发文档 接口幂等性处理 redis incr

### 4.1.4消息过滤：

enablePropertyFilter=true

Status=1 消费 status=2不需要

## 4.2Namesrv端

![图片](https://uploader.shimo.im/f/oT37V41FRLtQFfBE.png!thumbnail?fileGuid=dvytdvPPr9YTkRdX)

Namesrv 名称服务，是没有状态可集群横向扩展。可以理解为一个注册中心, 整个Namesrv的代码非常简单，主要包含两块功能：

1、管理一些 KV 的配置

2、管理一些 Topic、Broker的注册信息

大致提供服务为：

1. 每个 broker 启动的时候会向 namesrv 注册

2. Producer 发送消息的时候根据 topic 获取路由到 broker 的信息

3. Consumer 根据 topic 到 namesrv 获取 topic 的路由到 broker 的信息

## 4.3Broker端

# 5.优化

## 5.1

## 5.2

## 5.3


