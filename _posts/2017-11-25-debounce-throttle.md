---
layout:     post
title:      "js函数节流和去抖动"
subtitle:   "搜索框下拉提示"
date:       2017-11-25 18:13:43
author:     "ddxg"
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - JavaScript
    - html
---

## **搜索框下拉提示--js函数节流和去抖动**

#### **1.搜索框的事件监听**
前几天做了一个类似百度的那种自动提示的搜索框，需要动态监听输入框值变化。使用onkeydown、onkeypress、onkeyup这些事件监听的话，是无法监听鼠标右键的复制、剪贴和粘贴操作的。使用oninput事件是可以解决鼠标右击操作的，但是oninput事件不完全兼容IE9，这时可以再绑定IE 特有的 onpropertychange事件达到兼容的效果。

```javascript
$('textarea').on('input propertychange', function() {
    // ...
});
```

#### **2.js函数节流和去抖动**
做好这个功能的时候就发现使用中文输入法进行文字输入的时候，每输入一个拼音就会发送一次搜索请求，这对服务器肯定会造成压力的。
我就输入了一个“小”字就发送了5四请求：
![这里写图片描述](http://img.blog.csdn.net/20171125145928054?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

我使用的是百度搜索的接口，所以我就看来问一下百度搜索是怎么解决的，发现百度搜索也是每输入一个拼音就发送一次请求的。人家是百度嘛，多发几个请求也没什么啦。
![这里写图片描述](http://img.blog.csdn.net/20171125150235816?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

使用js函数节流和去抖动的方法可以缓解这种频繁的事件的触发。频繁的事件触发像window对象的resize、scroll事件，mousemove、mousedown、keydown、keyup等事件。
解决方法有两种：debounce和throttle

 1.  **debounce去抖动**
利用定时器，让函数执行延迟500毫秒，在500毫秒内如果有函数又被调用则删除上一次调用，这次调用500毫秒后执行，如此往复。就是当你停止输入500毫秒后才会执行函数

``` javascript
// 方法一：
function debounce(method,context){
    clearTimeout(method.timeout); // 清空上一个定时器
    method.timeout = setTimeout(function(){ // 重新定义定时器
          method.call(context); // 执行传入的回调函数
    },500);
 }
```
这里传入的method是触发事件回调函数，context是要调用的回调函数的作用域

``` javascript
// 方法二：
function debounce(method,delay){
	var timer = null; 
	return function(){
	    var context = this,args = arguments;
	    clearTimeout(timer); 
	    timer = setTimeout(function(){
	        method.apply(context,args); 
	    },delay);
	}
}
```
这里传入的method是触发事件回调函数，delay是延迟的时间。

我使用的是第一种方法，设置搜索的延迟时间为500毫秒。

```
search:function(ev){
	//30代表上键，40代表下键
	if(ev.keyCode==38||ev.keyCode==40){
		return;
	}
	if(ev.keyCode==13){
		    window.open('https://www.baidu.com/s?wd='+this.searchstr);
            this.searchstr='';             
    }
    clearTimeout(this.timer);
    var $tt = this;
    this.timer = setTimeout(function(){
		$tt.getSearchList();
	}, 500);
}
```
其中getSearchList()就是执行搜索的方法，使用拼音输入法输入“小”字的时候搜索请求的次数为1次：
![这里写图片描述](http://img.blog.csdn.net/20171125171552462?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

 2.  **throttle节流**
 节流与抖动不一样，节流是会说预先设定一个执行周期，当调用动作的时刻大于等于执行周期则执行该动作，然后进入下一个新周期。拿onscroll事件来说，如果使用的是抖动的话，只用当滚动停止后才会执行回调函数，在滚动的过程是不会执行的。节流不同，滚动的时候每隔一个时间周期就会执行一次回调，然后进入下一个新周期

```
function throttle(method, delay, time) {
     var timeout,startTime = new Date();
     return function() {
         var context = this,
         args = arguments,
         curTime = new Date();
         clearTimeout(timeout);
         // 如果达到了规定的触发时间间隔，触发 handler
         if (curTime - startTime >= time) {
           method.apply(context, args);
           startTime = curTime;
       } else {
	       // 没达到触发间隔，重新设定定时器
           timeout = setTimeout(method, delay);
     }
 };
```





























