---
layout:     post
title:      "jQuery  jsonp跨域请求"
subtitle:   "JSX"
date:       2018-01-07 21:51:43
author:     "ddxg"
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - JavaScript
---


## **jQuery  jsonp跨域请求**

浏览器的同源策略限制从一个源加载的文档或脚本与来自另一个源的资源进行交互。

#### **1、什么是同源策略**
源就是{协议，主机，端口}定义的。只有这些值完全一样才认为两个资源是同源的。
看个例子：

给出一个对比的URL：http://www.example.com/dir/page.html

![这里写图片描述](http://img.blog.csdn.net/20180107202144240?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

如果要在js中发起跨域请求，则要进行一些特殊处理了。

解决跨域请求的方式有好几种，这里有博客可以参考：
http://blog.csdn.net/joyhen/article/details/21631833

我这里讲的是jQuery  jsonp的跨域请求方式。

#### **2、直接请求不同源的数据**

用node.js开启了两个服务，端口不同，属于不同源，3000端口页面中的点击按钮会向4000端口的服务获取数据。
![这里写图片描述](http://img.blog.csdn.net/20180107203539332?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
ajax请求代码

```
$.ajax({
			url: 'http://localhost:4000/getInfo',
			type: 'GET',
			dataType: 'json',
			success: function (res) {
				$('.tt').text(JSON.stringify(res));
			}
		}
);
```

服务端的代码：刚开始学node.js，代码不是很好，见谅哈
```
var server = http.createServer(function (req, res) {
	
	var params = url.parse(req.url, true).query;
	var data = {
		'name': 'ddxg',
		'age': '22'
	};
	if (req.method == 'GET' && req.url == '/') {
		res.writeHead(200, {'Content-Type': 'text/html;charset=utf-8'});
		fs.createReadStream(__dirname + '/index.html').pipe(res);
	} else {
		res.writeHead(200, {'Content-Type': 'application/json;charset=utf-8'});
		res.end(JSON.stringify(data));
	}

});
```

直接点击按钮获取数据的话会报下面的错

![这里写图片描述](http://img.blog.csdn.net/20180107204518049?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

意思就是：已拦截跨源请求：同源策略禁止读取位于 http://localhost:4000/getInfo 的远程资源。（原因：CORS 头缺少 'Access-Control-Allow-Origin'）。

#### **3、jquery的jsonp方式及其原理**

在页面上直接发起一个跨域的ajax请求是不可以的，不过，在页面上引入不同域上的js脚本、图片、视频等是可以的，像图片，只要连接是对的就可以访问。
所以，就可以利用`<script src="">` 来完成跨域请求。
当点击"点击跨域获取数据"的按钮时，添加一个`<script>`标签，用于发起跨域请求，src为：http://localhost:4000/getInfo?callback=getInfo，getInfo就是回调函数名称，传到后台，用于包裹数据。数据返回到前端后，就是getInfo(res)的形式，因为是script脚本，所以自动调用getInfo函数，而res就是getInfo的参数。

```
<!DOCTYPE html>
<html>
<head>
	<title>跨域请求</title>
</head>
<body>
<h1>3000端口</h1>
<button class="getInfo">点击跨域获取数据</button>
<hr>
<textarea class="tt">123</textarea>

<script src='js/jquery-3.2.1.min.js'></script>
<script type="text/javascript">
	function getInfo (res) {
		$('.tt').text(res.name);
	}

	$(".getInfo").on("click", function () {
		//向头部输入一个脚本，该脚本发起一个跨域请求
		$("head").append("<script src='http://localhost:4000/getInfo?callback=getInfo'><\/script>");
	});
</script>
</body>
</html>
```
服务端的代码：

```
var server = http.createServer(function (req, res) {
	
	var params = url.parse(req.url, true).query;
	var data = {
		name: 'ddxg',
		age: 22
	};
	if (req.method == 'GET' && req.url == '/') {
		res.writeHead(200, {'Content-Type': 'text/html;charset=utf-8'});
		fs.createReadStream(__dirname + '/index.html').pipe(res);
	} else {
		var callbackName = params.callback;
		console.log(req.url);
		res.writeHead(200, {'Content-Type': 'application/json;charset=utf-8'});
		res.end(callbackName+'(' + JSON.stringify(data) + ')');
	}

});
```

在浏览器中查看请求确实是有数据返回的，head标签中增加了一个script标签。
![这里写图片描述](http://img.blog.csdn.net/20180107214132057?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
![这里写图片描述](http://img.blog.csdn.net/20180107213738698?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
这样就完成了跨域的请求，但是比较麻烦，需要自己写脚本发起请求，然后写个回调函数处理数据，不是很方便。

#### **4、jquery的jsonp方式跨域请求**
在ajax请求代码中，需配置一个dataType:'jsonp'，jsonp指定服务器返回的数据类型为jsonp格式，并且jQuery自动在连接中增加了callback参数，这个参数是随机的。ajax中的success方法就跟上面的getInfo一样，如果有success函数则默认success()作为回调函数。
ajax的代码:
```
$.ajax({
			url: 'http://localhost:4000/getInfo',
			type: 'GET',
			dataType: 'jsonp',
			success: function (res) {
				$('.tt').text(JSON.stringify(res));
			}
		});
```
服务端的代码不变。
请求是可以获取数据的，并且请求自动加上了callback参数
![这里写图片描述](http://img.blog.csdn.net/20180107215139555?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
![这里写图片描述](http://img.blog.csdn.net/20180107215242465?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

注意：
设置了`type："jsonp"` 方式后不支持POST方式跨域请求，就算指定成POST方式，会自动转为GET方式。
如果服务端设置成POST方式了，那就不能实现跨域请求了。
jquery的jsonp的实现方式其实就是script脚本请求地址的方式一样，只是ajax的jsonp对其做了封装，更方便使用。

参考：
https://www.cnblogs.com/chiangchou/p/jsonp.html























