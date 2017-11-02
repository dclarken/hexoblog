---
title: 基于Hexo和Github的博客搭建
date: 2017-10-19 20:45:44
tags: [博客,npm,hexo,github]
categories: [博客,hexo]
---
![封面](https://i.loli.net/2017/10/20/59e98c20af367.png)
<!--more-->
## 搭建原理

`github pages` 
github是项目托管网站，列出了项目的源文件，github有一个pages功能，可以自定义主页，用来代替默认的列出源列表的这个页面。所以，github Pages可以被认为是用户编写的、托管在github上的静态网页。对于个人博客的主页页面内容可以位于 master 下。user pages只有一个, project pages可以有多个, 对于个人博客而言, 两种方式都可以.如果用户申请了自己的域名, 还可以使用CNAME文件自定义domain name,这样访问你的域名就自动访问到github上的页面. 用户也可以自定义404页面.
`Hexo`
Hexo是一个快速、简洁并且高效的博客框架。Hexo使用Markdown解析文章，还可以使用期主题生成静态网页

---
## 搭建步骤
大概可以分为以下几个步骤：

* 搭建环境准备（包括node.js和git环境，gitHub账户的配置）
* 安装Hexo
* 配置Hexo
* 怎样将Hexo与github page联系起来
* 怎样发布文章
* 主题 推荐
* 主题Next的配置
* 添加404 公益页面

## 环境准备
配置Node.js安装环境：JavaScript工具，前端架构，编写出可扩展性高的服务器

* 安装Online documentation shortcuts模块
* 新打开的窗口中输入cmd，敲击回车，打开命令行界面
* 查看安装的是否成功
```vim
node -v npm -v
```
配置Git环境:Git工具可以在windows系统中的任意文件下进入命令行界面，可以建立Hexo和Github的连接

* 安装Online documentation shortcuts模块
* 查看安装是否成功
```vim
git --version
```
github账号的配置

* 新建一个repository，命名为hexoblog
* 在settings页面中的Github Pages中开启gh pages功能，记录网址可以用于后续配置
安装Hexo

* 新建一个目录并且创建Hexo文件夹，用于安装Hexo，并在命令行的窗口进入到该目录
* 命令行输入:安装hexo以及查看是否安装
```vim
npm install hexo-cli -g
npm install hexo --save
hexo -v
```
Hexo的体验

* 初始化
```vim
hexo init
```
* 安装npm组件
```vim
npm install
```
* 生成
```vim
hexo g
```
* 开启端口
```vim
hexo s
```
提示，在浏览器中输入以后网址既可以访问hexo界面
```vim
INFO Hexo is running at http://0.0.0.0:4000/. Press Ctrl+C to stop.
```

---
## 将hexo和github相连
配置Git个人信息

* 设置Git的user.name和user.email
```vim
git config --global user.name "Lander"
git config --global user.email "dclarken@163.com"
```
* 生成秘钥，生成的秘钥要使用需要配置为github上的安全ssh秘钥
[Git ssh 配置使用](http://blog.csdn.net/gdutxiaoxu/article/details/53573399)
```vim
ssh-keygen -t rsa -C "dclarken@163.com"
```
配置Deployment

* 在hexo安装目录下找到根配置文件_config.yml文件，找到Deployment句段
```vim
deploy:
  type: git
  repo: repo: git@github.com:dclarken/hexoblog.git
  branch: master
```
git插件安装

* 提前安装一个扩展
```vim
npm install hexo-deployer-git --save
```

---
## 主题配置以及文章发布
Next主题配置使用

* 安装Next，利用Git工具将下载的主题文件拷贝在themes目录下
```vim
cd your-hexo-site
git clone https://github.com/iissnan/hexo-theme-next themes/next
```
* 启用主题，切换主题一般可以用hexo clean来清空缓存
```vim
theme: next
```
* 验证主题，生成端口，访问网址观察是否成功配置
```vim
hexo s –debug
```
* 主题设定
[Next主题设定资料地址](http://theme-next.iissnan.com)
* 添加文章，添加新的文章在source文件夹下的post文件中，文件名为引号中的字符
```vim
hexo new post "article title"
```
文件格式为markdown格式，初始生成的文件内容如下：
```vim
---
title: article title
date: 2017-8-12 11:31:21
tags: [随笔,freinds]
categories: [随笔]
---
```
* 添加标签页面，如果不添加该页面在主题config配置文件中，添加的tags会指向404界面
```vim
cd your-hexo-site
hexo new page tags
```
文件补充为以下格式：添加type字段，comments字段可以关闭评论
```vim
title: 标签
date: 2017-8-12 12:39:04
type: "tags"
comments: false
```
* 添加分类页面
```vim
cd your-hexo-site
hexo new page categories
```
文件补充以下格式：
```vim
title: 分类
date: 2017-8-12 12:39:04
type: "categories"
comments: false
```

---

搭建过程参考资料：[参考文章地址](http://blog.csdn.net/gdutxiaoxu/article/details/53576018)
致谢作者