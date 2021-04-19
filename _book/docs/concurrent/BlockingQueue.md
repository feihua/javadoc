**要点：**

1. 基本概念介绍/DIY 并发/阻塞队列
2. JDK 阻塞队列实战与原理分析
3. 高性能并发队列讨论
# 1.基本概念介绍/DIY 并发/阻塞队列

# 2.JDK 阻塞队列实战与原理分析

![图片](https://uploader.shimo.im/f/WbgA5C8xytCsxfSI.png!thumbnail?fileGuid=dJtGHj9qdDw6HT3r)

## 2.1ArrayBlockingQueue

基于数组结构的有界阻塞队列（长度不可变）

## 2.2LinkedBlockingQueue

基于链表结构的有界阻塞队列（默认容量 Integer.MAX_VALUE）

## 2.3LinkedTransferQueue

基于链表结构的无界阻塞/传递队列

## 2.4LinkedBlockingDeque

基于链表结构的有界阻塞双端队列（默认容量 Integer.MAX_VALUE）

## 2.5SynchronousQueue

不存储元素的阻塞/传递队列

## 2.6PriorityBlockingQueue

支持优先级排序的无界阻塞队列

## 2.7DelayQueue

支持延时获取元素的无界阻塞队列


# 3.高性能并发队列讨论

