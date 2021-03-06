# TARS

TARS是基于名字服务使用Tars协议的高性能RPC开发框架，配套一体化的运营管理平台，并通过伸缩调度，实现运维半托管服务。


## 1 前言

### 1.1 简介

Tars是基于名字服务使用Tars协议的高性能RPC开发框架，同时配套一体化的服务治理平台，帮助个人或者企业快速的以微服务的方式构建自己稳定可靠的分布式应用。

Tars是将腾讯内部使用的微服务架构TAF（Total Application  Framework）多年的实践成果总结而成的开源项目。Tars这个名字来自《星际穿越》电影中机器人Tars，  电影中Tars有着非常友好的交互方式，任何初次接触它的人都可以轻松的和它进行交流，同时能在外太空、外星等复杂地形上，超预期的高效率的完成托付的所有任务。 拥有着类似设计理念的Tars也是一个兼顾易用性、高性能、服务治理的框架，目的是让开发更简单，聚焦业务逻辑，让运营更高效，一切尽在掌握。

### 1.2 相关网址和参考资料

- 官网：https://tars.tencent.com/base/tars_index/cn/index.html

- 官方GitHub：https://github.com/TarsCloud/Tars

- 官方中文文档：https://tarscloud.github.io/TarsDocs/SUMMARY.html

- 参考博客：

  https://www.bookstack.cn/read/TarsPHP/Framework-tars2php.md

  https://www.jianshu.com/p/fedab16cd615

  

## 2 TARS架构

### 2.1 TARS设计思想

Tars的设计思路是采用微服务的思想对服务进行治理，同时对整个系统的各个模块进行抽象分层，将各个层次之间相互解耦或者松耦合，如下图：



![design](https://github.com/Humbertzhang/microservice-projects-intro/blob/master/pictures/tars/design.png?raw=true)



最底的协议层，设计思路是将业务网络通信的协议进行统一，以IDL(接口定义语言)的方式，开发支持多平台、可扩展、协议代码自动生成的统一协议。在开发过程中，开发人员只需要关注通讯的协议字段的内容，不需要关注其实现的细节，大大减轻了开发服务时需要考虑的协议是否能跨平台使用、是否可能需要兼容、扩展等问题。

中间的公共库、通讯框架、平台层，设计思路是让业务开发更加聚焦业务逻辑的本身。因此，从使用者的角度出发，封装了大量日常开发过程中经常使用的公共库代码和远程过程调用，让开发使用更简单方便；从框架本身的角度出发，做到高稳定性、高可用性、高性能，这样才能让业务服务运营更加放心；从分布式平台的角度出发，解决服务运营过程中，遇到的容错、负载均衡、容量管理、就近接入、灰度发布等问题，让平台更加强大。

最上面的运营层，设计思路是让运维只需要关注日常的服务部署、发布、配置、监控、调度管理等操作。



### 2.2 TARS整体架构拓扑

![architecture](https://github.com/Humbertzhang/microservice-projects-intro/blob/master/pictures/tars/topo.png?raw=true)



整体架构的拓扑图主要分为2个部分：服务节点与公共框架节点。



#### 2.2.1 服务节点

服务节点可以认为是服务所实际运行的一个具体的操作系统实例，可以是物理主机或者虚拟主机、云主机。随着服务的种类扩展和规模扩大，服务节点可能成千上万甚至数以十万计。  每台服务节点上均有一个Node服务节点和N(N>=0)个业务服务节点，Node服务节点会对业务服务节点进行统一管理，提供启停、发布、监控等功能，同时接收业务服务节点上报过来的心跳。



#### 2.2.2 公共框架节点

除了服务节点以外的服务，其他服务节点均归为一类。

公共框架节点，数量不定，为了自身的容错容灾，一般也要求在在多个机房的多个服务器上进行部署，具体的节点数量，与服务节点的规模有关，比如，如果某些服务需要打较多的日志，就需要部署更多的日志服务节点。

- Web管理系统：在Web上可以看到服务运行的各种实时数据情况，以及对服务进行发布、启停、部署等操作。
- Registry（路由+管理服务）：提供服务节点的地址查询、发布、启停、管理等操作，以及对服务上报心跳的管理，通过它实现服务的注册与发现。
- Patch（发布管理）：提供服务的发布功能。
- Config（配置中心）：提供服务配置文件的统一管理功能。
- Log（远程日志）：提供服务打日志到远程的功能。
- Stat（调用统计）：统计业务服务上报的各种调用信息，比如总流量、平均耗时、超时率等，以便对服务出现异常时进行告警。
- Property（业务属性）：统计业务自定义上报的属性信息，比如内存使用大小、队列大小、cache命中率等，以便对服务出现异常时进行告警。
- Notify（异常信息）：统计业务上报的各种异常信息，比如服务状态变更信息、访问db失败信息等，以便对服务出现异常时进行告警。

原则上要求全部的节点之间网络互通，至少每台机器的node能够与公共框架节点之间都是可以连通的。



## 3 TARS特性

### 3.1 TARS优势

#### 3.1.1 功能特点

##### 多语言：TARS支持C++,JAVA,PHP,NodeJS等多种语言

TARS最早使用C++语言进行开发, 后续随着业务的发展, 逐步实现了C++/JAVA/PHP/NodeJS/Go的服务端和客户端功能。同时使用Python语言等作为客户端,也可以进行服务的调用。更多语言包括C#等也在逐步的开发中。

不同语言之间, 使用统一的接口定义语言tars来约定协议, 各语言的基本RPC能力均已支持, 同时也会针对不同的语言特性,  开发相对差异化的功能。性能是不同语言实现的时候, 遵循的重要准则, 使用现有语言开发的服务, 均经过大流量的线上验证,  并符合C侧业务的高并发与高可用要求。



##### 敏捷研发：快速构建，自动代码生成，持续集成

使用TARS可以快速的构建微服务, 并且由于TARS本身多语言的特性,  这对团队技术栈的差异有很高的兼容性。对于需要快速开发的逻辑层需求而言,开发者完全可以选择使用适合快速开发的PHP。而对于一些性能要求比较极致的场景, 就可以使用TARS搭建基于C++的微服务了。

除此之外, TARS在各个语言的实现中, 都配备了服务端和客户端的自动代码生成工具。通过这个工具:

- 服务调用者可以非常方便的根据协议生成调用代码, 并像调用本地函数一样调用远程服务
- 服务提供者可以很快的生成代码的骨架, 并在其中进行快速的需求开发



##### 高可用：服务发现，容灾容错，智能调度

TARS本身提供了一系列的基础服务, 这些基础服务有效的保证了TARS的高可用。这可以从几个服务的维度来阐述:

- 主控服务Tars-Registry: 主控服务, 中心化存储所有服务的可用地址, 分布式部署, db存储;
- 节点服务Tars-Node: 负责监控机器上所有的tars服务, 并在发现异常后及时恢复服务;
- 监控服务Tars-State: 负责收集服务本身、机器本身、调用情况等监控服务, 通过设置不同的告警策略, 让服务异常及时通知;

除此之外, 各语言为了保证高可用, 都会对服务可用列表进行一定时间的缓存。保证主控有问题的时候, 可以fallback到本地可用服务列表。而部分语言也已经实现了如果遇到某个服务ip不可用的情况, 自动标记并剔除的功能。



##### 高效运营：无损变更，立体化监控，可视化管理

TARS在发布的时候, 也提供了无损的选项。由于各语言框架不同, 实现的方式也各不相同。通过使用无损, 可以实现类似于NGINX reload的功能。服务分批无损重启, 在可用时再为对外提供服务, 已经成为了大部分高可用发布的标准选择。

同时, 由于TARS是自带管理运维后台的, 因此它天然比单纯的RPC框架更强大一些。通过这个管理后台, 使用者可以轻松完成如下内容:

- 查看现有服务ip列表
- 对单台机器进行服务配置变更
- 服务发布、版本回退
- 调用监控上报查看
- 机器或服务本身监控上报查看
- 单个或批量进行服务配置



#### 3.1.2 Tars与业界其他RPC框架相比：

- Tars提供了支持多语言（C++/Java）的高性能(性能可达40w/s)RPC开发框架，比如业界开源的Dubbo只支持Java，业界开源的Thrift、gRPC性能没有Tars好；
- Tars具有针对服务进行治理的运营管理平台，比如名字路由与发现、部署/发布/扩缩容、立体化监控、日志管理、配置管理等，让系统的运行状态一切尽在掌握，而业界的Thrift、gRPC只是RPC通信框架，业务在它们之上还要做很多时期；
- Tars经过多年在不同业务上的实践和发展，其成熟度和稳定性更好。



### 3.2 Tars缺点

目前开源的还不支持docker，内部已支持docker，后续会开源。

