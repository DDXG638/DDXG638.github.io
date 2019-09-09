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

绝对定位的 宽高百分比 计算是相对于 padding box 的。在布局的时候需要注意。[例子](https://demo.cssworld.cn/3/2-11.php)

有个小点，无论 替换元素（像input、textarea、img、video）的 `display` 值是 什么，水平方向上都无法100%自适应父容器，需要手动设置`width:100%`。


### height 相关


之前遇到一个问题，现在有了答案。

如果父元素没有一个可以生效的高度值（`height: auto;`），子元素的高度设置为 百分比形式（`height:100%;`） 是无效的。
这个跟浏览器渲染有关，浏览器下载完文档内容之后，是按照从上到下、从外到内的顺序渲染的。当渲染到父元素的时候子元素还没有开始渲染，
等到子元素开始渲染之后，父元素已经渲染完毕高度已经固定。此时子元素的 `height:100%` 就是父元素的高度了。


### min/max-height/width 相关

**超越!important**

`max-width` 会超越覆盖 `width` ，css中的`!important` 权重比 内联样式还高，一般用于覆盖 js 设置的样式。但是 `max-width` 权重比 `!important` 还高。

``` html
<style>
img {
    max-width: 200px;
}
</style>

<img src="..." alt="" style="width: 400px!important;">
```

最终图片的宽度为 `200px`。


**超越最大**

`min-width` 会覆盖 `max-width`。

``` css
.box {
    min-width: 400px;
    max-width: 200px;
}
```

最终结果，宽度为 至少`400px`。

**总结**

权重： `min-width > max-width > width(inline !important)`


### max-height 的巧用

下拉列表的 展开/隐藏 的动画效果是很常见的，一般列表的内容都是不固定的，高度不统一，写动画的时候可能会遇到计算高度的问题。

如果使用 `max-height` 实现的话，可以巧妙的避开计算高度。

``` css
.list-box {
    max-height: 0;
    transition: max-height .2s;
}

.list-box.active {
    /* 设置一个足够大的高度 */
    max-height: 100px;
}
```

这样就可以巧妙的实现动画啦，这里的 `max-height` 值的设置是有讲究的，设置太大的话会影响动画效果。一般设置一个最小的足够大的高度。



## 内联元素 - 幽灵空白点

> 存在与内联盒模型中，在HTML5文档声明中，内联元素的所有解析和渲染表现就如同每个行框盒子的前面有一个“空白节点”一样。
> 这个“空白节点”永远透明，不占据任何宽度。

“空白节点”是一个存在与每个“行框盒子”前面，同时具有该元素的**字体**和**行高**属性的0宽度的内联盒子。

要处理“空白节点”的话，最简单的方式是设置样式：`font-size:0;`。


