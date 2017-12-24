---
layout:     post
title:      ":before与:after"
subtitle:   "css伪类效果"
date:       2017-12-24 21:51:43
author:     "ddxg"
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - CSS3
---


## **:before与:after**

:before与:after是css2中的伪类，::before与::after是css3中的伪元素。平时使用伪类比较少，伪类有时候使用还是挺方便的。

#### **1、:before与::before的区别**

 1. :befor是Css2的写法，::before是Css3的写法，css3为了区分伪类和伪元素，伪元素采用双冒号写法。
 2. :before的兼容性要比::before好 ，如果网站只需要兼容webkit、firefox、opera等现代浏览器，像H5开发，建议对于伪元素采用双冒号的写法，如果不得不兼容IE浏览器，还是用CSS2的单冒号写法比较安全。
 3. :befor和::before写法是等效的。

需要注意的是：伪类对象要配合content属性一起使用，用于在css渲染中向元素逻辑上的头部或尾部添加内容。这些添加不会出现在DOM中，所以不能通过js来操作，不会改变文档内容，不可复制，仅仅是在css渲染层加入

#### **2、用法**

先来一个例子：在超链接后面加href中的内容，标签中的属性都是可以获取的。

```
<style type="text/css">
	.exp1::after {
		content: '('attr(href)')';
	}
	.h1::before {
		content: attr(data-text);
	}
</style>

<h3 class='h1' data-text='attr'>例子:</h3>
<a class="exp1" href="https://ddxg638.github.io">东东小瓜的博客</a>
```
![这里写图片描述](http://img.blog.csdn.net/20171224173009078?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

比较重要的是content属性
![这里写图片描述](http://img.blog.csdn.net/20171224170937634?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
**注意：** IE8只有指定!DOCTYPE才支持Content属性。

#### **3、实现的效果**

**1. 清理浮动**
清理浮动平时用得还是比较多的：

```
	.clearfix:after{
		content:"";//设置内容为空
		height:0;//高度为0
		line-height:0;//行高为0
		display:block;//将文本转为块级元素
		visibility:hidden;//将元素隐藏
		clear:both//清除浮动
	}
	.clearfix{
		zoom:1;为了兼容IE，因为ie6/7不能使用伪类
	}
```

**2. 硕大的引号**

```
.exp2::before {
	content: open-quote;
	color: #DDD;  
    font-size: 40px;  
    font-family: serif;  
    font-weight: bolder;  
}
.exp2::after {
	content: close-quote;
	color: #DDD;  
    font-size: 40px;  
    font-family: serif;  
    font-weight: bolder;  
}

<p class="exp2">扭扭车坤宁宫弄呢女傲慢</p>
```
![这里写图片描述](http://img.blog.csdn.net/20171224175052429?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**3. 添加题标**
```
.exp3::before {
	content: url(img/xtb2.jpg);
}

<p class="exp3">扭扭车坤宁宫弄呢女傲慢</p>
```

**4. 设置编号**
counter-reset属性创建或重置一个或多个计数器。
counter-increment属性递增一个或多个计数器值。
详情请看：[counter-reset](http://www.runoob.com/cssref/pr-gen-counter-reset.html)
[counter-increment](http://www.runoob.com/cssref/pr-gen-counter-increment.html)
```
.exp4 {
	counter-reset: section;
}
h1{
    counter-reset: subsection;
}
h1:before{
    counter-increment:section;
    content:counter(section) "、";
}
h2:before{
    counter-increment:subsection;
    content: counter(section) "." counter(subsection) "、";
}


<div class="exp4">
	<h1>HTML tutorials</h1>
	<h2>HTML Tutorial</h2>
	<h2>XHTML Tutorial</h2>
	<h2>CSS Tutorial</h2>

	<h1>Scripting tutorials</h1>
	<h2>JavaScript</h2>
	<h2>VBScript</h2>

	<h1>XML tutorials</h1>
	<h2>XML</h2>
	<h2>XSL</h2>
</div>
```
![这里写图片描述](http://img.blog.csdn.net/20171224182127234?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**5. 鼠标悬浮效果**

```
.exp5 {
	width: 400px;
	height: 200px;
	background: #1869bf; 
	text-align: center;
}
.exp5 a {
	text-decoration: none;
	font-size: 20px;
	color: red;
}

.exp5 a:hover::before 
{ 
	position: relative;
	content: "["; left: -20px; 
}  
.exp5 a:hover::after 
{ 
	position: relative;
	content: "]"; right:  -20px; 
} 

<div class="exp5">
	<a href="#">悬浮出现方括号</a>
</div>
```
当鼠标悬浮到文字上的时候，通过::before和::after在左右两边添加中括号，只是简单的实现，要更精致或者更多效果要花一些心思。
![这里写图片描述](http://img.blog.csdn.net/20171224193301108?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


**6. 为文字设置两种颜色**
通过::before添加有颜色的部分，控制width和height的百分比，要注意的是要设置文字不换行，还有overflow隐藏。
![这里写图片描述](http://img.blog.csdn.net/20171224200402928?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

```
.exp6 {
	position: relative;
	margin-right: 20px;
	font-size: 30px;
	color: #aaa;
}
.exp6::before {
	content: attr(data-text);
    position: absolute;
    width: 50%;
    color: blue;
    white-space: nowrap;
    overflow: hidden;
}

<span class="exp6" data-text='小冬瓜'>小冬瓜</span>
```
要设置上下两部分的话只需要把width改为height。

**7. 实现消息的尖角**
这里是使用了两个三角形重叠的效果，before是一个用边框形成的较大的三角形，颜色是红色的；after是一个用边框形成的较小的三角形，颜色是斑色的。通过绝对定位，重叠后漏出来的部分就是尖角了
![这里写图片描述](http://img.blog.csdn.net/20171224210012036?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

```
.exp7 {
	margin: 10px;
	position: relative;
	width: 100px;
	height: 100px;
	border: 2px solid red;
}
.exp7::before {
	position: absolute;
	content: ' ';
	width: 0;
	height: 0;
	left: 100%;
	top: 40px;
	border: 12px solid transparent;
	border-left-color: red;
}
.exp7::after {
	position: absolute;
	content: ' ';
	width: 0;
	height: 0;
	left: 100%;
	top: 42px;
	border: 10px solid transparent;
	border-left-color: #fff;
}

<div class="exp7"></div>
```

参考：
http://blog.csdn.net/u010737354/article/details/50835731
https://www.cnblogs.com/Li-Cheng/p/3644395.html
http://blog.csdn.net/Sabrina_TSM/article/details/52102182














