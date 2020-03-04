# Spring Cloud



## 1. 前言



### 1.1 简介

Spring Cloud 为开发人员提供了快速构建分布式系统中一些常见模式的工具（例如配置管理、服务发现、断路器、智能路由、微代理、控制总线、一次性令牌、全局锁、leader选举、分布式会话、集群状态等）。 SpringCloud并没有重复制造轮子，它只是将目前各家公司开发的比较成熟、经得起实际考验的服务框架组合起来，通过Spring Boot风格进行再封装，屏蔽掉了复杂的配置和实现原理，最终给开发者留出了一套简单易懂、易部署和易维护的分布式系统开发工具包。它们在任何分布式环境中都能很好地工作，包括开发人员自己的笔记本电脑、裸机数据中心和云计算等托管平台等。



### 1.2 相关网址和参考资料

SpringCloud官网: https://spring.io/projects/spring-cloud

SpringCloud Github Org: https://github.com/spring-cloud/

SpringCloud Netflix 系列组件介绍: https://cloud.spring.io/spring-cloud-netflix/reference/html/

Spring Cloud 开源教程： https://github.com/forezp/SpringCloudLearning  ;  https://github.com/dyc87112/SpringCloud-Learning



### 1.3 解决的问题

SpringCloud集成了很多微服务相关组件，包含了多个子项目，是对于分布式系统的一整套的解决方案，对于微服务中大部分问题都有对应的项目来解决。





## 2. 常用组件介绍

SpringCloud下的常用组件包括以下：

- Spring Cloud Config

  由git仓库支持的集中式外部配置管理。配置资源直接映射到Spring Environment，但是如果需要，可以由非Spring应用程序使用。

- Spring Cloud Netflix

  和多个 Netflix OSS 组件集成(Eureka, Hystrix, Zuul, Archaius, etc.).

  * Netflix Eureka：服务注册与发现。在应用启动时，Eureka客户端向服务端注册自己的服务信息，同时将服务端的服务信息缓存到本地，客户端会和服务端周期性买的进行心跳交互，以便更新服务租约和服务信息。

  * Netflix Hystrix：熔断器，保护系统，控制故障范围。

  * Netflix Zuul：智能API网关，是提供动态路由，监控，弹性，安全等的边缘服务。

  * Netflix Ribbon：客户端负载均衡，将外界的rest调用，根据设定的负载均衡策略转换为对微服务负载均衡的调用。

- Spring Cloud Bus

  用于将服务和服务实例连接到分布式消息的 event bus。用于在集群内传播状态变更。(如配置变更事件)

- Spring Cloud CloudFoundry

  集成应用到Pivotal Cloudfoundry。提供服务发现实现，并可以简化实现 SSO 和 OAuth2 保护的资源，也可以创建 Cloudfoundry 服务中介。

- Spring Cloud Open Service Broker

  为构建实现Open Service Broker API的服务代理提供起点。

- Spring Cloud Cluster

  leader 选举和通用有状态模式，有抽象和用于 Zookeeper, Redis, Hazelcast, Consul 的实现。

- Spring Cloud Consul

  使用 Hashicorp 公司的 Consul 来进行服务发现和配置管理。

- Spring Cloud Security

  为负载均衡的 OAuth2 rest 客户端和在 Zuul 代理中认证 header 转发提供支持。

- Spring Cloud Sleuth

  用于 Spring Cloud 应用的分布式追踪，兼容 Zipkin， HTrace 和基于log(如 ELK)的追踪。

* Spring Cloud Data Flow

  针对 可组合的微服务应用程序 的云原生编排服务。易于使用的DSL，拖放式GUI和REST-API共同简化了基于微服务的数据管道的总体编排。

- Spring Cloud Stream

  轻量级的事件驱动型微服务框架，用于快速构建可以连接到外部系统的应用程序。在Spring Boot应用程序之间使用Apache Kafka或RabbitMQ发送和接收消息的简单声明性模型。 

- Spring Cloud Zookeeper

  使用 Apache Zookeeper 做服务发现和配置管理。

* Spring Cloud Connectors 

  使各种平台上的PaaS应用程序轻松连接到后端服务，例如数据库和消息代理（该项目以前称为“ Spring Cloud”）。

* Spring Cloud Gateway

  基于Project Reactor的智能可编程Router.

* Spring Cloud Pipelines

  Spring Cloud Pipelines提供了一个可靠的部署管道，其中包含一些步骤，以确保您的应用程序可以零停机时间进行部署，并且可以轻松回滚某些错误。





## 3. 特性



### 3.1 优点



#### 3.1.1 功能丰富

提供了丰富的功能组件，并标准化的将微服务的成熟组件和Spring框架结合到一起，提供整套的微服务解决方案，开发成本较低，且风险较小。



#### 3.1.2 基于SpringBoot, 上手简单,生态良好

因为依托于Spring，因此可以很方便的与Spring生态中的组件进行集成。

对于使用Spring进行开发的程序员而言，使用体验好，学习上手难度降低，具有简单配置、快速开发、轻松部署、方便测试的特点。



#### 3.1.3 社区活跃

有强大的 Spring 社区、Netflix 等公司支持，并且开源社区贡献非常活跃。



### 3.2 缺点



#### 3.2.1 部分功能不够开箱即用

虽然SpringCloud功能丰富，但是部分功能不够开箱即用，还需要进行一些定制化处理。



#### 3.2.2 性能相较于二进制协议通信较差

使用基于HTTP的REST进行通信，性能相比使用二进制协议进行通信较差



