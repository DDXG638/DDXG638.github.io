---
layout:     post
title:      "CSS 世界 - 流、元素与基本尺寸"
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


# 流、元素与基本尺寸



## 块级元素

常见的块级元素有：`div` 、 `table` 、 `li` 等。
块级元素 不等于就是 `display: block` 的元素，`<table>的display: table`， `<li>de display: list-item`。


每个元素都由两个盒子组成：外在盒子 和 内在盒子
- 外在盒子：负责元素是一行显示，还是换行显示
- 内在盒子：负责宽高、内容等

| display  | 外在盒子 | 内在盒子 |
|---|---|---|
|  block | 块级盒子 | 块级容器盒子 |
|  inline-block | 内联盒子 | 块级容器盒子 |
|  inline | 内联盒子 | 内联盒子 |


## width/height

width/height 属性是作用在 内在盒子 上面的。


### 内部尺寸 - 包裹性
 
`<button>` 标签是 `inline-block` 的代表元素，具有 包裹性 的特性。

标签形式的按钮中的文字是会自动换行的，`input` 形式的按钮由于 `white-space:pre` 的默认样式作用下是不会换行的。

有一个这样子的需求：当按钮文字少的时候，文字水平居中显示，当文字多的时候文字左对齐。

这里可以使用 `inline-block` 的包裹性。文字少的时候居中的是按钮元素，文字多的时候左对齐的是按钮中的文字。
``` html
<style>
p {
    width: 50px;
    text-align: center;
}

button {
    text-align: left;
}
</style>

<p>
    <button>内同内容内同内容</button>
</p>

``` 
[例子](https://demo.cssworld.cn/3/2-5.php)


### 内部尺寸 - 首选最小宽度



