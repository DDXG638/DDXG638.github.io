---
layout:     post
title:      "学习Flex布局"
subtitle:   "请多指教"
date:       2017-10-29 12:00:00
author:     "ddxg"
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 前端开发
    - CSS
    - 布局
---



# **Flex布局**

正在实习web前端，写页面的时候，我比较不会写有垂直居中的布局，就感觉我不是很专业，每次都要调调这里调调哪里，有时甚至要调很久才会达到我想要的结果。Flex布局我之前就了解过，在做一个移动端的页面的时候简单地使用过，它在实现垂直居中的问题上还是挺方便的。

先看一下兼容性吧：
![这里写图片描述](http://img.blog.csdn.net/20171029211817875?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
IE的兼容是不太好的，11一下的都gg了

### **Flex布局的基本概念**
flex 的核心的概念就是 容器 和 轴。容器包括外层的 父容器 和内层的 子容器，轴包括 水平主轴(main axis) 和垂直的交叉轴(cross axis)，可以通过修改使垂直方向变为主轴，水平方向变为交叉轴
![Flex布局的基本概念](http://img.blog.csdn.net/20171029212250500?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### **1.Flex父容器**
先要指定一个容器为Flex布局,如果你使用块元素如 div，你就可以使用 flex，而如果你使用行内元素，你可以使用 inline-flex。

```CSS
#dad {
    display: flex | inline-flex;
}
```
当时设置 flex 布局之后，子元素的 float、clear、vertical-align 的属性将会失效。
可以用在父容器中的CSS属性：

 - 列表内容
 - flex-direction
 - flex-wrap
 - flex-flow
 - justify-content
 - align-items
 - align-content
#### **1.1 flex-direction**
决定主轴的方向(即项目的排列方向)

```
#dad {
    display: flex;
    flex-direction : row | row-reverse | column | column-reverse;
}
```
![这里写图片描述](http://img.blog.csdn.net/20171029214546150?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
![这里写图片描述](http://img.blog.csdn.net/20171029214941005?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#### **1.2 flex-wrap: 决定容器内项目是否可换行**
```
#dad {
    display: flex;
    flex-wrap : nowrap | wrap | wrap-reverse;
}
```
![这里写图片描述](http://img.blog.csdn.net/20171029215414702?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
![这里写图片描述](http://img.blog.csdn.net/20171029215526545?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
#### **1.3 flex-flow: flex-direction 和 flex-wrap 的简写形式**
```
#dad {
    display: flex;
    flex-flex : <flex-direction> || <flex-wrap>;
}
```

#### **1.4 justify-content：定义了项目在主轴的对齐方式**

 1. flex-start：起始端对齐
 ![这里写图片描述](http://img.blog.csdn.net/20171029220309992?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
 2. flex-end：末尾段对齐
 ![这里写图片描述](http://img.blog.csdn.net/20171029220357852?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
 3. center：居中对齐
 ![这里写图片描述](http://img.blog.csdn.net/20171029220436810?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
 4. space-around：子容器沿主轴均匀分布，位于首尾两端的子容器到父容器的距离是子容器间距的一半
 ![这里写图片描述](http://img.blog.csdn.net/20171029220513269?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
 5. space-between：子容器沿主轴均匀分布，位于首尾两端的子容器与父容器相切
 ![这里写图片描述](http://img.blog.csdn.net/20171029220554379?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#### **1.5 align-items：定义了项目在交叉轴上的对齐方式**

 1. flex-start：起始端对齐
 ![这里写图片描述](http://img.blog.csdn.net/20171029221045129?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
 2. flex-end：末尾段对齐
 ![这里写图片描述](http://img.blog.csdn.net/20171029221126961?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
 3. center：居中对齐
 ![这里写图片描述](http://img.blog.csdn.net/20171029221153309?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
 4. baseline：基线对齐
 ![这里写图片描述](http://img.blog.csdn.net/20171029221223368?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
 这里的 baseline 默认是指首行文字，即 first baseline，所有子容器向基线对齐，交叉轴起点到元素基线距离最大的子容器将会与交叉轴起始端相切以确定基线
 5. stretch：子容器沿交叉轴方向的尺寸拉伸至与父容器一致
 ![这里写图片描述](http://img.blog.csdn.net/20171029221330763?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
 
#### **1.6 align-content:**
定义了多根轴线的对齐方式，如果项目只有一根轴线，那么该属性将不起作用
```
#dad {
    display: flex;
    align-content : flex-start | flex-end | center | space-between | space-around | stretch;
}
```
![这里写图片描述](http://img.blog.csdn.net/20171029222516310?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### **2.Flex子容器**
有弹性的（flex 即弹性），它们会自动填充剩余空间，子容器的伸缩比例由 flex 属性确定。
flex 的值可以是无单位数字（如：1, 2, 3），也可以是有单位数字（如：15px，30px，60px），还可以是 none 关键字。子容器会按照 flex 定义的尺寸比例自动伸缩，如果取值为none 则不伸缩。
虽然 flex 是多个属性的缩写，允许 1 - 3 个值连用，但通常用 1 个值就可以满足需求，它的全部写法可参考下图。
![这里写图片描述](http://img.blog.csdn.net/20171029222824295?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
#### **2.1 align-self：单独设置子容器如何沿交叉轴排列**
每个子容器也可以单独定义沿交叉轴排列的方式，此属性的可选值与父容器 align-items 属性完全一致，如果两者同时设置则以子容器的 align-self 属性为准。
![这里写图片描述](http://img.blog.csdn.net/20171029222941728?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
例如：
![这里写图片描述](http://img.blog.csdn.net/20171029223049505?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
其他属性参考align-items

### **3.轴**
轴 包括 主轴 和 交叉轴，justify-content 属性决定子容器沿主轴的排列方式，align-items 属性决定子容器沿着交叉轴的排列方式。flex-direction 属性决定主轴的方向，交叉轴的方向由主轴确定。

 1. 向右：flex-direction: row
 ![这里写图片描述](http://img.blog.csdn.net/20171029223758551?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
 2. 向下：flex-direction: column
 ![这里写图片描述](http://img.blog.csdn.net/20171029223829193?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
 3. 向左：flex-direction: row-reverse
 ![这里写图片描述](http://img.blog.csdn.net/20171029223858573?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
 4. 向上：flex-direction: column-reverse
 ![这里写图片描述](http://img.blog.csdn.net/20171029223923754?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)



另：水平垂直居中：
```
#dad {
    display: flex;
    justify-content: center;
    align-items: center
}
```
![这里写图片描述](http://img.blog.csdn.net/20171029224658637?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)



还有align-content属性的用法，持续更新


主要参考：
[一劳永逸的搞定 flex 布局](http://blog.csdn.net/magneto7/article/details/70854472)
[前端早读课【第854期】30 分钟学会 Flex 布局](https://mp.weixin.qq.com/s/-N-q3U-UY4yOrAhHr8uMeg)

























