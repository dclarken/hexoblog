---
title: K8s结合LVS高可用负载均衡与集群外服务访问实践
date: 2017-9-24 11:08:12
tags: [笔记,k8s]
categories: [笔记,k8s]
---

[文档资料](https://www.kubernetes.org.cn/2812.html)
[K8s官方文档翻译](http://docs.kubernetes.org.cn/)

## Kubernetes平台Ingress 介绍

Ingress 是一个规则的集合，它允许集群外的流量通过一定的规则到达集群内的 Service 。
Ingress三个组件:

* 反向代理负载均衡器
反向代理负载均衡器，即常见的负载均衡软件，如 nginx、Haproxy 等。
* Ingress Controller
Ingress Controller 与 kubernetes API 进行交互，实时的感知后端 service、pod 等变化， Ingress Controller 再结合下文的 Ingress 生成配置，然后更新反向代理负载均衡器，并刷新其配置，实现动态服务发现与更新。
* Ingress
Ingress是规则集合；定义了域名与Kubernetes的service的对应关系;这个规则将与 Ingress Controller 结合， Ingress Controller 将其动态写入到负载均衡器配置中，从而实现整体的服务发现和负载均衡。

`Traefik`
Træfɪk 是一个为了让部署微服务更加便捷而诞生的现代HTTP反向代理、负载均衡工具。 它支持多种后台来自动化、动态的应用它的配置文件设置。
特性：

* 它非常快
* 无需安装其他依赖，通过Go语言编写的单一可执行文件
* 支持 Rest API
* 多种后台支持：Docker, Swarm, Kubernetes, Marathon, Mesos, Consul, Etcd
* 后台监控, 可以监听后台变化进而自动化应用新的配置文件设置
* 配置文件热更新。无需重启进程:Traefik 可以与Kubernetes的API进行交互，每当Kubernetes使用Ingress对微服务进行添加、移除、或更新都会被感知，并且可以自动生成它们的配置文件。 指向到你服务的路由将会被直接创建出来。
* 正常结束http连接
* 后端断路器
* 轮询，rebalancer 负载均衡
* Rest Metrics
* 支持最小化 官方 docker 镜像
* 后台支持SSL
* 前台支持SSL（包括SNI）
* 清爽的AngularJS前端页面
* 支持Websocket
* 支持HTTP/2
* 网络错误重试
* 支持Let’s Encrypt (自动更新HTTPS证书)
* 高可用集群模式

---
## K8s结合LVS高可用负载均衡与集群外服务访问组网拓扑：

这个案例也可以学习为：如何将四层负载均衡和七层负载均衡相结合来实现对外访问服务。

组网思路如下：

* 前端为两台LVS服务器，通过keepalive实现负载集群高可用以及虚拟IP，实现外部流量的四层负载，以及作为Kubernetes集群服务访问的入口。LVS负载均衡采用DR模式提高集群的处理速度。
* 后端三台服务器组成Kubernetes集群，每台节点使用hostPort 的方式部署traefik容器（或者其他能够实现反向代理与负载均衡的服务器如nginx），traefik监听节点的80端口，前端LVS负载均衡监听后端三台Kubernetes节点的80端口将外部访问负载分担至traefik。
* 然后由Traefik进行七层负载均衡，可以实现基于域名或访问目录等来实现映射与负载，将访问流量映射至Kubernetes Service并通过Service负载至最终业务POD所在的容器。
![tu1](https://i.loli.net/2017/10/20/59e9a4894e40e.png)
![tu2](https://i.loli.net/2017/10/20/59e9a4895b6d8.png)