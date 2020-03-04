# Promethues



## 1. 前言



### 1.1 简介 

Prometheus 是一套开源的系统监控报警框架。它启发于 Google 的 borgmon 监控系统，于 2015 年正式发布。2016 年，Prometheus 正式加入 Cloud Native Computing Foundation，成为受欢迎度仅次于 Kubernetes 的项目。

作为新一代的监控框架，Prometheus 具有以下特点：

- 强大的多维度数据模型：
  1. 时间序列数据通过 metric 名和键值对来区分。
  2. 所有的 metrics 都可以设置任意的多维标签。
  3. 数据模型更随意，不需要刻意设置为以点分隔的字符串。
  4. 可以对数据模型进行聚合，切割和切片操作。
  5. 支持双精度浮点类型，标签可以设为全 unicode。

- 灵活而强大的查询语句（PromQL）：在同一个查询语句，可以对多个 metrics 进行乘法、加法、连接、取分数位等操作。
- 易于管理： Prometheus server 是一个单独的二进制文件，可直接在本地工作，不依赖于分布式存储。
- 高效：平均每个采样点仅占 3.5 bytes，且一个 Prometheus server 可以处理数百万的 metrics。
- 使用 pull 模式采集时间序列数据，这样不仅有利于本机测试而且可以避免有问题的服务器推送坏的 metrics。
- 可以采用 push gateway 的方式把时间序列数据推送至 Prometheus server 端。
- 可以通过服务发现或者静态配置去获取监控的 targets。
- 有多种可视化图形界面，其中最常配合Grafana进行展示。
- 易于伸缩。

需要注意的是，由于数据采集可能会有丢失，所以 Prometheus 不适用对采集数据要 100% 准确的情形。但如果用于记录时间序列数据，Prometheus 具有很大的查询优势，此外，Prometheus 适用于微服务的体系架构。



### 1.2 相关网址和参考链接

github: https://github.com/prometheus/prometheus

官方网站：https://prometheus.io/

PrometheusBook: https://yunlzheng.gitbook.io/prometheus-book/parti-prometheus-ji-chu/quickstart/why-monitor

一些不错的博客: https://www.ibm.com/developerworks/cn/cloud/library/cl-lo-prometheus-getting-started-and-practice/index.html

  

### 1.3 解决的问题

* 降低了监控系统的运维难度。

  Prometheus核心部分只有一个单独的二进制文件，不存在任何的第三方依赖(数据库，缓存等等)。唯一需要的就是本地磁盘，不会有潜在级联故障的风险。

* 可以方便的与现有系统进行集成，即可以监控系统内部的运行状态，又可以监控系统外部的运行环境状态。

* 提供了查询语言PromQL，可以实现对监控数据高效的查询、聚合。



## 2. 架构

![architecture](https://github.com/Humbertzhang/microservice-projects-intro/blob/master/pictures/prometheus/architecture.png?raw=true)





### Prometheus Server

Prometheus Server是Prometheus组件中的核心部分，负责实现对监控数据的获取，存储以及查询。 Prometheus Server可以通过静态配置管理监控目标，也可以配合使用Service Discovery的方式动态管理监控目标，并从这些监控目标中获取数据。其次Prometheus Server需要对采集到的监控数据进行存储，Prometheus Server本身就是一个时序数据库，将采集到的监控数据按照时间序列的方式存储在本地磁盘当中。最后Prometheus Server对外提供了自定义的PromQL语言，实现对数据的查询以及分析。

Prometheus Server的联邦集群能力可以使其从其他的Prometheus Server实例中获取数据，因此在大规模监控的情况下，可以通过联邦集群以及功能分区的方式对Prometheus Server进行扩展。

### Exporters

Exporter将监控数据采集的端点通过HTTP服务的形式暴露给Prometheus Server，Prometheus Server通过访问该Exporter提供的Endpoint端点，即可获取到需要采集的监控数据。

一般来说可以将Exporter分为2类：

- 直接采集：这一类Exporter直接内置了对Prometheus监控的支持，比如cAdvisor，Kubernetes，Etcd，Gokit等，都直接内置了用于向Prometheus暴露监控数据的端点。
- 间接采集：间接采集，原有监控目标并不直接支持Prometheus，因此我们需要通过Prometheus提供的Client Library编写该监控目标的监控采集程序。例如： Mysql Exporter，JMX Exporter，Consul Exporter等。

### AlertManager

在Prometheus Server中支持基于PromQL创建告警规则，如果满足PromQL定义的规则，则会产生一条告警，而告警的后续处理流程则由AlertManager进行管理。在AlertManager中我们可以与邮件，Slack等等内置的通知方式进行集成，也可以通过Webhook自定义告警处理方式。AlertManager即Prometheus体系中的告警处理中心。

### PushGateway

由于Prometheus数据采集基于Pull模型进行设计，因此在网络环境的配置上必须要让Prometheus Server能够直接与Exporter进行通信。 当这种网络需求无法直接满足时，就可以利用PushGateway来进行中转。被监控实例可以通过PushGateway将内部网络的监控数据主动Push到Gateway当中，这样Prometheus Server则可以采用同样Pull的方式从PushGateway中获取到监控数据。

### Web UI

Prometheus内置一个简单的Web控制台，可以查询指标，查看配置信息或者Service Discovery等，实际工作中，查看指标或者创建仪表盘通常使用**Grafana**，Prometheus作为Grafana的数据源。



## 3. 特性

### 3.1 优势



#### 3.1.1 便于集成到现有系统

使用HTTP协议提供数据收集接口，并提供了各个语言编写exporter的SDK，使得监控模块可以方便地集成到现有系统。

且因为没有使用私有协议，Prometheus的**开放性**也大大增加。

#### 3.1.2 提供了强大的查询语言

提供了PromQL来对监控数据进行查询与聚合，使得使用者可以方便的分析监控数据。

#### 3.1.3  高效

单一的Prometheus实例可以处理数以百万的监控指标和每秒处理数十万的数据点。

通过高效的压缩算法使得存储数据方面也十分高效。



### 3.2 局限



#### 3.2.1 数据完整性得不到保证

Prometheus无法保证收集到的数据是完全完整的详细的，在需要数据高度准确的时候（如金融等使用场景）不推荐使用Prometheus。

