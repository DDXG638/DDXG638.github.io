---
layout:     post
title:      "工作记录"
subtitle:   "记录一些工作中指的记录的东西，或者是经常忘记的东西。"
date:       2019-10-19 20:00:00
author:     "ddxg"
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - work
---

# 工作记录
> 哈哈，码农一个，记录一下使用率较高又经常忘记的东西。省的要用的时候到处找代码复制粘贴

## [async-validator](https://github.com/yiminghe/async-validator) 表单校验
> 一个表单校验库，Antd 和 ElenentUI 表单组件使用的就是这个库，对输入框比较多的表单使用很方便，避免了无尽的 `if ... return`。
> 英文不好还可以看别人[翻译的文档](https://www.cnblogs.com/wozho/p/10955525.html)

具体用法就不多说了，直接看文档。码农都是直接上代码的，哈哈哈

```vue
<script>
import Schema from 'async-validator'
export default {
  name: 'ReplyWorkPage',
  data () {
    return {
      form: { // form是表单校验的数据源
        solutions: '', // 处理方案
        resolved_quickly: '1', // 是否当天处理 1是 0不是
        estimated_time: '1', // 预估处理时效/天
        remark: '',
        change_responsible: '1',
      },
      errorText: {
        solutions: '',
        resolved_quickly: '',
        estimated_time: '',
      },
      // rules 是表单校验规则。有多种校验方式，这里只是展示了两种，具体看文档
      rules: {
        solutions: {
          type: 'string',
          required: true,
          message: '请输入处理方案',
        },
        resolved_quickly: {
          type: 'string',
          required: true,
          message: '选择是否当天解决',
        },
        estimated_time: {
          type: 'string',
          message: '请输入预估时效',
          validator: (rule, value) => {
            // 比较复杂或者有依赖的校验，可以自定义校验方法，return false则不通过
            if (this.form.resolved_quickly === '0' && !value) {
              return false
            }
            return true
          },
        },
      },
      validator: null,
    }
  },
  methods: {
    changeValue (field, value) {
      this.$set(this.form, field, value)
      // 校验单个输入框
      this.validateSingleField(field)
    },
    onError ({ ret, errmsg }) {
      this.$toast.center(errmsg)
    },
    onSubmit () {
      // 校验前需要new Schema实例
      this.validator = new Schema(this.rules)
      this.validator.validate(this.form).then(() => {
        // 全部一起校验
        console.log('校验成功！')
        for (let key in this.errorText) {
          this.errorText[key] = ''
        }
        // TODO：校验完成需要的操作
      }).catch(({ errors, fields }) => {
        // 校验错误的话，fields会包含所有检验失败的数据，打log就一目了然了
        for (let field in this.rules) {
          if (fields[field] && fields[field].length) {
            this.errorText[field] = fields[field][0].message
          } else {
            this.errorText[field] = ''
          }
        }
      })
    },
    validateSingleField (field) {
      // 单个表单的校验，需要将rules的单个挑出来
      if (!this.rules[field]) { return }
      const validator = new Schema({
        [field]: this.rules[field],
      })
      validator.validate(this.form).then(() => {
        this.errorText[field] = ''
      }).catch(({ errors, fields }) => {
        this.errorText[field] = errors[0].message
      })
    },
    remarkFocus () {
      if (browser.versions.android) {
        let that = this
        setTimeout(function () {
          that.$refs.remarkArea.scrollIntoView(false)
        }, 300)
      }
    },
  },
}
</script>
```
`async-validator` 还有很多其他的校验功能，可以完成非常复杂的校验，我这个只是最基本的用法，惭愧呀。


## 软键盘触发搜索

> 像原生app那样，页面搜索框旁边不需要搜索按钮，可以通过软键盘右下角的搜索按钮直接出发H5的表单搜索。这种体验更好。

主要是要处理4个问题：
- 表单需要包在 `form` 标签中
- `<input type="search" />` `type` 为 `search` 才能是让软键盘出现 “搜索” 按钮
- 监听 `input` 的 `search` 事件
- 修改搜索框后面默认的取消文字输入的小图标

TODO: 缺一张图

码农直接上代码：
```vue
<template>
  <section>
    <!-- onsubmit="return false" 阻止表单默认的行为 -->
    <form action="#" onsubmit="return false" class="search-input-form">
      <input
        ref="productNameInput"
        v-model="filter.title"
        type="search"
        class="search-input"
        @search="productSearch"
        placeholder=" 请输入关键词" />
    </form>
  </section>
</template>
<style lang="scss">
.search-input {
  width: 100%;
  height: 40px;
  line-height: 40px;
  border: none;
  outline: none;
  font-size: 28px;
  text-align: left;
  color: #333;
  background-color: transparent;

  &::placeholder { /* 大部分现代浏览器 */
    color: #B3B3B3;
  }
  /* 修改默认的清除图标样式 */
  &::-webkit-search-cancel-button {
    -webkit-appearance: none;
    position: relative;
    width: 32px;
    height: 32px;
    background: url("./imgs/icon-close.png") no-repeat center;
    background-size: 32px 32px;
  }
}
</style>
```


## 下拉加载更多

> 移动端常见的下拉加载更多，也可以直接使用 [`better-scroll 1.x`](https://better-scroll.github.io/docs-v1/doc/zh-hans/events.html#pullingup) 实现。

实现方法很简单：
- 外层一个固定高度的容器，设置超出滚动
- 内层一个容器，高度随内容的高度
- 监听外层容器的滚动事件
- 判断距离底部多远，触发加载

码农直接上代码：

```vue
<template>
<!-- 外层容器，固定高度的 -->
<div class="timeline-block" id="timeline-scroll">
  <!-- 内层容器 -->
  <div>
    <!-- 无尽的内容 -->
    <div
      v-for="item in materialLists"
      :key="item.id"
      class="row timeline-li"
    ></div>
  </div>
</div>
</template>
<script>
import { throttle } from 'lodash'
let scrollFn = null
export default {
  data () {
    return {
      scrollDom: null, // 滚动的容器
      scrollDomFirstChild: null, // 滚动容器中的第一个元素
      scrollDomHeight: 0, // 滚动容器的高度
    }
  },
  mounted () {
    if (!this.filter.id) {
      this.initBscroll()
    }
  },
  methods: {
    initBscroll () {
      this.scrollDom = document.querySelector('#timeline-scroll')
      this.scrollDomFirstChild = this.scrollDom.children[0]
      if (this.scrollDomFirstChild) {
        this.scrollDomHeight = this.scrollDom.offsetHeight || 800
        // 节流
        scrollFn = throttle(this.beforeGetNews, 300)
        // 监听滚动
        this.scrollDom.addEventListener('scroll', scrollFn)
      }
    },
    beforeGetNews () {
      // 当滚动到距离底部一屏距离的时候就开始加载下一页
      // 内层容器的高度（即内容的总高度）- (外层容器的滚动高度+外层容器的高度) 的差值 小于 外层容器的高度时
      // 说明距离底部小于一个外层容器的高度了
      // this.scrollDomFirstChild.offsetHeight - (this.scrollDom.scrollTop + this.scrollDomHeight) <= this.scrollDomHeight
      if (this.scrollDomFirstChild.offsetHeight <= this.scrollDomHeight * 2 + this.scrollDom.scrollTop && !this.isNoMoreData) {
        this.getDate()
      }
    },
    getDate () {
      if (this.isRequestLock) {
        return
      }
      this.isRequestLock = true
      getFriendMaterialList({
        ...this.filter,
      }).then(res => {
        let lists = res.data || []
        if (this.filter.page === 1) {
          this.materialLists = lists
          if (lists.length) {
            this.isEmptyData = false
          } else {
            this.isEmptyData = true
          }
        } else {
          this.materialLists = this.materialLists.concat(lists)
        }
        this.filter.page++
        if (lists.length === 0) {
          this.materialLists = []
          this.$toast.center('当前筛选没有更多数据了~')
        }
        if (lists.length < this.filter.size) {
          this.isNoMoreData = true
        }
        this.isRequestLock = false
      }).catch(err => {
        console.error('getFriendMaterialList', err)
        this.isRequestLock = false
        this.$toast.center(err.message || err || '获素材列表失败')
      })
    },
    removeBscroll () {
      this.scrollDom && this.scrollDom.removeEventListener('scroll', scrollFn)
    },
  },
  destroyed () {
    this.removeBscroll()
    scrollFn = null
  },
}
</script>
```



## input placeholder样式修改

> 这个老是忘记，码农记性就是不好

```scss
  &::-webkit-input-placeholder { /* WebKit, Blink, Edge */
    font-size: 28px;
    color: #B3B3B3;
  }
  &:-moz-placeholder { /* Mozilla Firefox 4 ~ 18 */
    font-size: 28px;
    color: #B3B3B3;
    opacity:  1;
  }
  &::-moz-placeholder { /* Mozilla Firefox 19+ */
    font-size: 28px;
    color: #B3B3B3;
    opacity:  1;
  }
  &:-ms-input-placeholder { /* Internet Explorer 10 ~ 11 */
    font-size: 28px;
    color: #B3B3B3;
  }
  &::-ms-input-placeholder { /* Microsoft Edge */
    font-size: 28px;
    color: #B3B3B3;
  }
  &::placeholder { /* 大部分现代浏览器 */
    font-size: 28px;
    color: #B3B3B3;
  }
```


## 超过省略...

```scss
.duohang {
    /* 多行 */
    display: -webkit-box;
    -webkit-box-orient: vertical;
    -webkit-line-clamp: 2;
    overflow: hidden;
}
.single-line {
  overflow:hidden;
  white-space:nowrap;
  text-overflow:ellipsis;
}
```

参考：
- [CSS实现单行、多行文本溢出显示省略号（…）](https://www.cnblogs.com/gopark/p/8875655.html)
