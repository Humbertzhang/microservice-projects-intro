# K3s



## 1. 前言

### 1.1 简介

K3s是一个符合标准的、CNCF官方认证的Kubernetes发行版，他是一个轻量级版本的kubernetes，它在几乎所有特性上与kubernetes保持一致，但为了降低资源占用，k3s在一些地方和k8s做了不同的选择，主要包括：

* 使用了相比 Docker 更轻量的 [containerd](https://containerd.io/) 作为容器运行时。
* 去掉了 k8s 的 Legacy, alpha, non-default 的特性
* 用 sqlite3 作为默认的存储，而不是 etcd

以及一些其他的优化，最终k3s打包成了一个二进制文件，通过官方的脚本可以很方便的进行部署。

k3s做了很多轻量化的改造，效果也很明显，server端(对应k8s中的Master节点)只需要512MB内存即可正常运行，而Agent端（对应K8s中的Node节点）仅需要少于100M即可运行。也因此适合于服务器资源匮乏，尤其是边缘计算以及物联网的场景，也适合搭建CI服务等使用场景。



### 1.2 相关网址和资料

Github: https://github.com/rancher/k3s

官网: https://k3s.io/

官方文档：https://rancher.com/docs/k3s/latest/en/

官方博客: https://rancher.com/tags/k3s/

k3s在线命令行体验: https://www.katacoda.com/ben_hall/scenarios/k3s

一些其他博客: https://juejin.im/post/5dc915dbf265da4d1d330160 ; 



### 1.3 解决的问题

Kubernetes虽然是个很优秀的容器编排与部署平台，但是其占用的资源也相当多，安装也比较繁琐，k3s的目标就是解决这些问题，具体包括：

* K8s占用资源过多。k3s基于k8s，删除了不必要的功能，使用低资源占用的组件替换高资源占用的组件，使得整个系统占用资源大大下降。
* K8s安装繁琐。k3s将需要使用的组件都打包到了一个二进制文件中，并自动化处理了TLS证书相关问题，使整个安装过程简单了很多。

也因为以上的特性，k3s可以在边缘计算以及物联网等资源匮乏的场景有比较好的表现。





## 2. 架构

![架构](https://github.com/Humbertzhang/microservice-projects-intro/blob/master/pictures/k3s/architecture.png?raw=true)



k3s和kubernetes架构相似，其将API Server, Scheduler, ControllerManager, Flannel, Kubelet 等组件都封装在了一个二进制文件中，运行在一个进程中。k3s Server 与 k3s Agent 在部署时使用的是同一个二进制文件，根据Node节点角色的不同在运行时分别使用封装在其内的不同组件。 

此外还需要注意，在k3s server 中使用SQLite替换了etcd作为默认的存储组件，不过使用者仍然可以通过配置使用其他的存储组件进行替代，如MySQL, etcd等。

在容器运行时方面，k3s默认使用了containerd作为底层的容器运行时，而不是像k8s一样默认使用Docker，这也降低了服务器的资源压力。





## 3. 特性



### 3.1 优点

* 资源占用少，适合边缘计算、物联网等场景
* 部署方便
* 一直跟随Kubernetes进行版本更新



### 3.2 缺点

* 与标准Kubernetes 相比仍有一些差异，在某些场景可能不能完全与Kubernetes兼容。

  例如k3s没有包含与各云服务厂商如GKE, AWS, Azure等相关的功能，在这些平台进行部署时与Kubernetes使用体验会有所差异。

* 无法体验Kubernetes中的测试功能。

  k3s删除了Kubernetes中属于Beta以及Alpha的功能，因此使用者无法体验一些新的特性。





