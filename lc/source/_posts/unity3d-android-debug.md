---
title: 【Unity3D】Android真机断点调试
date: 2017-11-18 15:59:16
tags: unity3d android 
---
# 【Unity3D】Android真机断点调试

1. 首先在手机上开启USB调试功能，并安装驱动（这一步很多手机助手都可以完成）
2. 用USB电缆连接手机和电脑
3. 确保手机和电脑在一个局域网内，简单的说就是电脑和手机共用一个路由器，网段一样。
4. 打开电脑上CMD窗口，输入以下命令：

	 Adb tcpip 5555（该命令打开手机adb网络调试功能）
	正常情况下输入命令后控制台会出现回显
	  restarting in TCP mode port: 5555
	 打开手机查看手机的IP地址（不会请百度）假设手机的地址是192.168.1.x输入命令
	adb connect 192.168.1.x
	如果一切正常控制台会回显以下内容
	connected to 192.168.1.x:5555
	如果你想查看是否连接成功请输入以下内容
	  adb devices
	  控制台会回显连接的设备
	  
5. 如果一切连接成功，请拔掉USB电缆，选择File->Build&Run，在编译之前要勾选上
Development Build 和Script Debugging这两项在build setting里面勾选不要忘记否则是不能调试的）电脑会自动编译文件并将APK推送至手机，在手机上同意并安装

6. 当程序运行后再Monodevelop里面打开Run->Attach to process 会发现你手机的选项，选择手机，在脚本里面添加断点，你发现可以调试了，那叫一个爽！出现问题再也不用去瞎猜，或者添加Debuglog了
