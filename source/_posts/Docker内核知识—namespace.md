---
title: Docker内核知识——namespace
date: 2017-9-15 00:08:12
tags: [笔记,docker,内核]
categories: [笔记,docker]
comments: false
---

为了在分布式的环境下进行通信和定位，容器必然需要一个独立的IP、端口、路由等等，自然就想到了网络的隔离。同时，你的容器还需要一个独立的主机名以便在网络中标识自己。想到网络，顺其自然就想到通信，也就想到了进程间通信的隔离。可能你也想到了权限的问题，对用户和用户组的隔离就实现了用户权限的隔离。最后，运行在容器中的应用需要有自己的PID,自然也需要与宿主机中的PID进行隔离。
Linux内核中就提供了这六种namespace隔离的系统调用，如下表所示。

|Namespace|系统调用参数|隔离内容
|-|-|-|
|UTS|CLONE_NEWUTS|主机名与域名
|IPC|CLONE_NEWIPC|信号量、消息队列和共享内存
|PID|CLONE_NEWPID|进程编号
|Network|CLONE_NEWNET|网络设备、网络栈、端口等等
|Mount|CLONE_NEWNS|挂载点（文件系统）
|User|CLONE_NEWUSER|用户和用户组

实现轻量级虚拟化（容器）服务。在同一个namespace下的进程可以感知彼此的变化，而对外界的进程一无所知。这样就可以让容器中的进程产生错觉，仿佛自己置身于一个独立的系统环境中，以此达到独立和隔离的目的。

---
**`六种namespace具体介绍 `**

**UTS namespace**
提供了主机名和域名的隔离，这样每个容器就可以拥有了独立的主机名和域名，在网络上可以被视作一个独立的节点而非宿主机上的一个进程。Docker中，每个镜像基本都以自己所提供的服务命名了自己的hostname而没有对宿主机产生任何影响，用的就是这个原理。

**IPC**
容器中进程间通信采用的方法包括常见的信号量、消息队列和共享内存。然而与虚拟机不同的是，容器内部进程间通信对宿主机来说，实际上是具有相同PID namespace中的进程间通信，因此需要一个唯一的标识符来进行区别。申请IPC资源就申请了这样一个全局唯一的32位ID，所以IPC namespace中实际上包含了系统IPC标识符以及实现POSIX消息队列的文件系统。在同一个IPC namespace下的进程彼此可见，而与其他的IPC namespace下的进程则互相不可见。目前使用IPC namespace机制的系统不多，其中比较有名的有PostgreSQL。Docker本身通过socket或tcp进行通信

**PID namespace**
隔离非常实用，它对进程PID重新标号，即两个不同namespace下的进程可以有同一个PID。每个PID namespace都有自己的计数程序。内核为所有的PID namespace维护了一个树状结构，最顶层的是系统初始时创建的，我们称之为root namespace。他创建的新PID namespace就称之为child namespace（树的子节点），而原先的PID namespace就是新创建的PID namespace的parent namespace（树的父节点）。通过这种方式，不同的PID namespaces会形成一个等级体系。所属的父节点可以看到子节点中的进程，并可以通过信号等方式对子节点中的进程产生影响。反过来，子节点不能看到父节点PID namespace中的任何内容。由此产生如下结论
[部分内容引自](http://blog.dotcloud.com/under-the-hood-linux-kernels-on-dotcloud-part)
	
* 每个PID namespace中的第一个进程“PID 1“，都会像传统Linux中的init进程一样拥有特权，起特殊作用。
* 一个namespace中的进程，不可能通过kill或ptrace影响父节点或者兄弟节点中的进程，因为其他节点的PID在这个namespace中没有任何意义。
* 如果你在新的PID namespace中重新挂载/proc文件系统，会发现其下只显示同属一个PID namespace中的其他进程。
* 在root namespace中可以看到所有的进程，并且递归包含所有子节点中的进程。

在外部监控Docker中运行程序的方法：监控Docker Daemon所在的PID namespace下的所有进程即其子进程，再进行删选即可
附：Docker Daemon是Docker架构中运行在后台的守护进程，大致可以分为Docker Server、Engine和Job三部分。Docker Daemon可以认为是通过Docker Server模块接受Docker Client的请求，并在Engine中处理请求，然后根据请求类型，创建出指定的Job并运行，运行过程的作用有以下几种可能：向Docker Registry获取镜像，通过graphdriver执行容器镜像的本地化操作，通过networkdriver执行容器网络环境的配置，通过execdriver执行容器内部运行的执行工作等。
[docker Daemon](http://www.infoq.com/cn/articles/docker-source-code-analysis-part3/)


**Mount namespace**
通过隔离文件系统挂载点对隔离文件系统提供支持，它是历史上第一个Linux namespace，所以它的标识位比较特殊，就是CLONE_NEWNS。隔离后，不同mount namespace中的文件结构发生变化也互不影响。你可以通过/proc/[pid]/mounts查看到所有挂载在当前namespace中的文件系统，还可以通过/proc/[pid]/mountstats看到mount namespace中文件设备的统计信息，包括挂载文件的名字、文件系统类型、挂载位置等等。
进程在创建mount namespace时，会把当前的文件结构复制给新的namespace。新namespace中的所有mount操作都只影响自身的文件系统，而对外界不会产生任何影响。这样做非常严格地实现了隔离，但是某些情况可能并不适用。比如父节点namespace中的进程挂载了一张CD-ROM，这时子节点namespace拷贝的目录结构就无法自动挂载上这张CD-ROM，因为这种操作会影响到父节点的文件系统。
挂载状态：共享挂载（shared）、从属挂载（slave）、共享/从属挂载（shared and slave）、私有挂载（private）、不可绑定挂载（unbindable）

**Network namespace**
Network namespace主要提供了关于网络资源的隔离，包括网络设备、IPv4和IPv6协议栈、IP路由表、防火墙、/proc/net目录、/sys/class/net目录、端口（socket）等等。一个物理的网络设备最多存在在一个network namespace中，你可以通过创建veth pair（虚拟网络设备对：有两端，类似管道，如果数据从一端传入另一端也能接收到，反之亦然）在不同的network namespace间创建通道，以此达到通信的目的。

**User namespaces**
User namespace主要隔离了安全相关的标识符（identifiers）和属性（attributes），包括用户ID、用户组ID、root目录、key（指密钥）以及特殊权限。说得通俗一点，一个普通用户的进程通过clone()创建的新进程在新user namespace中可以拥有不同的用户和用户组。这意味着一个进程在容器外属于一个没有特权的普通用户，但是他创建的容器进程却属于拥有所有权限的超级用户，这个技术为容器提供了极大的自由。

[参考资料来源](http://www.infoq.com/cn/articles/docker-kernel-knowledge-namespace-resource-isolation)