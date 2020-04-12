---
title: 为什么iOS更新UI操作必须在主线程
date: 2018-04-07 15:38:53
tags: iOS
---

`iOSer`都知道UI操作如果在非主线程进行的话是会Crash的。但是本质原因可能很少人真正明白。

正面的概括回答是：  
**UI操作涉及到渲染访问各种View对象的属性，如果是异步操作会有读写问题。加锁呢，性能损耗大(视图层次深，属性多)。所以主线程操作UI，是约定俗成的开发规则。**

<!--more-->

我们来看看Quora网友关于这个”经典”问题的回答：

	Why must the UI always be updated on Main Thread?

来自 Reinder de Vries 的回答：

	The first one is that, in Cocoa Touch, the UIApplication gets set up on the main thread
	Another reason is graphics rendering: the graphics pipeline of the iPhone is ultimately synchronous.

他的一个理由是：

	1.在Cocoa Touch框架中，UIApplication初始化工作是在主线程进行的。而界面上所有的视图都是在UIApplication 实例的叶子节点(内存管理角度)，所以所有的手势交互操作都是在主线程上才能响应  
	2.图形渲染在iPhone设备本质上是同步的。图形渲染计算最终要显示的像素值，以每秒60帧的频率刷新到屏幕上，绘制到屏幕的过程实际上就是通过LED display点亮各个像素。这个过程需要一次将所有将要实现的像素刷新到屏幕上(同时的)。如果要异步化的话，对应的你无法确定这个处理过程是否真正的全部完成。

他最后指出，在主线程操作UI。能帮助你避免不少的麻烦和减少产生产品缺陷。当然，有部分UI操作是可异步化的，只要最后的更新操作是在主线程

* 叠加滤镜效果在视图上  
* 创建动态图形，比如动画

最后推荐了```WWDC```上关于UI渲染的录像 [Building Concurrent User Interfaces on iOS](https://developer.apple.com/videos/play/wwdc2012/211/)


相关阅读

[Thread-Safe Class Design](https://www.objc.io/issues/2-concurrency/thread-safe-class-design/)

[Why must UIKit operations be performed on the main thread?](https://stackoverflow.com/questions/18467114/why-must-uikit-operations-be-performed-on-the-main-thread)
