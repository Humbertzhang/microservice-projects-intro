# Zipkin

## 前言

### 简介

[Zipkin](https://zipkin.io/)是一种分布式跟踪系统。它有助于收集解决微服务架构中的延迟问题所需的时序数据。它管理这些数据的收集和查找。Zipkin的设计基于Google Dapper论文。

### 相关网址和参考资料

- 官网：https://zipkin.io/
- 参考博客：
  - [zipkin,pinpoint和skywalking对比](https://www.jianshu.com/p/4fa81b661f55)
  - [Zipkin介绍](https://my.oschina.net/guol/blog/871678) 
  - [分布式跟踪系统（一）：Zipkin的背景和设计](http://manzhizhen.iteye.com/blog/2348175)
  - [分布式跟踪系统（二）：Zipkin的Span模型](http://manzhizhen.iteye.com/blog/2347153)
  - [zipkin 生产环境 使用经验](https://blog.csdn.net/wuzhuge1990/article/details/80424592)

## 架构



![](https://github.com/Humbertzhang/microservice-projects-intro/blob/master/pictures/zipkin/architecture.png?raw=true)







## 使用场景

背景:  在微服务架构中，怎么监控系统运行状况以及抓取请求链路，是个很麻烦的问题，因为都分布在不通的系统中

ZIPKIN解决方案如下图所示：

      各个应用系统，上报日志span到kafka
      zipkin server 消费span, 一方面写入es存储链路，另一方面写入统计报表数据
      通过TracingUi来查看链路信息
      自建监控UI来查看报表信息

![img](https://github.com/Humbertzhang/microservice-projects-intro/blob/master/pictures/zipkin/usage.png?raw=true)



## Zipkin生产问题

- 客户端传递
- Zipkin消费者的时区问题
- Span错误数统计问题
- 存储elasticsearch采样问题
- 消费日志的大小问题
- 存储elasticsearch的方式
- servicespan的问题
-  生产环境上报kafka的日志太多