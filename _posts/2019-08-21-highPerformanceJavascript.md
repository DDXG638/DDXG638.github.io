---
layout:     post
title:      "《高性能JavaScript》快读记录"
subtitle:   "记录一些总结地比较好经验，知识点和需要进一步去学习的东西。为之后的精读做准备。"
date:       2019-08-21 15:00:00
author:     "ddxg"
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - JavaScript
    - 读书笔记
---

# 《高性能JavaScript》记录

# chapter 1 加载和执行

> 多数浏览器使用单一进程来处理用户界面（UI）刷新和JavaScript脚本执行，同一时刻只能做一件事。
> 页面中有`<script>`标签的地方，不管js是内嵌型的还是外链型的，页面的下载和渲染都必须停下来等待脚本执行完成。
> 因为js代码可能会修改页面内容（增删改节点、修改能容等），浏览器遇到`<script>`标签会停止页面处理，先执行js代码然后再继续解析和渲染页面。
> 如果是外链js，浏览器处理js的过程：`下载->解析->执行`，在这个过程中，页面的渲染和用户交互是完全被阻塞的。

### 脚本位置

> 将脚本放在底部

由于 脚本的
下载、解析和执行会阻塞其他资源（图片等）的下载，也会阻塞页面渲染，**一般推荐将所有`<script>`标签放到`<body>`标签的底部**，尽可能减少对整个页面下载的影响。

### 组织脚本

> 减少JavaScript文件大小，尽量减少页面中`<script>`标签的数量，不管是外链还是内嵌。

- 浏览器解析HTML时每遇到一个`<script>`标签都会因执行脚本而导致一定的延时，需要最小化延迟时间
- HTTP请求会带来额外的性能开销

使用CDN的话尽可能将多个js文件合并在一起加载，或者在服务端处理资源合并操作。

### 延迟的脚本
HTML4为`<script>`标签定义了一个拓展属性defer

> `<script src="file.js" defer></script>` 

defer的特点： 
- 不阻塞，下载时不会阻塞浏览器的其他进程，defer属性文件可以与页面中的其他资源并行下载
- defer 属性指明本元素所含的脚本不会修改DOM，代码可以安全地延迟执行
- 对应的js文件将在页面解析到`<script>`标签时开始下载，但不会执行，知道DOM加载完成（onload事件被触发前）

```html
<html>
    <head>
        <title>defer属性</title>
    </head>
    <body>
        <!-- 外部文件只打印js文件名 -->
        <script src="./js/defer/defer.js" defer></script>
        <script src="./js/defer/script.js"></script>
        <script src="./js/defer/load.js"></script>
    </body>
</html>
```

支持defer属性的浏览器的执行顺序是：`script -> defer -> load`

不支持defer属性的浏览器的执行顺序是：`defer -> script -> load`

> 对于js库的加载、不会马上修改DOM的js加载可以使用defer属性


** 注意：主流浏览器都实现了defer属性，W3C的HTML5规定：defer属性仅当src属性声明时才生效，就是对于内联js代码不起作用 **


### 动态脚本元素

> 动态脚本时最通用的无阻塞加载解决方案

``` javascript
let $script = document.createElement('script');
$script.type = 'text/javascript';
$script.src = './js/defer/defer3.js';
document.head.appendChild($script);
```

- 动态脚本在script元素被添加到页面时开始下载
- 文件的下载和执行过程不会阻塞页面的其他进程

如果想在动态脚本加载完之后进行一些操作的话，可以使用script节点的`onload`事件

``` javascript
$script.onload = function() {
    // TODO
}
```

封装成一个方法
``` javascript
// 动态加载脚本
function loadScript(url, cb) {
    let $script = document.createElement('script');
    $script.type = 'text/javascript';

    // 动态脚本的onload事件
    // IE与众不同
    if ($script.readyState) {
        $script.onreadystatechange = function() {
            let state = $script.readyState;
            if (state === 'loaded' || state === 'complete') {
                // 防止loaded 和 complete 都触发，导致代码执行两次
                $script.onreadystatechange = null;
                cb();
            }
        };
    } else {
        // 正常浏览器
        $script.onload = function() {
            cb();
        };
    }

    $script.src = url;
    document.head.appendChild($script);
}

loadScript('./js/defer/dynamic.js', function () {
    console.log('--- 动态加载后执行 ---');
});
```

### XMLHttpRequest简本注入

> 通过xhr请求的方式来加载js

``` javascript
let xhr = new XMLHttpRequest();
xhr.open('get', 'file.js', true);

xhr.onreadystatechange = function() {
    if (+xhr.readyState === 4) {
        let status = +xhr.status;
        if (status >= 200 && status < 300 || status === 304) {
            let $script = document.createElement('script');
            $script.type = 'text/javascript';
            $script.text = xhr.responseText;
            document.head.appendChild($script);
        }
    }
};

xhr.send(null);
```

优点：
- 无阻塞加载脚本
- 可以先下载js代码，但不立刻执行，执行可控

缺点：
- 会涉及到跨域的问题

**因为跨域问题，大型的web应用一般不会采用这种方式加载脚本。**

### 本章总结
- 将所有的`<script>`标签放到`<body>`标签的底部，这可以确保在简本执行前页面已经完成了渲染。
- 合并脚本，尽量减少`<script>`标签的数量，内嵌和外链都如此。
- 可以使用无阻塞加载js的方法
    - `<script>`标签的defer属性
    - 动态创建`<script>`元素来下载和执行脚本【推荐】
    - 使用XHR对象下载js代码并注入到页面中



# chapter 2 数据存储


