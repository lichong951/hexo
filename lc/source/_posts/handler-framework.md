---
title: Handler机制
date: 2017-04-22 08:37:31
tags: Handler 框架 Android
---
#Handler机制
##Message结构分析
Handler部分源码：
		
		private boolean enqueueMessage(MessageQueue queue, Message msg, long uptimeMillis) {
        msg.target = this;
        if (mAsynchronous) {
            msg.setAsynchronous(true);
        }
        return queue.enqueueMessage(msg, uptimeMillis);
    }