---
layout:     post
title:      "React Native 基础知识2"
subtitle:   "React Native"
date:       2018-01-14 21:51:43
author:     "ddxg"
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 前端开发
    - React Native


## **React Native 基础知识2** 

#### **1. 父组件向子组件通信、子组件之间通信** 

 - 父组件以自身的state作为子组件的props；父组件调用setState，于是子组件的props相应变化
 - 通过ref调用子组件的方法

```
import React, { Component } from 'react'
import {
  AppRegistry,
  StyleSheet,
  Text,
  View,
  TouchableOpacity,
} from 'react-native'

class CountDown extends Component {
  /* 设置初始时间为20s */
  state = {
    count : 20
  };
  /* 提供一个成员方法addTime来供外部组件调用 */
  addTime = (time) => {
    this.setState({
      count: this.state.count + time
    });
  };
  render() {
    return (
      <View>
        <Text style={styles.welcome}>
          倒计时：{this.state.count}
        </Text>
      </View>
    )
  }
  /* 生命周期，render后会调用componentDidMount() */
  componentDidMount() {
    /* 来一个定时器 */
    
    this.timer = setInterval(() => {
      const {count} = this.state;
      if (count == 0) {
        clearInterval(this.timer);
      } else {
        this.setState({
          count: count - 1
        });
      }
    }, 1000);
  }
  /* 生命周期中组件卸载后调用的方法，清空定时器 */
  componentWillUnmount() {
    clearInterval(this.timer);
  }
}

class App extends Component {
  addTime = () => {
    this.countDown.addTime(10);
  };
  render() {
    return (
      <View style={styles.container}>
        {/* 通过ref引用获取CountDown实例，并且赋值给this.countDown */}
        <CountDown ref={countDown => this.countDown = countDown}/>
        {/* 添加点击事件 */}
        <TouchableOpacity onPress={this.addTime}>
          <Text>延长10秒</Text>
        </TouchableOpacity>
      </View>
    )
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
})

AppRegistry.registerComponent('App', () => App)

```
![这里写图片描述](http://img.blog.csdn.net/20180121173837345?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

最重要的是这个ref的赋值
```
<CountDown ref={countDown => this.countDown = countDown}/>
```
将CountDown组件的实例复制给this.countDown，这样就可以通过`this.countDown.addTime(10)` 来调用CountDown组件的`addTime()` 成员方法。

#### **2. 子组件向父组件通信** 

父组件将函数作为props传递给子组件，子组件在需要时调用，将数据作为函数参数传回。此谓之“回调”（callback）

当时间没有的时候，子组件会通知父组件（调用父组件穿过来的方法）。
```
import React, { Component } from 'react'
import {
  AppRegistry,
  StyleSheet,
  Text,
  View,
  TouchableOpacity,
} from 'react-native'

class CountDown extends Component {
  /* 设置初始时间为20s */
  state = {
    count : 20
  };
  /* 提供一个成员方法addTime来供外部组件调用 */
  addTime = (time) => {
    this.setState({
      count: this.state.count + time
    });
  };
  /* 调用父组件通过props传来的方法 */
  noTime = () => {
    this.props.noTime && this.props.noTime('123');
  }
  render() {
    return (
      <View>
        <Text style={styles.welcome}>
          倒计时：{this.state.count}
        </Text>
      </View>
    )
  }
  /* 生命周期，render后会调用componentDidMount() */
  componentDidMount() {
    /* 来一个定时器 */
    this.timer = setInterval(() => {
      const {count} = this.state;
      if (count == 0) {
      /* 当时间为0时，调用noTime方法告诉父组件没有时间了 */
        this.noTime();
        clearInterval(this.timer);
      } else {
        this.setState({
          count: count - 1
        });
      }
    }, 1000);
  }
  /* 生命周期中组件卸载后调用的方法，清空定时器 */
  componentWillUnmount() {
    clearInterval(this.timer);
  }
}

class App extends Component {
  arr = ['111', '222', '333'];
  noTimeParent = (parentData, childData) => {
    alert("父组件传过去的数据：" + parentData + "\n子组件传回的数据：" + childData);
  };
  render() {
    return (
      <View style={styles.container}>
        {/* 像子组件传递一个父组件的函数，为了传递参数需要在父组件函数外面多包一层函数 */}
        {/* 第一个参数是用于标识哪一个组件的，第二个参数是为子组件回传参数做准备 */}
        {this.arr.map((arrName) => <CountDown key={arrName} noTime={(child) => this.noTimeParent(arrName, child)}/>)}
        
      </View>
    )
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
})

AppRegistry.registerComponent('App', () => App)

```
比较复杂的地方：
```
{
this.arr.map((arrName) => 
	<CountDown key={arrName} 
		noTime={
			(child) => this.noTimeParent(arrName, child)
		}
	/>)
}
```
将父组件的`noTimeParent` 成员方法通过props传给子组件，为了传递有标识度的参数需要在父组件函数外面多包一层函数，这里传了数组arr中的内容作为参数。


```
  noTime = () => {
    this.props.noTime && this.props.noTime('123');
  }
```
在子组件的noTime方法中，`this.props.noTime('123')` 这里调用的方法是`(child) => this.noTimeParent(arrName, child)` 这个方法，`'123'` 对应的就是这个child。
所以在父组件的`noTimeParent`方法中，`parentData` 就是传给子组件的数据，`childData` 就是子组件回传的数据。

```
noTimeParent = (parentData, childData) => {
    alert("父组件传过去的数据：" + parentData + "\n子组件传回的数据：" + childData);
  };
```
![这里写图片描述](http://img.blog.csdn.net/20180121194153685?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
