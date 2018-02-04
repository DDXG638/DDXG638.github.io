---
layout:     post
title:      "H5开发中遇到的问题"
subtitle:   "H5开发"
date:       2018-02-04 21:51:43
author:     "ddxg"
header-img: "img/post-bg-ios9-web.jpg"
header-mask: 0.3
catalog:    true
tags:
    - H5
    - 常见问题
---


## **H5开发中遇到的问题**

#### **1.滑动页面卡顿、慢**

有时app中的H5页面滑动起来感觉阻力很大，划得很慢。
可以在滚动的block元素中添加如下样式，滑动就会变得更顺滑，用户体验更好

```
body {
	-webkit-overflow-scrolling: touch;
	overflow-scrolling: touch;
}
```
*概述：*
`-webkit-overflow-scrolling` ：属性控制元素在移动设备上是否使用滚动回弹效果
*值：*

 - `auto` ：使用普通滚动, 当手指从触摸屏上移开，滚动会立即停止。
 - `touch` ：使用具有回弹效果的滚动, 当手指从触摸屏上移开，内容会继续保持一段时间的滚动效果。继续滚动的速度和持续的时间和滚动手势的强烈程度成正比。同时也会创建一个新的堆栈上下文。

[与scrollTop一起使用可能会有点问题](https://www.cnblogs.com/931298654yy/p/6122211.html)

[与position:absolute/relative一起使用可能会有问题](http://www.bcty365.com/content-142-5250-1.html)



#### **2.app中的H5页面禁止复制、选中文本**
添加`-webkit-user-select:none;` 这段代码后在IOS 上会有问题，input 框，textarea框无法正常输入了内容，解决这个方法 就是 在css 文件中同时设置一下input 和 textarea 的属性
```
Element {
      -webkit-touch-callout:none;  /*系统默认菜单被禁用*/   
      -webkit-user-select:none; /*webkit浏览器*/   
      -khtml-user-select:none; /*早期浏览器*/   
      -moz-user-select:none;/*火狐*/   
      -ms-user-select:none; /*IE10*/   
      user-select:none;   
}

/* 解决safari上input无法输入的问题 */
input,
textarea {
    user-select: text;
}
```

#### **3.iphone及ipad下输入框默认内阴影**
我遇到的情况是在iphone中input输入框会有一个上阴影，解决：

```
Element{
	-webkit-appearance: none;
}
```


#### **4.input输入框中光标高度的变化问题**

在安卓中，H5页面中input输入框光标显示正常，在iOS中光标撑满input的高度。
产生的原因：
有些浏览器对光标高度的设置原则为，当没有内容的时候光标的高度=input的line-height的值，当有内容时，光标从input的顶端到文字的底部。

解决方法：
方法1：将line-height的值设置为跟字体大小一致；如果高度达不到，用padding去撑；
方法2：不设置line-height，它会自动文字居中，对ie进行hack设置line-height的值以保证文字垂直居中；这里要注意一下，如果在reset的样式文件中有设置line-height等值，请进行line-height的重置，可以用line-height:normal。


#### **5.边框0.01rem小屏手机不显示**

做过一个H5，设置`<html lang="en" style="font-size: 44.44px;">`，然后页面上有0.01rem的边框，在iphone6上测试时边框是正常显示的，换用小屏的iphone5后就不显示了，我把边框调大就可以显示。
rem是相对于html font size的。
0.01rem可能少于1px，所以直接为0了。
解决方法：
如果对边框的宽度要求不是很高的话，直接用1px，或者用0.02rem。
如果对边框的宽度要求比较高，又要适应像有些iphone那种retina屏的话，额。。。还是感觉没有很方便的解决方案
[http://top.css88.com/archives/722](http://top.css88.com/archives/722)
[https://www.cnblogs.com/sese/p/7067961.html](https://www.cnblogs.com/sese/p/7067961.html)






参考：
[http://blog.csdn.net/luochao_tj/article/details/17755457](http://blog.csdn.net/luochao_tj/article/details/17755457)


[https://segmentfault.com/a/1190000008271481](https://segmentfault.com/a/1190000008271481)





















