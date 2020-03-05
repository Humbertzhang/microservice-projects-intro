# Zipkin



## 1. 前言

### 简介

[Zipkin](https://zipkin.io/)是一种分布式跟踪系统。它有助于收集解决微服务架构中的延迟问题所需的时序数据。它管理这些数据的收集和查找。Zipkin的设计基于Google Dapper论文。

### 相关网址和参考资料

- 官网：https://zipkin.io/
- Github: https://github.com/openzipkin/zipkin
- 参考博客：
  - [zipkin,pinpoint和skywalking对比](https://www.jianshu.com/p/4fa81b661f55)
  - [Zipkin介绍](https://my.oschina.net/guol/blog/871678) 
  - [分布式跟踪系统（一）：Zipkin的背景和设计](http://manzhizhen.iteye.com/blog/2348175)
  - [分布式跟踪系统（二）：Zipkin的Span模型](http://manzhizhen.iteye.com/blog/2347153)
  - [zipkin 生产环境 使用经验](https://blog.csdn.net/wuzhuge1990/article/details/80424592)



## 2. 概念介绍

在ZipKin追踪日志中，有几个基本概念: spanId、traceId、parentId

traceId：用来确定一个追踪链的16字符长度的字符串，在某个追踪链中保持不变。

spanId：区域Id，在一个追踪链中spanId可能存在多个，每个spanId用于表明在某个服务中的身份，也是16字符长度的字符串。

parentId：在跨服务调用者的spanId会传递给被调用者，被调用者会将调用者的spanId作为自己的parentId，然后自己再生成spanId。

如下图：



刚发起调用时traceId和spanId是一致，parentId不存在。

![](https://github.com/Humbertzhang/microservice-projects-intro/blob/master/pictures/zipkin/concepts1.png?raw=true)



被调用者的traceId和调用者的traceId时一致的，被调用者会产生自己的spanId，并且被调用者的parentId是调用者的spanId

![](https://github.com/Humbertzhang/microservice-projects-intro/blob/master/pictures/zipkin/concepts2.png?raw=true)



## 3. 架构



![](https://github.com/Humbertzhang/microservice-projects-intro/blob/master/pictures/zipkin/architecture.png?raw=true)



其中左边部分代表了客户端分别为：

1. InstrumentedClient：使用了Zipkin客户端工具的服务调用方
2. InstrumentedServer：使用了Zipkin客户端工具的服务提供方
3. Non-InstrumentedServer：未使用Trace工具的服务提供方，当然还可能存在未使用工具的调用方
4. 总结：一个调用链路是贯穿InstrumentedClient->InstrumentedServer的，每经过一个服务都会以Span的形式通过Transport把经过自身的请求上报的Zipkin服务端中

右边线框内代表了Zipkin的服务端，其中各组件的功能如下：

1. UI：提供web页面，用来展示Zipkin中的调用链和系统依赖关系等
2. Collector：对各个客户端暴露，负责接受调用数据，支持HTTP、MQ等
3. Storage：负责与各个存储适配后存储数据，支持内存，MySQL，ES等
4. API：为web界面提供查询存储中的数据的接口



具体记录流程如下:

```
┌─────────────┐ ┌───────────────────────┐  ┌─────────────┐  ┌──────────────────┐
│ User Code   │ │ Trace Instrumentation │  │ Http Client │  │ Zipkin Collector │
└─────────────┘ └───────────────────────┘  └─────────────┘  └──────────────────┘
       │                 │                         │                 │
           ┌─────────┐
       │ ──┤GET /foo ├─▶ │ ────┐                   │                 │
           └─────────┘         │ record tags
       │                 │ ◀───┘                   │                 │
                           ────┐
       │                 │     │ add trace headers │                 │
                           ◀───┘
       │                 │ ────┐                   │                 │
                               │ record timestamp
       │                 │ ◀───┘                   │                 │
                             ┌─────────────────┐
       │                 │ ──┤GET /foo         ├─▶ │                 │
                             │X-B3-TraceId: aa │     ────┐
       │                 │   │X-B3-SpanId: 6b  │   │     │           │
                             └─────────────────┘         │ invoke
       │                 │                         │     │ request   │
                                                         │
       │                 │                         │     │           │
                                 ┌────────┐          ◀───┘
       │                 │ ◀─────┤200 OK  ├─────── │                 │
                           ────┐ └────────┘
       │                 │     │ record duration   │                 │
            ┌────────┐     ◀───┘
       │ ◀──┤200 OK  ├── │                         │                 │
            └────────┘       ┌────────────────────────────────┐
       │                 │ ──┤ asynchronously report span     ├────▶ │
                             │                                │
                             │{                               │
                             │  "traceId": "aa",              │
                             │  "id": "6b",                   │
                             │  "name": "get",                │
                             │  "timestamp": 1483945573944000,│
                             │  "duration": 386000,           │
                             │  "annotations": [              │
                             │--snip--                        │
                             └────────────────────────────────

```





## 4. 使用场景

背景:  在微服务架构中，怎么监控系统运行状况以及抓取请求链路，是个很麻烦的问题，因为都分布在不通的系统中

ZIPKIN解决方案如下图所示：



![img](https://github.com/Humbertzhang/microservice-projects-intro/blob/master/pictures/zipkin/usage.png?raw=true)



1. 各个应用系统，上报日志span到kafka

2. zipkin server 消费span, 一方面写入es存储链路，另一方面写入统计报表数据

3. 通过Tracing UI来查看链路信息

4. 自建监控UI来查看报表信息



## 5. Zipkin在生产环境中问题

- 客户端传递
- Zipkin消费者的时区问题
- Span错误数统计问题
- 存储elasticsearch采样问题
- 消费日志的大小问题
- 存储elasticsearch的方式
- servicespan的问题
-  生产环境上报kafka的日志太多