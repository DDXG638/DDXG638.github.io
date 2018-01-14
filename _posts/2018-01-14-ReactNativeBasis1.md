---
layout:     post
title:      "React Native 基础知识"
subtitle:   "React Native"
date:       2018-01-14 21:51:43
author:     "ddxg"
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 前端开发
    - React Native
---


## **React Native 基础知识**

#### **1. React 组件**
React 允许将代码封装成组件（component），然后像插入普通 HTML 标签一样，在网页中插入这个组件。
可以使用class定义组件，也可以使用一个函数来定义一个组件，下面使用class来定义一个GoodMorning组件，使用函数定义一个GoodEvening组件。

```
import React, { Component } from 'react'
import {
  AppRegistry,
  StyleSheet,
  Text,
  View,
} from 'react-native'

{/*GoodMorning组件*/}
class GoodMorning extends Component {
    render () {
      return (
        <Text>
          GoodMorning !
        </Text>
      )
    }
}

{/*GoodEvening组件*/}
const GoodEvening = () => {
  return (
    <Text>GoodEvening !</Text>
  ) 
}

class App extends Component {
  render() {
    return (
    {/*只能含有一个顶层标签*/}
      <View style={styles.container}>
        <Text style={styles.welcome}>
          Welcome to React Native!
        </Text>
        {/*引用组件*/}
        <GoodMorning/>
        <GoodEvening/>
      </View>
    )
  }
}

{/*样式*/}
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
})

AppRegistry.registerComponent('App', () => App)

```
![这里写图片描述](http://img.blog.csdn.net/20180114155809029?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
注意，原生 HTML 元素名以小写字母开头，而自定义的 React 类名以大写字母开头，比如 GoodMorning 不能写成 goodMorning。除此之外还需要注意组件类只能包含一个顶层标签，否则也会报错。

组件名不一定是用单标签，也可以是双标签，`<HelloMessage /> == <HelloMessage></HelloMessage>` 


#### **2. props 属性、propTypes、defaultProps**
组件间的数据传递是使用`props` 属性

```
class GoodMorning extends Component {
    render () {
      return (
        <Text>
          GoodMorning {this.props.name}!
        </Text>
      )
    }
}

const GoodEvening = (props) => {
  return (
    <Text>GoodEvening {props.name}!</Text>
  ) 
}

class App extends Component {
  nameArr = ['vvv', 'nnn', 'lll'];
  render() {
    return (
      <View style={styles.container}>
        <Text style={styles.welcome}>
          Welcome to React Native!
        </Text>
        <GoodMorning name='java'/>
        <GoodEvening name='php'/>
      </View>
    )
  }
}
```
![这里写图片描述](http://img.blog.csdn.net/20180114163143578?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
class组件中，name 属性通过 `this.props.name` 来获取。
function组件中，需要在函数传入一个参数，假如传入的参数是props，则name属性通过`props.name` 来获取。

添加组件属性，有一个地方需要注意，就是 `class` 属性需要写成 `className` ，`for` 属性需要写成 `htmlFor` ，这是因为 `class` 和` for` 是 JavaScript 的保留字。

**默认 Props**
如果在使用某一个组件忘记传入需要的数据时，组件中对传入数据进行处理的时候很有可能报错，所以就需要设置默认的props，使用defaultProps。

```
class GoodMorning extends Component {
  static defaultProps = {
    name: 'defName'
  };
    render () {
      return (
        <Text>
          GoodMorning {this.props.name}!
        </Text>
      )
    }
}

class App extends Component {
  render() {
    return (
      <View style={styles.container}>
        <Text style={styles.welcome}>
          Welcome to React Native!
        </Text>
        <GoodMorning/>
      </View>
    )
  }
}
```
![这里写图片描述](http://img.blog.csdn.net/20180114164332841?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
当使用GoodMorning组件不设置name属性是，会显示defaultProps 中默认设置的值 defName。

**Props 验证**
Props 验证使用 propTypes，它可以保证我们的应用组件被正确使用，React.PropTypes 提供很多验证器 (validator) 来验证传入数据是否有效。当向 props 传入无效数据时，JavaScript 控制台会抛出警告。

```
var title = "菜鸟教程";
// var title = 123;
var MyTitle = React.createClass({
  propTypes: {
    title: React.PropTypes.string.isRequired,
  },
 
  render: function() {
     return <h1> {this.props.title} </h1>;
   }
});
ReactDOM.render(
    <MyTitle title={title} />,
    document.getElementById('example')
);
```
Mytitle 组件中，属性 title 是必须的且是字符串，非字符串类型会自动转换为字符串 。
更多验证器：

```
propTypes: {
    // 可以声明 prop 为指定的 JS 基本数据类型，默认情况，这些数据是可选的
   optionalArray: React.PropTypes.array,
    optionalBool: React.PropTypes.bool,
    optionalFunc: React.PropTypes.func,
    optionalNumber: React.PropTypes.number,
    optionalObject: React.PropTypes.object,
    optionalString: React.PropTypes.string,
 
    // 可以被渲染的对象 numbers, strings, elements 或 array
    optionalNode: React.PropTypes.node,
 
    //  React 元素
    optionalElement: React.PropTypes.element,
 
    // 用 JS 的 instanceof 操作符声明 prop 为类的实例。
    optionalMessage: React.PropTypes.instanceOf(Message),
 
    // 用 enum 来限制 prop 只接受指定的值。
    optionalEnum: React.PropTypes.oneOf(['News', 'Photos']),
 
    // 可以是多个对象类型中的一个
    optionalUnion: React.PropTypes.oneOfType([
      React.PropTypes.string,
      React.PropTypes.number,
      React.PropTypes.instanceOf(Message)
    ]),
 
    // 指定类型组成的数组
    optionalArrayOf: React.PropTypes.arrayOf(React.PropTypes.number),
 
    // 指定类型的属性构成的对象
    optionalObjectOf: React.PropTypes.objectOf(React.PropTypes.number),
 
    // 特定 shape 参数的对象
    optionalObjectWithShape: React.PropTypes.shape({
      color: React.PropTypes.string,
      fontSize: React.PropTypes.number
    }),
 
    // 任意类型加上 `isRequired` 来使 prop 不可空。
    requiredFunc: React.PropTypes.func.isRequired,
 
    // 不可空的任意类型
    requiredAny: React.PropTypes.any.isRequired,
 
    // 自定义验证器。如果验证失败需要返回一个 Error 对象。不要直接使用 `console.warn` 或抛异常，因为这样 `oneOfType` 会失效。
    customProp: function(props, propName, componentName) {
      if (!/matchme/.test(props[propName])) {
        return new Error('Validation failed!');
      }
    }
  },
  /* ... */
```

#### **3. 动态列表与key**
根据多个数据（数组）动态生成多个组件一般使用map方法。

```
class App extends Component {
  nameArr = ['vvv', 'nnn', 'lll'];
  render() {
    return (
      <View style={styles.container}>
        <Text style={styles.welcome}>
          Welcome to React Native!
        </Text>
        {
          this.nameArr.map((name, index) => {
            return <GoodMorning name={name} key={index}/>
          })
        }
        
      </View>
    )
  }
}
```
![这里写图片描述](http://img.blog.csdn.net/20180114174440521?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
循环生成的组件需要有唯一的key值区分（Virtual DOM），key属性放在循环的直接容器上，优先使用区分度高的属性（id、具体内容等），其次选择数组下标，只需在当前循环中唯一不重复。设置key可以减少不必要的渲染开销。
nameArr是App类的成员变量，只在单个class的实例内读写，实例销毁时一并销毁，使用时需要在前面加上`this.` 

#### **4. State(状态)**

React 把组件看成是一个状态机（State Machines）。通过与用户的交互，实现不同状态，然后渲染 UI，让用户界面和数据保持一致。
React 里，只需更新组件的 state，然后根据新的 state 重新渲染用户界面（不要操作 DOM）。

```
class App extends Component {
  state = {
    likes: 2
  };
  addLike = () => {
    let like = this.state.likes;
    this.setState({
      likes: like + 1
    });
  };
  render() {
    return (
      <View style={styles.container}>
        <Text style={styles.welcome}>
          Welcome to React Native!
        </Text>
        <Text>{this.state.likes}</Text>
        <TouchableOpacity onPress={this.addLike}>
          <Text style={styles.bigFont}>赞</Text>
        </TouchableOpacity>
      </View>
    )
  }
}
```
![这里写图片描述](http://img.blog.csdn.net/20180114211228430?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

没点一次赞，赞数就会增加1，通过`this.state.likes` 获取当前的点赞数，通过`this.setState` 修改state中的数据，每次修改以后，自动调用 `this.render` 方法，再次渲染组件。

 - 一切界面变化都是状态state变化。 
 - state的修改必须通过setState()方法
 - this.state.likes = 100;  这样的直接赋值修改无效
 - setState是一个merge合并操作，只修改指定属性，不影响其他属性
 - setState是异步操作，修改不会马上生效


#### **5. 组件的生命周期**

直接上图：
![这里写图片描述](http://orj17532v.bkt.clouddn.com/7.png)

 - componentWillMount：在渲染前调用,在客户端也在服务端
 - componentDidMount：在第一次渲染后调用，只在客户端。之后组件已经生成了对应的DOM结构，可以通过this.getDOMNode()来进行访问。 如果你想和其他JavaScript框架一起使用，可以在这个方法中调用setTimeout, setInterval或者发送AJAX请求等操作(防止异部操作阻塞UI)
 - componentWillReceiveProps： 在组件接收到一个新的prop时被调用。这个方法在初始化render时不会被调用
 - shouldComponentUpdate ：返回一个布尔值。在组件接收到新的props或者state时被调用。在初始化时或者使用forceUpdate时不被调用。 
 - componentWillUpdate：在组件接收到新的props或者state但还没有render时被调用。在初始化时不会被调用。
 - componentDidUpdate ：在组件完成更新后立即调用。在初始化时不会被调用。
 - componentWillUnmount：在组件从 DOM 中移除的时候立刻被调用。

注意：
render中只做与渲染有关的操作，只读取、不修改任何数据（临时变量除外），因为界面的更改是经常的，所以render是经常触发的，所以如果你有修改数据等操作，就会多次触发，使结果难以预料。
随组件加载只执行一次的操作，放在WillMount或者DidMount中，比如远程取首页数据（fetch），比如弹出提示框。
记得在WillUnmount中销毁定时器和一些订阅事件。
props发生变化，使用WillReceiveProps来处理（比如将变动同步给state）














