---
title: Git基础教程
date: 2017-10-20 00:08:12
tags: [笔记,git]
categories: [笔记,git]
comments: false
---
![tu](https://i.loli.net/2017/10/27/59f2a5d1d9fee.jpg)
<!--more-->
## 新建一个仓库添加本地上传服务：
**现在的情景:**
你已经在本地创建了一个Git仓库后，又想在GitHub创建一个Git仓库，并且让这两个仓库进行远程同步，这样，GitHub上的仓库既可以作为备份，又可以让其他人通过该仓库来协作，真是一举多得。
对于新创建的仓库，从本地做备份到github网站上gitbash的代码段如下：

```vim
$ echo "# learngit.github.io" >> README.md
$ git init
$ git add README.md
$ git commit -m "first commit"
$ git remote add origin git@github.com:dclarken/dai.github.io.git
$ git push -u origin master
```

**操作解释：**

* 新建一个README文件；
* 初始化本地文件得到一个.git的隐藏文件，用于追踪地位；
* 添加README文件到缓存区；
* 给刚刚添加的文件记录cmmit信息，便于以后管理，并且通过该操作将缓存区的文件上传到master分支上；
* 在本地远程建立与github仓库的连接；
* 利用push将上传到master的文件发布到github网站上；

**error:src refspec master does not match any**

**原因：**
引起该错误的原因是，目录中没有文件，空目录是不能提交上去的

**解决方法：**

```vim
$ touch README
$ git add README 
$ git commit -m 'first commit'
$ git push origin master
```

---
## 本地和远程仓库建立连接之后，如何添加和修改文件：

```vim
$ git add "filename"          添加文件到缓存区 ；
$ git commit -m "add/update…."给刚刚添加的文件记录cmmit信息；
$ git push                    利用push将上传到master的文件发布到github网站上；
```

---
## 本地删除文件并且同步到仓库上：

```vim
$ git rm filename
$ git commit -m "remove filename"
$ git push
```
对于新建和删除修改文件的小结：
这些操作都需要三个步骤：

* 修改/删除/新建
* 添加commit
* git push上传到github

另外：
git pull --rebase 从github上下拉到本地，进行同步工作，加上--rebase可以避免跳入merge commit窗口，从而导致的无用记录,一般来说在网页上直接操作即使没有添加commit也会自动添加，但是为了记录好看还是养成加--rebase的习惯吧
[聊下git pull --rebase](<http://www.cnblogs.com/wangiqngpei557/p/6056624.html>)
git status可以查看当前的修改情况
git checkout -- file 让这个文件回到最近一次git commit或git add时的状态。
git reset HEAD file可以把暂存区的修改撤销掉（unstage），重新放回工作区

---
## 将一个已有的github仓库clone到新创建的learngit以及本地仓库中：

* 在github上创建项目
* 使用 git clone git@github.com:dclarken/dclarken.github.io.git克隆到本地
* 编辑项目
* git add "dclarken.github.io/"
* git commit -m "clone dclarken.github.io/ "
* git push origin master 将本地更改推送到远程master分支。

如果在github的remote上已经有了文件，会出现错误。此时应当先pull一下，即：
git pull origin master

---
## 上传时候的某报错：

```vim
To github.com:dclarken/learngit.git
 ! [rejected]        master -> master (non-fast-forward)
error: failed to push some refs to 'git@github.com:dclarken/learngit.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Integrate the remote changes (e.g.
hint: 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

**原因：** 
GitHub远程仓库中的README.md文件不在本地仓库中。

**解决方案：**

```vim
$ git pull --rebase origin master
$ git push -u origin master
```

若此时又出现报错或者在平时也有这种报错：

```vim
Git Cannot rebase: You have unstaged changes.
```

**原因：**
那说明有修改过的文件

**解决方案：**

```vim
$ git stash
$ git pull --rebase （每次push之前最好这样做一次）
$ git push origin master
```

之后用git stash pop stash

**解析：**

git stash 可用来暂存当前正在进行的工作， 比如想pull 最新代码， 又不想加新commit， 或者另外一种情况，为了fix 一个紧急的bug,  先stash, 使返回到自己上一个commit, 改完bug之后再stash pop, 继续原来的工作。所以以前自己在网页上面直接上传代码时，经常不写commit于是在想要添加本地管理的时候，会出现以上报错。

基础命令：

```vim
$ git stash
$ do some work
$ git stash pop
```

---
## 添加一个文件夹并且上传到github：
其实这个和上传文件的原理一样，github支持在上传某个文件夹下的文件的时，将文件夹也上传。所以：

```vim
$ mkdir CodeCut
$ cd CodeCut
$ touch test.txt
$ git add "test.txt"
$ git commit -m " add test.txt"
$ git push
```

## 本地更改目录结构同步到远程仓库：

```vim
$ git mv filefrom dir/  #移动操作
$ …#多次移动操作
$ git add .
$ git commit -m "Modified directory structure"
$ git push origin master
```

文件改名

```vim
$ git mv oldfile newfile  #改名操作
$ …#多次改名操作
$ git add .
$ git commit -m "Change file name"
$ git push origin master
```

[git移动操作资料](http://www.yiibai.com/git/git_move_operation.html)

## hexo博客的迁移

[Hexo博客从一台电脑迁移到其他电脑](http://www.jianshu.com/p/beb8d611340a)
背景：
我们有的时候写博客想要不单单从一台电脑上面，希望多个经常使用的机器都可以随时进行文章的编写工作，或者博客主题的变更修改。
具体的思路是：
在生成的已经推到github上的hexo静态代码出建立一个分支，利用这个分支来管理自己hexo的源文件。
准备工作：
对于其他的机器，node.js和git的安装配置工作，备好一个空的文件夹，由于后续对于隐藏文件.git是通过克隆github的静态页面文件生成，所以不需要hexo init初始化操作，但是还是需要其他的hexo配置过程

```vim
$ npm install hexo-cli -g
$ npm install hexo --save
$ npm install hexo-deployer-git --save
$ hexo -v
$ npm install
```
具体的操作：
克隆gitHub上面生成的静态文件到本地
```vim
$ git clone https://github.com/yourname/hexo-test.github.io.git
```
把克隆到本地的文件除了git的文件都删掉，找不到git的文件的话就到删了吧。不要用hexo init初始化。
将之前使用hexo写博客时候的整个目录（所有文件）搬过来。把该忽略的文件忽略了

```vim
$ touch .gitignore
```

创建一个叫hexo的分支

```vim
git checkout -b hexo
```

提交复制过来的文件到暂存区

```vim
git add --all
```
提交

```vim
git commit -m "新建分支源文件"
```
推送分支到github

```vim
git push --set-upstream origin hexo
```

到这里基本上就搞定了，以后再推就可以首先要git push，然后hexo的操作跟以前一样。如果对于多个机器一般来说一台机器上的博客并不一定是最新的，通过git pull --rebase 获得最新的版本内容，在进行以上操作发布内容，一般如果只是修改一篇文章，需要一次git push。如果需要发布多次文章，则需要add/commit/push这个流程，从而实现多台机器的备份。
问题：在原始的搭建博客机器上，也就是master的本地仓库，如果不先同步，直接在这个上面利用hexo命令做了更改之后，其他机器的改变的内容好像会失效的。
执行之后，在 aero上的两个test文件都被删除了，所以在发博文的时候，大家还是都push吧，毕竟在云端托管的静态文件我们其实并不一定要同步下来，多地云备份的事情再说吧。

资料参考：
[廖学峰官方网站](<https://www.liaoxuefeng.com/>)
[error: failed to push some refs to 'git@github.com:](http://blog.csdn.net/u011471873/article/details/51462871)
[error:Cannot pull with rebase](http://blog.csdn.net/u012385190/article/details/70670213)


