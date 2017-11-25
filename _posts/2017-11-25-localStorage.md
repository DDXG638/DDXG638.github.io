---
layout:     post
title:      "localStorage存储"
subtitle:   "store.js"
date:       2017-11-25 21:51:43
author:     "ddxg"
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - javascript
    - html5
---

## **localStorage存储**

localStorage的api：

``` javascript
localStorage.setItem(key,value); // 保存数据
localStorage.getItem(key); // 读取数据
localStorage.removeItem(key); // 删除单个数据
localStorage.clear(); // 删除所有数据
localStorage.key(index); // 得到某个索引的key
localStorage.length; // localStorage存储变量的个数
```

封装一下cookie和localStorage：
``` javascript
var storage = {
    addCookie: function (key, value, domain, expireMilliseconds) {
        var date, str;
        str = key + '=' + value;
        date = new Date();
        date.setTime(date.getTime() + expireMilliseconds);
        return document.cookie = str + ';Domain=' + domain + ';expires=' + date.toUTCString() + ";Path=/";
    },
    getCookie: function (key) {
        var array, c, cookies, i, len, value;
        key = this.trim(key);
        cookies = document.cookie.split(';');
        value = null;
        for (i = 0, len = cookies.length; i < len; i++) {
            c = cookies[i];
            array = c.split('=');
            if (this.trim(array[0]) === key) {
                value = this.trim(array[1]);
                break;
            }
        }
        return value;
    },
    removeCookie: function (key, domain) {
        var value;
        if (key) {
            value = this.getCookie(key);
            if (value) {
                this.addCookie(key, value, domain, -1);
                return true;
            }
        }
        return false;
    },
    //假如浏览器支持本地存储则从localStorage里getItem，否则乖乖用Cookie
    getItem:function(key){
      return window.localStorage? localStorage.getItem(key): this.getCookie(key); 
    },
    //假如浏览器支持本地存储则调用localStorage，否则乖乖用Cookie
    setItem:function(key,val){
        if (window.localStorage) {
            localStorage.setItem(key,val); 
        } else {
            this.addCookie(key,val); 
        }
    }
};
```

如果需要存取json数据需要处理一下：

```
JSON.stringify(json) // 将json对象转为json字符串
JSON.parse(str) // 将json字符串转为json对象

var obj = {a: 'Hello', b: 'World'};
storage.setItem("key", JSON.stringify(obj));
JSON.parse(storage.getItem("key"))
```

#### **store.js **
store.js 是一个兼容所有浏览器的 LocalStorage 包装器，不需要借助 Cookie 或者 Flash。store.js 会根据浏览器自动选择使用 localStorage、globalStorage 或者 userData 来实现本地存储功能。store.js 在存取的时候会自动 stringify、parse。
[GiHub上的地址](https://github.com/marcuswestin/store.js/)

先引入js
![这里写图片描述](http://img.blog.csdn.net/20171125214617617?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

api是非常简单的：

```
// Store current user
store.set('user', { name:'Marcus' })

// Get current user
store.get('user')

// Remove current user
store.remove('user')

// Clear all keys
store.clearAll()

// Loop over all stored values
store.each(function(value, key) {
	console.log(key, '==', value)
})
```




























