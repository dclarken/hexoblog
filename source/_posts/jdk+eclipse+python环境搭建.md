---
title: jdk+eclipse+python环境搭建
date: 2017-8-15 11:31:21
tags: [笔记,python]
categories: [笔记,python]
---
jdk下载：（java环境配置）
[jdk下载地址](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
环境变量配置
首先设置JAVA_HOME,点击新建，变量名：JAVA_HOME,变量值：D:\java\jdk1.7.0,即jdk安装的路径。
设置CLASSPATH属性，变量名：CLASSPATH，变量值：.;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar;此时需要注意的是最前有.;，不能忘记，%JAVA_HOME%代表D:\java\jdk1.7.0此路径。
设置path属性，变量名：path，变量值：%java_home%\bin;%java_home%\jre\bin;，此属性一般都是有的，只需添加即可，注意分号的问题。
在cmd中输入命令java检测安装是成功
ps：%SystemRoot%\system32;%SystemRoot%;%SystemRoot%\System32\Wbem;环境变量原本的文件内容表示的dos系统下cmd的解释器，在环境变量配置时，定义的环境变量文件名不区分大小写path=PATH。
eclipse：
[eclipse下载地址](http://www.eclipse.org/downloads/packages/eclipse-ide-java-developers/heliossr1/)
选择的下载版本为一个zip而不是官网的exe文件，官网的文件还需要后续联网下载，网络不稳定容易失败，zip文件解压缩之后既可以使用。
python插件：
1.
打开eclipse——Help——add-PyDev-http://pydev.org/updates——PyDev——PyDev for eclipse——下一步——重启——建立Python工程
上述过程同样需要上境外网络，连接VPN实施。
或者参考：
2.
[python插件下载地址](https://jingyan.baidu.com/article/cd4c2979101f02756f6e6064.html)
[pyDev包下载地址](http://sourceforge.net/projects/pydev/files/pydev/PyDev%204.1.0/)下载PyDev 4.1.0.zip包，
把压缩包里面的plugins中的文件解压到Eclipse安装目录下plugins文件夹中，压缩包里面features中的文件目录也是同样操作。之后重启Eclipse。
检查是否已经正确安装pydev：打开Eclipse–>Windows–>preferences就能找到Pydev。
配置解释器。官网下载Python27或者Python34
选择Window > Preferences > Pydev > Python Interpreter>New ，继续配置解释器：Python安装在F:\Python27 路径下。单击 New，进入对话框。Interpreter Name可以随便命名，Interpreter Executable选择Python解释器python.exe，在安装文件夹下查找。而后下一步下一步。
建立Python工程：
工程建立：file-new-project-PyDev-PyDev Project-命名-finish
文件建立：项目名右键-new-PyDev Module（.py文件）-编写-运行Python Run