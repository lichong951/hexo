---
title: 【Jenkins】构建后生成下载二维码并邮件通知
date: 2018-05-13 15:18:25
tags: Jenkins Email通知	二维码
---
### 二维码生成配置
#### http-server(MAC)
检测 node -v、npm -v 然后安装http-server

	npm install http-server -g
	
启动服务“http-server -p 8000” 设置端口为8000.避免jenkins端口冲突
在/Users/XXX/Public 目录下面建立jenkins目录，把构建后的apk复制到/Users/XXX/Public/jenkins/apk/目录下面
然后把二维码图片也复制到/Users/XXX/Public/jenkins

> 为什么要http-server服务

因为在jenkins里的图片在邮件里是没有办法直接显示的。必须要有一个没有拦截的访问路径。以避开jenkins的用户验证。同理apk的下载也是如此
不过一般都是部署在局域网内。安全性还是有保障的。如果要发布到外网的可以使用第三方服务上传apk包生成公网下载二维码。

#### 安装pip（MAC）（python>3.1）[如果版本不够看”其他“]
	
	sudo easy_install pip
[mac下安装pip](https://jingyan.baidu.com/article/ca2d939d6299eaeb6c31cee2.html)

[windows下面安装Python和pip终极教程](http://www.cnblogs.com/yuanzm/p/4089856.html)
#### 安装myqr

	命令1：pip install Pillow （失败）
	命令2：pip install -I --no-cache-dir -v Pillow （成功）
	至于为什么失败，解释颇多也不想深究。暂且都记上多试试。
	安装qrcode 命令：pip install myqr
生成二维码命令
	myqr http://localhost:8000/jenkins/apk/XXXX.apk -n jenkins-qrcode-${JOB_NAME}.png -v 1 -l L -d /Users/XXX/Public/jenkins
	

### Jenkins邮件通知配置
在邮件配置content字段里添加
	
	<img src="http://localhost:8000/jenkins/jenkins-qrcode-${JOB_NAME}.png " width="200px" height="200px" /></br></hr>
	<a href='http://localhost:8000/jenkins/apk/XXXX.apk'>点击下载apk</a>



邮件通知配置样例
	<hr/>

	(本邮件是程序自动下发的，请勿回复！)<br/><hr/>

	项目名称：$PROJECT_NAME<br/><hr/>

	构建编号：$BUILD_NUMBER<br/><hr/>

	构建状态：$BUILD_STATUS<br/><hr/>

	触发原因：${CAUSE}<br/><hr/>

	扫描二维码下载<img src="http://192.168.10.214:8000/JenkinsApk/jenkins-qrcode-${JOB_NAME}.png" width="200px" height="200px"/> <br/><hr/>
	<a href="http://192.168.10.214:8000/JenkinsApk/apk/${JOB_NAME}-Debug-${BUILD_NUMBER}.apk">点击下载${JOB_NAME}-Debug-${BUILD_NUMBER}.apk</a><br/><hr/>
	构建日志地址：<a href="${BUILD_URL}console">${BUILD_URL}console</a><br/><hr/>

	构建地址：<a href="$BUILD_URL">$BUILD_URL</a><br/><hr/>

	变更集:${JELLY_SCRIPT,template="html"}<br/><hr/>


参考：
https://www.jianshu.com/p/915c1ae69144

### 其他
【MAC】
> “OSError: [Errno 13] Permission denied: '/Library/Python/2.7/site-packages/PIL'”

**使用 sudo pip install -I --no-cache-dir -v Pillow 命令安装**

> mac OSError: [Errno 1] Operation not permitted: '/tmp/pip-ZaNR4Q-uninstall/System/Library/Frameworks

升级python 版本

	$ pip install --upgrade pip
	$ sudo pip install numpy --upgrade --ignore-installed
	$ sudo pip install scipy --upgrade --ignore-installed
	$ sudo pip install scikit-learn --upgrade --ignore-installed
	
参考https://www.jianshu.com/p/7a18c78b5982

> OSError: [Errno 13] Permission denied: '/Library/Python/2.7/site-packages/imageio-2.3.0.dist-info'
解决方法：
**sudo pip install myqr**

> matplotlib 1.3.1 requires nose, which is not installed.
> matplotlib 1.3.1 requires tornado, which is not installed.

解决方法：运行sudo pip install matplotlib

## 联系我
> Email:lichongmac@163.com

支付宝打赏：[https://pan.baidu.com/s/1UMWjU1FHv7hYpdlcCQrJ3A](https://pan.baidu.com/s/1UMWjU1FHv7hYpdlcCQrJ3A)

微信打赏：[https://pan.baidu.com/s/1dSBXk3eFZu3mAMkw3xu9KQ](https://pan.baidu.com/s/1dSBXk3eFZu3mAMkw3xu9KQ)

公众号推荐：

![](http://i2.51cto.com/images/blog/201805/11/0e2ece55187caabd7b246e9550a8d5a0.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)