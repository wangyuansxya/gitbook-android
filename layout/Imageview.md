---
layout: post
category: "android"
title:  "Android ImageView"
tags: [Android, ImageView, ScaleType，adjustBounds]
---
**android ImageView ScaleType和adjustBounds介绍**

Android开发过程中ImageView的前景src和背景background是比较常用的属性，与之关联的常用属性有ScaleType和adjustBounds也是比较常用的属性，这篇文章我想主要说明的是ScaleType和adjustBounds<br>
开发者网站地址：[https://developer.android.com/reference/android/widget/ImageView.ScaleType.html](https://developer.android.com/reference/android/widget/ImageView.ScaleType.html)

在介绍之前，我首先说明一个问题，ImageView的view大小和设置在其上面的图片的大小是无关联的。ScaleType是与图片大小相关的一个属性，与view的大小无关，属性的含义控制的是view.ondraw的图片的大小和位置

**1，**adjustBounds <br>

	是一个与view相关属性，设置View是否调整自己的边界来保持图片的长宽比，就是vIew的大小会变。但是在Android4.3之前这个属性只对图片的宽或高大于View的宽或高的时候有效

**2，**ScaleType：

	1,CENTER:将图片中心设置在view的中心位置，不做任何的缩放，当图片长/宽超过View的长/宽，则截取图片的居中部分显示

	2,CENTER_CROP:将图片等比例缩放，使得宽和高都能等于或者大于View的宽和高（宽和高必须都等于或者大于view的宽高），然后裁剪

	3,CENTER_INSIDE:将图片等比例缩放，使得图片的宽或高等于或者小于view的宽或高

	4，FIT_CENTER:将图片按比例缩放到View的宽度，然后居中显示

	5,FIT_END:将图片按比例缩放到View的宽度，然后右下显示(如果图片小于view宽高，则显示在view的右下方，不会拉伸)

	6，FIT_START:将图片按比例缩放到View的宽度，然后居上显示(如果图片小于view宽高，则显示在view的左上方，不会拉伸)

	7，FIT_XY:将图片填充整个view，将图片的宽和高都等于view的宽和高

	8，MATRIX：Scale using the image matrix when drawing.默认的scaleType方式，使用matrix的方式显示图片
 
