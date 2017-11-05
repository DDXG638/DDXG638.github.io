---
layout:     post
title:      "创建指定的React-Native版本"
subtitle:   "请多指教"
date:       2017-11-05 12:00:00
author:     "ddxg"
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 前端开发
    - React Native
---


## **创建指定的React-Native版本**

刚刚开始学习React Native，很多都不懂，搭建环境的时候遇到了挺多问题的，一直在折腾。

我是按照[React Native文档](http://reactnative.cn/docs/0.41/getting-started.html)来搭建环境的，安装react-native-cli使用的是下面的命令。
```
npm install -g react-native-cli
```
安装完之后创建了一个项目test，发现react-native的版本是最新的0.50.1，这个版本目录中是没有index.android.js和index.ios.js这两个文件的，多了index.js和App.js等文件
![这里写图片描述](http://img.blog.csdn.net/20171105211517446?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


刚学React Native就使用最新的版本肯定会遇到很多别人没有遇到的坑，而且我买的书《React Native跨平台移动应用开发》这本书是使用0.41.0版本的，为了统一所以还是使用低版本吧
结果我想当然的使用react-native init demo @0.41.0 这个命令来创建低版本的项目，结果是不对的，这样创建出来的依然是最新版本的项目。
后来在百度上看到说，在安装react-native-cli的时候可以指定版本，使用npm install -g react-native-cli @0.41.0，然后我把react-native-cli卸载了，重新安装，不管怎么试都是不行的，直接就报错了。也可能是可以的，但是我试了是不行的。
后来又找到了一个方法：

```
react-native init demo --verbose --version 0.41.0
```
出自：http://www.jianshu.com/p/717dee645939
我试了是可以的，但是具体原理我是不太懂的。
![这里写图片描述](http://img.blog.csdn.net/20171105214456811?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
参考：https://stackoverflow.com/questions/34211131/new-react-native-project-with-old-version-of-react-native/37623531#37623531












