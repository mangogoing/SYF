---
layout:     post
title:      "Accessbility"
subtitle:   "\"辅助功能\""
date:       2016-09-22 14:00:00
author:     "石云飞"
header-img: "img/accessbility/accessbility.jpg"
catalog: true
tags:
    - 生活
---


> "从S城到W城，有749公里，749000米。你和我之间，是不是比这更远？"

>"Hey,come on."

---

## 引言

由于公司做的主要是盲人项目，所以对辅助功能API接触的多一点，主要是Accessbility。

Accessibility功能和服务可以帮助用户更加简单地操作设备，包括文字转语音（这个不支持中文），触觉反馈，手势操作，轨迹球和手柄操作。

主要分享一下使app无障碍化及AccessbilityService服务的使用。



---

## 1.app无障碍化

android:contentDescription---- 在用户界面控件上通过使用该属性，添加描述性文本，就可使触摸到该控件时可播报文本（打开talkback情况下）

	<ImageButton
    android:id=”@+id/add_note_button”
    android:src=”@drawable/add_note”
    android:contentDescription=”@string/add_note”/>
	
如上ImageButton控件，当用户焦点移动到该控件可获得口头反馈的辅助功能。

Android提供了几个api允许您控制用户界面控制是否可以获取焦点，是否可以获取一个焦点：

setFocusable()获取焦点

isFocusable()是否失去焦点

requestFocus()//优先获取焦点

关于自定义view支持辅助功能，需要做一些额外的工作来确保你的view支持辅助功能，这些工作主要包括：

1.Handle directional controller clicks//处理方向控制器点击

2.Implement Accessibility API methods//实现accessibility API 的方法

3.Send AccessibilityEvent objects specific to your custom view//发送你自定义视图特定的AccessibilityEvent对象

4.Populate AccessibilityEvent and AccessibilityNodeInfo for your view//为你自定义视图填充AccessibilityEvent和AccessibilityNodeInfo对象

具体细节可以查询google[官网](http://android-doc.com/guide/topics/ui/accessibility/apps.html)查阅了解。



---



## 2.AccessbilityService服务
AccessbilityService是一个应用程序,这个程序提供了增强的用户界面以帮助残障人士,或者可能暂时无法与设备充分交互的人们。例如,正在驾驶的用户,正照顾小孩子或参加一个非常常闹的聚会，可能需要额外的或替代的接口反馈。
Android提供了标准的无障碍服务,包括TalkBack,开发人员可以创建和分配自己的服务。该文档解释了创建无障碍服务的基本内容。


那么怎么配置一个AccessbilityService呢

### 1.无障碍服务声明及配置

要将其作为一个无障碍服务，在应用程序的Manifest中，必须包括service元素（而不是activity元素）。此外，在service元素中，还必须包括一个无障碍服务的intent filter。为了与Android 4.1以上版本的兼容性,manifest中需要指定BIND_ACCESSIBILITY_SERVICE 权限：

---

<a href="#">
    <img src="{{ site.baseurl }}/img/accessbility/accessbility1.jpg" alt="Post Sample Image">
</a>

如上图 intent-filter中配置了AccessibilityService，表明该service为无障碍服务，meta-data中配置的xml文件为无障碍事件过滤规则；

---

<a href="#">
    <img src="{{ site.baseurl }}/img/accessbility/accessbility2.jpg" alt="Post Sample Image">
</a>



				   
更多用于无障碍服务配置文件的XML属性的信息,请按照以下链接到参考文档：

[android:description](http://android-doc.com/reference/android/R.styleable.html#AccessibilityService_description)

[android:packageNames](http://android-doc.com/reference/android/R.styleable.html#AccessibilityService_packageNames)

[android:accessibilityEventTypes](http://android-doc.com/reference/android/R.styleable.html#AccessibilityService_accessibilityEventTypes)

[android:accessibilityFlags](http://android-doc.com/reference/android/R.styleable.html#AccessibilityService_accessibilityFlags)

[android:accessibilityFeedbackType](http://android-doc.com/reference/android/R.styleable.html#AccessibilityService_accessibilityFeedbackType)

[android:notificationTimeout](http://android-doc.com/reference/android/R.styleable.html#AccessibilityService_accessibilityFeedbackType)

[android:canRetrieveWindowContent](http://android-doc.com/reference/android/R.styleable.html#AccessibilityService_canRetrieveWindowContent)

[android:settingsActivity](http://android-doc.com/reference/android/R.styleable.html#AccessibilityService_settingsActivity)

---

### 2.无障碍服务方法

一个无障碍服务必须继承accessibilityservice，并且重写这个类中下面的方法。这些方法由Android系统按顺序调用的，当服务开启时 （onserviceconnected()），当服务运行时（onaccessibilityevent()，oninterrupt( )），当服务结束时（onunbind()）。

onserviceconnected() -(可选)当应用成功连接到你的无障碍服务时，系统调用该方法。使用这个方法为你的无障碍服务做任何一次性设置的步骤，包括连接到用户反馈的系统服务,如音频管理器或设备振动器。如果你想在运行时或者一次性调整你服务的配置,这是一个方便的位置,这种情况时调用setServiceInfo()。

onaccessibilityevent() -(必需)当系统检测到一个匹配你无障碍服务过滤器中设置参数的AccessibilityEvent相时，调用该方法。例如,当用户在一个应用程序中点击一个按钮或焦点放在用户界面控件上时，,你的无障碍服务就可以提供反馈。这种情况下，系统就通过相关连的AccessibilityEvent,调用这个方法，然后服务就可以解释和使用为用户提供的反馈了。这个方法在服务的生命周期中可能会被多次调用。

oninterrupt( ) - (必需)当系统中断在你的服务中提供的反馈时调用该方法，通常是在响应用户的诸如移动焦点到不同的控件上的动作时，调用该方法。该方法在你服务的生命周期中被多次的调用。

onunbind() - (可选)当系统即将关闭当前无障碍服务时，调用该方法。使用这个方法来一次性关闭任何程序,包括释放用户反馈系统服务,如音频管理器或设备振动器。 

这些回调方法为你的无障碍服务提供基本的结构框架。Android系统以AccessibilityEvent对象的形式对用户提供的返回的数据，如何处理它们由你来决定。

---

### 3.获取事件注意事项

一个无障碍服务获取用户界面事件的信息，通过系统服务的onaccessibilityevent()回调方法传递的AccessibilityEvent。这个对象提供关于事件的详细信息，包括对象的类型，其描述性文本和其他细节。从Android 4.0(和以前版本中通过在Support Library的accessibilityeventcompat支持),通过下面这些方法获取额外的关于事件的信息：

AccessibilityEvent.getRecordCount()和getRecord(int)——这些方法允许你检索AccessibilityRecord对象集合,这也使得你通过系统获得AccibilityEvent。这种层次的细节为这个触发你无障碍服务的事件提供了更多的上下文。

AccessibilityEvent.getSource() - 这个方法返回一个AccessibilityNodeInfo对象。该对象允许您请求(父母和孩子)的组件的视图布局层,源于无障碍事件。这个特性允许一个无障碍服务调查事件的整个上下文,包括内容和任何封闭视图或子视图的状态。

### 4.为用户做出动作

无障碍服务可以侦听特定的手势，对做出这个手势的用户给予响应。这个特性,添加在Android 4.1(API级别16),要求你的无障碍服务的请求通过触摸功能来激活的。你的服务可以请求通过设置服务的AccessibilityServiceInfo实例的成员标志为FLAG_REQUEST_TOUCH_EXPLORATION_MODE来激活，如下例所示。

	public void onAccessibilityEvent(AccessibilityEvent event) {
		// TODO Auto-generated method stub
		this.processAccessibilityEnvent(event);
	}

	private void processAccessibilityEnvent(AccessibilityEvent event) {

		if (event.getSource() == null) {
		} else {
			switch (INVOKE_TYPE) {
			case TYPE_KILL_APP:
				processKillApplication(event);
				break;
			case TYPE_INSTALL_APP:
				processinstallApplication(event);
				break;
			case TYPE_UNINSTALL_APP:
				processUninstallApplication(event);
				break;				
			default:
				break;
			}						
		}
	}
	
	


监听事件之后，我们可以为用户做出动作，为了做出的动作代表用户，你的无障碍服务必须注册去接收从几个或多个应用程序接收事件，必须通过在服务配置文件设置android:canRetrieveWindowContent为true，请求允许查看应用程序的内容。当你的服务接收到事件,它可以使用getSource()从事件检索AccessibilityNodeInfo对象。通过AccessibilityNodeInfo对象,您的服务可以探索视图层来决定采取何种行动,然后使用performAction()代理用户。


	public class MyAccessibilityService extends AccessibilityService {  
  
    @Override  
    public void onAccessibilityEvent(AccessibilityEvent event) {  
        // get the source node of the event  
        AccessibilityNodeInfo nodeInfo = event.getSource();  
  
        // Use the event and node information to determine  
        // what action to take  
  
        // take action on behalf of the user  
        nodeInfo.performAction(AccessibilityNodeInfo.ACTION_SCROLL_FORWARD);  
  
        // recycle the nodeInfo object  
        nodeInfo.recycle();  
    }  
    ...  
}

performAction()方法允许你的服务在一个应用程序中做出动作。如果你的服务需要执行全局性动作，如导航到主屏幕,按后退按钮,打开屏幕通知或最近的应用程序列表,使用performGlobalAction()方法。

ps:在我看来Accseebility服务不仅仅可以使用在无障碍化功能实现上，也可以用来调试程序，AccessibilityEvent.getSource()可返回解析的当前view的节点树结构，可帮助我们深入理解view结构，performAction()更是用处很大，某种意义上类似于按键精灵，像微信自动抢红包app，豌豆荚、应用宝的软件自动安装等都是通过这个方法实现。

---







---
<a href="#">
    <img src="{{ site.baseurl }}/img/accessbility/accessbility_end.jpg" alt="Post Sample Image">
</a>

---

如果你恰好逛到了这里，希望你也能喜欢这个博客主题。

—— SYF 后记于 2016.8.18


