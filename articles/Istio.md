# Istio





## 1. 前言



### 1.1 简介

微服务和云平台为开发团队提供了很多好处，但是采用微服务等技术虽然降低了开发的部分压力，但是却提升了DevOps的难度与复杂性，istio的目标就是解决这些问题。

istio可以使我们方便的连接，保护，控制和观察云平台中的服务。他是一个完全开源的ServiceMesh实现，可以在服务与网络之间**透明**地注入一个基础设施层以将其应用到现有的应用程序上。该基础设施层可以赋予运维人员对所需功能的控制，同时将开发人员从编码实现分布式系统的问题中解放出来。

istio按照servicemesh的理念在功能上分为控制平面和数据平面，分别负责不同的功能。

**数据平面**由一组以sidecar方式部署的智能代理组成，这些代理可以调节和控制微服务之间所有的网络通信。 数据平面真正触及到对网络数据包的相关操作，是上层控制平面策略的具体执行者。这些任务包括：服务发现，健康监测，流量路由，复杂均衡，身份验证和授权，链路追踪等。简单来说，**数据平面就是负责有条件地转换、转发以及观察进出服务实例的每个网络包。**

**控制平面**主要提供API以及工具用于**配置的管理，并与数据平面中的代理进行通信以下发策略和配置**。维护人员只需要使用istio中控制平面的API来配置整个集群的流量与安全规则，数据平面的智能代理就会和控制中心进行同步，并根据用户期望改变自己的行为。



### 1.2 相关网址与参考资料

* istio 官网: https://istio.io/
* istio 代码仓库: https://github.com/istio/istio
* servicemesher 社区: https://www.servicemesher.com/
* istio服务网格实战电子书: https://www.servicemesher.com/istio-handbook/



### 1.3 解决的问题

* 服务注册与发现

* 流量控制：控制服务之间的流量和API调用的流向，使得调用更可靠，并使网络在恶劣情况下更加健壮。包括istio的负载均衡、金丝雀发布、A/B测试、限速等功能。

* 可观察性：了解服务之间的依赖关系，以及它们之间流量的本质和流向，从而提供快速识别问题的能力。包括对服务各种指标的监控等。

* 策略执行：将组织策略应用于服务之间的互动，确保访问策略得以执行，资源在消费者之间良好分配。策略的更改是通过配置网格而不是修改应用程序代码。

* 服务身份和安全：为网格中的服务提供可验证身份，并提供保护服务流量的能力，使其可以在不同可信度的网络上流转。包括访问控制和端到端认证等。



## 2. 架构介绍

Istio主要由Envoy、Pilot、Mixer三部分组成，整体结构如下：

![image-20200220170152053](https://github.com/Humbertzhang/microservice-projects-intro/blob/master/pictures/istio/architecture.png?raw=true)



* Envoy：以sidecar的形式和应用程序运行与同一个pod中，通过修改iptables来代理应用程序的所有出入流量
* Pilot：接受系统管理员发出的指令或者规则，遥控所有Envoy的行为。其为 Envoy 提供了服务发现，流量管理和智能路由（AB 测试、金丝雀发布等），以及错误处理（超时、重试、熔断）功能。 用户通过 Pilot 的 API 管理网络相关的资源对象，Pilot 会根据用户的配置和服务的信息把网络流量管理变成 Envoy 能识别的格式分发到各个 Sidecar 代理中。
* Mixer：为整个集群执行访问控制（哪些用户可以访问哪些服务）和 Policy 管理（Rate Limit，Quota 等），并且收集代理观察到的服务之间的流量统计数据。



### 2.1 Envoy

* Envoy作为sidecar部署到各个svc到pod内，代理所有到进出流量。

* SvcA服务作为客户端点用服务端svcB，svcB存在有两个版本，但是svcA并不知情。

* 当svcA通过svcB到域名访问其服务时，Envoy根据Pilot配置的路由规则，将1%的流量分给了svcB的alpha版本。

![image-20200220170958629](https://github.com/Humbertzhang/microservice-projects-intro/blob/master/pictures/istio/envoy.png?raw=true)







### 2.2 Pilot

Pilot具有平台无关性的设计。

Pilot提供REST客户端接受用户请求，具体的实现则由各个平台自己实现Platform Adapter。Envoy同样实现Envoy API以实现路由配置功能和服务发现功能。

通过配置，Pilot可以使Envoy实现：路由配置、故障注入、流量迁移、请求超时、熔断等诸多能力。

![image-20200220173742379](https://github.com/Humbertzhang/microservice-projects-intro/blob/master/pictures/istio/pilot.png?raw=true)





### 2.3 Mixer

Mixer从Envoy处获取流量属性，根据自定义的属性匹配规则进行流量处理，如：流量限制、日志记录等

Mixer主要提供三个核心功能：

- 前提条件检查：发生在服务响应请求之前，如果检查不通过可终止响应
- 配额管理：
- 遥测报告：可监控服务、上报日志信息

![image-20200220202803392](https://github.com/Humbertzhang/microservice-projects-intro/blob/master/pictures/istio/mixer.png?raw=true)





## 3. Istio 特性



### 3.1 优点

#### 3.1.1 透明、非侵入式设计

得益于sidecar的设计模式，istio可以在不更改源代码的情况下透明地部署到现有的分布式应用程序上。



#### 3.2 功能丰富

istio拥有众多功能：服务发现、负载均衡、故障恢复、监控、A/B测试、金丝雀发布、限速、访问控制、端到端认证等诸多功能。



### 3.2 缺点

#### 3.2.1 复杂性

因为Istio拥有众多功能，因此其复杂性很高，学习曲线也较为陡峭。

#### 3.2.2 性能问题

istio选择了控制平面与数据平面完全分离的架构，在这样的架构中，各个部分职责分明，边界清晰，耦合性低，有很多优点，但是缺点是性能不佳。也是因此，istio团队最近在对架构进行调整，计划整合现有架构中各个组件到一个名叫istiod的单体应用中，降低安装与配置复杂性，提高性能。
参考博客：[回归单体 —— Istio的自我救赎？](https://www.servicemesher.com/blog/istio-self-salvation/)


