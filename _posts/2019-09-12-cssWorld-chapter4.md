---
layout:     post
title:      "CSS 世界 - 盒尺寸四大家族"
subtitle:   "《CSS 世界》读书笔记"
date:       2019-09-07 22:00:00
author:     "ddxg"
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - CSS
    - CSS World
---

# 盒尺寸四大家族

> 盒尺寸中有四个盒子：`content box`、`padding box`、`border box` 和 `margin box`。分别对应css中的`content`、`padding`、`border` 和 `margin`。

## content 与 替换元素

> 替换元素：修改某个属性值呈现的内容就可以被替换的元素，典型的就是`<img>`，还有`<video>`、`<iframe>`、`<textarea>` 和 `input`等等。。。

### 替换元素的尺寸计算规则

> 替换元素从内到外有：固有尺寸、HTML尺寸和css尺寸。

- 固有尺寸，就是元素本身的尺寸，`<img src="1.png">`，图片`1.png`的尺寸是`100*50`，则图片的尺寸就为`100*50`。
- HTML尺寸，通过HTML属性设置的尺寸，`<img width="100" height="50">`、`<input size="30">` 等
- css尺寸，就是通过css设置的尺寸

这三种尺寸的优先级： `css尺寸 > HTML尺寸 > 固有尺寸`。

``` html
<!-- 1.png的尺寸为：100px * 52px -->
<!-- 图片最终的尺寸为：200*150 -->
<img src="1.png" width="200px" height="150">


<!-- 如果设置了css样式，则图片最终的尺寸为：111*55 -->
<style>
img {
    width: 111px;
    height: 55px;
}
</style>
```

这里要注意的是，css是无法改变替换元素内容的固有尺寸的，对于图片来时，css没有改变替换元素的固有尺寸。
而是因为图片中的content替换内容默认的适配方式是填充(object-fit:fill)，就是外部尺寸多大就填满、跟着一样大。
css3中有个[object-fit](https://developer.mozilla.org/zh-CN/docs/Web/CSS/object-fit)的属性


内联替换元素 和 块级替换元素(设置了`display:block;`) 使用同一套尺寸计算规则。
这就是为什么给 `表单` 等替换元素设置 `display:block;` 之后宽度没有 `100%` 容器的原因。


额外话题，做图片懒加载的时候，渲染图片的时候可以先不设置`<img>`的`src`属性，
因为即使设置`src=""`，有些浏览器也是会发起请求的，请求的就是当前页面的数据。
``` css
img {/* 没有src属性的样式 */}
img[src] {/* 有src属性的样式 */}
```
使用过一个图片懒加载的库：[toddmotto/echo](https://github.com/toddmotto/echo)，不过感觉好老了(T_T)

[基于伪元素的图片内容生成技术](https://demo.cssworld.cn/4/1-2.php)
当 `<img>` 标签没有 `src` 属性时，`::before 和 ::after` 可以生效，可以利用`alt` 属性展示一些信息，
当有 `src` 属性时，`::before 和 ::after` 全部无效了。应用场景还是很巧妙的。

`:empty` 选择器，当元素里面没有内容的时候进行匹配。可以根据内容的有无展示不同的样式。



### content 内容生成技术

有好几个例子，这里列举一下
#### [两端对齐和下边缘对齐](https://demo.cssworld.cn/4/1-7.php)
#### [字体图标](https://demo.cssworld.cn/4/1-8.php)
#### [动态...](https://demo.cssworld.cn/4/1-9.php)
#### `content: attr(alt)` attr属性值内容生成，自定义的属性也可以使用。
#### content 生成的混合特性
    ``` css
        a:after{
            content: "(" attr(href) ")";
        }
        q:before{
            content: open-quote url(1.png);
        }
        .counter.before{
            content: counter(sdf, '-') '. ';
        }
    ```
#### content 计数器

计数器中有三个相关的属性。

- counter-reset，用于给计数器起名字 和 计数初始值。可以叠加，使用空格分开
[例子](https://demo.cssworld.cn/4/1-11.php)
``` css
.counter {
  counter-reset: wangxiaoer 2; 
  font-size: 32px; 
  font-family: Arial Black; 
  color: #cd0000; 
}
.counter:before { 
  content: counter(wangxiaoer); 
}

/* 可以叠加 */
.diejia {
    counter-reset: wangxiaoer 2 wangxiaoer2 3;
}
```

- counter-increment，计数器可以递增（或递减）某个值，这可以是正值或负值。
如果没有提供 number 值，则默认为 1。[例子](https://demo.cssworld.cn/4/1-13.php)
计数器的数值变化遵循 `HTML` 渲染顺序，每遇到 `increment` 计数器就变化，什么时候 `counter` 输出就输出此时的计数值。

``` css
.counter {
  counter-reset: wangxiaoer 2;
  font-size: 32px; 
  font-family: Arial black; 
  color: #cd0000; 
}
.counter:before,
.counter:after { 
  content: counter(wangxiaoer);
  counter-increment: wangxiaoer;
}

/* 可以指定增加的数值 */
.counter:after { 
  content: counter(wangxiaoer);
  counter-increment: wangxiaoer 2; /* 每次增加2 */
}
```

- counter()/counters()，方法，从上面看，`conuter(myCounter)` 就是输出计数值。
还有第二个参数：[`list-style-type`](https://www.w3school.com.cn/css/pr_list-style-type.asp)，可以指定递增的是英文字母还是罗马数字等
counter也是支持叠加的，[例子](https://demo.cssworld.cn/4/1-17.php)

``` css
.counter {
    counter(myCounter, styleType);
}
```

`counters()` 可是实现 `1.1、1.2、1.3...` 这种形式的序号。基本用法：`counters(name, string)`，
`string` 就是中间的连接符。[例子](https://demo.cssworld.cn/4/1-18.php)

#### 应用

可以使用css实现列表选择个数的展示。勾选item之后，就会显示勾选的个数。[例子](https://github.com/DDXG638/myLearn/blob/master/css-world/charpter4/css-counter.html)

``` html
<style>
    .wrapper{
        counter-reset: mySelect;
    }

    input:checked {
        counter-increment: mySelect;
    }

    p::after {
        content: counter(mySelect) '课程';
    }
</style>


<div class="wrapper">
    <input type="checkbox">语文
    <input type="checkbox">数学
    <input type="checkbox">英语
    <input type="checkbox">历史
    <input type="checkbox">地理
    <input type="checkbox">政治

    <p>已经选择</p>
</div>
```



## padding
