---
title: android.os.TransactionTooLargeException Intent 传递大数据
date: 2017-02-09 17:33:29
categories: Android
tags: Android基础
---

测试的时候崩溃过，没有注意，主要是选项太多，忘记点的哪个，后来没有重现，今天在 `bugly` 发现 `TransactionTooLargeException` 这个问题

## 解决办法
1. 临时文件存储(xml,File,Database)，跳转 效率太低了，好处是崩溃了都能取到
2. `static` 大法，一般不推荐使用 `static` 因为是破坏封装

我最终的解决办法是在跳转的页面添加 `static` 变量接受参数 在 `Activity` `onDestory` 方法中将 `static` 变量清空了

同事提醒我要考虑一下 `Activity` 到后台内存紧张被杀死会清掉 `static` 变量，我想着先用 `static` 变量传递一下，再用成员变量存一下吧




参考 [http://blog.csdn.net/ekeuy/article/details/37913583](http://blog.csdn.net/ekeuy/article/details/37913583)


