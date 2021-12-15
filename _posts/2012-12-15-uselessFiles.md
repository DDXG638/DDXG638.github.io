---
layout:     post
title:      "useless-files-webpack-plugin插件"
subtitle:   "使用useless-files-webpack-plugin插件找出项目中未使用的文件"
date:       2021-12-15 11:00:00
author:     "ddxg"
header-img: "img/post-bg-nextgen-web-pwa.jpg"
header-mask: 0.3
catalog:    true
tags:
    - webpack
    - plugin
---

## useless-files-webpack-plugin插件
> 一个项目经历N次迭代之后变成了页面繁杂的大项目，因业务变化，很多页面已经不再使用，需要定期清理一波，使用这个插件可以帮助你找出已经没有使用的文件。
> 对于vue项目来说，只需要将不需要的页面路由删除，再使用这个插件，就可以找出已经不使用的文件了，检查再删除即可。
> [插件传送门](https://github.com/Viyozc/useless-files-webpack-plugin)

## 使用

使用非常简单，官方例子：
``` javascript
const UselessFile = require('useless-files-webpack-plugin')

plugins: [
  new UselessFile({
    root: './src', // 项目目录
    out?: './fileList.json', // 输出文件列表
    out?: (files) => deal(files), // 或者回调处理
    clean?: false // 删除文件,
    exclude?: path // 排除文件列表, 格式为文件路径数组
  })
]
```

在 `vue.config.js` 中：
```javascript
const UselessFilePlugin = require('useless-files-webpack-plugin')

module.exports = {
  chainWebpack: config => { 
    // 检测没有使用的文件
    config.plugin('uselessFiles').use(
      new UselessFilePlugin({
        root: path.resolve(__dirname, './src'), // 项目目录
        out: './uselessFiles.json', // 输出文件名字
        clean: false, // 删除文件,
        exclude: /node_modules/, // 排除文件列表, 格式为文件路径数组
      })
    )
  }
}
```

跑完项目之后就会生成一个 `uselessFiles.json` 文件，里面列出了没有使用的文件路径，可以一一对应，把不需要留的文件删除


## 参考：
- [useless-files-webpack-plugin](https://github.com/Viyozc/useless-files-webpack-plugin)
- [vue项目性能优化：去除无用的文件](https://blog.csdn.net/qq_41619796/article/details/121121399)
