---
title: Docker内核知识——AUFS及安全性
date: 2017-9-14 00:08:12
tags: [笔记,docker,内核]
categories: [笔记,docker]
comments: false
---

`关于 AUFS（联合文件系统） 的几个特点：`

* AUFS 是一种联合文件系统，它把若干目录按照顺序和权限 mount 为一个目录并呈现出来
* 默认情况下，只有第一层（第一个目录）是可写的，其余层是只读的。
* 增加文件：默认情况下，新增的文件都会被放在最上面的可写层中。
* 删除文件：因为底下各层都是只读的，当需要删除这些层中的文件时，AUFS 使用 whiteout 机制，它的实现是通过在上层的可写的目录下建立对应的whiteout隐藏文件来实现的。
* 修改文件：AUFS 利用其 CoW （copy-on-write）特性来修改只读层中的文件。AUFS 工作在文件层面，因此，只要有对只读层中的文件做修改，不管修改数据的量的多少，在第一次修改时，文件都会被拷贝到可写层然后再被修改。
* 节省空间：AUFS 的 CoW 特性能够允许在多个容器之间共享分层，从而减少物理空间占用。
* 查找文件：AUFS 的查找性能在层数非常多时会出现下降，层数越多，查找性能越低，因此，在制作 Docker 镜像时要注意层数不要太多。
* 性能：AUFS 的 CoW 特性在写入大型文件时第一次会出现延迟。

`一个Linux 系统之中`

* 所有 Docker 容器都共享主机系统的 bootfs 即 Linux 内核
* 每个容器有自己的 rootfs，它来自不同的 Linux 发行版的基础镜像，包括 Ubuntu，Debian 和 SUSE 等
* 在操作系统镜像上面一层层附加应用程序的开源组件的镜像
* 所有基于一种基础镜像的容器都共享这种 rootfs

![tu](https://i.loli.net/2017/10/26/59f168e853994.png)

`安全性: `

AppArmor, SELinux, GRSEC
安全永远是相对的，这里有三个方面可以考虑Docker的安全特性:

* 由kernel namespaces和cgroups实现的Linux系统固有的安全标准;
* Docker Deamon的安全接口;
* Linux本身的安全加固解决方案,类如AppArmor, SELinux;

资料参考：
[内核知识资料](http://www.infoq.com/cn/articles/docker-core-technology-preview/)
[AUFS](http://www.cnblogs.com/sammyliu/p/5931383.html)
[安全性](http://www.infoq.com/cn/articles/docker-core-technology-preview/)
