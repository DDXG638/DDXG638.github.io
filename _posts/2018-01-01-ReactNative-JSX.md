---
layout:     post
title:      "React Native -- JSX 语法"
subtitle:   "JSX"
date:       2018-01-01 21:51:43
author:     "ddxg"
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - React Native
---


## **React Native -- JSX 语法**


字面上来看JSX即 JavaScript XML取首字母结合，所以JSX并不是一门新语言，仅仅是个语法糖。React发明了JSX，React的核心机制之一就是虚拟DOM，可以在内存中创建虚拟DOM元素。React利用虚拟DOM来减少对实际DOM的操作从而提升性能。 利用HTML语法来创建虚拟DOM，JSX语法会通过Babel转换成浏览器认识的JS。


看个例子，下图中，上面的一块是jsx语法，下面的是纯js语法，这两段代码是完全等价的，很明显上面的更简单更直观，能够使用类似HTML代码高效地定义界面，构建组件及组件之间关系时也会显得很清晰。在这里实质是调用React.createElement函数。
![这里写图片描述](http://img.blog.csdn.net/20180101204002526?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


#### **JSX 中嵌入变量，表达式，样式**

JSX本身就和XML语法类似，可以定义属性以及子元素。唯一特殊的是可以用大括号来加入JavaScript表达式。遇到 HTML 标签（以 < 开头），就用 HTML 规则解析；遇到代码块（以 { 开头），就用 JavaScript 规则解析。
```
import React, { Component } from 'react';
import {
    AppRegistry,
    StyleSheet,
    Text,
    View,
    Dimensions,
    PixelRatio
} from 'react-native';

const {height, width} = Dimensions.get('window');
const pixelRatio = PixelRatio.get();

export default class demo extends Component {
    render() {
        console.log('屏幕的物理高像素为： ' + height * pixelRatio);
        return (
            <View style={styles.container}>
                <Text style={styles.welcome}>
                    Welcome to React Native!{'\n'}
                    真正努力过的人才知道天赋的重要性！
                </Text>
                <Text style={styles.instructions}>
                    To get started, edit index.android.js{'\n'}
                    手机屏幕高度(逻辑像素)：{height}{'\n'}
                    手机屏幕宽度(逻辑像素)：{width}{'\n'}
                    1逻辑像素等于{pixelRatio}实际像素{'\n'}
                    {1 + 1}
                </Text>
                <Text style={styles.instructions}>
                    Double tap R on your keyboard to reload,{'\n'}
                    Shake or press menu button for dev menu
                </Text>
            </View>
        );
    }
}

const styles = StyleSheet.create({
    container: {
        flex: 1,
        justifyContent: 'center',
        alignItems: 'center',
        backgroundColor: '#F5FCFF',
    },
    welcome: {
        fontSize: 20,
        textAlign: 'center',
        margin: 10,
    },
    instructions: {
        textAlign: 'center',
        color: '#333333',
        marginBottom: 5,
    },
});
```
**表达式**
可以在 JSX 中使用 JavaScript 表达式。表达式写在花括号 {} 中，像`{1 + 1}`。
在 JSX 中不能使用 if else 语句，但可以使用 conditional (三元运算) 表达式来替代。像下面这样，当i==1时，输出True，否则输出False
```
<div>
    <h1>{i == 1 ? 'True!' : 'False'}</h1>
</div>
```

JSX中也不能使用for语句，`{ for(let i=0;i<arr.length;i++) {…} }`，像这种是错误的，需要写成：

```
{
  arr.map(i => 
	<Text>{i.text}</Text>)
}
```
如果逻辑复杂的话可以写在函数中，然后调用：

```
{ doSomeThing() }
```

**样式**
在JSX中使用样式，通过style属性来定义
像下面这样子，第一个大括号是：JSX语法，第二个大括号是:javaScript对象，我们把需要定义的样式都以对象的方式写在这个大括号里。 
```
<h1 style={{color: '#ff0000', fontSize: '15px'}}>Hello ReactJS.</h1>
```
重要的是，样式属性需要以驼峰命名格式，若是不转换的话，需要加引号括起来
```
//驼峰的命名格式
fontSize: '14px'
//加引号的方式
'font-size':'14px'
```

为了方便，也可以像一开始的例子一样把一个样式赋给一个变量：然后传进去

```
<View style={styles.container}>
```

**JSX的注释**
JSX的注释还是有点特别的，使用`{/*` 和 `*/}`
```
{/* <Text style={styles.welcome}>
    Welcome to React Native!
</Text> */}
```

**显示和隐藏组件**
由于对于null或者false值React不会输出任何内容，因此你可以使用一个后面跟随了期望字符串的布尔值来实现条件判断。如果这个布尔值是true，那么后续的字符串就会被使用。

```
<View>
{ showWarning && <Text>FBI warning</Text> }
</View>
```

**需要注意的是**

 1. 组件必须大写字母开头
 ![这里写图片描述](http://img.blog.csdn.net/20180101214610526?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
 2. 文本必须写在Text组件内
 3. 必须单一子节点
 ![这里写图片描述](http://img.blog.csdn.net/20180101214842185?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

































