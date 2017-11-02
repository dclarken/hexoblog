---
title: K8s集群基本组成
date: 2017-9-19 11:31:21
tags: [笔记,k8s]
categories: [笔记,k8s]
---

[K8s集群基本概念](http://www.cnblogs.com/chris-cp/p/5766153.html>)
Kubernetes 是Google开源的容器集群管理系统，它构建于docker技术之上，基于Docker构建一个容器的调度服务，提供资源调度、均衡容灾、服务注册、动态扩缩容等功能套件，本质上可看作是基于容器技术的mini-PaaS平台。

**`基本概念：`**
`Node:`
Node也就等同于Mesos集群中的Slave节点，是所有Pod运行所在的工作主机，可以是物理机也可以是虚拟机。不论是物理机还是虚拟机，工作主机的统一特征是上面要运行kubelet管理节点上运行的容器。

`Pod:`
若干相关容器的组合，Pod包含的容器运行在同一host上，这些容器使用相同的网络命令空间、IP地址和端口，相互之间能通过localhost来发现和通信。另外，这些容器还可共享一块存储卷空间。在k8s中创建，调度和管理的最小单位就是Pod，而非容器，Pod通过提供更高层次的抽象，提供了更加灵活的部署和管理模式；
	
* k8s的基本操作单元，一个Pod由一个或多个容器组成，通常pod里的容器运行的相同的应用；一个容器相当于一个进程，”一个容器一个进程”的原则，一个pod含有多个容器，就像虚拟机工作时有多个进程在执行操作，POD中的所有容器都是并行启动。结合多个容器集成进单一的Kubernetes节点pod，即容器互联通信。
* 同一pod包含的容器运行在同一host上，作为统一管理单元：同一pod 共享着相同的volumes， network命名空间， ip和port空间，这是通过 Mapped Container做到的；  
* pid ns：处于同一pod中的应用可以看到彼此的进程
* network ns：处于同一pod中的应用可以访问一样的ip和port空间
* ipc ns：处于同一pod的应用可以用systemV ipc 或者posix消息队列进行通信
* UTC ns：处于同一pod应用共用一个主机名

多容器POD的主要用途是支持主要应用程序的共定位、共管辅助进程。

* 跨斗容器“帮助”主容器。示例包括日志或数据更改监视程序、监视适配器等。例如，日志监视程序可以由一个团队创建一次，并在不同的应用程序中重用。另一个例子，一个跨斗容器是一个文件或数据装载器，为主容器生成数据。
* 代理、网桥和适配器将主容器与外部世界连接起来。例如，Apache HTTP服务器或Nginx可以提供静态文件。它还可以充当主容器中Web应用程序的反向代理，以记录和限制HTTP请求。另一个例子是辅助容器，它将请求从主容器路由到外部网络。这使得主容器可以连接到本地主机访问，例如，外部数据库，但不需任何的服务发现。

Pod容器间的通信：
[Pod介绍](https://www.kubernetes.org.cn/2767.html)

* 在Kubernetes Pod中共享卷，使用一个共享的Kubernetes卷作为一种简单而有效的方式，在一个POD内容器间进行数据共享。
* 进程间通信（IPC），一个POD里的容器共享相同的IPC命名空间，这意味着他们也可以互相使用标准进程间通信，如SystemV信号系统或POSIX共享内存。

`ReplicationController （RC）`
	
* RC是用来管理Pod的，每个RC由一个或多个Pod组成；在RC被创建之后，系统将会保持RC中的可用Pod的个数与创建RC时定义的Pod个数一致，如果Pod个数小于定义的个数，RC会启动新的Pod，反之则会杀死多余的Pod。
* RC通过定义的Pod模板被创建，创建后对象叫做Pods（也可以理解为RC），可以在线修改Pods的属性，以实现动态缩减、扩展Pods的规模
* RC通过label关联对应的Pods，通过修改Pods的label可以删除对应的Pods在需要对Pods中的容器进行更新时，RC采用一个一个替换原则来更新整个Pods中的Pod；
* reschudeling: 维护pod副本，“多退少补”；即使是某些minion宕机
* scaling：通过修改rc的副本数来水平扩展或者缩小运行的pods
* Rolling updates:一个一个地替换pods来rolling updates服务；
* multiple release tracks：如果需要在系统中运行multiple release 服务，replication controller使用labels来区分multiple release tracks

`Label`

* Label是用于区分Pod、Service、RC的key/value键值对
* Pod、Service、RC可以有多个label，但是每个label的key只能对应一个value
* 整个系统都是通过Label进行关联，得到真正需要操作的目标
  
`Service`

* Service也是k8s的最小操作单元，是真实应用服务的抽象
* Service通常用来将浮动的资源与后端真实提供服务的容器进行关联
* Service对外表现为一个单一的访问接口，外部不需要了解后端的规模与机制
* Service是定义在集群中一组运行Pod集合的抽象资源，它提供了所有相同的功能。当一个Service资源被创建后，将会分配一个唯一的IP（也叫做集群IP），这个IP地址将存在于Service的整个生命资源，Service一旦被创建，整个IP无法进行修改。

Pod可以通过Service进行通信，并且所有的通信将会通过Service自动负载均很到所有的Pod中的容器，service是抽象化的pod，为集群提供服务。

`目前 kubernetes 共有三种服务暴露的方式:`
[K8s集群外服务访问](https://www.kubernetes.org.cn/2812.html)

* LoadBlancer Service
LoadBlancer Service是kubernetes深度结合云平台的一个组件；当使LoadBlancer Service暴露服务时，实际上是通过向底层云平台申请创建一个负载均衡器来向外暴露服务；目前LoadBlancer Service支持的云平台已经相对完善，比如国外的GCE、DigitalOcean，国内的 阿里云，私有云 OpenStack 等等，由于LoadBlancer Service深度结合了云平台，所以只能在一些云平台上来使用。
* NodePort Service
NodePort Service，实质上就是通过在集群的每个node上暴露一个端口，然后将这个端口映射到某个具体的service来实现的，虽然每个node的端口有很多(默认的取值范围是 30000-32767)，例如：在做反向代理服务器时候，将nginx的80端口映射到30000端口，然后将30000端口暴露，用于做对外服务，但是由于安全性和易用性(服务多了就乱了，还有端口冲突问题)实际使用可能并不多。
* Ingress
Ingress可以实现使用nginx等开源的反向代理负载均衡器实现对外暴露服务，可以理解Ingress就是用于配置域名转发，在nginx中就类似upstream，它与ingress-controller结合使用，通过ingress-controller监控到pod及service的变化，动态地将ingress中的转发信息写到诸如nginx、apache、haproxy等组件中实现方向代理和负载均衡。

---
**`kubernetes组成`**　

* kubectl 客户端命令行工具：将接收的命令，发送给kube-apiserver，作为对整个平台操作的入口。
* kube-apiserver　REST API服务：作为整个系统的控制入口，以REST API的形式公开，可以横向扩展在高可用的架构中。
* kube-controller-manager 多个控制器的合体，用来执行整个系统中的后台任务，多个控制进程的合体：
* Node Controller 负责整个系统中node up 或down的状态的响应和通知
* Replication Controller 负责维持Pods中的正常运行的Pod的个数
* Endpoints Controller 负责维持Pods和Service的关联关系
* Service Account & Token Controllers负责为新的命名空间创建默认的账号和API访问的Token　　　　
* kube-scheduler　任务调度、命令下发,负责监视新创建的Pods任务，下发至未分配的节点运行该任务
* kube-proxy　网络代理转发：kube-proxy运行在每个节点上，负责整个网络规则的连接与转发，使k8s中的service更加抽象化，为Service提供cluster内部的服务发现和负载均衡
* kubelet　容器的管理,kubelet运行在每个节点上，作为整个系统的agent，监视着分配到该节点的Pods任务，负责挂载Pods所依赖的卷组，下载Pods的秘钥，运行Pods中的容器（通常是docker），周期获取所有容器的状态，通过导出Pod和节点的状态反馈给REST系统；
* etcd 信息存储，保存了整个集群的状态
* flannel　IP地址的分配
![tujie](https://i.loli.net/2017/10/20/59e9a1d50897a.png)
Cluster，即集群：虚拟机或者物理机的一组集合，运行着Kubernetes
	* ETCD
		* 一个分布式强一致性的key/value存储
		* 可理解为一个存储k8s信息的数据库
	* Node
		* 工作节点，运行Master节点交付的任务
		* 能运行一个或多个Pods
		* 运行的组件
			* Kubelet
				* 管理容器的守护进程
				* 管理Docker主机来启动容器的管理程序
				* 定期从etcd获取分配到本机的pod信息，启动或停止容器
				* 接收apiserver的HTTP请求，汇报pod的运行状态
			* Proxy
				* 服务发现（IP寻址）
				* 定期从etcd获取所有的service根据service信息创建代理
				* 客户pod访问其他pod都经过proxy转发
	* Master
		* 提供了集群统一视图的中心控制点
		* 一个Master节点来控制多个Node节点
		* 运行的组件
			* API Server
				* 对操作对象的增删改查
				* 提供RESTful K8s API接口
				* 校验和配置Pod、Service和Replication Controller
				* 统一管理集群系统的入口
			* Scheduler
				* 资源调度
				* 为新建的pod分配机器
			* Controller-Manager
				* 容错处理
				* 扩容、缩容
				* 负责执行各种控制器
				* endpoint-controller
				定期关联service和pod(关联信息由endpoint对象维护)，保证service到pod的映射总是最新的
				* replication-controller
				定期关联replicationController和pod，保证定义的复制数量与实际运行pod的数量总是一致的
可操作对象

	* Pod：k8s上可创建、调度和管理的最小单位，是一个或一组拥有共享卷的容器集
	* Replication Controller：管理着Pod的生命周期，建议创建Pod都是用rc，他会确保任何时刻Pod的数量都维持在一个特殊设定的值
	* Service：基本的负载均衡器，为外部某提供一组Pod的一个稳定操作接口

[文档资料来自](http://blog.csdn.net/qq1010885678/article/details/48719923)

