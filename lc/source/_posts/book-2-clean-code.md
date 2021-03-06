---
title: 《代码整洁之道》
date: 2017-03-17 05:17:03
tags: 代码规范 cleancode 编程风格 重构
---

![Alt text](http://omnk3xsvf.bkt.clouddn.com/timg.jpeg)

    无规矩不成方圆；书写代码就像写作一样。要有一定的书写方式方法才能写出让人一目了然的好代码。
    最后升华到犹如艺术的境界

### 第一章

举例说明什么是糟糕的、混乱的代码；什么是整洁的代码；介绍多位大牛对代码的思想；

童子军军规“**让营地比你来时更干净**”
### 第二章 有意义的命名
给变量、函数、参数、类、和包等命名。

名副其实；避免误导；做有意义的区分；使用读的出来的名称；使用可搜索的名称；避免使用编码；

匈牙利语标记法；无前缀；

接口和实现IShapFactory-->ShapFactoryImp

避免思维映射“**明确是王道**”，循环计数不要用“l”

类名和对象名应该是名词或名词短语

方法名应当是动词或动词短语；别耍宝；别用双关词；使用解决方案领域名称

添加有意义的语镜；

### 第三章 函数
短小；只做一件事；

每个函数一个抽象层级

switch语句（**只出现一次，用于创建多态对象，且隐藏在某个继承关系中，系统其他部分看不到**）

**使用异常替代返回错误码**

**抽离Tty／Catch代码块**

错误处理就是一件事

别重复自己；结构化编程；如何写出这样的函数

### 第四章 注释
喃喃自语；废话注释；注释掉的代码；

### 第五章 格式
向报纸学习；概念间垂直方向上的区隔

团队规则；
### 第六章 对象和数据结构
The Law of Demeter 对象隐藏数据，曝露操作

### 第七章 错误处理
使用异常而非返回码；使用不可控异常；依调用者需要定义异常类；定义常规流程；

**别返回null**；**别传递null**

### 第八章 边界
**学习性测试**；学习log4j；

### 第九章 单元测试
TDD三定律；保持测试整洁；测试和代码一样重要；

每个测试一个断言；

F（快速fast）I（独立independ）R（repeatable可重复）S（自足验证Self-Validating）T（Timely及时）

### 第十章 类
类的组织；类应该短小；为了修改而组织；

### 第十一章 系统
将系统构造与使用分开；纯Java AOP框架；

### 第十二章 迭进
运行所有测试；重构；不可重复；尽可能少的类和方法

### 第十三章 并发编程（建立多个demo来理解）
对象是过程的抽象，线程是调度的抽象

并发防御原则；了解执行模型；

### 第十五章 JUnit内幕

### 第十七章 味道与启发
注释；环境；函数；重复；晦涩的意图；

用多态替代If/else或者switch／case；避免否定性条件；

避免传递浏览； 