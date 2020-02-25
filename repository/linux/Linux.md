---
layout: post
category: "linux"
title:  "Linux Android Root"
tags: [Linix, Root, Android]
---
**Android Root解析**

判断操作系统的本质：系统内核+文件系统

1，Android就是linux系统，只是没有桌面系统的一些特征罢了。android的内核和文件系统都是Linux提供的。在ubuntu的终端命令行下，可以通过输入‘su’命令来获取最高权限。相当于windows系统的管理员权限

2，为什么我们需要管理员权限。做Linux驱动开发的时候可以在获取到root权限的情况下，直接使用android手机来测试驱动。最坏的情况下就是手机无法开机，直接刷一个新的rom包就行了。不会对机器的硬件有什么损伤。这样方便了开发。提升了效率

3，提取root权限的步骤：<br>
	
	1，刷一个合适的recovery 
	方法1:在bootloader模式下复制整个文件系统
	方法2:在recovery模式下将文件复制到android设备的指定目录
	
	2，改造su命令
	android 系统自带的su命令，只允许root和shell用户调用 
	
	3，recovery刷机文件 
	就是一个zip文件压缩包。只要有待复制的文件和edify语言编写的脚本文件组成
	
	4，执行su命令提取root权限 
	改造后的su命令可以被终端和app调用
	让rom本身具有root权限 
	
	5，刷机后，android系统直接拥有root权限

4，刷一个很酷的recovery

	http://www.clockworkmod.com/这个网站上可以查找对应的机型下载。另外沉默之星的也不错
	1，下载好对应的recovery之后，打开终端进入到下载好recovery的目录。
	2，输入adb reboot bootloader，回车。刷.img的包，必须进入到bootloader模式下
	3，危险命令相关：
	fast flash bootloader bootloader.img 刷bootloader镜像。需要保证bootloader文件完全没有问题，否则设备将无法使用（基本都是主板销毁）。解决方法更换主板或者返厂维修
	自毁命令：fastboot erase bootloader (100%)	4，fastboot flash recovery system.img
	除了boot loader分区，回车。其他分区都可以刷机，不会对机器有任何的损害
	5，启动设备：fastboot reboot
	
5,edify语言简介(编写脚本的语言)
	
	1，用于编写recovery升级包中的update-script这个脚本文件，这个文件是recovery rom和recovey的核心文件，完全由函数组成。该脚本文件用于将su命令复制到/system/xbin目录中
	
	2， 常用函数：
	ui_print(msg1 ..... msgN):该函数用与在recovery界面输出字符串。其中msg1 。。。msgN表示N个字符串参数，该函数需要指定至少一个参数，如果指定多个参数，会将这些参数值连接起来输出
	用法：ui_print("hello world")
	
	run_program(prog, arg1...argN):用于执行程序。prog表示要执行的程序文件(要写完整路径，)，arg1...argN表示要执行程序的参数。prog参数是必选的，其他的都是可选的
	
	delete(file1, file2 ..fileN):用于删除一个或者多个文件，参数表示要删除文件的路径。至少需要指定一个文件。
	
	package_extract_dir(package_path, destination_path):用户复制文件。将package_path目录下的所有文件都提取到destination_path目录下。package_path 表示刷机包下的目录，destination_path表示目标目录
	package_extract_dir("system", "/system")
	
	set_perm(uid, gid, mode, file1, file2 .. fileN):用于设置一个或者多个文件的权限。uid表示用户ID，gid表示用户组id，如果想让文件的用户和用户组都是root，uid和gid需要都为0，mode参数表示设置的权限。与chmod命令类似
	set_perm（0， 0， 0777， "/system/xbin/su")
	
	mount(fs_type, partition)type, location, mount_point):挂载分区
	mount("ext4", "EMMC", "/dev/block/platform/s3c-sdhci.0/by-name/system", "/system")
	
	unmount(mount_point):卸载分区，解除文件系统的挂载。mount_point参数表示文件系统
	unmount("/system")
	
6，编写update-script脚本文件

	1,以读写模式挂载/system
	2,删除旧的su文件
	3,复制新的su文件
	4,修改su文件的权限
	5,卸载/system
	
代码：

	ui_print("Recovery update package");
	#以读写模式挂载/system
	run_program("/sbin/busybox", "mount", -o", "-rw", "/system");
	
	ui_print("delete /system/sbin/su“);
	#删除旧的su文件
	delete("/system/sbin/su")
	
	ui_print(“package_extract_dir”);
	#将刷机包中的system目录的所有文件复制到/system目录的相应位置
	package_extract_dir("system", "/system")
	
	ui_print(“package_extract_dir”);
	#设置su命令权限，将文件设置为可执行
	set_perm（0， 0， 0777， "/system/xbin/su")
	
	ui_print(“unmount”);
	#卸载sysytem
	unmount("/system")
	
	ui_print(“finish”）;
	
	
7,制作recovery升级包
	
	META-INF/com/google/android
	文件夹下有一个update_binary文件相当于是update-script的解析器，将update-script文件copy到该目录下即可
	system/xbin
	将META-INF和system目录一起打包成zip压缩包即可
	
8，复制su命令到/system/xbin目录下
	
	1，正常模式下，首先需要进入到recovery模式下 adb reboot recovery
	2，在recoovery模式下，执行adb sideload update.zip，会将update.zip复制到sd卡上，然后刷机

9，使用su命令获取root权限

	1，使用su命令，提取root权限俩种方法
	在Android设备的终端中执行su命令提取root权限。
	1，adb devices :查看设备列表
	2，进入shell：adb shell
	3, 输入su命令，获取到root权限
	4，cd ／system／app
	5，mount | grep system :只查看system目录相关
	输出：/dev/block/sda6 on /system type ext4 (ro,noatime,data=ordered)
	6, 将该目录改为可读写的：mount -o rw,remount /dev/block/sda6 /system
	7, 将该目录改为只读的：mount -o ro,remount /dev/block/sda6 /system
	
	在App中执行su命令提取root权限
	Runtime.getRuntime.exce("su");
	OutputStream os = process.getOutputStream();
	os.write("ls system/app".getBytes());
	os.flush();
	os.close();
	
10,root权限的妙用

	修改android设备的启动画面：
	基本方法：只需替换/system/media/目录下bootanimation.zip文件即可
	
	desc.txt:
	[width] [height] [frame-rate]
	p [loop] [pause] [floder]
	p [loop] [pause] [floder]
	...
	p [loop] [pause] [floder]
	
	bootanimation.zip下的desc.txt文件内容含义解析：
	width：动画播放的宽度
	height：动画播放的高度
	frame-rate：每秒播放的帧数
	loop：part动画循环的次数
	pause：当前part动画播放完成之后暂停的帧数
	floder：当前part动画对应的静态图像的文件存储目录名称
	
11,su命令的源代码解析：

	1，源代码文件：su.c
	Android 源代码根目录/system/extras/su
	是一个可执行的文件程序
	
	2， 编译su.c文件
	build/envsetup.h
	进入su目录，执行mm命令编译su.c
	
	
	
