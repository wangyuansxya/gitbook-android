---
layout: post
category: "android"
title:  "Android 屏幕适配"
tags: [屏幕适配, Android]
---
**Android 屏幕适配**

1,为什么要做屏幕适配

	由于android的开放性，各个手机厂商可以对其进行定制，

2,使用.9.patch图片

这是一种格式特殊的 PNG 文件，其中会指明可以拉伸以及不可以拉伸的区域。

.9的制作，实际上就是在原图片上添加1px的边界，然后按照我们的需求，把对应的位置设置成黑色线，系统就会根据我们的实际需求进行拉伸。

下图是对.9图的四边的含义的解释，左上边代表拉伸区域，右下边代表padding box，就是间隔区域

3,什么是屏幕尺寸、屏幕分辨率、屏幕像素密度？

**屏幕尺寸**

	屏幕尺寸指屏幕的对角线的长度，单位是英寸，1英寸=2.54厘米
	
	比如常见的屏幕尺寸有2.4、2.8、3.5、3.7、4.2、5.0、5.5、6.0等
	
**屏幕分辨率**
	
	屏幕分辨率是指在横纵向上的像素点数，单位是px，1px=1个像素点。一般以纵向像素*横向像素，如1960*1080。
	
**屏幕像素密度**
	
	屏幕像素密度是指每英寸上的像素点数，单位是dpi，即“dot per inch”的缩写。屏幕像素密度与屏幕尺寸和屏幕分辨率有关，在单一变化条件下，屏幕尺寸越小、分辨率越高，像素密度越大，反之越小。

4,什么是dp、dip、dpi、sp、px？他们之间的关系是什么？

**px** <br>
	
	我们应该是比较熟悉的，前面的分辨率就是用的像素为单位，大多数情况下，比如UI设计、Android原生API都会以px作为统一的计量单位，像是获取屏幕宽高等。

**dip和dp** <br>
	
	是一个意思，都是Density Independent Pixels的缩写，即密度无关像素，上面我们说过，dpi是屏幕像素密度，假如一英寸里面有160个像素，这个屏幕的像素密度就是160dpi，那么在这种情况下，dp和px如何换算呢？在Android中，规定以160dpi为基准，1dip=1px，如果密度是320dpi，则1dip=2px，以此类推。

	假如同样都是画一条320px的线，在480*800分辨率手机上显示为2/3屏幕宽度，在320*480的手机上则占满了全屏，如果使用dp为单位，在这两种分辨率下，160dp都显示为屏幕一半的长度。这也是为什么在Android开发中，写布局的时候要尽量使用dp而不是px的原因。

**sp** <br>
	
	即scale-independent pixels，与dp类似，但是可以根据文字大小首选项进行放缩，是设置字体大小的御用单位。

5,什么是mdpi、hdpi、xdpi、xxdpi？如何计算和区分？

	其实之前还有个ldpi，但是随着移动设备配置的不断升级，这个像素密度的设备已经很罕见了，所在现在适配时不需考虑。
	
	mdpi、hdpi、xdpi、xxdpi用来修饰Android中的drawable文件夹及values文件夹，用来区分不同像素密度下的图片和dimen值。
	
	在进行开发的时候，我们需要把合适大小的图片放在合适的文件夹里面。下面以图标设计为例进行介绍。
	
![](https://github.com/wangyuansxya/wangyuansxya.github.io/blob/master/assets/dpi.jpg?raw=true)
	
	在设计图标时，对于五种主流的像素密度（MDPI、HDPI、XHDPI、XXHDPI 和 XXXHDPI）应按照 2:3:4:6:8 的比例进行缩放
	
![](https://github.com/wangyuansxya/wangyuansxya.github.io/blob/master/assets/1x-2x-3x-4x.jpg?raw=true)

6，支持各种屏幕尺寸

	使用wrap_content、match_parent、weight
	
	要确保布局的灵活性并适应各种尺寸的屏幕，应使用 “wrap_content” 和 “match_parent” 控制某些视图组件的宽度和高度。
	
	使用 “wrap_content”，系统就会将视图的宽度或高度设置成所需的最小尺寸以适应视图中的内容，而 “match_parent”（在低于 API 级别 8 的级别中称为 “fill_parent”）则会展开组件以匹配其父视图的尺寸。
	
	如果使用 “wrap_content” 和 “match_parent” 尺寸值而不是硬编码的尺寸，视图就会相应地仅使用自身所需的空间或展开以填满可用空间。此方法可让布局正确适应各种屏幕尺寸和屏幕方向。

7,使用相对布局，禁用绝对布局

	在开发中，我们大部分时候使用的都是线性布局、相对布局和帧布局，绝对布局由于适配性极差，所以极少使用

8,使用限定符

使用尺寸限定符

使用最小宽度限定符

使用布局别名

使用屏幕方向限定符
	

参考文章：http://www.cocoachina.com/android/20151030/13971.html
