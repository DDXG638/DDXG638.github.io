---
layout:     post
title:      "vue2.5组件化"
subtitle:   "vue2.5组件化-零碎知识点记录"
date:       2019-08-11 21:00:00
author:     "ddxg"
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - vue
    - 框架
    - kaikeba
---

# vue2.5组件化

### vm.$watch
监听器有另一种写法： `immediate`和`deep`参数

[vm.$watch( expOrFn, callback, [options] )](https://cn.vuejs.org/v2/api/#vm-watch)
``` javascript
watch: {
    cart: {
        handler(newVal, oldVal) {
            // 处理逻辑
        }
    },
    deep: true
}
```

### [webpack devServer.before](https://www.webpackjs.com/configuration/dev-server/#devserver-before)
vue.config.js中： 
``` javascript
module.exports = {
    devServer: {
        before(app) {
            // 可以做数据mock使用
            app.get('/some/path', function(req, res) {
                res.json({ custom: 'response' });
            });
        }
    }
}
```

### 挂载在vue上的EventBus
vue项目的main.js中
``` javascript
Vue.prototype.$EventBus = new Vue();
```

在组件中就可以`this.$EventBus`直接使用。主要用途是组件之间的通信： 
``` javascript
// 注册事件
this.$EventBus.$on('evnetName', function(params) {
  // 事件处理
  console.log(params);
});
```
``` javascript
// 触发事件
this.$EventBus.$emit('eventName', params);
```


如果是子组件要通知父组件的话，可以直接使用`this.$emit`，这里的`this`指向的是当前组件。 

父组件中使用myButton组件
```javascript
// 父组件中绑定aaabbb事件
<my-button @aaabbb="doAaabbb"></my-button>

export default {
    methods: {
        doAaabbb(params) {
            // 处理自定义事件aaabbb
        }
    }
}
```

myButton组件中触发自定义事件： 
``` javascript
this.$emit('aaabbb', params);
```

**
$EventBus是一个新的Vue实例，不使用$EventBus也是可以的，可以直接使用`this.$root.$on`
代替`this.$EventBus.$on`，不过通常都不这么做。 **

### 自定义组件的v-model
https://cn.vuejs.org/v2/guide/components-custom-events.html#%E8%87%AA%E5%AE%9A%E4%B9%89%E7%BB%84%E4%BB%B6%E7%9A%84-v-model

provide & inject

async-validator