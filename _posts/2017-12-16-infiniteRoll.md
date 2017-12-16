---
layout:     post
title:      "html 文字、图片水平无限滚动"
subtitle:   "文字、图片滚动"
date:       2017-12-16 21:51:43
author:     "ddxg"
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - CSS3
    - javascript
---


## **html 文字、图片水平无限滚动** 

经常在淘宝、京东的app页面中看见水平的广告轮播和中奖信息的轮播。

我知道的实现这种无限滚动效果的方法有三种：

 - 使用CSS3样式实现
 - 使用js实现
 - marquee标签（好像不推荐使用了，我没用过，百度可以找到挺多相关博客的）
 

### **1.方式** 
不管是用css实现还是js，实现方式是差不多的。为了让无限循环滚动时内容衔接更好一点，滚动内容会有两份一模一样的，大概步骤如下：
![这里写图片描述](http://img.blog.csdn.net/20171216185146992?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### **2.CSS3实现** 
主要使用到了绝对定位、动画、@keyframes属性，主要是靠@keyframes中的from和to来控制。
HTML代码中，前三张图片与后三张图片是一样的，前三张图片就是要无限滚动的图片。

``` HTML
<div class="container">  
	    <ul class="imgcss">  
	        <li>
	        	<img src="./img/shop4.jpg"> 
	        </li>
	        <li>
	        	<img src="./img/shop5.jpg"> 
	        </li>
	        <li>
	        	<img src="./img/shop7.jpg"> 
	        </li>
	        <li>
	        	<img src="./img/shop4.jpg"> 
	        </li>
	        <li>
	        	<img src="./img/shop5.jpg"> 
	        </li>
	        <li>
	        	<img src="./img/shop7.jpg"> 
	        </li>
	    </ul>  
	</div>  
```
CSS代码是比较关键的，要滚动的部分（我这是拿ul标签作为滚动）需要设置动画`animation:scoll 6s linear 0s infinite;`，并且要设置循环`infinite`；图片和文字是需要展示成一行的，我这里用浮动来实现；还有就是写@keyframes，需要计算要滚动的内容的宽度，我的滚动内容是三张图片，是320*240的尺寸，所以@keyframes中to部分就该写960（320*3=960），这样三张图片滚动过了之后，动画才会重新执行，达到无限循环的效果；还有`animation-play-state: paused;` 的意思是当鼠标悬浮在滚动内容上面的时候动画会停止。
也可以直接将图片换成文字，要注意的是需要获取文字的宽度。
``` CSS
.container{  
        width:400px;  
        height: 240px;
        margin:20px auto;  
        overflow: hidden;  
        position: relative;  
        border: 1px solid red;
    }  
    .container .imgcss{  
        animation:scoll 6s linear 0s infinite;  
    } 
    .container .textcss {
		animation:textScroll 6s linear 0s infinite;  
    }
	.container ul {
        width:4000px;  
        left:0;  
        top:0;  
        position: absolute;  
	}
    .container ul:hover {
		animation-play-state: paused;
	} 
    .container ul li{  
        float:left;  
    }  
	@keyframes scoll{  
        from{  
            left:0;  
        }  
        to{  
            left:-960px;  
        }  
    }   
	@keyframes textScroll{  
        from{  
            left:0;  
        }  
        to{  
            left:-986px;  
        }  
    }   
```
我简单实现了一下：
![这里写图片描述](http://img.blog.csdn.net/20171216202534943?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

这种方式看起来挺方便的，在原有的页面上加一些动画的样式就可以实现，但是比较麻烦的是需要知道滚动内容的宽度，如果内容是服务端返回的就比较麻烦了。


### **3.js实现** 
js主要是使用定时器，用scrollLeft、scrollTop来控制滚动的方向。
内容可以使图片，也可以是文字。

HTML：
``` HTML
<div id="container2" class="container2">
		<ul id="ul1">
			<!-- <li>11111111111111111111111111111111111</li>
			<li>22222222222222222222222222222222222</li>
			<li>33333333333333333333333333333333333</li> -->
			<li>
	        	<img src="./img/shop4.jpg"> 
	        </li>
	        <li>
	        	<img src="./img/shop5.jpg"> 
	        </li>
	        <li>
	        	<img src="./img/shop7.jpg"> 
	        </li>
		</ul>
		<ul id="ul2">
			
		</ul>
	</div>
```
CSS：

``` CSS
.container2 {
    	width:600px;  
        height: 240px;
        margin:20px auto;  
        overflow: hidden;   
        border: 1px solid red;
    }
    .container2 ul {
    	display: table-cell;
    }
	.container2 li {
		display: table-cell;
	}
```
js：

``` javascript
var container = document.getElementById("container2");
		var ul1 = document.getElementById("ul1");
		// 将内容复制到ul2中
		var ul2 = document.getElementById("ul2");
		ul2.innerHTML = ul1.innerHTML;

		function marquee() {
			// 当滚动到滚动内容的末端时，就回到原位重新开始滚动
			if (container.scrollLeft >= ul1.scrollWidth) {
				container.scrollLeft = 0;
			} else {
				container.scrollLeft++;
			}
		}
		// 设置一个定时器
		var s = setInterval(marquee, 50);
		// 鼠标悬浮滚动停止
		container.onmouseover = function() { clearInterval(s);  } 
  		container.onmouseout = function() { s = setInterval('marquee()', 50);}
```
实现的效果更上面的是一样的，用js的话感觉会比较灵活。


[我再网上看到了一个封装好了的滚动的源码，可以实现上下左右滚动，文字图片都可以，使用挺方便的。](http://haiqiancun.com/bbs/bbsPage/0/detail/297e9e7946a599780146a5a38dde0003/1/20140826154818)

参考：

 - http://haiqiancun.com/bbs/bbsPage/0/detail/297e9e7946a599780146a5a38dde0003/1/20140826154818
 - http://blog.163.com/liu_hai_feng@126/blog/static/43580500201391125235463/


