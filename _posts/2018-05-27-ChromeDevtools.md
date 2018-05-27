---
layout:     post
title:      "学习Chrome Devtools"
subtitle:   "Chrome Devtools"
date:       2018-05-27 21:51:43
author:     "ddxg"
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - Devtools
---


Chrome 开发者工具devTools是一个非常强大的工具，我基本只会使用其中一丁点的功能，浏览了一下[Chrome 开发者工具中文文档](http://www.css88.com/doc/chrome-devtools/) 发现里面介绍了好多调试功能，值得学习。
我看了里面比较常使用到的几点：Elements(元素面板)、Console(控制台面板)、Sources(源代码面板)、Network(网络面板)和Application(资源面板)。

这周我做了一件很蠢的事情，在跟后台联调一个接口的时候，我误点了以下："Block request URL"项
![这里写图片描述](https://img-blog.csdn.net/20180527194641158?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FsaWd1YWd1YQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
意思即是阻止这个请求，当再次请时会出现以下：
![这里写图片描述](https://img-blog.csdn.net/20180527195121273?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FsaWd1YWd1YQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
其实在status中描述得很清楚，“blocked:devtools”意思就是请求被devtools阻止了。我当时没有认真看，只是发现没有状态码和请求返回值，我就以为是后台接口的问题。后台查了一下，发现根本就没有发送请求。然后瞎搞了好久，才发现我对那个接口点击了"Block request URL"，最后把它取消了就什么事都没有。
![这里写图片描述](https://img-blog.csdn.net/20180527200004800?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FsaWd1YWd1YQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

折腾了我一个小时，我的天呐！！！