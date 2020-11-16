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

## 小问题
> ` const arr = [4, 15, 20, 1, 3, 13, 2, 20] `，取出arr中元素小于10的前三个数字

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
> filter它处理数组中的所有元素，而实际上，这个例子中，我们只需要读取到第5个元素，就可以完成了。
- `filter` 函数对arr的进行遍历，判断是否小于10，执行了8次
- `take` 函数去除上一步返回的数组的前3个，执行3细


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

- 通过链式，先收集迭代函数。例如：`arr.map(fn1).map(fn2).filter(fn3)`，收集 fn1、fn2和fn3存储于 `iteratees` 数组中
- 调用了 `value()` 方法后开始执行
- 遍历 `arr`，每一个元素都要经过 `iteratees` 迭代函数。即：`fn3(fn2(fn1(arr[0])))`
- 如果元素通过了所有的迭代函数的判断，则该元素是符合要求的；否则为不符合要求，立即停止判断，转而进行下一个元素的判断


## 参考：
- [How to Speed Up Lo-Dash ×100? Introducing Lazy Evaluation.](http://filimanjaro.com/blog/2014/introducing-lazy-evaluation/)
- [惰性求值——lodash源码解读](https://cloud.tencent.com/developer/article/1494539)
- [学习 lodash 源码整体架构，打造属于自己的函数式编程类库](https://segmentfault.com/a/1190000020350442)
- [Lazy.js ： 让 JavaScript 变懒](https://segmentfault.com/a/1190000000358463)
