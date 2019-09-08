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

图片 和 文字 的宽度权重远大于布局，容器宽度为0的情况下，容器宽度宽度将会是容器内容（图片和文字）的最小宽度。

- 文字：像中文，最小宽度为每个汉字的宽度，英文则为连续字符串的宽度。可以使用`word-break:break-all`使得英文按一个字符换行。
- 图片：最小宽度就是它本身的宽度。

样式中，`outline` 不占据空间，`border` 占据空间。


### 内部尺寸 - 最大宽度

经常做可以 左右滚动的 类似跑马灯的效果，显示内容区域的宽度是一定的，里面的内容排列成一排，可以左右滚动。

问题是要怎么才能让里层的元素排成一行不换行

我的做法有两种：
- 使用 `table` 布局，`display: table-row` 包 `display: table-cell`，因为表格是不会换行的。
- 外层元素使用 `white-space:nowrap` 样式，内层元素 需要是 `inline` 形式的。
- 外层元素使用 `width: max-content` 样式，则外层宽度根据内层元素个数而定。虽然有兼容性问题，但是移动端基本可以使用。


### width值作用细节

`width/height` 值是作用在 内在盒子 上面的

内在盒子 从里到外由：content box 、padding box 、 border box 和 margin box 构成。

默认情况下，`width/height` 值是作用在 内在盒子 的 `content box` 上面的。

可以通过 `box-size` 属性改变。属性值没有`margin-box`。

还有一个比较偏的是 `background-origin` 属性的默认值是 `padding-box`。

有个小点，无论 替换元素（像input、textarea、img、video）的 `display` 值是 什么，水平方向上都无法100%自适应父容器，需要手动设置`width:100%`。



