# Docker 

2010年成立与美国旧金山的公司—dotCloud（基于PaaS的云计算服务）。

诞生于2013年初的开源项目，基于Go语言实现的，**将容器技术简化和标准化**，称为Docker（轻量级的虚拟化技术）。

* 优点：占用空间小、启动快、资源利用率高。
* 缺点：编排、管理和调度等方面比较困难。



**<font color="#00a4ff">注意：</font>**Docker本身并不是容器、而是**容器创建的工具**、是**应用容器引擎**。



**Docker三板斧：**Build、Ship(发送) and Run

**Docker三大核心：**镜像(Image)、容器(Container)、仓库(Repository)

**Docker Registry**服务，是对Docker镜像进行管理的。官方（Docker Hub)



[Docker中文文档](http://www.dockerinfo.net/image%e9%95%9c%e5%83%8f)



# K8S

2014年6月由Google公司开源，基于**容器的集群管理平台**，全称**Kubernetes**。（单词来自于希腊语，含义舵手、领航员。中间用8单词，简称K8S）

一个K8S系统，通常称为一个**K8S集群**（Cluster）包括了一个Master节点（主节点）、和一群Node节点（计算节点）。

* Master节点：主要负责管理和控制。

* Node节点：是工作负载节点



## Master

Master节点包括了

1. API Server（整个系统的对外接口），供客户端和其他组件调用，相当于营业厅；
2. Scheduler（对集群内部的资源进行调度），类似于调度室；
3. Conterller manager（管理控制器）；
4. etcd；



## Node

Node节点包括了

1. Pod，K8S最基本的操作单元，一个Pod代表集群中允许的一个进程，它的内部封装了一个或多个容器。
2. Service，可以看作一组提供相同服务的Pod的对外访问接口。
3. Docker，创建容器的工具；
4. kubelet，负责监视指派到它所在Node上的Pod，包括创建、修改、监控、删除等；
5. kube-proxy，负责代理Pod对象提供代理；
6. Fluentd，负责日志收集、存储与查询
7. kube-dns；





# 额外篇

虚拟化优点：低成本、高利用率、充分灵活、动态调度。













<font color="gray">注：本文纯用于个人学习笔记。</font>



