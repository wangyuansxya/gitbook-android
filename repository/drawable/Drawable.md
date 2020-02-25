---
layout: post
category: "android"
title:  "Android Drawable类"
tags: [Drawable, Android]
---
**Android Drawable解析**

Drawable是一个抽象类，是对可绘制物体的描述。和View不同，没有点击事件和交互方法。Drawable不是直接面向我们的，是看不见的。不能为其添加点击事件

1，Drawable有很多子类操作具体类型资源。
比如BitmapDrawable是用来操作位图
ColorDrawable使用来操作颜色的
CilpDrawable用来操作剪切板

2，Drawable的原理：主要是draw(Canvs c)(用于在drawable的画布上画，画布是由drawable附着的view对象提供的)和setBounds()(设置绘制的边界，通常会传入一个矩形或者上左右下的位置信息)俩个方法

3，BitmapDrawable是对bitmap的包装，可以设置它包装的bitmap在bitmapDrawable在区域内的绘制方式，如平铺填充，拉伸填充，保持原始图片大小

	<?xml version="1.0" encoding="utf-8"?>
	<bitmap xmlns:android="http://schemas.android.com/apk/res/android"
	    android:src="@drawable/ic_launcher"
	    android:tileMode="mirror"
	    android:antialias="true"
	    android:dither="true"
	    >
	</bitmap>

4，LayerDrawable 可以管理一组drawable对象，在layerdrawable的drawable的drawable的资源按照列表的顺序绘制，列表的最后一个drawable绘制在最上层
	
	<?xml version="1.0" encoding="utf-8"?>
	<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
	
	    <item android:drawable="@drawable/bitmap"
	        android:gravity="center"
	        android:top="10dp"
	        android:left="10dp"
	        ></item>
	
	    //............
	
	</layer-list>

5，StateListDrawable 就是在在XML中定义，StateListDrawable可以根据不同的状态提供不同的背景，比如一个按钮可以有多种状态，获取焦点，失去焦点，点击，选中等

	<?xml version="1.0" encoding="utf-8"?>
	<selector xmlns:android="http://schemas.android.com/apk/res/android">
	   <item android:drawable="@drawable/bitmap"android:state_selected="true">
	    </item>
	    <item android:drawable="@drawable/bitmap" android:state_checked="true">
	    </item>
	    <item android:drawable="@drawable/bitmap" android:state_enabled="true">
	    </item>
	    <item android:drawable="@drawable/bitmap" android:state_focused="true">
	    </item>
	    <item android:drawable="@drawable/bitmap" >
	    </item>
	</selector>

6，levelListDrawable管理一组drawable资源与一个最大值结合起来，作为LevelListDrawable资源的一项。调用Drawable的setLevel()方法可以加载Level-list或者代替代码中定义的某个drawable资源

	<level-list xmlns:android="http://schemas.android.com/apk/res/android">
	    <item android:drawable="@drawable/bitmap"
	        android:minLevel="6"
	        android:maxLevel="10"
	        ></item>
	
	    <item android:drawable="@drawable/bitmap"
	        android:minLevel="11"
	        android:maxLevel="15"
	        ></item>
	
	</level-list>
	
	设置为ImageView的src或者background，代码中调用ImageView.setImageLevel方法设置level

7，TransitionDrawable是layerDrawable的子类，它自负责管理俩层drawable，并且提供一个透明度变化的动画，可以控制从一层drawable过度到另一层的drawable的动画效果

	<?xml version="1.0" encoding="utf-8"?>
	<transition xmlns:android="http://schemas.android.com/apk/res/android">
	    <item android:drawable="@drawable/book_succeed_default" ></item>
	    <item android:drawable="@drawable/common_choice_pressed" ></item>
	</transition>

8，InsertDrawable顾名思义就是嵌入一个drawable。相当于设置Imageview的前景，然后给imageview设置相应的padding

	<inset xmlns:android="http://schemas.android.com/apk/res/android"
	    android:drawable="@drawable/bg"
	    android:inset="50dp"
	    >
	</inset>

9，ClipDrawable对drawable进行裁剪操作，控制裁剪区域。android的进度条就是clipdrawable的一个实现，根据level的值来确定裁剪的区域。ClipDrawable通过　setLevel(int level)方法来设置裁剪的区域，level从0到10000。0为完全不显示(相当于完全裁剪)，10000为完全显示

	<clip xmlns:android="http://schemas.android.com/apk/res/android"
	    android:drawable="@drawable/bg"
	    android:clipOrientation="horizontal"
	    android:gravity="left"
	    >
	
	</clip>

10,自定义drawable，直接上代码：
自定义的圆形drawable

	public class CustomCircleDrawable extends Drawable {

    private static Paint mPaint = new Paint(Paint.ANTI_ALIAS_FLAG);
    private int mWidth;
    private Bitmap mBitmap;


    public CustomCircleDrawable(Bitmap bitmap) {
        this.mBitmap = bitmap;
        //位图Shader，Shader 又被称之为着色器，渲染器，它用来实现一系列的渐变，渲染效果
        BitmapShader bitmapShader = new BitmapShader(bitmap, Shader.TileMode.CLAMP, Shader.TileMode.CLAMP);
        mPaint.setAntiAlias(true);//设置抗锯齿
        mPaint.setDither(true);//设置放抖动
        mPaint.setShader(bitmapShader);
        mWidth = Math.min(bitmap.getWidth(), bitmap.getHeight());
    }

    @Override
    public void draw(Canvas canvas) {
        canvas.drawCircle(mWidth / 2, mWidth / 2, mWidth /2, mPaint);
    }

    @Override
    public void setBounds(int left, int top, int right, int bottom) {
        super.setBounds(left, top, right, bottom);
    }

    @Override
    public void setAlpha(int alpha) {
        mPaint.setAlpha(alpha);
    }

    /**
     * 滤镜效果
     * @param colorFilter
     */
    @Override
    public void setColorFilter(ColorFilter colorFilter) {
        mPaint.setColorFilter(colorFilter);
    }

    /**
     * 不透明度
     * @return
     */
    @Override
    public int getOpacity() {
        return PixelFormat.TRANSLUCENT;
    }

    /**
     * 返回drawable的实际的宽
     * @return
     */
    @Override
    public int getIntrinsicWidth() {
        return mWidth;
    }

    @Override
    public int getIntrinsicHeight() {
        return mBitmap.getHeight();
    }
	}

自定义的圆角图片

	public class CustomRectDrawable extends Drawable {

    private static Paint mPaint = new Paint(Paint.ANTI_ALIAS_FLAG);
    private int mWidth;
    private int mHeight;
    private Bitmap mBitmap;
    private RectF mRectF= null;//指定一个矩形区域的大小


    public CustomRectDrawable(Bitmap bitmap) {
        this.mBitmap = bitmap;
        //位图Shader，Shader 又被称之为着色器，渲染器，它用来实现一系列的渐变，渲染效果
        BitmapShader bitmapShader = new BitmapShader(bitmap, Shader.TileMode.CLAMP, Shader.TileMode.CLAMP);
        mPaint.setAntiAlias(true);//设置抗锯齿
        mPaint.setDither(true);//设置放抖动
        mPaint.setShader(bitmapShader);
        mWidth = bitmap.getHeight();
        mHeight = bitmap.getHeight();

    }

    @Override
    public void draw(Canvas canvas) {
	//        canvas.drawCircle(mWidth / 2, mWidth / 2, mWidth /2, mPaint);
        canvas.drawRoundRect(mRectF, 30, 30, mPaint);
    }

    @Override
    public void setBounds(int left, int top, int right, int bottom) {
        super.setBounds(left, top, right, bottom);
        mRectF = new RectF(left, top, right, bottom);
    }

    @Override
    public void setAlpha(int alpha) {
        mPaint.setAlpha(alpha);
    }

    /**
     * 滤镜效果
     * @param colorFilter
     */
    @Override
    public void setColorFilter(ColorFilter colorFilter) {
        mPaint.setColorFilter(colorFilter);
    }

    /**
     * 不透明度
     * @return
     */
    @Override
    public int getOpacity() {
        return PixelFormat.TRANSLUCENT;
    }

    /**
     * 返回drawable的实际的宽
     * @return
     */
    @Override
    public int getIntrinsicWidth() {
        return mWidth;
    }

    @Override
    public int getIntrinsicHeight() {
        return mHeight;
    }
	}

11，setColorFilter可以实现滤镜效果：

	final WallpaperManager wallpaperManager = 	WallpaperManager.getInstance(this);
	//获取壁纸
	final Drawable wallpaperDrawable = wallpaperManager.getDrawable();
	wallpaperDrawable.setColorFilter(Color.RED, PorterDuff.Mode.MULTIPLY);

	注意：PorterDuff.Mode枚举值：
	1.PorterDuff.Mode.CLEAR    
	//所绘制不会提交到画布上。
	2.PorterDuff.Mode.SRC
	//显示上层绘制图片
	3.PorterDuff.Mode.DST
	//显示下层绘制图片
	4.PorterDuff.Mode.SRC_OVER
	//正常绘制显示，上下层绘制叠盖。
	5.PorterDuff.Mode.DST_OVER
	//上下层都显示。下层居上显示。
	6.PorterDuff.Mode.SRC_IN
	//取两层绘制交集。显示上层。
	7.PorterDuff.Mode.DST_IN
	//取两层绘制交集。显示下层。
	8.PorterDuff.Mode.SRC_OUT
	//取上层绘制非交集部分。
	9.PorterDuff.Mode.DST_OUT
	//取下层绘制非交集部分。
	10.PorterDuff.Mode.SRC_ATOP
	//取下层非交集部分与上层交集部分
	11.PorterDuff.Mode.DST_ATOP
	//取上层非交集部分与下层交集部分
	12.PorterDuff.Mode.XOR
	//变暗
	13.PorterDuff.Mode.DARKEN
	//调亮
	14.PorterDuff.Mode.LIGHTEN
	//用于颜色滤镜
	15.PorterDuff.Mode.MULTIPLY
	
	16.PorterDuff.Mode.SCREEN
	
	
12,Shader 又被称之为着色器，渲染器，它用来实现一系列的渐变，渲染效果。
	
	Android中的Shader包括以下几种： 
	1.BitmapShader 位图Shader 
	2.LinearGradient 线性Shader 
	3.RadialGradient 光束Shader 
	4.SweepGradient 梯度Shader 
	5.ComposeShader 混合Shader
	
BitmapShader的作用就是通过Paint对画布进行置顶Bitmap的填充，填充时有以下几种模式可以选择： 
1.CLAMP 拉伸 拉伸的是图片最后的哪一个像素，不断重复 
2.REPEAT 重复 横向、纵向不断重复 
3.MIRROR 镜像 横向不断翻转重复，纵向不断翻转重复

