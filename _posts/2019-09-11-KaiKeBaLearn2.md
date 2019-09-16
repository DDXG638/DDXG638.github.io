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

## 路由元信息

> 可以在路由中设置一些额外的信息供导航守卫处理

``` javascript
const router = new VueRouter({
  routes: [
    {
      path: '/foo',
      component: Foo,
      children: [
        {
          path: 'bar',
          component: Bar,
          // 设置一个meta字段，表示请求时需要校验登录
          meta: { requiresAuth: true }
        }
      ]
    }
  ]
})
```



## Cube-UI 

> 滴滴公司的vue UI组件库，[传送门](https://didi.github.io/cube-ui/#/zh-CN/docs/form)



## axios 拦截器

> 请求发出之前 和 请求会来之前 可以做一些统一的操作，比如可以统一在请求发出之前做统一的header处理。

[Interceptors](http://www.axios-js.com/docs/#Interceptors)

类似代码：

``` javascript
axios.interceptors.request.use(config => {
  if (store.state.sign) {
    // 统一设置headers信息
    config.headers._sign = store.state.sign;
  }
  return config;
});

// 响应拦截器，可以提前处理一些公共逻辑
axios.interceptors.response.use(
  response => {
    // 如果status == -1，sign校验错误，需要重新登录
    if (response.status == 200) {
      const {status} = response.data;
      if (+status === -1) {
        loginHandler()
      }
    }
    return response;
  },
  err => {
      if (err.response.status === 401) { // 未授权
        loginHandler()
      }
  }
);
```


## vuex的订阅 [subscribe](https://vuex.vuejs.org/zh/api/#subscribe)

> 订阅 store 的 mutation。每个 mutation 完成后调用，接收 mutation 和经过 mutation 后的状态作为参数。

可以统一处理多个 mutation 完成后的逻辑。

``` javascript
// 订阅
store.subscribe((mutation, state) => {
  switch (mutation.type) {
    case "addBookshelf":
      localStorage.setItem("token", JSON.stringify(state.book));
      break;
    case "addCart":
      localStorage.setItem("cart", JSON.stringify(state.cart));
      break;
  }
});
```

除了可以订阅 mutation ，也可以订阅 [ store 的 action](https://vuex.vuejs.org/zh/api/#subscribeaction)


## VUE 插件

[开发插件](https://cn.vuejs.org/v2/guide/plugins.html#%E5%BC%80%E5%8F%91%E6%8F%92%E4%BB%B6)

[这个博客记录得比较详细](https://www.cnblogs.com/adouwt/p/9211003.html)

Vue.js 的插件最重要的是要暴露一个 install 方法。第一个参数是 Vue 构造器，第二个参数是一个可选的选项对象。然后使用`Vue.use(xxx)` 进行挂载，vue就会去调用`install` 方法了。

``` javascript
// 记录浏览器历史栈的小插件
const History = {
    _history: [], // 历史记录堆栈
    install(Vue) {
        // 在VUE的原型上挂一个$routerHistory字段用于快速获取历史对象
        Object.defineProperty(Vue.prototype, '$routerHistory', {
            get() {
                return History;
            }
        })
        
        /* 也可以用这种方式在vue原型上面挂 */
        // Vue.prototype.$routerHistory = History
        
        /* 也可以在这里定义全局组件 */
        Vue.component('my-component-name', {
            // ... 选项 ...
        })
    },
    push(path) { // 页面跳转时推入栈
        this._history.push(path);
    },
    pop() { // 页面回退出栈
        this._history.pop();
    },
    canBack(){ // 只有栈中有历史记录才能回退
        return this._history.length > 1;
    }
}
export default History;
```



## vue 自定义使用api调用的全局组件

> 像Loading、弹窗和Toast这些使用频率比较组件做成全局组件也不算很方便，要用的地方还是得`import`一下`vue`文件或者写显示的写`html`代码。
> 我之前的方式是将Loading、弹窗和Toast组件都在`App.vue`文件中引入，其他组件通过`$EventBus` 的 `$emit` 来调用，这样就不会到处引入公共组件了。
> 现在介绍一些UI库常用的方式，就是通过api调用的方法调用公共组件，像 `this.$Toast({...})` 这样子调用。

这里举一个 `Toast` 组件为例。`Toast.vue` 方面的东西就不说了，主要记录一下是怎么实现可以用api的方式调用 `Toast` 的。

大概流程是这样子的：
- 创建一个新的Vue实例，用于渲染 `Toast` 组件。
- 使用 `render` 函数对 `Toast` 进行渲染，使用 `$mount` 方法进行挂载。
- 获取 `Toast` 实例，就可进行Toast操作了。

``` javascript
import Toast from "@/components/Notice.vue";
import Vue from "vue";

// 给Toast添加一个创建组件实例的方法，可以动态编译自身模板并挂载
Toast.getInstance = props => {
  // 创建一个Vue实例
  const instance = new Vue({
    render(h) {
      // h 是 createElement 方法的缩写
      // 渲染函数：用于渲染Toast模板为虚拟dom
      return h(Toast);
    }
  }).$mount(); // 执行挂载,若不指定选择器，则模板将被渲染为文档之外的元素

  // 由于上面的$mount()没有指定挂载的选择器
  // 必须使用原生dom api把它插入文档中
  // $el指的是渲染的Toast中真实dom元素
  document.body.appendChild(instance.$el); // $mount()指定了选择器，则不需要这一行代码了

  // 获取Toast实例，$children指的是当前Vue实例中包含的所有组件实例
  const toast = instance.$children[0];
  return toast;
};

// 设计单例模式，全局范围唯一创建一个Toast实例，反复利用已经存在的Toast实例
let msgInstance = null;
function getInstance() {
  msgInstance = msgInstance || Toast.getInstance();
  return msgInstance;
}

// 暴露接口
export default {
  info({ duration = 2, content = "" }) {
    getInstance().add({
      content,
      duration
    });
  }
};


// main.js中
import Toast from '../components/Toast.vue';
Vue.prototype.$toast = Toast.info;


// 在其他组件中使用
this.$toast({
    content: '123',
    duration: 2
});
```


也可以使用 `Vue.extend` vue构造器，创建一个‘子类’的方式来挂载 `Toast` 组件。

``` javascript
// 改动代码
Toast.getInstance = props => {
  const Profile  = Vue.extend(Toast)
  const instance = new Profile().$mount()

  // 由于上面的$mount()没有指定挂载的选择器
  // 必须使用原生dom api把它插入文档中
  // $el指的是渲染的Toast中真实dom元素
  document.body.appendChild(instance.$el); // $mount()指定了选择器，则不需要这一行代码了
  return instance;
};
```

参考：[Vue 自定义全局消息框组件](https://www.cnblogs.com/conglvse/p/9641550.html)
