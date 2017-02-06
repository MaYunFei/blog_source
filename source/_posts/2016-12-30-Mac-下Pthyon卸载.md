---
title: Mac 下Pthyon卸载
date: 2016-12-30 18:14:54
categories: Python
tags: 
---
# Mac 下Pthyon 卸载

最近再次捣鼓 `Python`，由于我之前在官网下载的 `Python3.4` 安装文件，卸载成了问题，特记录下

1. 删除Python库

	```bash
	sudo rm -rf /Library/Frameworks/Python.framework/Versions/3.4
	```
2. 删除Python应用
	
	```bash
	sudo rm -rf /Applications/Python\ 3.4
	```
3. 清除 /usr/local 无效链接
	
	```bash
	brew prune
	```
	提示 `Pruned 18 symbolic links from /usr/local` 18个无效链接
	命令行运行下 `python3` 
	提示 `command not found: python3`
4. 安装 `Python3.6`
	
	```bash
	brew install python3
	```
结束 😋

参考 [http://stackoverflow.com/questions/22774529/what-is-the-safest-way-to-removing-python-framework-files-that-are-located-in-di](http://stackoverflow.com/questions/22774529/what-is-the-safest-way-to-removing-python-framework-files-that-are-located-in-di)


