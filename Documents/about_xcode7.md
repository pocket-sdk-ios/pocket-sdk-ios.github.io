# 使用Xcode7打游戏包注意事项

> Xcode7使用的是iOS SDK9，其中有一些改变需要在打游戏包时注意。

## 一、https请求

iOS SDK9 默认要求App内的网络请求使用https协议。

目前PocketSDK尚在使用http协议，所以需要在游戏工程的info.plist文件中单独设置。

可以如下设置允许任何http请求，亦可指定能访问哪些特定的http（可自行查询设置）。

![](https://camo.githubusercontent.com/0319de82a394f9d6d6a5c24c80c1f4534a0118e2/687474703a2f2f6935372e74696e797069632e636f6d2f3975713263372e6a7067)

## 二、iPad分屏

iOS SDK9 默认iPad App支持分屏操作。

需要如下图设置Requires full screen（也可在info.plist文件中设置）关闭分屏支持。

否则上传时会报错，提示支持分屏操作的iPad必须支持四个方向、启动页面必须支持story board等信息：

	ERROR ITMS-90474: "Invalid Bundle. iPad Multitasking support requires these orientations: 'UIInterfaceOrientationPortrait,UIInterfaceOrientationPortraitUpsideDown,UIInterfaceOrientationLandscapeLeft,UIInterfaceOrientationLandscapeRight'. Found 'UIInterfaceOrientationLandscapeLeft' in bundle 'com.pocketgames.xxxx'."

	ERROR ITMS-90475: "Invalid Bundle. iPad Multitasking support requires launch story board in bundle 'com.pocketgames.xxxx'."

![](http://i13.tietuku.com/7dd23c7510082a32.jpg)