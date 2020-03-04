# Apache Thrift

> The Apache Thrift software framework, for scalable cross-language  services development, **combines a software stack with a code generation engine** to build services that work efficiently and seamlessly between  C++, Java, Python, PHP, Ruby, Erlang, Perl, Haskell, C#, Cocoa,  JavaScript, Node.js, Smalltalk, OCaml and Delphi and other languages.    

Apache Thrift软件框架（用于可扩展的跨语言服务开发）将软件栈与代码生成引擎结合在一起，以高效构建支持C ++，Java，Python，PHP，Ruby，Erlang，Perl，Haskell，C＃， Cocoa，JavaScript，Node.js，Smalltalk，OCaml和Delphi等语言的服务。

**关键字：**远程服务器调用、支持多种编程语言、高效。



## 1 前言

### 1.1 简介

Apache Thrift是一个轻量级、跨语言的远程服务调用框架。Apache Thrift 最初是 Facebook 实现的一种支持多种编程语言、高效的远程服务器调用框架，它于 2008 年进入 Apache 开源项目。

Apache Thrift 采用接口描述语言（IDL）定义 RPC 接口和数据类型，通过代码生成引擎生成不同语言的RPC服务端/客户端模板代码（支持 C++，Java，Python，Ruby等）。其数据传输采用二进制格式，相对 XML 和 JSON 来说体积更小，对于高并发、大数据量和多语言的环境更有优势。

Apache Thrift支持多种不同的编程语言，包括C++、Java、Python、PHP、Ruby等，本系列主要讲述基于Java语言的Thrift的配置方式和具体使用。



### 1.2 相关网址和参考资料

- 官方网站：http://thrift.apache.org/

- 官方下载地址：http://thrift.apache.org/download

- 官方IDL示例文件（自己写IDL文件时可以此为参考）：

  https://git-wip-us.apache.org/repos/asf?p=thrift.git;a=blob_plain;f=test/ThriftTest.thrift;hb=HEAD

- 各种环境下搭建方法：http://thrift.apache.org/docs/install/

- 参考博客：

  https://blog.csdn.net/baidu_22254181/article/details/82814489

  https://blog.csdn.net/zkp_java/article/details/81879577

  https://blog.csdn.net/houjixin/article/details/42778335

  https://blog.csdn.net/lihao21/article/details/54909236



## 2 Apache Thrift架构（软件栈）

Apache Thrift主要用于各个服务之间的RPC通信，支持跨语言。Apache Thrift是一个典型的CS结构，客户端和服务端可以使用不同的语言开发，Apache Thrift通过IDL（Interface Description Language）来关联客户端和服务端。

Apache Thrift的整体架构图如下图所示 ：



![thrift架构](https://github.com/Humbertzhang/microservice-projects-intro/blob/master/pictures/thrift/architecture.png?raw=true)



| 名称          | 层     | 工作                                                         |
| ------------- | ------ | ------------------------------------------------------------ |
| Your Code     | /      | 用户实现的业务逻辑                                           |
| ServiceClient | 处理层 | RPC桩部分。Apache Thrift 根据 IDL 生成的客户端和服务端代码。<br />其中红色部分用来实现数据的读写操作。 |
| TProtocol     | 协议层 | 定义了数据传输格式，负责对网络传输数据进行序列化与反序列化。具体方法包括二进制，JSON 或者 Apache Thrift 定义的格式。 |
| TTransport    | 传输层 | 提供数据传输功能，负责直接从网络中读取和写入数据。使用 Apache Thrift 可以方便地定义一个服务并选择不同的传输协议。 |



## 3 Apache Thrift特性

### 3.1 Apache Thrift优势



#### 3.1.1 开发速度快

通过编写RPC接口Thrift IDL文件，利用编译生成器自动生成服务端骨架(Skeletons)和客户端桩(Stubs)。从而省去开发者自定义和维护接口编解码、消息传输、服务器多线程模型等基础工作。

- 服务端：只需要按照服务骨架即接口，编写好具体的业务处理程序(Handler)即实现类即可。
- 客户端：只需要拷贝IDL定义好的客户端桩和服务对象，然后就像调用本地对象的方法一样调用远端服务。



#### 3.1.2 接口维护简单

通过维护`Thrift`格式的IDL（**接口描述语言**）文件（注意写好注释），即可作为给`Client`使用的接口文档使用，也**自动生成**接口代码，始终保持代码和文档的一致性。且`Thrift`协议可灵活支持**接口**的**可扩展性**。



#### 3.1.3 学习成本低

因为其来自Google Protobuf开发团队，所以其IDL文件风格类似Google Protobuf，且更加易读易懂；特别是RPC服务接口的风格就像写一个面向对象的Class一样简单。

初学者只需参照：http://thrift.apache.org/，一个多小时就可以理解Thrift IDL文件的语法使用。

#### 3.1.4 多语言支持

Thrift支持C++、 Java、Python、PHP、Ruby、Erlang、Perl、Haskell、C#、Cocoa、JavaScript、Node.js、Smalltalk等多种语言，即可生成上述语言的服务器端和客户端程序。

对于我们经常使用的Java、PHP、Python、C++支持良好，虽然对iOS环境的Objective-C(Cocoa)支持稍逊，但也完全满足我们的使用要求。

#### 3.1.5 稳定/广泛使用

`Thrift`在很多开源项目中已经被验证是稳定和高效的，例如`Cassandra`、`Hadoop`、`HBase`等；国外在`Facebook`中有广泛使用，国内包括百度、美团小米、和饿了么等公司。



### 3.2 Apache Thrift缺点

网上说的有点杂，感觉也不具代表性，这里摘录一些

> 缺点么，和其他facebook开源的项目都有类似的问题，只管拉不管擦
>
> 1. 基本没有官方文档，使用参考可以看看有人专门写的这个 Thrift: The Missing Guide
> 2. RPC在 0.6.1 升级到 0.7.0 是不兼容的！这个对于早于 0.6.1 开始使用的用户来说是个大坑
> 3. bug fix和更新不积极，好在序列化和RPC服务都不是太复杂的问题，需要考量的设计问题不多，自己维护patch的成本不高，如果我没有记错的话，0.6.1的java的ThreadPool Server是会有Thread死亡之后的Thread泄露问题的
> 4. Facebook今年说，我们开源了一个新的performance更好的 fbthrift，你说你该用apache thrift还是fbthrift呢?



> 1. 不能指定IP TNonblockingServer.cpp
>
> 2. 每个service独占一个端口号 
>
> 3. C++客户端异步支持不好，或者至少这方面的信息太少，让人了解困难 暂只略知有个基于http的异步支持，java客户端的异步支持感觉不错，但没有实践过 
>
> 4. 对外直接暴露了由automake系列命名autoheader生成的config.h头文件。如果使用它的也是使用automake编译，容易造成冲突，结果是警告难消息，目前简单的措施是注释掉Thread.hpp中的#include "config.h" 
>
> 5. 文件、目录组织缺乏严谨 进入config.h所在目录就可以发现 6.命名方式让人吐槽 类都是以T打头的微软骆驼风格，这个还可以接受，但文件名和类名完成相同，这个就有点过了
>
>    TBufferTransports.h
>    TFDTransport.h
>    TFileTransport.h
>    THttpClient.h
>    THttpServer.h
>    THttpTransport.h
>
> 6. 内部代码的质量也只能算是一般

 