---
title: Small 插件化使用(1)创建宿主App添加main模块
date: 2016-12-30 13:39:50
categories: Android
tags: [Small,插件化,Android进阶]
---

## 前言
&nbsp;&nbsp;为了实现多人协同开发，才有的插件化/组件化。

## 创建宿主 App
建议先详细阅读`Small` [github](https://github.com/wequick/Small)中的[入门](https://github.com/wequick/Small/tree/master/Android)

1. `git clone` 工程
2. 导入模板 

    cd 到 Small/Android 目录 
    
    复制 `templates` 文件夹 到 `Android Studio`程序的 /plugins/android/lib/ 目录中
    命令行 ：
        
    ```bash
    cp -r templates /Applications/Android\ Studio.app/Contents/plugins/android/lib/
```

   我的Mac环境，`Android Studio`的目录是 `/Applications/Android\ Studio.app`你找到对应目录就可以了

3. 创建宿主 `app`
    
    这里我创建 GanK app
    `Application name:` GanK
    `Company domain:` com.yunfei.gank
    `Package name:` com.yunfei.gank
    ![2016123013453SmallNewPorjet.png](http://ofi52yvuh.bkt.clouddn.com/2016123013453SmallNewPorjet.png)
    选择Small模板

    ![2016123018796SelectSmallLayout.png](http://ofi52yvuh.bkt.clouddn.com/2016123018796SelectSmallLayout.png)
    创建成功后直接打开了个目录的`build.gradle`这里将`Small`插件生成的注释去掉(释放注释，🙅不要删除)
    
    ```gradle
    ////-------------------------------------------------------------------
    
    //// Small plugin
    
    ////
    
    //// Cannot automatically merge the buildscript in Android Studio 2.0+,
    
    //// uncomment this section and sync the project to go into force.
    
    ////-------------------------------------------------------------------
    
    //
    
    buildscript  {
    
        dependencies {
    
            classpath 'net.wequick.tools.build:gradle-small:1.1.0-beta4'
    
        }
    
    }
    
    
    
    apply plugin: 'net.wequick.small'
    
    
    
    small {
    
        // Whether build all the bundles to host assets.
    
        buildToAssets = false
    
    
    
        // The compiling `net.wequick.small:small` aar version.
    
        aarVersion = "1.1.0-beta9"
    
    
    
        // The project-wide Android version configurations
    
        android {
    
            compileSdkVersion = 25
    
            buildToolsVersion = "25.0.1"
    
            supportVersion = "25.0.1" // replace this with an explicit value
    
        }
    
    }
    
    ```
    
    到此宿主`App`创建成功（顺便说一下，我用的 `gradle` 是2.14.1，`gradle build tools`是 2.2.1 如果不能 `gradle sync` 可以尝试这两个版本，我之前时的时候出现过 ）
    可以直接 Run App 查看是否可以运行成功
我们来看看创建的工程中有什么 `SmallApp` `LaunchActivity`  `assets/bundle.json`
先看 `SmallApp` 吧，主要是 `Small.preSetUp(this)` 方法，~~会注册插件~~（当然源码我还没看，这里先这么理解）
再看 `LaunchActivity` 中的 `onStart`方法，这个是打开`main` 模块的注册`Activity`

```java
@Override
protected void onStart() {
   super.onStart();

   Small.setUp(this, new Small.OnCompleteListener() {
       @Override
       public void onComplete() {
           if (Small.openUri("main", LaunchActivity.this)) {
               finish();
           } else {
               Toast.makeText(LaunchActivity.this,
                       "Open failed, see log for detail!",
                       Toast.LENGTH_LONG).show();
           }
       }
   });
}
```
这里可以看到一个神奇方法`Small.openUri("main", LaunchActivity.this)`只需要传入**“main”**就可以打开 `main` 插件的 `Activity`，为什么呢，这就需要看`assets/bundle.json`了

`assets/bundle.json` 文件

```json
{
  "version": "1.0.0",
  "bundles": [
    {
      "uri": "main",
      "pkg": "com.yunfei.gank.app.main"
    }
  ]
}
```
这里可以找到 **“main”** 字符串了，绑定了一个 `pkg` 是一个包名，`com.yunfei.gank.app.main`,就是因为这个`json`文件的配置，才使得`Small.openUri("main", LaunchActivity.this)`这个方法可以调用到 `main` 插件的`Activity`，这个 main 相当于网站的一个目录，打开的 `Activity` 相当于 `index.html`，具体会打开哪个`Activity`，取决于你的`AndroidManifest.xml`中配置的`LAUNCHER`Activity，想打开特定的 `Activity` 我们下次再说

这里提一下，个人建议宿主 App 功能只是加载插件使用

## 创建 main 模块
> File->New->Module来创建插件模块，需要满足：
>     
>     1. 模块名形如：`app.*`, `lib.*`或者`web.*`
>     2. 包名包含：`.app.`, `.lib.`或者`.web.`
>     
>       > 为什么要这样？因为Small会根据包名对插件进行归类，特殊的域名空间如：“.app.” 会让这变得容易。
>     
>     对`lib.*`模块选择**Android Library**，其他模块选择**Phone & Tablet Module**。
>     
>     创建一个插件模块，比如`app.main`：
>     
>     1. 修改**Application/Library name**为`App.main`
>     2. 修改**Package name**为`com.example.mysmall.app.main`


New Module 选择 `Phone & Table Module`,这次可以选择 `Empty Activity`，不需要再选择Small模板，**Small模板只是在创建 宿主 App 时使用**
![2016123022377new_small_app_main.png](http://ofi52yvuh.bkt.clouddn.com/2016123022377new_small_app_main.png)
`Application/Library name:` App.main
`Module domain:` app.main
`Package name:` com.yunfei.gank.app.main

**一定要注意 使用 .app.**,**一定要注意 使用 .app.**,**一定要注意 使用 .app.** 重要的事情说三遍

编译绑定插件

1. 编译libries
 
    ```bash
    ./gradlew buildLib -q   
    ```
    (-q是安静模式，可以让输出更好看，也可以不加)
  
    
2. 绑定组件
    
    ```bash
    ./gradlew buildBundle -q
    ```
    
我这里报了一个**错误**
> Execution failed for task ':app.main:processReleaseResources'.
> In strict mode, we do not allow vendor aars, please declare them in host build.gradle:
>       - compile('com.android.support.constraint:constraint-layout:1.0.0-beta4')
>   or turn off the strict mode in root build.gradle:
>       small {
>           strictSplitResources = false
>       }

> * Try:
> Run with --stacktrace option to get the stack trace. Run with --info or --debug option to get more log output.
 
这个是因为使用了 `constraint-layou`t 因为我用的新版的Android Studio 默认布局用的 `constraint-layout`这个是aars的资源库，`small` 告诉我 插件还不允许 使用 `aars` 给了我两个建议 在 App 宿主（`host`）`build.gradle` 中添加 `compile('com.android.support.constraint:constraint-layout:1.0.0-beta4')` 或者在 `root` 根目录的 `build.gradle` 中添加 

```gradle
small {
   strictSplitResources = false
 }
```

我选择了删除 `constraint-layou` 😂，当然以后还会遇到很多，大家按提示操作就可以了

命令行都成功后直接选择宿主 App 运行下看看![jpg](http://ofi52yvuh.bkt.clouddn.com/20161230148306789118214.jpg?imageView2/0/format/jpg)

还有问题，如果你开的是模拟器，发现运行不了，原因是模拟器是x86的，这个时候只需要把宿主 app 中的`smallLibs`文件夹复制一份，改名为`x86` 再次运行就可以了
![jpg](http://ofi52yvuh.bkt.clouddn.com/20161230148307569581168.jpg?imageView2/0/format/jpg)

## 暂且到这里
运行成功
![jpg](http://ofi52yvuh.bkt.clouddn.com/20161230148307598437902.jpg?imageView2/0/format/jpg)
[GitHub 代码地址](https://github.com/MaYunFei/SmallDemo)


