# Motan

> Motan is a cross-language remote procedure call(RPC) framework for rapid development of high performance distributed services.

Motan是一套高性能、易于使用的分布式远程服务调用(RPC)框架。

**关键字：** 简单、易用、高可用



## 1 前言

### 1.1 简介

Motan 是微博在2016年8月开源的用于高性能分布式服务快速开发的 RPC 框架, 但是也兼具基本的服务化框架的功能。Motan偏重于简洁实用的服务治理功能和优秀的RPC协议扩展能力，既可以提供高效的RPC远程调用，又能提供服务发现、服务高可用（High  Available）、负载均衡、服务监控、管理等服务治理功能。通过SPI机制提供强大的扩展能力，可以支持不同的RPC协议、传输协议。Motan能够无缝支持Spring配置方式使用RPC服务，通过简单、灵活的配置就可以提供或使用RPC服务。通过使用Motan框架，可以十分方便的进行服务拆分、分布式服务部署。



### 1.2 主要功能

Motan 提供的主要功能包括：

- 服务发现 ：服务发布、订阅、通知
- 高可用策略 ：失败重试（Failover）、快速失败（Failfast）、异常隔离（Server 连续失败超过指定次数置为不可用，然后定期进行心跳探测）
- 负载均衡 ：支持低并发优先、一致性 Hash、随机请求、轮询等
- 扩展性 ：支持 SPI 扩展（service provider interface）
- 其他 ：调用统计、访问日志等

功能上，对比成熟而完善的 dubbo， motan 的功能还嫌单薄，不过 motan 本来也声称是轻量级 RPC 框架，定位不同，情有可原。Motan的解释：

> 与同类型的 Dubbo 相比，Motan 在功能方面并没有那么全面，也没有实现特别多的扩展，但 Motan 是一个小而精的 RPC 框架，它的特点是简单、易用，是一个不断向着实用、易用方向发展的 RPC 服务框架。



### 1.3 相关网址和参考资料 

- 官方GitHub：https://github.com/weibocom/motan

- 官方中文文档：https://github.com/weibocom/motan/wiki/zh_overview

- 参考博客：

  https://www.bookstack.cn/read/learning-microservice/implementation-framework-motan.md

  https://www.cnblogs.com/lovebean/p/10622166.html



## 2 Motan架构

### 2.1 架构概述

Motan中分为服务提供方(RPC Server)，服务调用方(RPC Client)和服务注册中心(Registry)三个角色。

- Server提供服务，向Registry注册自身服务，并向注册中心定期发送心跳汇报状态；

- Client使用服务，需要向注册中心订阅RPC服务，Client根据Registry返回的服务列表，与具体的Sever建立连接，并进行RPC调用。

- 当Server发生变更时，Registry会同步变更，Client感知后会对本地的服务列表作相应调整。

  三者的交互关系如下图：



![架构](https://github.com/Humbertzhang/microservice-projects-intro/blob/master/pictures/motan/architecture.png?raw=true)





### 2.2 模块概述

Motan框架中主要有register、transport、serialize、protocol几个功能模块，各个功能模块都支持通过SPI进行扩展，各模块的交互如下图所示：



![Module](https://github.com/Humbertzhang/microservice-projects-intro/blob/master/pictures/motan/module.png?raw=true)



#### 2.2.1 register

用来和注册中心进行交互，包括注册服务、订阅服务、服务变更通知、服务心跳发送等功能；Server端会在系统初始化时通过register模块注册服务，Client端在系统初始化时会通过register模块订阅到具体提供服务的Server列表，当Server 列表发生变更时也由register模块通知Client。



#### 2.2.2 protocol

用来进行RPC服务的描述和RPC服务的配置管理，这一层还可以添加不同功能的filter用来完成统计、并发限制等功能。



#### 2.2.3 serialize

将RPC请求中的参数、结果等对象进行序列化与反序列化，即进行对象与字节流的互相转换；默认使用对java更友好的hessian2进行序列化。



#### 2.2.4 transport

用来进行远程通信，默认使用Netty nio的TCP长链接方式。



#### 2.2.5 cluster

Client端使用的模块，cluster是一组可用的Server在逻辑上的封装，包含若干可以提供RPC服务的Server，实际请求时会根据不同的高可用与负载均衡策略选择一个可用的Server发起远程调用。

在进行RPC请求时，Client通过代理机制调用cluster模块，cluster根据配置的HA和LoadBalance选出一个可用的Server，通过serialize模块把RPC请求转换为字节流，然后通过transport模块发送到Server端。



## 3 Motan特性

### 3.1 Motan优势

Motan 功能特点：**简单、易用、高可用**

> - Create distributed services without writing extra code.
> - Provides cluster support and integrate with popular service discovery services like [Consul](http://www.consul.io) or [Zookeeper](http://zookeeper.apache.org).
> - Supports advanced scheduling features like weighted load-balance, scheduling cross IDCs, etc.
> - Optimization for high load scenarios, provides high availability in production environment.
> - Supports both synchronous and asynchronous calls.
> - Support cross-language interactive with Golang, PHP, Lua(Luajit), etc.

- 无侵入集成、简单易用，通过 Spring 配置方式，无需额外代码即可集成分布式调用能力。
- 集成服务发现和服务治理能力，灵活支持多种配置管理组件，如 Consul、ZooKeeper 等。
- 支持自定义动态负载均衡、跨机房流量调整等高级服务调度能力。
- 基于高并发、高负载场景优化，具备 Failover、Failfast 能力，保障 RPC 服务高可用。



### 3.2 Motan缺点

- 刚出来，功能和稳定性有待观望。
- 对跨语言调用支持较差，主要支持java。
