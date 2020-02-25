**Android activity启动模式**

Android开发中在AndroidManifest.xml配置所有的activity的时候有的时候需要添加一个activity的启动模式(android:launchMode)，对activity的

1，standard <br>

	标准的activity启动模式，这是activity的默认启动方式，该模式下activity可以被创建多个实例，例如启动activity-A,在activity-A调用intent 启动activity-B,activity-B再次启动activity-A,会创建新的activity-A的实例

2，singleTop<br>

	它的表现几乎和standard模式一模一样，一个singleTop Activity 的实例可以无限多，唯一的区别是如果在栈顶已经有一个相同类型的Activity实例，Intent不会再创建一个Activity，而是通过onNewIntent()被发送到现有的Activity。需要同时在onCreate() 和 onNewIntent()中处理发来的intent，以满足不同情况。适合当前页面搜索实现

3，singleInstance<br>

	如果应用1的任务栈中创建课一个MainActivity的实例，如果应用2也要启动，则不创建，俩个应用共享activity实例

4，singleTask

	官方文档的解析是：系统会创建一个新的任务，并将这个Activity实例化为新任务的根部（root）。但是实际的情况并不是这样的。
	这种模式的Activity只允许在系统中有一个实例。如果系统中已经有了一个实例，持有这个实例的任务将移动到顶部，同时intent将被通过onNewIntent()发送。如果没有，则会创建一个新的Activity并置放在合适的任务中。会移除位于singleTask之上任务栈的实例，适合邮件系统的开发

什么是Intent Flags？

	除了在AndroidManifest.xml
	中直接设置launch mode，我们还可以通过叫做 Intent Flags的东西设置更多的行为，比如：
	Intent intent = new Intent(StandardActivity.this, StandardActivity.class);
	intent.addFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP);
	startActivity(intent);
	这段代码将会启动一个singleTop启动模式的的StandardActivity
 。
