---
layout: post
category: "android"
title:  "LayoutTransition 容器布局动画"
tags: [LayoutTransition, 容器布局动画]
---
**当viewgroup发生变化的时候可以自动的添加动画效果。**

给viewgroup在xml设置android:animateLayoutChanges="true"即可，<br>
此时显示的是系统自带的默认动画效果，如果觉得不好看，也可以自定义，下面介绍一下自定义动画效果的实现方法：<br>
想看原版的可以去官网：https://developer.android.com/reference/android/animation/LayoutTransition.html<br>
Android中提供了四种容器动画：<br>
1、APPEARING: 动画所运行的viewgroup出现在这个容器中时，即：view显示时的动画<br>
2、CHANGE_APPEARING: 由于在这个容器总新增加了一个view，而导致原来的view位置发生改变所以会触发这个动画。<br>
3、DISAPPEARING: view在这个容器中消失时触发的动画 <br>
4、CHANGE_DISAPPEARING: 由于在这个容器中移除了一个view，而导致原来的view位置发生改变所以会触发这个动画。 <br>

使用的步骤： <br>
1、创建LayoutTransition对象mTransitioner <br>
2、创建动画 <br>
3、在xml文件中将相应布局的属性android:animateLayoutChanges="true"设置为true <br>
4、将动画通过mTransitioner的setAnimator方法设置给mTransitioner <br>
5、通过布局控件的setLayoutTransition方法将mTransitioner设置进去 <br>

