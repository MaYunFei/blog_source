---
title: Ubuntu 14.04 64位 Android aapt error
date: 2017-03-09 00:10:38
categories: Android
tags: [Android,aapt,Ubuntu]
---

## 前言
公司装的 Ubuntu 64位 , gradle build 出现问题,报 `aapt` 有问题,搜索了良久终于知道了是因为 `Android Sdk` 编译的时候用了 32 位的库,我的机器没有这个链接库,导致异常 
## 解决方法
首先执行 :
```bash
sudo apt-get install lib32stdc++6
```
此时执行: 
```bash
sudo apt-get install lib32z1 lib32z1-dev
```
运行aapt，问题解决．