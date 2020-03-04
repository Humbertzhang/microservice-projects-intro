# ServiceComb

## 前言

### 简介

ServiceComb源于华为云CSE微服务引擎（Cloud Service Engine）平台。其包括一站式的服务注册、服务治理、动态配置功能，具备服务化契约增强、多语言SDK支持、多通信协议支持等优势特性, 并提供SAGA数据最终一致性方案解决微服务架构数据一致性难题。ServiceComb 兼容Spring Cloud等业界流行微服务框架，互通业界生态。

CSE自身的的开发模式门槛低、通用性强、服务接口规范统一、严格遵循微服务设计理念等特点使能智能助手业务实现快速上下线发布，化繁为简，更简单高效实现降级熔断、弹性伸缩和故障迁移等运维治理能力，缩短迭代开发周期，使能该业务SLA提升到99.96%。

### 相关网址和参考资料

- 项目地址：https://servicecomb.incubator.apache.org/
- 目前ServiceComb在Apache孵化的子项目有：
  - Java Chassis，Java底座： 
    http://github.com/apache/incubator-servicecomb-java-chassis
  - Service Center，服务注册中心： 
    http://github.com/apache/incubator-servicecomb-service-center
  - Saga，分布式事务最终一致性解决方案： 
    http://github.com/apache/incubator-servicecomb-saga
- 参考博客：
  - https://blog.csdn.net/qq_27384769/article/details/80895990
  - https://www.cnblogs.com/Bkxk/p/10382377.html

## 架构

目前ServiceComb主要包含的是Java底座（Chassis），整个语言开发包包含服务契约、编程模型、运行模型与通信模型四个部分。



![architecture](https://github.com/Humbertzhang/microservice-projects-intro/blob/master/pictures/servicecomb/architecture.png?raw=true)





Chassis是Chris Richardson提出一种微服务模式，它可以使用户在构建微服务的过程中，只聚焦于业务逻辑本身，而不需要去处理日志、外部配置、分布式追踪等环节，Chassis将这些环节交给专门的框架来处理。

**服务契约：**  ServiceComb通过服务契约支持Java、Go语言底座开发的服务可以相互访问。服务契约是建立在OpenAPI基础之上的，OpenAPI规范是Linux基金会的一个项目，通过定义一种用来描述API格式或API定义的语言，来规范RESTful服务开发。Open API提供了一套比较完整的开发工具链，在服务契约的基础上我们可以自动生成服务文档，服务调用开发包以及服务调用测试代码。

**编程模型：** Java语言底座支持多种RESTful服务的编程方式，即支持应用广泛的Spring MVC与JAXRS编程模型，也支持POJO + 服务契约的开发方式来描述RESTful服务。由于在Java Chassis先将这些编程模型转换成为服务契约Swagger对象，然后交由内部运行模型以及通信模型进行处理， 所以Java Chassis并不依赖Spring MVC以及JAXRS的底层实现。目前Go Chassis和Java Chassis支持RESTful级别的互联互通。

**运行模型：** 大家在开发应用的过程中，会有很多服务扩展的需求，例如根据用户身份认证信息，对服务调用进行认证。Chassis架构支持在运行时通过配置处理链（handler）的方式来进行服务扩展。ServiceComb缺省提供服务发现、容错熔断以及调用链追踪等处理链。用户可以通过配置文件的方式，在应用启动的时候加载对应的处理链。

**通信模型：** 语言底座通过通信模型对外进行交互，其中包含了消息序列化和反序列化的模块以及底层传输的模块。值得一提的 Java Chassis的通信模型

## 特性

**Java-Chassis** 一个开箱即用的Java语言微服务SDK，包括四个部分：服务契约，编程模型，运行模型和通信模型，以及一整套微服务治理能力，如负载均衡、容错、限流和调用链跟踪。微服务治理和业务逻辑是隔离的。

**Service-Center ** 基于Etcd的、高性能、高可用性、无状态、Go语言实现的服务发现和注册中心，提供实时服务实例注册，实时服务实例通知和服务间契约测试。

**Saga** 为分布式事务提供最终的一致性解决方案，解决微服务痛点问题。

## 优缺点

### 优势

与目前市面上已经流行的各种微服务框架，如Spring Boot、Spring Cloud、Dubbo等相比，比Spring Cloud灵活，轻量，虽然实现上都是使用了Netflix的ribbon hystrix等若干微服务组件。ServiceComb早期的有个版本也是基于Spring Cloud做的，试图借用Spring Boot的annotation，虽然省了代码，但是用着不好用，这其实也是用Spring Cloud的一个感觉，不直接，不好用。

比Dubbo出来的晚，技术上比Dubbo更通用，提供的微服务治理手段更多，功能更强，扩展性也好一点。

ServiceComb的优势：

- 异步内核：基于VertX的同步和异步模型编程有效确保了无论是在传统企业或电商领域，还是在新兴的互联网或物联网等新兴企业中，都能够保持高性能和低延迟，以避免在达到峰值负载时应用出现雪崩效应。

- 开箱即用体验：开发人员通过脚手架网站start.servicecomb.io启动的微服务项目，可以集服务注册、发现、通信和微服务治理能力和默认的集中化配置为一体。

- OpenAPI：自动代码生成，业务逻辑代码和治理能力隔离，可以使能DevOps Pipeline， 使用契约文件和OpenAPI的双向生成能力可以使不同的团队高效且独立的开发和管理代码、测试和进行文档化工作。

### 不足之处

成熟度较差

- 目前还是0.4版本，有些地方还可以更友好一些，比如接口参数直接禁用了HashMap

- 启动有点慢，10秒以上是常态

- Saga还不成熟，写的是0.0.2版本，还算有自知之明

- highway协议可以免费用，但是协议本身是私有的





