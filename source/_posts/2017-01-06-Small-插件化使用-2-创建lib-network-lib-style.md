---
title: 'Small 插件化使用(2)创建lib.network,lib.style'
date: 2017-01-06 13:57:38
categories: Android
tags: [Small,插件化,Android进阶]
---

## Library 创建
&nbsp;&nbsp;上一次我创建了宿主`App`，和 `Main`模块，这次添加一个`Library`,我准备添加一个网络请求的 `Library`

`Adnroid Studio` 直接创建 `Module` 选择 `Android Library`
![jpg](http://ofi52yvuh.bkt.clouddn.com/20170104148351004830323.jpg?imageView2/0/format/jpg)

`Application/Library name:` Lib.network
`Module domain:` lib.network
`Package name:` com.yunfei.gank.lib.network

![jpg](http://ofi52yvuh.bkt.clouddn.com/20170104148351033132225.jpg?imageView2/0/format/jpg)

简单说一下，我配置的网络请求看 `gradle` 依赖如下，使用的是`retrofit` `rxjava` `gson` Server api [http://gank.io/api](http://gank.io/api)

```gradle
dependencies {
  compile 'com.squareup.retrofit2:retrofit:2.1.0'
  compile 'com.squareup.retrofit2:converter-gson:2.1.0'
  compile 'com.squareup.retrofit2:adapter-rxjava:2.1.0'
  compile 'com.squareup.okhttp3:logging-interceptor:3.5.0'
  compile 'io.reactivex:rxandroid:1.2.1'
}
```
这里我仅仅是添加了 `http` 请求最基础的功能，并没有添加与业务相关的具体代码 url 请求,也算是与业务隔离吧
## 与宿主APP 关联
直接修改宿主 App `assets`下的 `bundle.json` 添加

```json
{
  "version": "1.0.0",
  "bundles": [
    {
      "uri": "main",
      "pkg": "com.yunfei.gank.app.main"
    },
    {
      "uri": "lib.network",
      "pkg": "com.yunfei.gank.lib.network"
    }
  ]
}
```
如果不添加的话，会报`Class not find` 错误
## 使用 Library
我们的 `main` 插件关联一下 `network` gradle 添加

```gradle
compile project(':lib.network')
```
首页实现一个列表功能 server api
> 分类数据: http://gank.io/api/data/数据类型/请求个数/第几页

> 数据类型： 福利 | Android | iOS | 休息视频 | 拓展资源 | 前端 | all
> 请求个数： 数字，大于0
> 第几页：数字，大于0
> 例：
> http://gank.io/api/data/Android/10/1
> http://gank.io/api/data/福利/10/1
> http://gank.io/api/data/iOS/20/2
> http://gank.io/api/data/all/20/2
  
当然剩下的都是业务关系了，我这里就不详细说了，看我写的代码吧，反正写的也不多🤣就是网络请求加载了一个列表
coding... 🤖
编写过程中可以单独编译运行一下 **app.main** ,插件化的好处体现了
![jpg](http://ofi52yvuh.bkt.clouddn.com/20170104148352139069703.jpg?imageView2/0/format/jpg)

终于到插件组合的时候了,对了先解决模拟器 x86的问题，上一篇博客说过，把宿主 app 中的`smallLibs`文件夹复制一份，改名为`x86`，其实你会发现这里多了一个`libcom_yunfei_gank_lib_network.so`
clean 一下 ,build

```bash
./gradlew clean
./gradlew buildLib -q 
./gradlew buildBundle -q  
```
命令行都成功后直接选择宿主 App 运行下看看 ![jpg](http://ofi52yvuh.bkt.clouddn.com/20161230148306789118214.jpg?imageView2/0/format/jpg)

**坑**来了，并没有运行成功 报错了，是因为 com.yunfei.gank.app.main.MainActivity activity 没有 `toolbar` 的问题，并没有设置为 `NoActionBar` 
> java.lang.RuntimeException: Unable to start activity ComponentInfo{com.yunfei.gank/com.yunfei.gank.app.main.MainActivity}: java.lang.IllegalStateException: This Activity already has an action bar supplied by the window decor. Do not request Window.FEATURE_SUPPORT_ACTION_BAR and set windowActionBar to false in your theme to use a Toolbar instead.

填坑 -- 说是填坑只是我因为我没有很好的看 demo 实例
~~这里我先 在 `AndroidManifest` 文件 显示配置 `Activity` 的 `theme` `android:theme="@style/AppTheme"` 运行了一下，还是不行，只能改名字了，我给 `main` 模块theme 添加了一个前缀 `Main_AppTheme` 运行成功了~~

这里应该有更好的解决方案，就是将 `Style` 单独做 `lib.style` 插件

`Application/Library name:` Lib.style
`Module domain:` lib.style
`Package name:` com.yunfei.gank.lib.style

app.main 依赖 `compile project(':lib.style')`,把之前的`style`文件拷贝，
顺手把 app.main 中的 `styles.xml` 和 `colors.xml` 删除了

再次运行 `RuntimeException` `You need to use a Theme.AppCompat theme`,引发这个原因真的是会有很多种，去看[issues](https://github.com/wequick/Small/issues?q=is%3Aissue+theme)就知道了，真的好多种，当然我经过一天的各种试还是成功了🙈，重要的事情说三遍啊，新建的`lib`/`app`一定要**添加到`bundle.json`中**，**添加到`bundle.json`中**，**添加到`bundle.json`中！**

```json
{
  "version": "1.0.0",
  "bundles": [
    {
      "uri": "main",
      "pkg": "com.yunfei.gank.app.main"
    },
    {
      "uri": "lib.network",
      "pkg": "com.yunfei.gank.lib.network"
    },
    {
      "uri": "lib.style",
      "pkg": "com.yunfei.gank.lib.style"
    }
  ]
}
```

终于跑成功了，顺便说一句在查issues时，觉的挺重要的

> 作者为了插件最小化，Small默认不允许插件携带第三方资源，包含资源的第三方库必须在宿主里预定义。
> 后发现lib.*插件经常可能这么干，为了灵活性，加开该参数允许用户绕过。
就是这个参数`strictSplitResources = false`

## 资源冲突？
这次我们改一下插件的资源文件，手动做成和宿主`app`相同的名字不同值，看看会是什么结果
1. `color.xml` 
	宿主`app`
	![jpg](http://ofi52yvuh.bkt.clouddn.com/20170106148367129685645.jpg?imageView2/0/format/jpg)
	`lib.style`
	![jpg](http://ofi52yvuh.bkt.clouddn.com/20170106148367134139165.jpg?imageView2/0/format/jpg)
	之前运行的结果
	![jpg](http://ofi52yvuh.bkt.clouddn.com/20170104148352139069703.jpg?imageView2/0/format/jpg)
	修改后
	![jpg](http://ofi52yvuh.bkt.clouddn.com/20170106148367165339412.jpg?imageView2/0/format/jpg)
2. `string.xml`
	宿主`app`
	`<string name="main">app.main</string>`
	`app.main`
	`<string name="main">主模块</string>`
	`app.main`设置`toolbar` `mToolbar.setTitle(getString(R.string.main));`
	之前运行的结果
	![jpg](http://ofi52yvuh.bkt.clouddn.com/20170106148367165339412.jpg?imageView2/0/format/jpg)
	修改后
	![jpg](http://ofi52yvuh.bkt.clouddn.com/20170106148367223576658.jpg?imageView2/0/format/jpg)
3. `layout.xml`，`.png` 这个还没测 🤣，不过不影响我下面说明的东西

你会发现虽然**名字**冲突了，但是并没有影响我们使用，并且而且真是我们想要的结果，不需要插件开发者设置前缀，做到了对开发者的透明，真实流掰，原理我建议你好好读一下 [Dynamic load resources](https://github.com/wequick/Small/wiki/Android-dynamic-load-resources)

我简单说下原因
我们名字虽然冲突了，但是在程序运行时，我们是使用的是 `R.string.main`,编译后这会被替换为一个自动生成的`int`值 `public static final int main=0x7f070025;`运行时不冲突只需要这个`int`值不冲突就可以了，这个就是`Small`工具给我们做的事情，在编译的二进制文件中对资源文件做了区分，还是好好看看前边推荐的文档就好

先到这里😋 [GitHub 代码地址](https://github.com/MaYunFei/SmallDemo)


