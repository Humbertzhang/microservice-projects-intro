# GRPC



gRPC  是一个高性能、开源和通用的 RPC 框架，面向移动和 HTTP/2 设计。目前提供 C、Java 和 Go  语言版本，分别是：grpc, grpc-java, grpc-go. 其中 C 版本支持 C, C++, Node.js, Python,  Ruby, Objective-C, PHP 和 C# 支持。

gRPC 基于 HTTP/2 标准设计，带来诸如双向流、流控、头部压缩、单 TCP 连接上的多复用请求等特性。这些特性使得其在移动设备上表现更好，更省电和节省空间占用。



## 1 前言

### 1.1 简介

> In gRPC a client application can directly call methods on a server application on a different machine as if it was a  local object, making it easier for you to create distributed  applications and services. As in many RPC systems, gRPC is based around  the idea of defining a service, specifying the methods that can be  called remotely with their parameters and return types. On the server  side, the server implements this interface and runs a gRPC server to  handle client calls. On the client side, the client has a stub (referred to as just a client in some languages) that provides the same methods  as the server.

在gRPC中一个客户端可以像使用本地对象那样直接调用位于不同机器上的服务端应用的方法(methods)。这让你能够更容易的构建分布式的应用和服务。和其他`RPC`系统类似，`gRPC`也是基于定义一个服务，指定服务可以被远程调用的方法以及他们的参数和返回类型。在服务端，实现服务的接口然后运行一个`gRPC`服务来处理可出端的请求。在客户端，客户端拥有一个存根（stub在某些语言中仅称为客户端），提供与服务器相同的方法。

![Concept Diagram](https://github.com/Humbertzhang/microservice-projects-intro/blob/master/pictures/grpc/usage.png?raw=true)

> gRPC clients and servers can run and talk to each other in a variety of  environments - from servers inside Google to your own desktop - and can  be written in any of gRPC’s supported languages. So, for example, you  can easily create a gRPC server in Java with clients in Go, Python, or  Ruby. In addition, the latest Google APIs will have gRPC versions of  their interfaces, letting you easily build Google functionality into  your applications.

gRPC客户端和服务器可以在各种环境中运行并相互通信，并且可以使用gRPC支持的任何语言编写。因此，例如，您可以使用Go，Python或Ruby的客户端轻松地用Java创建gRPC服务器。此外，最新的Google API的接口将拥有gRPC，可让您轻松地在应用程序中内置Google功能。



### 1.2 相关网址和参考资料

- gRPC-java Github：https://github.com/grpc/grpc-java
- gRPC 官网主页：https://grpc.io/
- gRPC 官方文档中文版：http://doc.oschina.net/grpc?t=58009



## 2  gRPC 架构

### 2.1 protocol buffers

gRPC 默认使用 *protocol buffers*，这是 Google 开源的一套成熟的结构数据序列化机制（当然也可以使用其他数据格式如 JSON）。 *protocol buffers*能够将数据进行序列化，并广泛应用在数据存储、通信协议等方面。压缩和传输效率高，语法简单，表达力强。



### 2.2 服务定义

正如其他 RPC 系统，gRPC 基于如下思想：定义一个服务， 指定其可以被远程调用的方法及其参数和返回类型。gRPC 允许你定义四类服务方法。

#### 2.2.1 单项 RPC

客户端发送一个请求给服务端，从服务端获取一个应答，就像一次普通的函数调用。

#### 2.2.2 服务端流式 RPC

客户端发送一个请求给服务端，可获取一个数据流用来读取一系列消息。客户端从返回的数据流里一直读取直到没有更多消息为止。

#### 2.2.3 客户端流式 RPC

客户端用提供的一个数据流写入并发送一系列消息给服务端。一旦客户端完成消息写入，就等待服务端读取这些消息并返回应答。

#### 2.2.4 双向流式  RPC

两边都可以分别通过一个读写数据流来发送一系列消息。这两个数据流操作是相互独立的，所以客户端和服务端能按其希望的任意顺序读写，例如：服务端可以在写应答前等待所有的客户端消息，或者它可以先读一个消息再写一个消息，或者是读写相结合的其他方式。每个数据流里消息的顺序会被保持。



### 2.3 使用 API 接口

gRPC 提供 protocol buffer 编译插件，能够从一个服务定义的 .proto 文件生成客户端和服务端代码。通常 gRPC 用户可以在服务端实现这些API，并从客户端调用它们。

- 在服务侧，服务端实现服务接口，运行一个 gRPC 服务器来处理客户端调用。gRPC 底层架构会解码传入的请求，执行服务方法，编码服务应答。
- 在客户侧，客户端有一个*存根*实现了服务端同样的方法。客户端可以在本地存根调用这些方法，用合适的 protocol buffer 消息类型封装这些参数— gRPC 来负责发送请求给服务端并返回服务端 protocol buffer 响应。



## 3 特性

> ###### Simple service definition
>
> Define your service using Protocol Buffers, a powerful binary serialization toolset and language
>
> ###### Start quickly and scale
>
> Install runtime and dev environments with a single line and also scale to millions of RPCs per second with the framework
>
> ###### Works across languages and platforms
>
> Automatically generate idiomatic client and server stubs for your service in a variety of languages and platforms
>
> ###### Bi-directional streaming and integrated auth
>
> Bi-directional streaming and fully integrated pluggable authentication with http/2 based transport

- 接口定义简单（使用proto buffers）（Simple service definition）

  使用协议缓冲区，功能强大的二进制序列化工具集和语言定义服务

- 启动快速，可扩展性强（Start quickly and scale）

  只需一行即可安装运行时和开发环境，并通过该框架每秒可扩展至数百万个RPC

- 跨语言，跨平台（Works across languages and platforms）

  自动以多种语言和平台为您的服务生成惯用的客户端和服务器存根

- 双向通信并且集成认证（Bi-directional streaming and integrated auth）

  双向流传输和完全集成的可插入身份验证以及基于http / 2的传输



### 3.1 gRPC优势

- protobuf二进制消息，性能好/效率高（空间和时间效率都很不错）
- proto文件生成目标代码，简单易用
- 序列化反序列化直接对应程序中的数据类，不需要解析后在进行映射(XML,JSON都是这种方式)
- 支持向前兼容（新加字段采用默认值）和向后兼容（忽略新加字段），简化升级
- 支持多种语言（可以把proto文件看做IDL文件）
- Netty等一些框架集成



### 3.2 gRPC缺点

- GRPC尚未提供连接池，需要自行实现
- 尚未提供“服务发现”、“负载均衡”机制
- 因为基于HTTP2，绝大部多数HTTP Server、Nginx都尚不支持，即Nginx不能将GRPC请求作为HTTP请求来负载均衡，而是作为普通的TCP请求。（nginx1.9版本已支持）
- Protobuf二进制可读性差（貌似提供了Text_Fromat功能）
- 默认不具备动态特性（可以通过动态定义生成消息类型或者动态编译支持）

