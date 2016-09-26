---
layout:     post
title:      "Framework开发"
subtitle:   "\"android源码编译\""
date:       2016-09-22 14:00:00
author:     "石云飞"
header-img: "img/framework1/framework1.jpg"
catalog: true
tags:
    - 生活
---


> "从S城到W城，有749公里，749000米。你和我之间，是不是比这更远？"

>"Hey,come on."

---

## 引言

android 应用层开发一般都可以满足我们的需求，但有些时候要深入挖掘一些功能在app层很难做到，比如说修改系统ui，把应用加入到系统app等等都需要对系统源码层修改。

下面分享我对系统开发做的一些探索和经验。



---

## 1.环境搭建

---

### 1.安装Ubuntu系统

android源码是基于linux内核编写的，要编译android源码linux环境是必须的，我使用的是Ubuntu 14.04系统，安装Ubuntu过程可参考以前我的[博客](https://mangogoing.github.io/SYF//2016/09/03/Linux/);

	
### 2.安装必要的软件，部署环境

安装软件使用系统命令安装

jdk安装：

	sudo apt-get update
sudo apt-get install openjdk-7-jdk

必要组建安装：

	sudo apt-get install bison 
sudo apt-get install g++-multilib 
sudo apt-get install git gperf libxml2-utils make zlib1g-dev:i386 zip python-networkx 

usb刷机支持：

新建一个文件/etc/udev/rules.d/51-android.rules ，把下面这段文字复制进去，把文字中的username替换成自己虚拟机上的用户名（新建文件可以使用命令行 vim /etc/udev/rules.d/51-android.rules，然后修改权限 chmod 777 /etc/udev/rules.d/51-android.rules ）

	# adb protocol on passion (Nexus One)
	SUBSYSTEM=="usb", ATTR{idVendor}=="18d1", ATTR{idProduct}=="4e12", MODE="0600", OWNER="<username>"
	# fastboot protocol on passion (Nexus One)
	SUBSYSTEM=="usb", ATTR{idVendor}=="0bb4", ATTR{idProduct}=="0fff", MODE="0600", OWNER="<username>"
	# adb protocol on crespo/crespo4g (Nexus S)
	SUBSYSTEM=="usb", ATTR{idVendor}=="18d1", ATTR{idProduct}=="4e22", MODE="0600", OWNER="<username>"
	# fastboot protocol on crespo/crespo4g (Nexus S)
	SUBSYSTEM=="usb", ATTR{idVendor}=="18d1", ATTR{idProduct}=="4e20", MODE="0600", OWNER="<username>"
	# adb protocol on stingray/wingray (Xoom)
	SUBSYSTEM=="usb", ATTR{idVendor}=="22b8", ATTR{idProduct}=="70a9", MODE="0600", OWNER="<username>"
	# fastboot protocol on stingray/wingray (Xoom)
	SUBSYSTEM=="usb", ATTR{idVendor}=="18d1", ATTR{idProduct}=="708c", MODE="0600", OWNER="<username>"
	# adb protocol on maguro/toro (Galaxy Nexus)
	SUBSYSTEM=="usb", ATTR{idVendor}=="04e8", ATTR{idProduct}=="6860", MODE="0600", OWNER="<username>"
	# fastboot protocol on maguro/toro (Galaxy Nexus)
	SUBSYSTEM=="usb", ATTR{idVendor}=="18d1", ATTR{idProduct}=="4e30", MODE="0600", OWNER="<username>"
	# adb protocol on panda (PandaBoard)
	SUBSYSTEM=="usb", ATTR{idVendor}=="0451", ATTR{idProduct}=="d101", MODE="0600", OWNER="<username>"
	# adb protocol on panda (PandaBoard ES)
	SUBSYSTEM=="usb", ATTR{idVendor}=="18d1", ATTR{idProduct}=="d002", MODE="0600", OWNER="<username>"
	# fastboot protocol on panda (PandaBoard)
	SUBSYSTEM=="usb", ATTR{idVendor}=="0451", ATTR{idProduct}=="d022", MODE="0600", OWNER="<username>"
	# usbboot protocol on panda (PandaBoard)
	SUBSYSTEM=="usb", ATTR{idVendor}=="0451", ATTR{idProduct}=="d00f", MODE="0600", OWNER="<username>"
	# usbboot protocol on panda (PandaBoard ES)
	SUBSYSTEM=="usb", ATTR{idVendor}=="0451", ATTR{idProduct}=="d010", MODE="0600", OWNER="<username>"
	# adb protocol on grouper/tilapia (Nexus 7)
	SUBSYSTEM=="usb", ATTR{idVendor}=="18d1", ATTR{idProduct}=="4e42", MODE="0600", OWNER="<username>"
	# fastboot protocol on grouper/tilapia (Nexus 7)
	SUBSYSTEM=="usb", ATTR{idVendor}=="18d1", ATTR{idProduct}=="4e40", MODE="0600", OWNER="<username>"
	# adb protocol on manta (Nexus 10)
	SUBSYSTEM=="usb", ATTR{idVendor}=="18d1", ATTR{idProduct}=="4ee2", MODE="0600", OWNER="<username>"
	# fastboot protocol on manta (Nexus 10)
	SUBSYSTEM=="usb", ATTR{idVendor}=="18d1", ATTR{idProduct}=="4ee0", MODE="0600", OWNER="<username>"

安装usb开发包：

	apt-get install libusb-dev 
	
配置rules：
	
	sudo gedit /etc/udev/rules.d/20-mm-blacklist-mtk.rules
	
## 2.源码下载

android源码下载需要翻墙，当然国内也有一些网盘什么的可以直接下载到。


## 3.刷机工具

刷机工具是用来将编译好的源码刷到手机上的工具，此处我获得的一份源码是MTK提供的，所以对应的刷机工具为FlashTool（可以百度下载）。



## 4.编译

获取到源码后，在源码根目录下执行：

	source build/envsetup.sh
	
	
---
<a href="#">
    <img src="{{ site.baseurl }}/img/framework1/envsetup.jpg" alt="Post Sample Image">
</a>

---

该命令为加载envsetup.sh脚本，该文件作用主要是：

1. 加载了编译时使用到的函数命令，如：help，lunch，m，mm，mmm等
	
2. 添加了两个编译选项：generic-eng和simulator，这两个选项是系统默认选项

3. 查找vendor/<-厂商目录>/和vendor/<厂商目录>/build/目录下的vendorsetup.sh，如果存在的话，加载执行它，添加厂商自己定义产品的编译选项

	
然后执行lunch选择平台选项，可按照自己需求选择user、debug、eng等不同版本类型。

---
<a href="#">
    <img src="{{ site.baseurl }}/img/framework1/lunch.jpg" alt="Post Sample Image">
</a>

---

接下来make命令进行编译，make命令有很多种，分别做一下说明：

1.make -jXX

  XX表示数字，这个命令将编译Android系统并生成镜像，XX表示可以使用到的CPU核数，这在配置好的电脑上特别有用，公司的16核ubuntu服务器执行make -j16只要不到20分钟，而双核PC上需要4个小时！

2.make snod

  这条命令将重新生成镜像，消耗的时间很少，类似WinCE的makeimg过程，如果你修改了一些数据文件（如音乐、视频）等文件时比较有用。

3.make cts

  这条命令将编译CTS套机，编译出的结果放在out目录对应版的data/app目录下面。CTS测试时有用

4.make installclean

  这条命令清除out目录下对应板文件夹中的内容，也就是相当于make clean，通常如果改变了一些数据文件（如去掉）、最好执行以下make installclean，否则残留在out目录下的还会被打包进去。

5.mm/mm -B

  开发调试中最喜欢这条命令了，在修改了的目录下执行这条命令，就能智能地进行编译，输出的文件在通过adb推送到目标机，可以很方便地调试。

6.make sdk

  这条命令可以生成可发布的SDK，目前还没试过，据说需要JDK1.5

7.make Setting

  可以单独编译setting这个模块，目前还没试，猜想是不是可以单独编译Email、Music这些模块

8.make bootimage

  用这条命令可以生成boot.img,这个镜像文件中包含Linux Kernel，Ram disk，生成的boot.img只能通过fastboot进行烧写，这在只修改了Linux内核的时候有用。
  
接下来我们可以开始编译，使用以下命令：

	make -j8（后面这个j4的意思是开几个线程编译，一核是两个线程）;
	
---
<a href="#">
    <img src="{{ site.baseurl }}/img/framework1/make_j8.jpg" alt="Post Sample Image">
</a>

---
	
第一次编译过程时间很长，我这边i5处理器编译完大概是4.5小时，编译完成后打开上面下好的flashtool配置刷到手机上。

---
<a href="#">
    <img src="{{ site.baseurl }}/img/framework1/flash_tool.jpg" alt="Post Sample Image">
</a>

---	

注意：scatter_loading_file选择out/target/product（编译后的输出目录）下的***_scatter.txt文件。刷到手机上时，如果出现download过程进度条不动可能是usb接口问题，尝试插拔usb接口或者拔电池，刷到手机上后开启手机即可看到全新的刷入的系统。

ps:音量键与开机键同时长按可进入Recovery模式。





---







---
<a href="#">
    <img src="{{ site.baseurl }}/img/framework1/framework1_end.jpg" alt="Post Sample Image">
</a>

---

如果你恰好逛到了这里，希望你也能喜欢这个博客主题。

—— SYF 后记于 2016.9.30


