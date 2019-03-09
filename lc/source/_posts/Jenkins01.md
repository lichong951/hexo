---
title: 【Jenkins】自动构建配置
date: 2018-05-13 15:10:46
tags: Jenkins 自动构建 
---
### 时间设置说明

			* * * * *
			(五颗星，中间用空格隔开）
			第一颗*表示分钟，取值0~59
			第二颗*表示小时，取值0~23
			第三颗*表示一个月的第几天，取值1~31
			第四颗*表示第几月，取值1~12
			第五颗*表示一周中的第几天，取值0~7，其中0和7代表的都是周日
			
例子如下：

				每15分钟构建一次：H/15 * * * *   或*/5 * * * *
				每天8点构建一次：0 8 * * *
				每天8点~17点，两小时构建一次：0 8-17/2 * * *
				周一到周五，8点~17点，两小时构建一次：0 8-17/2 * * 1-5
				每月1号、15号各构建一次，除12月：H H 1,15 1-11 *
				*/5 * * * * （每5分钟检查一次源码变化）
				0 2 * * * （每天2:00 必须build一次源码）

### 触发远程构建 例如,使用脚本

### Build after other projects are built
Projects to watch “XXXXX”

	* Trigger only if build is stable：只有在构建稳定时才触发
	* 
	* Trigger even if the build is unstable：即使构建不稳定，也要触发
	* 
	* Trigger even if the build fails：即使构建失败，也要触发
	* 

### Build periodically
周期性进行项目构建，这个是到指定的时间必须触发构建任务
比如我想在每天的9点，17点，朝九晚五各构建一次，在Build periodically里设置如下
![](http://i2.51cto.com/images/blog/201803/26/9bafd3a8ed881183ce75f56363f54270.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)


### Build when a change is pushed to GitLab. GitLab CI Service URL: XXXXX

* 	Enabled GitLab triggers	：启用GitLab触发器
* 	Push Events：push 事件
* 	Opened Merge Request Events：打开合并请求事件
* 	Accepted Merge Request Events		：接受合并请求事件
* 	 Closed Merge Request Events		：关闭合并请求事件
* 	 Rebuild open Merge Requests		：重建开放合并请求
* 	 Comments		：评论
* 	 Comment for triggering a build：注释触发构建

### GitHub hook trigger for GITScm polling
GitHub钩子触发GITScm轮询

### Poll SCM
1.Poll SCM:定时检查源码变更（根据SCM软件的版本号），如果有更新就checkout最新code下来，然后执行构建动作

2.如果我想每隔30分钟（H/30 * * * *）检查一次源码变化，有变化就执行

Ignore post-commit hooks(可选项):忽略post-commit钩子


## 联系我
> Email:lichongmac@163.com

支付宝打赏：[https://pan.baidu.com/s/1UMWjU1FHv7hYpdlcCQrJ3A](https://pan.baidu.com/s/1UMWjU1FHv7hYpdlcCQrJ3A)

微信打赏：[https://pan.baidu.com/s/1dSBXk3eFZu3mAMkw3xu9KQ](https://pan.baidu.com/s/1dSBXk3eFZu3mAMkw3xu9KQ)

公众号推荐：

![](http://i2.51cto.com/images/blog/201805/11/0e2ece55187caabd7b246e9550a8d5a0.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)