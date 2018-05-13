---
layout:     post
title:      "判断页面中的图片是否加载完成"
subtitle:   "页面加载时判断图片是否完全加载"
date:       2018-05-13 21:51:43
author:     "ddxg"
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - HTML5
    - javaScript
---


## **判断页面中的图片是否加载完成**

有时候需要在页面一开始就获取图片的高度进行一些操作，有个问题就是在获取图片高度的时候图片还没有完全加载完成，这样获取的图片高度是0。所以需要在获取图片高度之前判断一下图片是否加载完成。

如果只要监听一张图片，可以使用`onload`

```
$('img').load(function(){
  // 加载完成    
});
```

如果有很多图片需要一起箭筒的话上面的方法就需要执行好几次
```
var t_img; // 定时器
var isLoad = true; // 指定的图片是否加载完成

// 判断图片加载状况，加载完成后回调
isImgLoad(function(){
    // 加载完成
});

// 判断图片加载的函数
function isImgLoad(callback){
    // 'cover'是要监听图片的类名。
    // 查找所有封面图，迭代处理
    $('.cover').each(function(index, val){
        // 找到为0就将isLoad设为false，并退出each
        if(!val.complete){ // 判断是否加载完成
            isLoad = false;
            return false; 
        }
    });
    // 为true，图片加载完毕
    if(isLoad){
        clearTimeout(t_img); // 清除定时器
        // 回调函数
        callback();
    // 为false，有图片没有加载完成，将调用定时器递归
    }else{
        isLoad = true;
        t_img = setTimeout(function(){
            isImgLoad(callback); // 递归扫描
        },200); // 200毫秒就判断一次，可以自己调整
    }
}
```

