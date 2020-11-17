---
layout:     post
title:      "lodash中的惰性求值（Lazy Evaluation）"
subtitle:   "简单了解lodash中惰性求值的实现"
date:       2020-11-15 20:00:00
author:     "ddxg"
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - lodash
---

# lodash中的惰性求值（Lazy Evaluation）
> 在编程语言理论中，惰性求值（英语：Lazy Evaluation），又译为惰性计算、懒惰求值，也称为传需求调用（call-by-need），是一个计算机编程中的一个概念，它的目的是要最小化计算机要做的工作。
> 惰性求值中的参数直到需要时才会进行计算。这种程序实际上是从末尾开始反向执行的。它会判断自己需要返回什么，并继续向后执行来确定要这样做需要哪些值。
> 与图片懒加载有点类似，当需要展示图片的时候才真正加载图片。
> js语言时不支持惰性求值的，但是可以用其他方式达到类似的效果。

## 小问题
> ` const arr = [4, 15, 20, 1, 3, 13, 2, 20] `，取出arr中元素小于10的三个数字

``` javascript
const arr = [4, 15, 20, 1, 3, 13, 2, 20]
let i = 0
function priceLessThan10(val) {
  console.log('priceLessThan10:', val)
  i++
  return val < 10;
}

// 普通实现
_.take(_.filter(arr, priceLessThan10), 3)
// arr.filter(priceLessThan10).splice(0, 3)

// 惰性实现
_(arr).map(doublePrice).filter(priceLt).take(3).value()
```

![jsBridge关系图](/img/2020/lodash/1.png)

### 普通的处理方式
> filter方法和take各司其职，互不相关。一共执行了8+3次循环。
> filter它处理数组中的所有元素，而实际上，这个例子中，只需要读取到第5个元素，就可以完成了。
- `filter` 函数对arr的进行遍历，判断是否小于10，执行了8次
- `take` 函数去除上一步返回的数组的前3个，执行3次


### 惰性处理方式
> 惰性的处理更像是一个协调的整体，一共执行了5次。
> 惰性求值算法通过处理数组中最小数量的元素来获得正确的结果。
- 判断 `arr[0]` 是否是前3个小于10的元素，满足要求
- 判断 `arr[1]` 是否是前3个小于10的元素，不满足要求
- ......
- 当到`arr[4]`时，已经找到了满足条件的3个元素了，完成


## 惰性处理的优势
- 减少了遍历次数，数据量大的时候性能提升明显
- 类似流水线，避免在链执行期间创建中间数组。
- 可以延迟执行，只有调用了 `value()` 方法是，才会真正执行。


## 实现原理

- 通过链式，先收集迭代函数。例如：`arr.map(fn1).filter(fn2).map(fn3).take(3)`，收集 fn1、fn2和fn3存储于 `iteratees` 数组中，迭代函数不会立即执行
- 只有调用了 `value()` 方法后开始执行
- 遍历 `arr`，每一个元素都要经过 `iteratees` 迭代函数。即：`fn3(fn2(fn1(arr[0])))`
- 如果元素通过了所有的迭代函数的判断，则该元素是符合要求的；否则为不符合要求，立即停止判断，转而进行下一个元素的判断

![流程](/img/2020/lodash/2.png)

### LazyWrapper对象
> 需要LazyWrapper对象对数据进行包装，收集迭代函数，和实现链式调用

``` javascript
function LazyWrapper(value) {
  this.__wrapped__ = value; // 存储传入的数据
  this.__iteratees__ = []; // 存储收集的迭代器函数，就是_.map(fn) 中的fn
  this.__takeCount__ = MAX_ARRAY_LENGTH; // 取多少个值，默认是数组的最大长度
}
```

### 添加方法
> 以 `filter` 和 `take` 作为例子

``` javascript
LazyWrapper.prototype.filter = function (iteratee) {
  this.__iteratees__.push({ // 将迭代函数iteratee收集起来
    'iteratee': iteratee,
    'type': LAZY_FILTER_FLAG
  })
  return this // 链式调用，为了简单直接返回this
}

LazyWrapper.prototype.take = function (count) {
  this.__takeCount__ = count // 设置取值的个数
  return this
}
```

### value方法
> value方法是比较关键的，嵌套了两个循环，先是遍历数组，然后__iteratees__中的迭代函数顺序对数组元素进行处理，不满足条件则直接进入下一个元素的处理，达到要求则中断循环

``` javascript
LazyWrapper.prototype.value = function () {
  let array = this.__wrapped__ // 元数据
  let iteratees = this.__iteratees__ // 迭代函数
  let index = -1 // 外层循环下标
  let takeCount = Math.min(array.length, this.__takeCount__) // 获取的个数
  let result = [] // 结果

  // 先遍历数据
  outer: while (index < array.length && result.length < takeCount) {
    index++
    let value = array[index]
    let iterIndex = 0
    // 然后value要经过每一个iteratees迭代器的计算
    while (iterIndex < iteratees.length) {
      let { iteratee, type } = iteratees[iterIndex]
      let computed = iteratee(value) // 迭代函数计算的结果
      if (type === LAZY_MAP_FLAG) { // map，一定要有返回值
        value = computed
      } else if (type === LAZY_FILTER_FLAG) {
        // LAZY_FILTER_FLAG类型
        if (!computed) {
          // 如果是假值则此value不需要继续判断，直接判断下一个value
          continue outer
        }
      }
      iterIndex++
    }
    result.push(value)
  }
  return result
}
```

## 局限性
- 能转化的方法比较少，除了 `filter` 和 `map` 之外好像就没有什么方法可以这么玩了
- 现实使用中，比较少有类似 `take(3)` 的需求，体现不了优势

在优化某些问题的时候可以参考惰性求值的思想，平时代码中应该比较少涉及。`lodash` 中也只是小部分数组方法使用到了惰性求值，实现的相对简单一些。还有一个叫 [`lazy.js`](https://github.com/dtao/lazy.js/) 的库实现地更纯粹，还支持字符串和对象。


## 平时可能回涉及到惰性的例子

给dom节点添加事件，兼容IE：
``` javascript
function addEvent (target, type, listener) {
  if (target.addEventListener) {
    target.addEventListener(type, listener);
  } else if(element.attachEvent){
    target.attachEvent('on' + type, listener);
  } else{
    target['on' + type] = listener;
  }
}
```

问题：每次执行 `addEvent()` 的时候都会重新判断，一遍 `if...else...`，这应该是没有必要的，因为正常来说环境是不会改变的。可以使用类似惰性载入的方式进行优化。

### 惰性载入

``` javascript
function addEvent (target, type, listener) {
  if (target.addEventListener) {
    addEvent = function (target, type, listener) {
      target.addEventListener(type, listener);
    }
  } else if (element.attachEvent) {
    addEvent = function (target, type, listener) {
      target.attachEvent('on' + type, listener);
    }
  } else {
    addEvent = function (target, type, listener) {
      target['on' + type] = listener
    }
  }
  return addEvent(target, type, listener)
}
```
> 当确定分支之后，直接对 `addEvent` 重新赋值，覆盖了原来的函数，下次调用的时候就不会在进行判断了。惰性载入，当要真正执行的时候才进行环境的判断，并且环境判断只执行一次。

### 立即执行函数
``` javascript
const addEvent = (function() {
  if (target.addEventListener) {
    return function (target, type, listener) {
      target.addEventListener(type, listener);
    }
  } else if (element.attachEvent) {
    return function (target, type, listener) {
      target.attachEvent('on' + type, listener);
    }
  } else {
    return function (target, type, listener) {
      target['on' + type] = listener
    }
  }
})()
```
> 代码初始的时候候回立即执行 并 判断好正确的分支，只进行一次判断



## 参考：
- [How to Speed Up Lo-Dash ×100? Introducing Lazy Evaluation.](http://filimanjaro.com/blog/2014/introducing-lazy-evaluation/)
- [惰性求值——lodash源码解读](https://cloud.tencent.com/developer/article/1494539)
- [学习 lodash 源码整体架构，打造属于自己的函数式编程类库](https://segmentfault.com/a/1190000020350442)
- [Lazy.js ： 让 JavaScript 变懒](https://segmentfault.com/a/1190000000358463)
- [javascript之惰性函数](https://juejin.im/entry/6844903552825950222)
- [lazy.js 惰性求值实现分析](https://zhuanlan.zhihu.com/p/24138694)
