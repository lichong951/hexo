---
title: hexo异常记录
date: 2017-03-03 16:32:48
tags: hexo debug 异常 解决办法
---

## 1、generateError 生成静态页面异常

	lc git:(master) ✗ hexo generate
	INFO  Start processing
	ERROR Process failed: _posts/book-1.md
	Error
	    at generateError (/Users/smart/hexo/lc/node_modules/js-yaml/lib/js-yaml/loader.js:162:10)
	    at throwError (/Users/smart/hexo/lc/node_modules/js-yaml/lib/js-yaml/loader.js:168:9)
	    at readBlockMapping (/Users/smart/hexo/lc/node_modules/js-yaml/lib/js-yaml/loader.js:1045:9)
	    at composeNode (/Users/smart/hexo/lc/node_modules/js-yaml/lib/js-yaml/loader.js:1331:12)
	    at readDocument (/Users/smart/hexo/lc/node_modules/js-yaml/lib/js-yaml/loader.js:1493:3)
	    at loadDocuments (/Users/smart/hexo/lc/node_modules/js-yaml/lib/js-yaml/loader.js:1549:5)
	    at Object.load (/Users/smart/hexo/lc/node_modules/js-yaml/lib/js-yaml/loader.js:1566:19)
	    at parseYAML (/Users/smart/hexo/lc/node_modules/hexo-front-matter/lib/front_matter.js:80:21)
	    at parse (/Users/smart/hexo/lc/node_modules/hexo-front-matter/lib/front_matter.js:56:12)
	    at /Users/smart/hexo/lc/node_modules/hexo/lib/plugins/processor/post.js:52:18
	    at tryCatcher (/Users/smart/hexo/lc/node_modules/bluebird/js/release/util.js:16:23)
	    at Promise._settlePromiseFromHandler (/Users/smart/hexo/lc/node_modules/bluebird/js/release/promise.js:507:35)
	    at Promise._settlePromise (/Users/smart/hexo/lc/node_modules/bluebird/js/release/promise.js:567:18)
	    at Promise._settlePromise0 (/Users/smart/hexo/lc/node_modules/bluebird/js/release/promise.js:612:10)
	    at Promise._settlePromises (/Users/smart/hexo/lc/node_modules/bluebird/js/release/promise.js:691:18)
	    at Promise._fulfill (/Users/smart/hexo/lc/node_modules/bluebird/js/release/promise.js:636:18)
	    at PromiseArray._resolve (/Users/smart/hexo/lc/node_modules/bluebird/js/release/promise_array.js:125:19)
	    at PromiseArray._promiseFulfilled (/Users/smart/hexo/lc/node_modules/bluebird/js/release/promise_array.js:143:14)
	    at PromiseArray._iterate (/Users/smart/hexo/lc/node_modules/bluebird/js/release/promise_array.js:113:31)
	    at PromiseArray.init [as _init] (/Users/smart/hexo/lc/node_modules/bluebird/js/release/promise_array.js:77:10)
	    at Promise._settlePromise (/Users/smart/hexo/lc/node_modules/bluebird/js/release/promise.js:564:21)
	    at Promise._settlePromise0 (/Users/smart/hexo/lc/node_modules/bluebird/js/release/promise.js:612:10)
	INFO  Files loaded in 252 ms
	
异常原因：在文章tag：冒号后面少了一个空格
解决：加一个空格即修复异常


## 2、hexo cannot get tags
参考：https://www.zhihu.com/question/29017171
	
	1.	hexo new page "tags"
	2. 如果有评论：
		type: "tags"
		comments: false
	

	 
	 
	 