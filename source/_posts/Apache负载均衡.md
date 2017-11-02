---
title: Apache负载均衡
date: 2017-10-12 00:08:12
tags: [笔记,Linux,apache]
categories: [笔记,Linux]
comments: false
---
![mahua](https://i.loli.net/2017/10/30/59f67be7cdb8c.jpg)
<!--more-->

## 配置文件

* 轮询均衡策略的配置

```vim
ProxyPass /balancer://proxy/      #注意这里以"/"结尾
<Proxy balancer://proxy>
      BalancerMember http://192.168.6.37:6888/
      BalancerMember http://192.168.6.38:6888/
</Proxy>
```

* 按权重分配均衡策略的配置

ProxyPass /balancer://proxy/       ProxyPass / balancer://proxy/协议地址可以随便定义。
ProxyPass指令允许你将一个远端服务器映射到本地服务器的URL空间中，此时本地服务器并不充当代理角色，而是充当远程服务器的一个镜像。path是一个本地虚拟路径名，url是一个指向远程服务器的部分URL，并且不允许包含查询字符串。
[ProxyPass文档](http://www.jinbuguo.com/apache/menu22/mod/mod_proxy.html#proxypass)

```vim
<Proxy balancer://proxy>
       BalancerMember http://192.168.6.37:6888/ loadfactor=3
       BalancerMember http://192.168.6.38:6888/ loadfactor=1
</Proxy>
```

参数”loadfactor”表示后台服务器负载到由Apache发送请求的权值,该值默认为1，可以将该值设置为1到100之间的任何值。
* 权重请求响应负载均衡策略的配置

```vim
ProxyPass / balancer://proxy/ lbmethod=bytraffic 理解为LB的参数是流量
<Proxy balancer://proxy>
        BalancerMember http://192.168.6.37:6888/ loadfactor=3
        BalancerMember http://192.168.6.38:6888/ loadfactor=1
</Proxy>
```
 
参数“lbmethod=bytraffic”表示后台服务器负载请求和响应的字节数，处理字节数的多少是以权值的方式来表示的。
(1)均衡配置，
(2)是以请求数作为权重负载均衡的，
(3)是以流量为权重负载均衡的，这是最大的区别。   
lbmethod表示负载均衡的算法,lbmethod可能的取值有：
 
* lbmethod=byrequests 按照请求次数均衡(默认)                 
* lbmethod=bytraffic 按照流量均衡，权重表示处理的比特流数据的权重
* lbmethod=bybusyness 按照繁忙程度均衡(总是分配给活跃请求数最少的服务器)

httpd.conf 配置文件分为3个部分

Httpd.conf文件的路径：/etc/httpd/conf/httpd.conf或者/usr/local/apache2/conf/httpd.conf分别对应的是RPM包安装和源码包安装两种安装方式的配置文件位置
第一部分：服务器全局环境配置
第二部分：本地服务器响应外部请求的处理方式的配置
第三部分：虚拟主机的配置

## 主要配置过程

**1、查看是否有mod_proxy_http.so、mod_proxy_balancer.so、mod_proxy.so三个模块**

```vim
$ll /usr/local/apache2/modules
```

mod_proxy
是一种分工合作的的形式，通过主服务器跳转到各台主机负责不同的任务而实现任务分工，这种形式不能实现负载均衡，只能提供主服务器的访问跳转
[Apache模块mod_proxy文档](http://www.jinbuguo.com/apache/menu22/mod/mod_proxy.html)
Apache的代理功能(除mod_proxy以外)被划分到了几个不同的模块中：mod_proxy_http, mod_proxy_ftp, mod_proxy_ajp, mod_proxy_balancer, mod_proxy_connect 。这样，如果想使用一个或多个代理功能，就必须将mod_proxy和对应的模块同时加载到服务器中(静态连接或用LoadModule动态加载)。
mod_proxy_balancer
是mod_proxy的扩展，提供负载平衡支持，通过mod_proxy_balancer.so包实现负载平衡，公司生产服务器暂时就采用这种方式，粗略查看该文档发现，apache的balan
cer模块支持论询方式的负载均衡。
[apache模块mod_proxy_balancer文档](http://www.jinbuguo.com/apache/menu22/mod/mod_proxy_balancer.html)

**2、加载模块**

apache模块安装：来自 <http://unixboy.iteye.com/blog/577981> 该模块可以是apache自己所拥有的模块 ，也可以是第三方模块。
加载第三方模块的方法：

* 编译并安装已发布的Apache模块或者第三方模块，比如编译mod_foo.c为mod_foo.so的DSO模块，利用GCC编译的相关知识如：./configure  、 make install等
* 配置Apache以便以后安装共享模块
* 用apxs在Apache源码树以外编译并安装第三方模块，如：/apxs -c -i
* 共享模块编译完毕后，必须在httpd.conf中用LoadModule指令使Apache启用该模块，如： LoadModule proxy_module

[Apxs-Apache扩展工具](http://man.chinaunix.net/newsoft/ApacheMenual_CN_2.2new/programs/apxs.html)
[Apache DSO模块原理](http://blog.chinaunix.net/uid-20773865-id-113909.html)

apxs是一个为Apache HTTP服务器编译和安装扩展模块的工具，用于编译一个或多个源程序或目标代码文件为动态共享对象，使之可以用由mod_so提供的LoadModule指令在运行时加载到Apache服务器中。
DSO究竟是什么？事实上DSO是Dynamic SharedObjects（动态共享目标）的缩写，它是现代Unix派生出来的操作系统都存在着的一种动态连接机制。它提供了一种在运行时将特殊格式的代码，在程序运行需要时，将需要的部分从外存调入内存执行的方法。
-c：属于 DSO编译选项
此选项表示需要执行编译操作。它首先会编译C源程序(.c)files为对应的目标代码文件(.o)，然后连接这些目标代码和files中其余的目标代码文件(.o和.a)，以生成动态共享对象dsofile 。如果没有指定 -o 选项，则此输出文件名由files中的第一个文件名推测得到，也就是默认为mod_name.so 。
-i：DSO的安装和配置选项
此选项表示需要执行安装操作，以安装一个或多个动态共享对象到服务器的modules目录中。

```vim
# cd /usr/local/src/httpd-2.2.23/modules/proxy/
# /usr/local/apache2/bin/apxs -c -i mod_proxy.c proxy_util.c
# /usr/local/apache2/bin/apxs -c -i mod_proxy_balancer.c
# /usr/local/apache2/bin/apxs -c -i mod_proxy_http.c
```

**3、修改http.conf配置文件**

```vim
# vim /usr/local/apache2/conf/httpd.conf
 LoadModule proxy_module    modules/mod_proxy.so
 LoadModule proxy_connect_module modules/mod_proxy_connect.so
 LoadModule proxy_balancer_module modules/mod_proxy_balancer.so
 LoadModule proxy_http_module  modules/mod_proxy_http.so
 ProxyRequests Off
 <Proxy balancer://clusterphpinfo>
 BalancerMember http://10.0.2.203:80 loadfactor=1;BalancerMember 及其后面的URL表示要配置的后台服务器、test表示该服务器下的项目名称
 BalancerMember http://10.0.2.204:80 loadfactor=1;参数”loadfactor”表示后台服务器负载到由Apache发送请求的权值,该值默认为1
 </Proxy>
 ProxyPass / balancer://clusterphpinfo  用于做反向代理的配置，一般开启ProxyPass，ProxyRequests应该设置为off，即为关闭正向代理请求。
```

**4、重启httpd服务**

```vim
# service httpd restart或者利用apachectl控制台操作# apachectl httpd restart
```

**5、通过apache将后端服务器做出主从,只需要在从服务器后面添加status=+H**

```vim
# vim /usr/local/apache2/conf/httpd.conf
 BalancerMember http://10.0.2.204:80 loadfactor=1 status=+H
```
 热备份(Hot Standby) ：热备份的实现很简单，只需添加 status=+H 属性，就可以把某台服务器指定为备份服务器：请求总是流向服务器，一旦服务器挂掉，Apache会检测到错误并把请求分流给备份服务器。Apache会每隔几分钟检测一下原服务器的状况，如果原服务器恢复，就继续使用原服务器。

 参考资料：
[Apache负载均衡的实现](http://www.linuxidc.com/Linux/2014-09/106581.htm)
[Apache负载均衡之Apache Proxy方式](http://blog.csdn.net/navy_xue/article/details/39030879)
Apache负载均衡策略（轮询方式）
[Apache策略原则](http://blog.csdn.net/cnyygj/article/details/53352699)
[apache模块mod_proxy_balancer文档](<http://www.jinbuguo.com/apache/menu22/mod/mod_proxy_balancer.html)

