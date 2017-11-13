---
layout:     post
title:      "初始化RN项目"
subtitle:   "请多指教"
date:       2017-11-13 12:00:00
author:     "ddxg"
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 前端开发
    - React Native
---


## **1.初始化RN项目**

搭建好React Native开发环境后（安装好Python 2、Node、react-native-cli、Android SDK等），就可以创建一个RN项目了

在目录中输入命令：
```
react-native init demo
```

就可以创建项目名为demo的项目了，创建时下载的东西比较多，网速慢一点的话可能会慢一点。这样就创建了最新版本的RN项目，如果初学的话不需要安装最新的版本，有两种方式可以达到目的:
**(1)可以使用下面的命令创建指定的版本：**
```
react-native init demo --verbose --version 0.41.0
```

这样就创建了0.41.0版本的RN项目。

**(2)使用rninit工具**
首先要安装rninit工具：
```
npm install -g rninit
```
然后使用以下命令创建指定版本的RN项目：

```
rninit init demo -source react-native@0.41.0
```

这里需要注意的是：项目名称不能使用像demo-app这种形式，要使用驼峰式的写法demoApp。

## **2.运行RN项目**
我使用的是window系统，我运行的是android的项目，我是使用真机调试的，模拟器在我的电脑上运行有点卡。

#### **(1)准备工作**

手机连接电脑，有三个确保：一是确保手机上安装了驱动（可以在电脑上使用手机助手软件在手机上安装驱动），二是确保手机打开了开发者模式；三是确保手机与电脑使用同一个局域网，可以连接同一个wifi
使用以下命令可以查看手机连接情况

```
adb devices
```
![这里写图片描述](http://img.blog.csdn.net/20171113003426633?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

说明有一个设备连接成功，

#### **运行Android平台的RN项目**
在安装目录下，输入命令：

```
react-native run-android
```
这个命令会编译初始化的项目，并且将编译好的安装包安装到手机上，在编译时会下载很多编译依赖包，用时会很长，我就等了好久好久好久。不过依赖包只要下载一次就好，再次执行这个命令就不用再下载了。

还有，在执行react-native run-android这个命令，会新打开React Packager命令行窗口，这个在调试的时候需要一直运行，千万不要关闭它。
## **3.重新运行RN项目**
RN项目安装到手机后就不需要再次使用react-native run-android这个命令了。手机连接电脑后再次运行RN项目的命令：

```
adb -s f295cecb reverse tcp:8081 tcp:8081
```
这里的f295cecb是Android手机设备的ID，通过adb devices命令可以查看得到，然后执行：

```
react-native start
```
React Packager运行，这里要注意如果电脑分配的IP地址发生了变化，需要重新执行第一步，即：react-native run-android

