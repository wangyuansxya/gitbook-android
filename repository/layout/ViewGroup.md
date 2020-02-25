---
layout: post
category: "android"
title:  "Android 自定义ViewGroup"
tags: [ViewGroup, Android]
---
**Android 自定义ViewGroup**

核心方法就是OnMeasure和OnLayout

	onMeasure:设置子view的宽和高，设置自己的宽和高。根据子View 的布局文件，为子 view设置测量模式和测量值
	测量 = 测量模式+测量值
	测量模式由三种：
	EXCTLY:指定大小
	AT_MOST:指定最大的大小
	UNSPCIFIED：不指定大小，要多大就是多大。一般用于ScroolView中
	
	OnLayout:设置子view的位置

	
