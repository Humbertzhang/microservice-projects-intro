# Dubbo



## 1. 前言



### 1.1 简介

Apach Dubbo是一个是一款高性能、轻量级的开源**Java RPC框架**，致力于提供高性能和透明化的远程服务调用(RPC)方案，以及SOA服务治理方案。它提供了三大核心能力：面向接口的远程方法调用，智能容错和负载均衡，以及服务自动注册和发现。



### 1.2 相关网址和参考资料

- Dubbo Github：https://github.com/apache/dubbo
- Dubbo 官网主页：http://dubbo.apache.org/zh-cn/
- Dubbo 官方博客: http://dubbo.apache.org/zh-cn/blog/index.html
- Dubbo 社区: http://dubbo.apache.org/zh-cn/community/index.html
- Dubbo-SpringBoot 官方Demo: https://github.com/apache/dubbo-spring-boot-project



### 1.3 解决的问题

1. 远程通讯: 提供对多种基于长连接的NIO框架抽象封装，包括多种线程模型，序列化，以及“请求-响应”模式的信息交换方式。 
2. 集群容错: 提供基于接口方法的透明远程过程调用，包括多协议支持，以及软负载均衡，失败容错，地址路由，动态配置等集群支持。
3. 服务注册与发现: 基于注册中心目录服务，使服务消费方能动态的查找服务提供方，使地址透明，使服务提供方可以平滑增加或减少机器。
4. RPC接口的定义与使用：与grpc或thrift不同，dubbo不需要使用中间文件定义接口，而是直接使用Java代码即可定义。基于服务注册与发现使我们可以透明地调用其他服务。



## 2. 架构

![架构](https://github.com/Humbertzhang/microservice-projects-intro/blob/master/pictures/dubbo/architecture.png?raw=true)









| Node      | 作用                                   |
| --------- | -------------------------------------- |
| Provider  | 暴露服务的服务提供方                   |
| Consumer  | 调用远程服务的服务消费方               |
| Register  | 服务注册与发现的注册中心               |
| Monitor   | 统计服务的调用次数和调用时间的监控中心 |
| Container | 服务运行容器                           |



更多架构相关可见官方文档: http://dubbo.apache.org/zh-cn/docs/user/preface/architecture.html

以及这篇博客:http://shiyanjun.cn/archives/325.html



## 3.特性



### 3.1 优势

#### 3.1.1 接口定义便捷，侵入性小

相比其他RPC框架，无需定义中间文件，使用代码即可定义与实现远程调用

#### 3.1.2 文档完善，生态较好

文档完善，生态较好，demo与例子很多。



### 3.2 局限

#### 3.2.1 只支持Java语言

但是需要注意的是，在2017年阿里巴巴重新开始维护Dubbo之后，已经开始逐步支持多语言。

到现在为止已经有PHP(https://github.com/apache/dubbo-php-framework), Node(https://github.com/apache/dubbo-js)，Python(https://github.com/apache/dubbo-python), golang(https://github.com/apache/dubbo-go), erlang(https://github.com/apache/dubbo-erlang) 等多个语言的支持。

其中PHP，Golang, Erlang 计划支持客户端和服务端，而Python Node仅支持客户端。

但是考虑到项目的成熟性，在选用前最好做好调研。

### 3.2.2 相较于SpringCloud功能较少

Dubbo只是实现了服务治理，其他组件需要另外整合以实现对应的功能，而Spring Cloud下面有很多子项目，分别覆盖了微服务架构下的方方面面。





