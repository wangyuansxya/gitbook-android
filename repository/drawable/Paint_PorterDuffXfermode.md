---
layout: post
category: "android"
title:  "Android Paint PorterDuffXfermode"
tags: [PorterDuffXfermode, Android]
---
**Android PorterDuffXfermode解析**

PorterDuffXfermode设置的是两个图层交集区域的显示方式，dst是先画的图形，src是后画的图形。

1，其中最常用的就是通过DST_IN,SRC_IN模式来实现将一个矩形图片编程圆角图片或者圆形图片的效果。先利用一个普通画笔画一个Mask遮罩层，再用带PorterDuffXfermode的画笔讲图像画在遮罩层上，这样就可以通过上面所说的效果来混合两个图像了

	mBitmap = BitmapFactory.decodeResource(getResources(),R.drawable.test);
	mOut = Bitmap.createBitmap(mBitmap.getWidth(), mBitmap.getHeight(), Bitmap.Config.ARGB_8888);
	Canvas canvas = new Canvas(mOut);
	paint = new Paint();
	paint.setAntiAlias(true);
	canvas.drawRoundRect(0, 0, mBitmap.getWidth(), mBitmap.getHeight(), 80, 80, paint);
	paint.setXfermode(new PorterDuffXfermode(PorterDuff.Mode.SRC_IN));
	canvas.drawBitmap(mBitmap,0,0,paint);

2，实现一个刮刮卡的效果：

	public class XfermodeView extends View {

    private Path mPath;//手刮动的path，过程
    private Paint mOutterPaint;//绘制mPath的画笔
    private Canvas mCanvas;//临时画布
    private Bitmap mBitmap;//临时图片

    //记录用户path每次的开始坐标值
    private int mLastX;
    private int mLastY;

    private Bitmap mOutterBitmap;//图片遮罩，就是手刮动，要擦掉的那张图

    public XfermodeView(Context context) {
        this(context, null);
    }

    public XfermodeView(Context context, AttributeSet attrs) {
        this(context, attrs, 0);
    }

    public XfermodeView(Context context, AttributeSet attrs, int defStyle) {
        super(context, attrs, defStyle);
        init();
    }


    @Override
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        super.onMeasure(widthMeasureSpec, heightMeasureSpec);
        //获得控件的宽高
        int width = getMeasuredWidth();
        int height = getMeasuredHeight();

    }


    private int mWidth;
    private int mHeight;

    @Override
    protected void onLayout(boolean changed, int left, int top, int right, int bottom) {
        super.onLayout(changed, left, top, right, bottom);
        if(mWidth <=0 || mHeight <=0) {
            //初始化bitmap
            mWidth = right - left;
            mHeight = bottom - top;
            mBitmap = Bitmap.createBitmap(mWidth, mHeight, Bitmap.Config.ARGB_8888);
            mCanvas = new Canvas(mBitmap);

            //设置画笔属性
            /**
             * 绘制path（也就是手刮动的path来绘制） 的画笔属性
             * 类似橡皮擦
             */
            mOutterPaint.setColor(Color.RED);
            mOutterPaint.setAntiAlias(true);
            mOutterPaint.setDither(true);
            mOutterPaint.setStrokeJoin(Paint.Join.ROUND);//设置圆角
            mOutterPaint.setStrokeCap(Paint.Cap.ROUND);
            mOutterPaint.setStyle(Paint.Style.FILL);
            mOutterPaint.setStrokeWidth(60);//设置画笔宽度


            mCanvas.drawColor(Color.parseColor("#c0c0c0"));
        }

    }

    @Override
    protected void onDraw(Canvas canvas) {

        mOutterPaint.setStyle(Paint.Style.STROKE);
        mOutterPaint.setXfermode(new PorterDuffXfermode(PorterDuff.Mode.DST_OUT));//Mode.DST_OUT改模式就类似橡皮檫，这个属性设置是关键

        canvas.drawBitmap(mOutterBitmap, 0, 0, null);
        canvas.drawBitmap(mBitmap, 0, 0, null);

        mCanvas.drawPath(mPath, mOutterPaint);
    }

    @Override
    public boolean onTouchEvent(MotionEvent event) {
        int action = event.getAction();
        int x = (int) event.getX();
        int y = (int) event.getY();

        switch (action) {
            case MotionEvent.ACTION_DOWN://按下
                //记录按下的时候的X和Y值，以便于之后移动的时候绘制
                mLastX = x;
                mLastY = y;
                mPath.moveTo(mLastX, mLastY);
                break;
            case MotionEvent.ACTION_MOVE://移动
                //拿到用户移动的X绝对值，Y轴绝对值
                int dx = Math.abs(x - mLastX);
                int dy = Math.abs(y - mLastY);
                //用户滑动超过3像素才会改变，这个可以不做，做只是为了避免很频繁的响应而已。
                if (dx > 3 || dy > 3) {
                    mPath.lineTo(x, y);
                }
                mLastX = x;
                mLastY = y;
                break;
        }
        invalidate();//刷新UI
        return true;
    }

    /**
     * 初始化信息
     */
    private void init() {
        mOutterPaint = new Paint();
        mPath = new Path();
        mOutterBitmap = BitmapFactory.decodeResource(getResources(),
                R.drawable.bg);

    }
	}

