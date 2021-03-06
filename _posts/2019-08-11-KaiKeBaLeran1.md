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

**$EventBus是一个新的Vue实例，不使用$EventBus也是可以的，可以直接使用`this.$root.$on`
代替`this.$EventBus.$on`，不过通常都不这么做。**


### 自定义组件的v-model

自定义双向绑定的组件需要满足两个条件：
- 绑定value，
- 响应input事件

对于输入框来说的话，默认会利用名为 `value` 的 `prop` 和名为 `input` 的事件。

当自定义像单选框、复选框等类型的组件的时候，就需要绑定`checked` 和 响应 `change` 事件了。这时就需要使用 `model` 选项来处理。

[自定义组件的 v-model](https://cn.vuejs.org/v2/guide/components-custom-events.html#%E8%87%AA%E5%AE%9A%E4%B9%89%E7%BB%84%E4%BB%B6%E7%9A%84-v-model)
[model](https://cn.vuejs.org/v2/api/#model)


表单组件包含关系： `Form > FormItem > Input`
- `From` 组件提供 model表单数据 和 rule校验规则
- `FormItem` 组件负责校验表单数据 和 显示错误信息
- `Input` 派发数据更新通知给FormItem做校验


`FormItem` 与 `From` 的层级是不确定的，`FormItem` 要获取model数据和rule数据 做校验，
数据使用 `props` 传递是不太靠谱的，所以使用 [provide & inject](https://cn.vuejs.org/v2/guide/components-edge-cases.html#%E4%BE%9D%E8%B5%96%E6%B3%A8%E5%85%A5)

使用第三方的 [async-validator](https://github.com/yiminghe/async-validator) 库进行校验。

代码：`myLearn/vue-project/vue-home/src/components/task/form/MyForm.vue`

`Input -> FormItem` 和 `FormItem -> Form` 都用通知的操作，一开始的做法是：
``` javascript
// this.$parent写太死了，如果又嵌套了一层，那所有逻辑都会出错
this.$parent.$emit('addFormItem', this);
```

如果层级结构改变，那 `this.$parent` 就指向错误了，功能就不能正常进行了。

后来参考了 [`element-ui`](https://github.com/ElemeFE/element/blob/dev/src/mixins/emitter.js) 的方法，一层一层的向上找对应的组件，`element-ui` 的源码：
``` javascript
// 参考element-ui
// see，https://github.com/ElemeFE/element/blob/dev/src/mixins/emitter.js

function broadcast(componentName, eventName, params) {
  this.$children.forEach(child => {
    var name = child.$options.componentName;
    if (name === componentName) {
      child.$emit.apply(child, [eventName].concat(params));
    } else {
      broadcast.apply(child, [componentName, eventName].concat([params]));
    }
  });
}

export default {
  methods: {
    dispatch(componentName, eventName, params) {
      var parent = this.$parent || this.$root;
      var name = parent.$options.componentName;
      // 存在父组件 && 组件名称不符合时，就再网上一层寻找。这里 !name 是为了过滤普通标签，这里找的都是自定义组件
      while (parent && (!name || name !== componentName)) {
        parent = parent.$parent;

        if (parent) {
          name = parent.$options.componentName;
        }
      }
      if (parent) {
        parent.$emit.apply(parent, [eventName].concat(params));
      }
    },
    broadcast(componentName, eventName, params) {
      broadcast.call(this, componentName, eventName, params);
    }
  }
};
```

``` javascript
Vue.prototype.$dispatch = function(eventName, data) {
  let parent = this.$parent;
  // 查找父元素
  while (parent) {
    if (parent) {
      // 父元素用$emit触发
      parent.$emit(eventName, data);
      // 递归查找父元素
      parent = parent.$parent;
    } else {
      break;
    }
  }
};

Vue.prototype.$boardcast = function(eventName, data) {
  boardcast.call(this, eventName, data);
};
function boardcast(eventName, data) {
  this.$children.forEach(child => {
    // 子元素触发$emit
    child.$emit(eventName, data);
    if (child.$children.length) {
      boardcast.call(child, eventName, data);
    }
  });
}
```
