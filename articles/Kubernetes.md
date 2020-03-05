# Kubernetes

> Kubernetes (K8s) is an open-source system for **automating deployment, scaling, and management of containerized applications.** It groups containers that make up an application into logical units for easy management and discovery. 

Kubernetes 是用于**自动部署，扩展和管理容器化应用程序**的开源容器编排引擎。 它将组成应用程序的容器组合成逻辑单元，以便于管理和服务发现。该项目托管在 [CNCF](https://www.cncf.io/about)。

## 1. 前言

### 1.1 简介

Kubernetes是一个可以自动化Linux容器操作的开源平台。它消除了部署和扩展容器化应用程序所涉及的许多繁琐的手动的过程。Kubernetes可以帮助我们轻松有效地管理将服务器集群。

对于使用者而言，只需要使用声明式API定义好“编排对象”（如Pod, Deployment, Job等） 以及服务于编排对象的“服务对象”（Service、Secret、Horizontal Pod Autoscaler等），Kubernetes就能**按照用户的意愿和整个系统的规则完全自动化的编排好容器间的各种关系。**

Kubernetes 项目的本质，就是为用户提供一个具有普遍意义的**容器编排工具**。

### 1.2 相关网址和参考资料

+ Kubernetes官网: https://kubernetes.io/  其中的文档特别是Concepts部分值得一读，并且有官方的中文翻译版本。
+ Kubernetes 代码仓库: https://github.com/kubernetes/kubernetes
+ Kubernetes官方推荐的,由谷歌出品的免费网课 [Scalable Microservices with Kubernetes] ： https://www.udacity.com/course/scalable-microservices-with-kubernetes--ud615
+ Kubernetes 中文社区: https://www.kubernetes.org.cn/

### 1.3 解决的问题

* **简化了微服务系统的编排与部署**：依靠Kubernetes内部定义的各类API对象以及其易于扩展的特性，简化了复杂分布式系统的部署。
* **自动部署与回滚**：Kubernetes逐步部署应用程序或其配置的更改，同时监视应用程序的运行状况，以确保它不会同时杀死所有实例。 如果出现问题，Kubernetes将为您回滚更改。 
* **服务发现与负载均衡**：无需修改应用程序即可使用陌生的服务发现机制。Kubernetes 为容器提供了自己的 IP 地址和一个 DNS 名称，并且可以在它们之间实现负载平衡。
* **服务的水平扩展**：可以使用一个简单的命令，使用UI界面或者基于CPU使用情况自动对应用程序进行伸缩。
* **服务的自我修复**： 重新启动失败的容器，在节点死亡时替换并重新调度容器，杀死不响应用户定义的健康检查的容器，并且在它们准备好服务之前不会将它们公布给客户端。

## 2. 架构介绍

![architecture](https://github.com/Humbertzhang/microservice-projects-intro/blob/master/pictures/kubernetes/architecture.png?raw=true)



Kubernetes 由Master和Node两种节点组成，Master节点代表控制节点，Node节点代表计算节点。

其中，Master节点由三个紧密协作的组件组合而成，他们分别是负责API服务的kube-apiserver, 负责调度的kube-scheduler 以及负责容器编排的controller-manager。整个集群的持久化数据，则由 kube-apiserver 处理后保存在 Etcd 中。

在Node节点的最上层，是一个叫做kubelet的组件。

在 Kubernetes 项目中，kubelet 主要负责同容器运行时（比如 Docker 项目）打交道。而这个交互所依赖的，是一个称作 **CRI（Container Runtime Interface）** 的远程调用接口，这个接口定义了容器运行时的各项核心操作，如启动一个容器需要的所有参数。也就是说Kubernetes 项目并不关心你部署的是什么容器运行时、使用的什么技术实现，只要你的这个容器运行时能够运行标准的容器镜像，它就可以通过实现 CRI 接入到 Kubernetes 项目当中。

而具体的容器运行时，比如 Docker 项目，则一般 **通过 OCI 这个容器运行时规范同底层的 Linux 操作系统进行交互** ，即：把 CRI 请求翻译成对 Linux 操作系统的调用（操作 Linux Namespace 和 Cgroups 等）。

kubelet 还通过 gRPC 协议同一个叫作 Device Plugin 的插件进行交互。这个插件，是 Kubernetes 项目用来管理 GPU 等宿主机物理设备的主要组件，也是基于 Kubernetes 项目进行机器学习训练、高性能作业支持等工作必须关注的功能。

而 kubelet 的另一个重要功能，则是调用**网络插件和存储插件**为容器配置网络和持久化存储。这两个插件与 kubelet 进行交互的接口，分别是 **CNI（Container Networking Interface）和 CSI（Container Storage Interface）**。





## 3. Kubernetes 特性



### 3.1 优势



#### 3.1.1 简化了复杂分布式系统的部署难度



#### 3.1.2 具备高度的横向扩展能力



#### 3.1.3 具备很强的故障恢复能力



#### 3.1.4 高度的定制性与可扩展性

Kubernetes项目从 API 到容器运行时的每一层，都为开发者暴露出了可以扩展的插件机制，鼓励用户通过代码的方式介入到 Kubernetes 项目的每一个阶段。



#### 3.1.5 有庞大、良好的生态系统



### 3.2 局限



#### 3.2.1 对于简单的应用来说，Kubernetes有些大材小用



#### 3.2.2 Kubernetes较为复杂，学习曲线较为陡峭



#### 3.2.3 从原有应用向Kubernetes迁移可能较为复杂



