---
layout:     post
title:      "display:table-cell布局"
subtitle:   "table-cell"
date:       2017-12-04 22:23:02
author:     "ddxg"
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - CSS
    - 布局
---


## **display:table-cell布局**

display:table-cell指让标签元素以表格单元格的形式呈现，使元素类似于td标签。IE8+及现代版本的浏览器都支持此属性，IE6/7不支持（可用其他方法实现类似效果）。同样，display：table-cell属性也会被float，position：absolute等属性破坏效果，所以尽量避免同时使用。

CSS display属性的表格布局相关属性的解释：

 - table    此元素会作为块级表格来显示（类似 <table>），表格前后带有换行符
 - table-row-group    此元素会作为一个或多个行的分组来显示（类似 <tbody>）
 - table-header-group    此元素会作为一个或多个行的分组来显示（类似 <thead>）
 - table-footer-group    此元素会作为一个或多个行的分组来显示（类似 <tfoot>）
 - table-row    此元素会作为一个表格行显示（类似 <tr>）
 - table-column-group    此元素会作为一个或多个列的分组来显示（类似 <colgroup>）
 - table-column    此元素会作为一个单元格列显示（类似 <col>）
 - table-cell    此元素会作为一个表格单元格显示（类似 <td> 和 <th>）
 - table-caption    此元素会作为一个表格标题显示（类似 <caption>）

不过常用的就只用table和table-cell吧。

设置了display：table-cell的元素有一下特点：

 - 对宽度高度敏感
 - 对margin值无反应
 - 响应padding属性
 - 内容溢出时会自动撑开父元素

使用display：table-cell可以实现一下几种效果：

 1. 大小不固定元素的垂直居中
 2. 两列自适应布局
 3. 左右等高


#### **1.大小不固定元素的垂直居中**
效果：
![这里写图片描述](http://img.blog.csdn.net/20171203211806323?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

``` HTML
<div class="content">
    <div style="height: 100px;width: 80px;background: #cccccc;"></div>
    <div style="height: 120px;width: 80px;background: #639146;"></div>
    <div style="height: 140px;width: 80px;background: #2B82EE;"></div>
    <div style="height: 160px;width: 80px;background: #F57900;"></div>
    <div style="height: 180px;width: 80px;background: #BC1D49;"></div>
    <p style="width: 100px">顶顶顶顶顶</p>
    <p style="width: 100px">顶顶顶顶顶顶顶顶顶</p>
</div>
```

``` CSS
	.content {
        display: table-cell;
        padding: 10px;
        border: 2px solid #999;
        height: 250px;
        vertical-align: middle;
    }
    
    .content div {
        display: inline-block;
        vertical-align: middle;
    }
    .content p {
		display: inline-block;
        vertical-align: middle;
    }
```
外层的div设置了display: table-cell;vertical-align: middle，内层的元素设置vertical-align: middle样式，内层的元素就会实现垂直居中，段落p也可以垂直居中。有时需要实现据居中效果是非常方便的。

#### **2.两列自适应布局**
想这种左边图片右边文字的布局真的很常见，实现起来也不难，可以左右浮动或者使用display:inline-block;等来实现，我感觉使用table-cell来做感觉更加简单。
![这里写图片描述](http://img.blog.csdn.net/20171203213141622?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

``` HTML
<div class="content2">
    <div class="left-box">
        <img src="./img/shop3.jpeg" width="70">
    </div>
    <div class="right-box">哈水水哈水水水水。。。。</div>
</div>
```

```
	.content2 {
		display: table;
        padding: 10px;
        width: 600px;
        border: 2px solid #999;
    }
    
    .left-box {
        float: left;
        margin-right: 10px;
    }
    
    .right-box {
        display: table-cell;
        padding: 10px;
        width: 2000px;
        vertical-align: top;
        border: 1px solid #ccc;
    }
```
最外层使用display: table,左边的图片使用左浮动来实现，右边文字部分使用table-cell样式，这样右边文字部分就像表格中的td一样，表格中的td的宽度是由内容撑开的，为了防止内容过少导致宽度很小的情况，就将右边内容部分的宽度设为一个很大的值，起到撑开断宽度的作用。

#### **3.左右等高**
![这里写图片描述](http://img.blog.csdn.net/20171203215422600?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
这个很好理解，表格中每一行的高度都是相同的，这个其实就是模拟表格。

``` HTML
<div class="content3">
    <div class="box1">左右一样高</div>
    <div class="box2">嘎嘎嘎嘎嘎嘎个哈哈哈哈哈哈。。。</div>
</div>
```
```
	.content3 {
        display: table;
        padding: 10px;
        border: 2px solid #999;
    }
    
    .box1 {
        display: table-cell;
        width: 100px;
        border: 1px solid red;
    }
    
    .box2 {
        display: table-cell;
        border: 1px solid red;
    }
```


我觉得平时比较想到会用table-cell的一般是在垂直居中上，两列自适应布局感觉挺方便的，以后有是相当的机会一定会用上的。
















