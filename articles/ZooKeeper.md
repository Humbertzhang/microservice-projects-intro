# Zookeeper

## 前言

### 简介

ZooKeeper是一个开源的**分布式协调服务**，由雅虎创建，是Google **Chubby的开源实现**。ZooKeeper的设计目标是将那些复杂且容易出错的分布式一致性服务封装起来，构成一个高效可靠的原语集，并以一系列简单易用的接口提供给用户使用。

ZooKeeper是一个典型的**分布式数据一致性**的**解决方案**。分布式应用程序可以基于它实现诸如**数据发布/订阅、负载均衡、命名服务、分布式协调/通知、集群管理、Master选举、分布式锁和分布式队列**等功能。

### 相关网址和参考资料

- 官网：http://zookeeper.apache.org/
- 官网下载：http://zookeeper.apache.org/doc/r3.5.7/index.html
- 参考博客：
  - [ZooKeeper原理与应用](https://www.jianshu.com/p/84ad63127cd1)
  - [Zookeeper的功能以及工作原理](https://www.cnblogs.com/felixzh/p/5869212.html)

## 架构

### 角色描述

**Zookeeper集群中的角色主要为**

- 领导者（leader）
- 学习者（learner），其中包含跟随者（Follower）和观察者（ObServer）
- 客户端（Client）

![img](https://github.com/Humbertzhang/microservice-projects-intro/blob/master/pictures/zookeeper/role.png?raw=true)

### Zookeeper与客户端

![img](https://github.com/Humbertzhang/microservice-projects-intro/blob/master/pictures/zookeeper/architecture.png?raw=true)



## 使用场景

### 设计目的

- 最终一致性
- 可靠性
- 实时性
- 等待无关
- 原子性

## 优缺点

### 优势

- 可靠存储
- 集群管理
- 服务注册发现管理

### 缺点

- 不是为高可用性设计的
- 性能是有限的
- 无法进行有效的权限控制
- 很难避免业务系统的数据不一致

