---
layout:     post
title:      "img标签 onerror事件"
subtitle:   "img"
date:       2017-11-25 22:23:02
author:     "ddxg"
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - JavaScript
    - html
---


## **img标签 onerror事件**

有时在img标签src中的图片是不存在的，页面上就会显示一张空的图片，挺难看的。这是可以在img标签中加上onerror事件，当图片的src访问不到是就会调用onerror事件，在时间中将src的连接更改为默认显示的图片。如果默认显示的图片也不能正常访问，这时又会调用onerror事件，一直循环，直到浏览器报内存什么的错误。解决这个问题需要设置img.onerror = null，让onerror事件值调用一次，默认图片错误也不会一直无线循环。

``` html
<img src="...." onerror="replaceImg(this)">
```

``` javascript
function replaceImg(img) {
    img.src = /*默认的图片地址*/;
    img.onerror = null; 
}
```



