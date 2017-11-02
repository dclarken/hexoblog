---
title: AWS简称及基本架构介绍
date: 2017-8-12 00:08:12
tags: [笔记,AWS,架构]
categories: [笔记,AWS]
comments: false
---
![tu](https://i.loli.net/2017/10/21/59ea9bac76764.png)
[文档教程位置](https://aws.amazon.com/cn/documentation/)

`AWS整体架构`


·VPC：·
私有网络AWS 云中逻辑隔离的虚拟网络。从所选的范围内定义 VPC 的 IP 地址空间
	
* 对VPC的IP寻CIDR块的控制
* 为您的VPC CIDR块划分子网的能力
* 网络访问控制列表
* 多个IP地址和多个弹性网络接口（ENI）的分配
* 通过私有连接将VPC和现场IT基础设施连接

·常见的路由：·
路由表的将决定子网的功能
	
* IGW：提供对internet的访问，公有子网带通过IGW实现进/出站internet连接
* VGW：提供对数据中心的访问，虚拟专用网关
* NAT 网关：一项高度可用的托管网络地址转换 (NAT) 服务，可便于私有子网中的资源访问 Internet，可以在官方AMI上直接启动NAT网关实例，私有子网通过公有子网中运行NAT的Amazon EC2实例实现的出战Internet连接。
* VPC对等：与NACL和安全组一起维护VPC的安全，允许在对等VPC之间路由流量，这样两个VPC中的实例可以彼此通信，就像在同一个网络中一样。两个对等的VPC不能具有CIDR块重叠的CIDR块。

·ELB：·
Elastic Load Balancing 弹性负载均衡，

* 在多个 Amazon EC2 实例之间自动分配应用程序的传入流量，软件负载均衡。
* 在同一区域可用区实例之间实现均衡
* 根据用户定义的运行状况检查检测状况欠佳的实例
* SSL终端支持自动一证书

·EC2：·
Elastic Compute Cloud  一种 Web 服务，云计算服务平台，用于计算相当于一个虚拟机，可以在云中提供安全并且大小可调的计算容量。该服务旨在让开发人员能够更轻松地进行 Web 规模的云计算。让使用者可以租用云端电脑运行所需应用的系统。
类型：C3计算优化、R3内存优化、G2 GPU优化、I2  HS1存储优化

`ENI：`
弹性网络接口，创建的EC2实例具有的虚拟网络接口，具有属性如下：MAC地址、主要私有IP地址，一个或多个次要私有IP地址、每个私有IP地址都有弹性EIP、公有IP地址、安全组、目标检查标记、说明。使用案例：创建管理网络；许可身份验证；在VPC中使用网络和安全性设备

`EIP：`
专为动态云计算设计的静态IP地址，EIP与AWS账号关联，而不是与特定实例关联；而公有IP地址也称为动态IP地址与实例相关。

`IGW：`
整体网关

`ALB：`
Application Load Balancer ALB是位于OSI模型第七层的负载均衡器，因此它能根据网络包的内容将该网络包路由到不同的后端服务。现有的负载均衡器多是位于OSI模型第四层的TCP/UDP均衡器。与这些均衡器不同的是，ALB将检查网络包的内容，并将该网络包发送给适当的服务。当前，ALB支持基于URL对路由流量定义多至十条的独立规则。

`Amazon CloudWatch `
是一项针对 AWS 云资源和在 AWS 上运行的应用程序进行监控的服务。CPU 使用率、数据传输和磁盘使用活动等，弹性负载均衡器、Amazon SQS 队列、Amazon SNS 主题等更多

`Amazon CloudFront`
 是一种全球内容分发网络 (CDN) 服务，用于内容传输，可以安全地以低延迟和高传输速度向浏览者分发数据、视频、应用程序和 API。建或修改现有 AWS CloudFormation 模板。一个描述了您的所有资源及其属性的模板。

 `Amazon Simple Storage Service (Amazon S3)`对象存储，使您能够轻松实用地大规模收集、存储和分析数据，而不管格式如何。S3 是专为从任意位置存储和检索任意大小的数据而构建的对象存储，包括来自网站和移动应用程序、公司应用程序的数据以及来自 IoT 传感器或设备的数据。S3可与CloudFront(CDN)结合使用，可以利用分布式网络给边远地区加速
	
* 一个对象的大小可以达到5TB
* 每个对象存储在存储桶中，通过开发人员分配的唯一密钥进行检索
* 无限的存储、无限的对象
* 原生在线HTTP/S访问
* 通过在同一区域不同可用区间进行复制，持久性
* 面对Internet的对象存储

`Amazon Route 53`
是一种可用性高、可扩展性强的云域名系统 (DNS) Web 服务，用于DNS域名服务。

`IAM：Identity and Access Management`
“身份识别与访问管理”，身份管理

`DMZ`
英文“demilitarized zone”的缩写，中文名称为“隔离区”，也称“非军事化区”。它是为了解决安装防火墙后外部网络的访问用户不能访问内部网络服务器的问题，而设立的一个非安全系统与安全系统之间的缓冲区。该缓冲区位于企业内部网络和外部网络之间的小网络区域内。在这个小网络区域内可以放置一些必须公开的服务器设施，如企业Web服务器、FTP服务器和论坛等。另一方面，通过这样一个DMZ区域，更加有效地保护了内部网络。因为这种网络部署，比起一般的防火墙方案，对来自外网的攻击者来说又多了一道关卡。

`本地存储`
本地存储适合临时存储不断变化的信息（如缓冲区、缓存、暂存数据和其他临时内容），或者在实例机群内复制的数据。

`EBS`
数据块存储 Elastic Block Store，相当于虚拟机硬盘，最大为1TB。实例依靠虚拟网络连接装载，持久性存储，可以重新附加在其他的EC2实例，利用快照实现，一个EC2实例可以有多个EBS卷，但是多个EC2不能共享卷。应用场景：简单硬盘设备；经常更改数据；对原始、未格式化数据块级别存储的访问权。

`EBS快照`
存储在S3中

`RDS`
Amazon Relational Database Service (Amazon RDS) 是一种可让用户在云中轻松设置、操作和扩展关系数据库的 Web 服务。它在管理耗时的数据库管理任务的同时，提供经济实用的可调容量，使您能够腾出时间专注于应用程序和业务。 RDS 让您能够访问非常熟悉的 MySQL、MariaDB、Oracle、Microsoft SQL Server 或 PostgreSQL 数据库引擎的功能。

`AWS Elastic Beanstalk`
是一项易于使用的服务，用于在熟悉的服务器（例如 Apache 、Nginx、Passenger 和 IIS）上部署和扩展使用 Java、.NET、PHP、Node.js、Python、Ruby、GO 和 Docker 开发的 Web 应用程序和服务。上传代码，Elastic Beanstalk 即可自动处理从容量预置、负载均衡、自动扩展到应用程序运行状况监控的部署。同时，您能够完全控制为应用程序提供支持的 AWS 资源，并可随时访问基础资源。

`AWS SQS、SNS、SWF`
消息发送各个工作流用

`Amazon ElastiCache`
缓存

`Amazon Glacier`
存档，将设置了生命周期规则（如90天）的S3存档，成本极低的存档存储服务，能够提供高耐久性存储，极低成本存储，允许在3-5小时内检索数据。可以利用Storage Gateway进行场外备份

`安全性`
SSL加密型终端节点、AES-256自动加密数据、IAM策略指定账户中的哪些用户有权对特定文件库执行操作

`Amazon Simple Email Service`
电子邮件
`Auto Scaling`
自动扩大或缩小Amazon EC2容量；响应需求峰值；当需求下降时，削减不需要的实例以节省费用；根据一天中的时间、按指标或通过API自动扩展

`AMI`
亚马逊系统映像，提供启动实例所需的信息

`AWS Direct Connect`
建立从本地设施到Amazon VPC的专用网络链接，建立AWS和数据中心、办公室或托管环境之间建立私有连接。

`MSDN`
Microsoft Developer Network用来帮助开发人员使用Microsoft产品和技术
