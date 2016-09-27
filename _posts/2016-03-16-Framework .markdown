---
layout:     post
title:      "Framework开发"
subtitle:   "\"预置apk\""
date:       2016-03-16 14:00:00
author:     "石云飞"
header-img: "img/systemApp/systemApp.jpg"
catalog: true
tags:
    - 生活
---


> "现在是我喜欢上你的第3862010秒，也是我第36次遇见你，每次我在心里问你喜不喜欢我，都得不到回答"

>"Hey,come on."

---

## 引言

最近公司在做系统与应用层的结合，其中一个重要内容就是将公司开发的app预置到系统中，变成系统应用，预置apk有四种情况：

1, 将带源码的 APK 预置进系统

2, 将无源码的APK预置进系统

3, 预置APK使得用户可以卸载，恢复出厂设置时不能恢复

4, 预置APK使得用户可以卸载，并且恢复出厂设置时能够恢复





---

## 1.将带源码的 APK 预置进系统

---

1) 在 packages/apps 下面以需要预置的 APK的 名字创建一个新文件夹，以预置一个名为Test的APK 为例

2) 将 Test APK的Source code 拷贝到 Test 文件夹下，删除 /bin 和 /gen 目录

3) 在 Test 目录下创建一个名为 Android.mk的文件，内容如下：

	LOCAL_PATH:= $(call my-dir)
	include $(CLEAR_VARS)
	LOCAL_MODULE_TAGS := optional
	LOCAL_SRC_FILES := $(call all-subdir-java-files)
	LOCAL_PACKAGE_NAME := Test
	include $(BUILD_PACKAGE)
	
4) 打开文件 build/target/product/${Project}.mk （其中 ${Project} 表示工程名）
将 Test 添加到 PRODUCT_PACKAGES 里面。

5) 重新 build 整个工程
	
## 2.将无源码的 APK 预置进系统

1) 在 packages/apps 下面以需要预置的 APK 名字创建文件夹，以预置一个名为Test的APK为例

2) 将 Test.apk 放到 packages/apps/Test 下面

3) 在 packages/apps/Test 下面创建文件 Android.mk，文件内容如下：

	LOCAL_PATH := $(call my-dir)
	include $(CLEAR_VARS)
	# Module name should match apk name to be installed
	LOCAL_MODULE := Test
	LOCAL_MODULE_TAGS := optional
	LOCAL_SRC_FILES := $(LOCAL_MODULE).apk
	LOCAL_MODULE_CLASS := APPS
	LOCAL_MODULE_SUFFIX := $(COMMON_ANDROID_PACKAGE_SUFFIX)
	LOCAL_CERTIFICATE := PRESIGNED
	include $(BUILD_PREBUILT)

4) 打开文件 build/target/product/${Project}.mk （其中 ${Project} 表示工程名）
将 Test 添加到 PRODUCT_PACKAGES 里面。

5) 将从Test.apk解压出来的 so库拷贝到alps/vendor/mediatek/${Project}/artifacts/out/target/product/${Project}/system/lib/目录下，若无 so 库，则去掉此步；

6) 重新 build 整个工程


## 3.预置APK使得用户可以卸载，恢复出厂设置时不能恢复

1) 在 packages/apps 下面以需要预置的 APK 名字创建文件夹，以预置一个名为Test的APK为例

2) 将 Test.apk 放到 packages/apps/Test 下面；

3) 在 packages/apps/Test 下面创建文件 Android.mk，文件内容如下

	LOCAL_PATH := $(call my-dir)
	include $(CLEAR_VARS)
	# Module name should match apk name to be installed
	LOCAL_MODULE := Test
	LOCAL_MODULE_TAGS := optional
	LOCAL_SRC_FILES := $(LOCAL_MODULE).apk
	LOCAL_MODULE_CLASS := APPS
	LOCAL_MODULE_SUFFIX := $(COMMON_ANDROID_PACKAGE_SUFFIX)
	LOCAL_CERTIFICATE := PRESIGNED
	<span style="color:#ff0000;">LOCAL_MODULE_PATH := $(TARGET_OUT_DATA_APPS)</span>
	include $(BUILD_PREBUILT)

4) 打开文件 build/target/product/${Project}.mk （其中 ${Project} 表示工程名）
将 Test 添加到 PRODUCT_PACKAGES 里面。

5) 重新 build 整个工程
注意：这个比不能卸载的多了一句

	LOCAL_MODULE_PATH := $(TARGET_OUT_DATA_APPS)





## 4.预置APK使得用户可以卸载，并且恢复出厂设置时能够恢复

在packages/apps下面以需要预置的 APK 名字创建文件夹，以预置一个名为Test的APK为例：

1) 将Test.apk 放到 packages/apps/Test 下面；

2) 在packages/apps/Test下面创建文件 Android.mk，文件内容如下：

	LOCAL_PATH := $(call my-dir)
	include $(CLEAR_VARS)
	# Module name should match apk name to be installed
	LOCAL_MODULE := Test
	LOCAL_MODULE_TAGS := optional
	LOCAL_SRC_FILES := $(LOCAL_MODULE).apk
	LOCAL_MODULE_CLASS := APPS
	LOCAL_MODULE_SUFFIX := $(COMMON_ANDROID_PACKAGE_SUFFIX)
	LOCAL_CERTIFICATE := PRESIGNED
	<span style="color:#ff0000;">LOCAL_MODULE_PATH := $(TARGET_OUT)/vendor/operator/app</span>
	include $(BUILD_PREBUILT)

3) 打开文件 build/target/product/${Project}.mk （其中 ${Project} 表示工程名）， 将 Test 添加到 PRODUCT_PACKAGES 里面

4) 重新 build 整个工程
	
	










---
<a href="{{ site.baseurl }}/img/systemApp/systemApp_end.jpg">
    <img src="{{ site.baseurl }}/img/systemApp/systemApp_end.jpg" alt="Post Sample Image">
</a>

---

如果你恰好逛到了这里，希望你也能喜欢这个博客主题。

—— SYF 后记于 2016.9.30


