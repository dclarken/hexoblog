---
title: ubuntu下安装dockers
date: 2017-9-14 11:31:21
tags: [笔记,docker]
categories: [笔记,docker]
---

[安装文档资料](http://www.linuxidc.com/Linux/2015-04/116051.htm)
[dockers安装troubleshooting资料](http://blog.csdn.net/quuqu/article/details/51488966)
**`内核变更`**
docker再Linux的kernel3.8上运行最佳。
利用 uname -r命令查看当前系统版本的内核，内核版本不满足要求，升级内核：
```vim
sudo apt-get install linux-image-generic-lts-raring linux-headers-generic-lts-raringsudo apt-get install --install-recommends linux-generic-lts-raring xserver-xorg-lts-raring libgl1-mesa-glx-lts-raring
~sudo reboot 
```
重启ubuntu

**`更新apt-get的安装源`**
```vim
root@linuxidc:~# vi /etc/apt/sources.list
```
是包管理工具 apt 所用的记录软件包仓库位置的配置文件
[/etc/apt/sources.list 详解](http://blog.csdn.net/gong_xucheng/article/details/53886271)
删除里面的所有内容，把下面的安装源写入

```vim
deb http://mirrors.aliyun.com/ubuntu/ trusty main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ trusty-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ trusty-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ trusty-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ trusty-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ trusty main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ trusty-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ trusty-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ trusty-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ trusty-backports main restricted universe multiverse
```

**`然后更新apt-get，利用apt-get安装docker.io`**
```vim
root@linuxidc:~#apt-get update
root@linuxidc:~#apt-get install docker.io
root@linuxidc:~# service docker.io restart或者 service docker start
```

错误信息 
●报错1：
```vim
FATA[0000] Post http:///var/run/docker.sock/v1.18/images/create?fromImage=wpscanteam%2Fvulnerablewordpress%3Alatest: dial unix /var/run/docker.sock: no such file or directory. Are you trying to connect to a TLS-enabled daemon without TLS?
```
解决办法：
```vim
$ sudo touch /var/run/docker.sock
$ sudo docker -d
```

touch命令有两个功能：一是用于把已存在文件的时间标签更新为系统当前的时间（默认方式），它们的数据将原封不动地保留下来；二是用来创建新的空文件。
[文档资料1](http://man.linuxde.net/touch)
~docker -d :Run container in background and print container ID 运行容量并且打印容器ID
[文档资料2](http://www.cnblogs.com/go2bed/p/5703117.html)


●报错2：
```vim
INFO[0000] +job serveapi(unix:///var/run/docker.sock) 
INFO[0000] Listening for HTTP on unix (/var/run/docker.sock) 
INFO[0000] +job init_networkdriver() 
INFO[0000] -job init_networkdriver() = OK (0) 
WARN[0000] Your kernel does not support cgroup swap limit. 
FATA[0000] Shutting down daemon due to errors: Error loading docker apparmor profile: fork/exec /sbin/apparmor_parser: no such file
```

解决办法：
```vim
$ sudo apt-get install apparmor
```
Apparmor---Linux内核中的强制访问控制系统，是Linux内核的一个安全模块，AppArmor允许系统管理员将每个程序与一个安全配置文件关联，从而限制程序的功能。简单的说，AppArmor是与SELinux类似的一个访问控制系统，通过它你可以指定程序可以读、写或运行哪些文件，是否可以打开网络端口等。作为对传统Unix的自主访问控制模块的补充，AppArmor提供了强制访问控制机制，它已经被整合到2.6版本的Linux内核中。
[Apparmor](http://www.cnblogs.com/-Lei/archive/2013/02/24/2923947.html)
