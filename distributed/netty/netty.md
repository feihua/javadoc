# 1.Java IO 与 NIO

## 1.1Linux I/O 模型介绍

### 1.1.1Linux I/O 流程

![图片](https://uploader.shimo.im/f/tSYh1RPfmq1tm1N8.png!thumbnail?fileGuid=wGC6xJkKc8qk3DgD)

### 1.1.2 将 I/O 模型划分为以下五种类型：

* 阻塞式 I/O 模型

![图片](https://uploader.shimo.im/f/6L7VJKhdrRImo3Ij.png!thumbnail?fileGuid=wGC6xJkKc8qk3DgD)

* 非阻塞式 I/O 模型

![图片](https://uploader.shimo.im/f/KwjmsW0SBekQVkYe.png!thumbnail?fileGuid=wGC6xJkKc8qk3DgD)

* I/O 复用

![图片](https://uploader.shimo.im/f/Q5rDsOK4C6SDcYum.png!thumbnail?fileGuid=wGC6xJkKc8qk3DgD)

* 信号驱动式 I/O

![图片](https://uploader.shimo.im/f/rKyY5Um3BJdxL6i7.png!thumbnail?fileGuid=wGC6xJkKc8qk3DgD)

* 异步 I/O

![图片](https://uploader.shimo.im/f/7m1RVDrteNMVHiBe.png!thumbnail?fileGuid=wGC6xJkKc8qk3DgD)

### 1.1.3 各种 I/O 模型的比较

![图片](https://uploader.shimo.im/f/AJJsv9QKGfTFi3hu.png!thumbnail?fileGuid=wGC6xJkKc8qk3DgD)

## 1.2Java I/O

![图片](https://uploader.shimo.im/f/mGjmAUkCcFBw86H2.png!thumbnail?fileGuid=wGC6xJkKc8qk3DgD)

## 1.3Java NIO

### 1.3.1java io 和 java nio 对比

![图片](https://uploader.shimo.im/f/TjmoanINTlsfo0YZ.png!thumbnail?fileGuid=wGC6xJkKc8qk3DgD)

### 1.3.2Java NIO 主要由3 部分核心组件组成

a. Buffer

一个 Buffer 本质上是内存中的一块， 可以将数据写入这块内存， 从这块内存获取数据

java.nio 定义了以下几个 Buffer 的实现

![图片](https://uploader.shimo.im/f/KMizyslX0DYuCeB5.png!thumbnail?fileGuid=wGC6xJkKc8qk3DgD)

Java NIO Buffer 三大核心概念：position、limit、capacity

最好理解的当然是 capacity，它代表这个缓冲区的容量，一旦设定就不可以更改。比如 capacity 为 1024 的 IntBuffer，代表其一次可以存放 1024 个 int 类型的值。

一旦 Buffer 的容量达到 capacity，需要清空 Buffer，才能重新写入值。

![图片](https://uploader.shimo.im/f/McB2lr7g9Gld4m5i.png!thumbnail?fileGuid=wGC6xJkKc8qk3DgD)

b. Channel

所有的 NIO 操作始于通道，通道是数据来源或数据写入的目的地，主要地，java.nio 包中主要实现的以下几个 Channel：

![图片](https://uploader.shimo.im/f/lnYqw380HOUDkLmP.png!thumbnail?fileGuid=wGC6xJkKc8qk3DgD)

c. Selector

Selector 是 Java NIO 中的一个组件，用于检查一个或多个 NIO Channel 的状态是否处于可读、可写

如此可以实现单线程管理多个 channels,也就是可以管理多个网络链接

![图片](https://uploader.shimo.im/f/5guZ2eLTAFP9FFou.png!thumbnail?fileGuid=wGC6xJkKc8qk3DgD)

![图片](https://uploader.shimo.im/f/AncMHkVXuDQ4iYLP.png!thumbnail?fileGuid=wGC6xJkKc8qk3DgD)

### 1.3.3NIO 带来了什么

* 事件驱动模型
    * 避免多线程
    * 单线程处理多任务
* 非阻塞 IO,IO 读写不再阻塞,而是返回 0
* 基于 block 的传输,通常比基于流的传输更高效
* 更高级的 IO 函数,zero-copy
* IO 多路复用大大提高了 java 网络应用的可伸缩性和实用性

注意

1. 使用NIO = 高性能
2. NIO不一定更快的场景
    1. 客户端应用
    2. 连接数<1000
    3. 并发程度不高
    4. 局域网环境下
3. NIO完全屏蔽了平台差异(Linux poll/select/epoll, FreeBSD Kqueue)
    1. NIO仍然是基于各个OS平台的IO系统实现的,差异仍然存在
4. 使用NIO做网络编程很容易
    1. 离散的事件驱动模型，编程困难
# 2.Netty 编程实践

![图片](https://uploader.shimo.im/f/vKbhcExH3zqYALDQ.png!thumbnail?fileGuid=wGC6xJkKc8qk3DgD)

## 2.1Netty 介绍

### 2.1.1特性

1. 设计
    1. 统一的API,适用于不同的协议(阻塞和非阻塞)
    2. 基于灵活、可扩展的事件驱动模型（SEDA）
    3. 高度可定制的线程模型
    4. 可靠的无连接数据Socket支持(UDP)
2. 性能
    1. 更好的吞吐量,低延迟
    2. 更省资源
    3. 尽量减少不必要的内存拷贝
3. 安全
    1. 完整的SSL/ TLS和STARTTLS的支持
4. 易用
    1. 完善的Java doc,用户指南和样例
    2. 仅依赖于JDK1.6（netty 4.x)
## 2.2Netty 主要组件介绍

### 2.2.1Bootstrap 和 ServerBootstrap

Netty Server启动主要流程：

1. 设置服务端ServerBootStrap启动参数
    1. group(parentGroup, childGroup):
    2. channel(NioServerSocketChannel): 设置通道类型
    3. handler()：设置NioServerSocketChannel的ChannelHandlerPipeline
    4. childHandler(): 设置NioSocketChannel的ChannelHandlerPipeline
2. 通过ServerBootStrap的bind方法启动服务端，bind方法会在parentGroup中注册NioServerScoketChannel，监听客户端的连接请求
    1. 会创建一个NioServerSocketChannel实例，并将其在parentGroup中进行注册

Netty Server执行主要流程：

Client发起连接CONNECT请求，parentGroup中的NioEventLoop不断轮循是否有新的客户端请求，如果有，ACCEPT事件触发

ACCEPT事件触发后，parentGroup中NioEventLoop会通过NioServerSocketChannel获取到对应的代表客户端的NioSocketChannel，并将其注册到childGroup中

childGroup中的NioEventLoop不断检测自己管理的NioSocketChannel是否有读写事件准备好

![图片](https://uploader.shimo.im/f/WNvpOKjGrSjcRzQb.png!thumbnail?fileGuid=wGC6xJkKc8qk3DgD)

### 2.2.2Transport Channel

提供了统一的API，支持不同类型的传输层：

1. OIO -阻塞IO
2. NIO - Java NIO
3. Epoll -  Linux Epoll(JNI)
4. Local Transport - IntraVM调用
5. Embedded Transport - 供测试使用的嵌入传输
6. UDS -  Unix套接字的本地传输
### 2.2.3EventLoop和EventLoopGroup

1. EventLoopGroup
    1. 包括多个EventLoop
    2. 多个EventLoop之间不交互
2. EventLoop：
    1. 每个EventLoop对应一个线程
    2. 所有连接(channel)都将注册到一个EventLoop，并且只注册到一个，整个生命周期中都不会变化
    3. 每个EventLoop管理着多个连接(channel)
    4. EventLoop来处理连接(Channel)上的读写事件
3. ServerBootstrap包括2个不同类型的EventLoopGroup:
    1. Parent EventLoop:负责处理Accept事件，接收请求
    2. Child EventLoop：负责处理读写事件
4. ByteBuf通过两个索引（reader index、writer index）划分为三个区域：
    1. reader index前面的数据是已经读过的数据，这些数据可以丢弃
    2. 从reader index开始，到writer index之前的数据是可读数据
    3. 从writer index开始，为可写区域
### 2.2.4ChannelHandler和ChannelPipeline

ChannelHandler  - 业务处理核心逻辑，用户自定义

Netty 提供2个重要的 ChannelHandler 子接口：

ChannelInboundHandler - 处理进站数据和所有状态更改事件

ChannelOutboundHandler - 处理出站数据，允许拦截各种操作

ChannelPipeline 是ChannelHandler容器

包括一系列的ChannelHandler 实例,用于拦截流经一个 Channel 的入站和出站事件

每个Channel都有一个其ChannelPipeline

可以修改 ChannelPipeline 通过动态添加和删除 ChannelHandler

定义了丰富的API调用来回应入站和出站事件

ChannelHandlerContext表示 ChannelHandler 和ChannelPipeline 之间的关联

在 ChannelHandler 添加到 ChannelPipeline 时创建

ChannelHandlerContext表示 ChannelHandler 和ChannelPipeline 之间的关联

![图片](https://uploader.shimo.im/f/lb3ZgWuQx7wTLZSr.png!thumbnail?fileGuid=wGC6xJkKc8qk3DgD)

### 2.2.5ByteBuf

1. 相比JDK ByteBuffer， 更加易于使用：
    1. 为读/写分别维护单独的指针，不需要通过flip()进行读/写模式切换
    2. 容量自动伸缩（类似于 ArrayList，StringBuilder）
    3. Fluent API (链式调用）
2. 更好的性能：
    1. 通过内置的CompositeBuffer来减少数据拷贝（Zero copy）
    2. 支持内存池，减少GC压力
## 2.3Netty 编程示例

# 3.Netty 线程模型解析

## 3.1Reactor 模式及其与 Netty 的对应关系

### 3.1.1单线程Reactor

### 3.1.2多线程Reactor

### 3.1.3Multiple Reactor

### 3.1.4主从Reactor

## 3.2Netty  EventLoop 源码解析

### 3.2.1NioEventLoopGroup整体结构

### 3.2.2NioEventLoop创建分析

### 3.2.3NioEventLoop启动流程分析

### 3.2.4NioEventLoop执行流程分析

# 4.Netty 编解码编程实战

## 4.1 半包粘包问题示例与分析

## 4.2Netty 半包粘包问题解决

### 4.2.1LineBasedFrameDecoder（\n, \r\n)

回车换行解码器

配合StringDecoder

### 4.2.2DelimiterBasedFrameDecoder

分隔符解码器

### 4.2.3FixedLengthFrameDecoder

固定长度解码器

### 4.2.4LengthFieldBasedFrameDecoder

基于'长度'解码器(私有协议最常用)

## 4.3Netty 编解码器分析

# 5.基于 Netty 实现高性能弹幕系统

## 5.1 弹幕系统概要设计

### 5.1.1弹幕系统特点

1.实时性高：你发我收， 毫秒之差

2.并发量大：一人吐槽，万人观看

### 5.1.2弹幕系统架构设计

业务架构

![图片](https://uploader.shimo.im/f/RBePFluRYF0dk3Vk.png!thumbnail?fileGuid=wGC6xJkKc8qk3DgD)

实现方案一

![图片](https://uploader.shimo.im/f/18M9B3CKIcAoO6Da.png!thumbnail?fileGuid=wGC6xJkKc8qk3DgD)

实现方案二

![图片](https://uploader.shimo.im/f/8vUgmYmpe4nkXgE7.png!thumbnail?fileGuid=wGC6xJkKc8qk3DgD)



## 5.2Netty 对 Http 协议解析实现

request 报文

![图片](https://uploader.shimo.im/f/AXzG6kNqM15AhioD.png!thumbnail?fileGuid=wGC6xJkKc8qk3DgD)

response 报文

![图片](https://uploader.shimo.im/f/4VXZFhFteuqG7UO3.png!thumbnail?fileGuid=wGC6xJkKc8qk3DgD)

### 5.2.1http报文解析方案：

1：请求行的边界是CRLF(回车)，如果读取到CRLF(回车)，则意味着请求行的信息已经读取完成。

2：Header的边界是CRLF，如果连续读取两个CRLF，则意味着header的信息读取完成。

3：body的长度是有Content-Length 来进行确定。

### 5.2.2netty关于http 的解决方案：

// 解析请求

很多http server的实现都是基于servlet标准，但是netty对http实现并没有基于servlet。所以在使用上比Servlet复杂很多。比如在servlet 中直接可以通过 HttpServletRequest 获取 请求方法、请求头、请求参数。而netty 确需要通过如下对象自行解析获取。

HttpMethod：主要是对method的封装，包含method序列化的操作

HttpVersion: 对version的封装，netty包含1.0和1.1的版本

QueryStringDecoder: 主要是对urI进行解析，解析path和url上面的参数。

HttpPostRequestDecoder：对post 中body 内容进行解析获取 form 参数。

HttpHeaders：包含对header的内容进行封装及操作

HttpContent：是对body进行封装，本质上就是一个ByteBuf。如果ByteBuf的长度是固定的，则请求的body过大，可能包含多个HttpContent，其中最后一个为LastHttpContent(空的HttpContent),用来说明body的结束。

HttpRequest：主要包含对Request Line和Header的组合

FullHttpRequest： 主要包含对HttpRequest和httpContent的组合

![图片](https://uploader.shimo.im/f/dtqqtVJNlc7vhhqo.png!thumbnail?fileGuid=wGC6xJkKc8qk3DgD)

### 5.2.3Netty Http的请求处理流程

![图片](https://uploader.shimo.im/f/dGpEHzOrq0nR26ut.png!thumbnail?fileGuid=wGC6xJkKc8qk3DgD)

从图中可以看出做为服务端的Netty 就是在做 编码和解码操作。其分别通过以下两个ChannelHandler对象实现：

HttpRequestDecoder :用于从byteBuf 获取数据并解析封装成HttpRequest 对象

HttpResponseEncoder：用于将业务返回数据编码成 Response报文并发送到ByteBuf。

将以上两个对象添加进 Netty 的 pipeline 即可实现最简单的http 服务。

Decoder 流程

![图片](https://uploader.shimo.im/f/IDjRVjvWuDTYbD0X.png!thumbnail?fileGuid=wGC6xJkKc8qk3DgD)

encode 流程

![图片](https://uploader.shimo.im/f/WGaEhN6EqdbUbZ15.png!thumbnail?fileGuid=wGC6xJkKc8qk3DgD)

## 5.3WebScoket 协议解析实现

### 5.3.1webSocket 协议简介：

webSocket 是html5 开始提供的一种浏览器与服务器间进行全双工二进制通信协议，其基于TCP双向全双工作进行消息传递，同一时刻即可以发又可以接收消息，相比Http的半双工协议性能有很大的提升，

### 5.3.2webSocket特点如下：

1.单一TCP长连接，采用全双工通信模式

2.对代理、防火墙透明

3.无头部信息、消息更精简

4.通过ping/pong 来保活

5.服务器可以主动推送消息给客户端，不在需要客户轮询

### 5.3.3WebSocket 协议报文格式：

我们知道，任何应用协议都有其特有的报文格式，比如Http协议通过 空格 换行组成其报文。如http 协议不同在于WebSocket属于二进制协议，通过规范进二进位来组成其报文。具体组成如下图：

![图片](https://uploader.shimo.im/f/h4mjHsFyFdYRJfte.png!thumbnail?fileGuid=wGC6xJkKc8qk3DgD)

```javascript
通过javaScript 中的API可以直接操作WebSocket 对象，其示例如下：
var ws = new WebSocket(“ws://localhost:8080”);
ws.onopen = function()// 建立成功之后触发的事件 {
console.log(“打开连接”); ws.send("ddd"); // 发送消息
};
ws.onmessage = function(evt) { // 接收服务器消息
console.log(evt.data);
};
ws.onclose = function(evt) {
console.log(“WebSocketClosed!”); // 关闭连接 };
ws.onerror = function(evt) {
console.log(“WebSocketError!”); // 连接异常
}; 
```
# 6.基于 Netty 实现 RPC 框架

## 6.1RPC构建需要考虑的主要因素

1. 通信协议
2. 文本协议或二进制协议（RESTful with JSON or RPC with Binary Encoding）
3. 支持的调用方式：单向、双向、Streaming
4. API容错、可伸缩性
## 6..1.2RPC框架

![图片](https://uploader.shimo.im/f/N1iMOCRPQ2RZcFsK.png!thumbnail?fileGuid=wGC6xJkKc8qk3DgD)



