---
layout:     post
title:      "vue-router && vuex"
subtitle:   "记录一些没有使用过的vue框架中的功能"
date:       2019-09-11 21:00:00
author:     "ddxg"
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - vue
    - 框架
    - kaikeba
---

# vue-router && vuex 未使用功能记录

## 路由模式

`router.js` 中的 `mode: "history"`

- hash ，原理是url中的‘#’hash部分，没有兼容性问题。
- history ，原理是`HTML5 中的 history api`，有兼容性问题，移动端还好。需要服务端对路由做一些处理。

我做过的vue项目的路由模式都是使用 ‘hash’ 的。
使用'history'模式的话，url看起来会自然一些，对SEO也有帮助。

## 路由配置

路由配置中还可以配置[props](https://router.vuejs.org/zh/guide/essentials/passing-props.html#%E5%B8%83%E5%B0%94%E6%A8%A1%E5%BC%8F)，props可以设置为组件属性。

``` javascript
function func({ params, query }) {
  return {
    id: params.id,
    msg: params.msg,
    foo: query.foo
  };
}

children: [
    { path: "static", component: Page1, props: { foo: "bar" } }, // 给组件传静态值
    { path: "page1/:foo", component: Page1, props: true }, // 将route.params
    {
      path: "page2/:id/:msg",
      name: "page2",
      component: Page2,
      props: func
    }
]
``` 


## 导航守卫

> 在路由前后跳转的时候都可以进行一些操作，功能强大，常见的是可以处理是否登录、页面权限设置等。

[vue-导航守卫](https://router.vuejs.org/zh/guide/advanced/navigation-guards.html)

```
// 全局路由守卫
router.beforeEach((to,from,next)=>{
  console.log(to);
  if (to.path !== '/login') { // 要求登录
    if (isLogin) {
      next();
    } else {
      next('/login?redirect='+to.path);
    }
  } else {
    next();
  }
  next();
})
```


## 