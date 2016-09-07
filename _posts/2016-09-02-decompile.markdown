---
layout:     post
title:      "Android 反编译"
subtitle:   "\"APK decompile\""
date:       2016-09-03 14:00:00
author:     "石云飞"
header-img: "img/decompile/decompile.jpg"
catalog: true
tags:
    - 生活
---




> "I changed who i was for you"


> "But you didn't."

---

## 引言
Android开发，我们常常会看到一些别人开发的apk，被其精美的界面、漂亮的动画或者特殊的功能多吸引，这时候就想了解这些功能的实现过程，那么反编译就可以帮助到我们
（注：反编译不是让各位开发者去对一个应用破解搞重装什么的，主要目的是为了促进开发者学习，借鉴好的代码，提升自我开发水平。）

---

## 1.准备工具

反编译一般要使用到三个工具分别为apktool 、dex2jar、jd-gui 

apktool为google官方提供的Android反编译、回编译工具，能将apk回编译成Smali文件，布局文件（XML等）可生成明文格式，通过该工具可直接查看布局文件动画、配置文件等。

apktool下载[链接](https://code.google.com/p/android-apktool/downloads/list)（需翻墙） ，csdn下载[链接](http://download.csdn.net/detail/vipzjyno1/7025111)


dex2jar为将Android虚拟机运行的classes.dex文件转换为jar文件的工具，如果我们不满足于只查看布局，更想了解目标apk代码，可使用该工具

dex2jar下载[链接](http://code.google.com/p/dex2jar/downloads/list)（需翻墙）,csdn下载[链接](http://download.csdn.net/detail/vipzjyno1/7025127)

jd-gui可以查看dex2jar生成的jar文件即源码class文件

jd-gui 下载[链接](http://java.decompiler.free.fr/?q=jdgui),csdn下载[链接](http://download.csdn.net/detail/vipzjyno1/7025145)

---
	
## 2.工具使用

### 1. 查看布局文件
当我们对目标apk文件的布局有兴趣的时候，我们使用apktool即可获取想要的文件，首先将下载好的apktool解压会得到三个文件：aapt.exe，apktool.bat，apktool.jar 

<a href="#">
    <img src="{{ site.baseurl }}/img/decompile/decompile1.jpg" alt="Post Sample Image">
</a>

将我们要反编译的apk文件放到该目录下，如上图的Chubao.apk,再使用cmd进入shell，切换到解压文件所在目录，如我的解压文件在E:\fanbianyi\apktool

<a href="#">
    <img src="{{ site.baseurl }}/img/decompile/decompile2.jpg" alt="Post Sample Image">
</a>

再执行命令apktool d Chubao.apk（此处我反编译的文件为Chubao.apk）

<a href="#">
    <img src="{{ site.baseurl }}/img/decompile/decompile3.jpg" alt="Post Sample Image">
</a>

执行之后在apktool根目录下看到一个文件夹（此处我文件名为chubao），该文件夹res中就可以看到布局文件


<a href="#">
    <img src="{{ site.baseurl }}/img/decompile/decompile4.jpg" alt="Post Sample Image">
</a>

---

### 2.查看class文件

如果想了解目标apk的class文件，先将apk解压可得到class.dex，也可以直接把apk文件名称改成.zip文件，再解压或者打开改.zip文件，可获取class.dex，

<a href="#">
    <img src="{{ site.baseurl }}/img/decompile/decompile5.jpg" alt="Post Sample Image">
</a>

得到class.dex文件拖到dex2jar工具解压的根目录下，执行命令

	dex2jar.bat   classes.dex
	
会生成classes_dex2jar.jar的文件（这个就是apk的class文件打包的jar文件），然后打开工具jd-gui文件夹里的jd-gui.exe，之后用该工具打开之前生成的classes_dex2jar.jar文件，便可以看到源码了，效果如下：


<a href="#">
    <img src="{{ site.baseurl }}/img/decompile/decompile6.jpg" alt="Post Sample Image">
</a>

此处我反编译的是lingjimiaosuan.apk.

注：现在网上有一些工具可以一键反编译，有兴趣的同学可以找找看，我下过一个但是用起来经常崩溃。另外需说明的是反编译得到的class文件不是100%还原，很多类名方法名都不可见，而且一些app经过加固反编译可能不会很顺利！




---

<a href="#">
    <img src="{{ site.baseurl }}/img/decompile/decompile_end.jpg" alt="Post Sample Image">
</a>

---
如果你恰好逛到了这里，希望你也能喜欢这个博客主题。

—— SYF 后记于 2016.8.18


