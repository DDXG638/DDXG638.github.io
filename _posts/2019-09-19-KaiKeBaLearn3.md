---
layout:     post
title:      "webpack入门练习"
subtitle:   "记录一些webpack相关的入门练习"
date:       2019-09-19 21:00:00
author:     "ddxg"
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - webpack
    - kaikeba
---

# webpack入门练习

> 在工作中经常接触webpack相关的东西，像Vue工程打包、内部封装的库和自己封装js功能库的时候都会用到webpack打包。
> 但是，我对webpack的接触是非常零散的，连皮毛都不及，经常不知道要将相关的配置写到webpack配置文件的哪里，对webpack太陌生了。

## webpack loader

> loader 让 webpack 能够去处理那些非 JavaScript 文件（webpack 自身只理解 JavaScript）。loader 可以将所有类型的文件转换为 webpack 能够处理的有效模块，然后你就可以利用 webpack 的打包能力，对它们进行处理。


### [file-loader](https://www.webpackjs.com/loaders/file-loader/)

处理静态资源模块，原理是把打包入口中识别出的资源模块，移动到输出目录，并且返回一个地址名称。
一般用在处理图片、svg、txt、excel等资源的时候。

```  javascript
{
    // 处理静态资源
    // 正则匹配需要用这个loader的文件
    test: /\.(png|jp?g|gif)$/,
    use: [
        {
            // 指定要使用的是那个loader
            loader: 'file-loader',
            options: {
                // 配置文件输出的名字
                name: '[name].[ext]',
                // 指定在输出的路径，相对以output.path
                outputPath: 'img/'
            }
        }
    ]
}
```

``` javascript
// 引入图片
const avatar = require('../img/avatar.jpg');
console.log('avatar', avatar); // avatar avatar.jpg
```


### [url-loader](https://www.webpackjs.com/loaders/url-loader/)

> url-loader 功能类似于 file-loader，但是在文件大小（单位 byte）低于指定的限制时，可以返回一个 DataURL。
> 就是在指定大小之内的图片会转换成base64的形式。

``` javascript
{
    // 使用url-loader
    test: /\.(png|jp?g|gif)$/,
    use: [
        {
            loader: 'url-loader',
            options: {
                // 图片大小低于1024B就转为base64
                limit: 1024,
                // 指定png格式的文件才转
                mimetype: 'image/png',
                // 配置文件输出的名字
                name: '[name].[ext]',
                // 指定在输出的路径，相对以output.path
                outputPath: 'img/'
            }
        }
    ]
}
```

### 样式loader

- [css-loader](https://www.webpackjs.com/loaders/css-loader/):css-loader 解释(interpret) @import 和 url() ，会 import/require() 后再解析(resolve)它们。引用资源的合适 loader 是 file-loader和 url-loader。
- [style-loader](https://www.webpackjs.com/loaders/style-loader/):会把css-loader生成的内容，以`<style>` 标签添加到`html`的`head`中。
- [postcss-loader](https://www.webpackjs.com/loaders/postcss-loader/):postcss 一种对css编译的工具，类似babel对js的处理，使用下一代css语法、自动补全浏览器前缀、自动把px代为转换成rem和css 代码压缩等等
- [stylus-loader](https://www.npmjs.com/package/stylus-loader):处理stylus预处理器
    
这里我是用了postCss的Autoprefixer自动补全的插件和压缩插件，需要创建 `postcss.config.js` 文件：
``` javascript
module.exports = {
    plugins: [
        require('autoprefixer'), // 自动补全兼容性前缀
        require('cssnano') // 压缩css代码
    ]
}
```


第一版：
``` javascript
{
    // 处理样式，使用了stylus的预处理器
    test: /\.styl$/,
    /*
    * 引入的顺序是有区别的，use数组中的顺序是从后往前的，执行的顺序是倒过来的。
    * postcss-loader：Use it after css-loader and style-loader, 
    * but before other preprocessor loaders like e.g sass|less|stylus-loader, if you use any.
    * */

    /**
     * 这种打包方式是将css引入js中的，是通过style-loader的作用将css插入到head中的，这样子会有一个时间差，会闪现一下无样式的状态，体验非常不好。
     */
    use: [
        'style-loader', // 会把css-loader生成的内容，以<style> 标签添加到html的head中
        'css-loader', // 解释(interpret) @import 和 url()
        'postcss-loader',
        'stylus-loader'
    ]
    
}
```

后来使用 `extract-loader` 和 `file-loader` 对单独抽出了css文件，然后在html文件中引入
``` javascript
{
    // 处理样式，使用了stylus的预处理器
    test: /\.styl$/,
    /*
    * 分离css
    * */
    use: [
        {
            loader: 'file-loader',
            options: {
                name: '[name].css'
            }
        },
        'extract-loader',
        'css-loader', // 解释(interpret) @import 和 url()
        'postcss-loader',
        'stylus-loader'
    ]
}
```

分离css也可以使用 [mini-css-extract-plugin插件](https://www.npmjs.com/package/mini-css-extract-plugin)
``` javascript
// loader
use: [{
    loader: MiniCssExtractPlugin.loader,
    options: {
        hmr: process.env.NODE_ENV === 'development'
    }
}]

// 插件配置
plugins: [
    /*
    * extract-text-webpack-plugin 目前不支持webpack4.x的版本，使用beta版本可以work，但是不支持生成hash文件名
    * 改用mini-css-extract-plugin
    * */
    // 传入extract-text-webpack-plugin的选项
    // new ExtractTextPlugin('index.css')

    new MiniCssExtractPlugin({
        filename: 'index.css',
        chunkFilename: "[id].css"
    })
]
```

参考：
- [PostCSS——css必备](https://www.jianshu.com/p/288963680642)
- [webpack4.x配置详情](https://www.cnblogs.com/zengfp/p/9561756.html)
- [webpack进阶之loader篇](https://www.cnblogs.com/shiyunfront/p/7338530.html)
  

### html loader

使用 [html-loader](https://www.webpackjs.com/loaders/html-loader/) 导出html文件

js代码中引入html`require("html-loader!../html/index.html");`

``` javascript
{
    test: /\.html$/,
    use: [
        {
            loader: 'file-loader',
            options: {
                name: '[name].[ext]'
            }
        },
        'extract-loader',
        {
        loader: 'html-loader',
        options: {
            minimize: true,
            removeComments: false,
            collapseWhitespace: false
        }
    }],
}
```

做了上面这么多我才发现webpack跟gulp的差别还是挺大的。loader一定是需要在js文件中引入才会触发功能的，
像上面的，为了导出html代码，然后在js文件中 `require("../html/index.html")`，这让我感到很奇怪，在js中引入html文件？？？

总结代码：
``` javascript
const path = require('path');

module.exports = {
    entry: './src/js/index.js',
    output: {
        path: path.resolve(__dirname, 'dist/js'),
        filename: 'index.js'
    },
    module: {
        rules: [
            {
                // 使用url-loader
                test: /\.(png|jp?g|gif)$/,
                use: [
                    {
                        loader: 'url-loader',
                        options: {
                            // 图片大小低于1024B就转为base64
                            limit: 1024,
                            // 指定png格式的文件才转
                            mimetype: 'image/png',
                            // 配置文件输出的名字
                            name: '[name].[ext]',
                            // 指定在输出的路径，相对以output.path
                            outputPath: '../img/'
                        }
                    }
                ]
            },
            {
                // 处理样式，使用了stylus的预处理器
                test: /\.styl$/,
                /*
                * 引入的顺序是有区别的，use数组中的顺序是从后往前的，执行的顺序是倒过来的。
                * postcss-loader：Use it after css-loader and style-loader, 
                * but before other preprocessor loaders like e.g sass|less|stylus-loader, if you use any.
                * */

                /**
                 * 这种打包方式是将css引入js中的，是通过style-loader的作用将css插入到head中的，
                 * 这样子会有一个时间差，会闪现一下无样式的状态，体验非常不好。
                 */
                /*use: [
                    'style-loader', // 会把css-loader生成的内容，以<style> 标签添加到html的head中
                    'css-loader', // 解释(interpret) @import 和 url()
                    'postcss-loader',
                    'stylus-loader'
                ]*/

                /*
                * 分离css
                * */
                use: [
                    {
                        loader: 'file-loader',
                        options: {
                            name: '[name].css',
                            outputPath: '../css/'
                        }
                    },
                    'extract-loader',
                    'css-loader', // 解释(interpret) @import 和 url()
                    'postcss-loader',
                    'stylus-loader'
                ]
            },
            {
                test: /\.html$/,
                use: [
                    {
                        loader: 'file-loader',
                        options: {
                            name: '[name].[ext]',
                            outputPath: '../'
                        }
                    },
                    'extract-loader',
                    {
                        loader: 'html-loader',
                        options: {
                            minimize: true,
                            removeComments: false,
                            collapseWhitespace: false
                        }
                    }
                ],
            }
        ]
    }
}
```
这个配置会将src中的html、js、css和图片都打包到dist目录。都是用loader实现的。感觉有点别扭。。。
![img](/img/2019/webpack1.png)


我觉得css文件和html文件都不应该依赖js的引入来生成，下面是用 插件 的形式来生成html文件，并且可以选择 以内联的方式引入js和css。


使用的是 `html-webpack-plugin` 插件 和 `mini-css-extract-plugin` 结合


### [html-webpack-plugin](https://github.com/jantimon/html-webpack-plugin) 分离html

> 结合 `mini-css-extract-plugin`，可以达到的效果是会将js文件和css文件自动添加到html文件中，不需要手动引入。
> 里面还介绍了很多使用的功能。

尝试使用 `html-webpack-plugin` 的插件[html-webpack-inline-source-plugin](https://github.com/DustinJackson/html-webpack-inline-source-plugin) 进行内联js和css的操作但是编译报错了，好像现在还没有相关的解决方法。

最终生成的目录结构跟上面的一样，只是不需要手动引入使用的js和css了。

完整的配置如下：
``` javascript
const path = require('path');
// const ExtractTextPlugin = require("extract-text-webpack-plugin");
const HtmlWebpackPlugin = require('html-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin'); // 抽离css的插件
const HtmlWebpackInlineSourcePlugin = require('html-webpack-inline-source-plugin'); // js和css内联进html的插件

module.exports = {
    entry: './src/js/index.js',
    output: {
        path: path.resolve(__dirname, 'dist/js'),
        filename: 'index.js'
    },
    module: {
        rules: [
            {
                // 使用url-loader
                test: /\.(png|jp?g|gif)$/,
                use: [
                    {
                        loader: 'url-loader',
                        options: {
                            // 图片大小低于1024B就转为base64
                            limit: 1024,
                            // 指定png格式的文件才转
                            mimetype: 'image/png',
                            // 配置文件输出的名字
                            name: '[name].[ext]',
                            // 指定在输出的路径，相对以output.path
                            outputPath: '../img/'
                        }
                    }
                ]
            },
            {
                // 处理样式，使用了stylus的预处理器
                test: /\.styl$/,
                /*
                * 分离css
                * */
                use: [
                    {
                        loader: MiniCssExtractPlugin.loader,
                        options: {
                            hmr: process.env.NODE_ENV === 'development'
                        }
                    },
                    'css-loader', // 解释(interpret) @import 和 url()
                    'postcss-loader',
                    'stylus-loader'
                ]
            },
        ]
    },
    plugins: [
        new MiniCssExtractPlugin({
            filename: '../css/index.css',
            chunkFilename: "[id].css"
        }),
        // 使用插件分离html
        new HtmlWebpackPlugin({
            filename: '../index.html',
            template: 'src/html/index.html',
            inlineSource: '.(js|css)$'
        }),
        // 将js和css内联进html的插件，编译有问题，暂时无法解决
        // new HtmlWebpackInlineSourcePlugin(HtmlWebpackPlugin)
    ]
}
```

## [devtool-sourceMap](https://www.webpackjs.com/configuration/devtool/#devtool)

> 主要是用来控制source-map的，我觉的主要的用处是可以看到具体出错的位置，编译后的代码基本都是混淆和压缩过后的，
> 浏览器的报错信息指向的是压缩后的代码。即使找到位置也很难判断是源码中的哪行代码出问题了，有了source-map就可
> 以定位到源码中的位置。但是这会生成额外的source-map代码，增大打包后的资源大小。

当 `webpack` 配置中的 `mode: 'development'` 时默认开启source-map功能，可以使用 `devtool` 字段来指定使用
哪种格式的source-map。官网上面也有解释各种格式的差异。

你可以直接使用 `SourceMapDevToolPlugin/EvalSourceMapDevToolPlugin` 来替代使用 `devtool` 选项，插件与 `devtool` 不可同时设置。

- 开发环境适合：`cheap-module-eval-source-map` ：只映射行数。
- 生产环境适合：不配置 或者 `cheap-module-source-map`。

生产环境我是觉得不需要配置source-map比较好。在浏览器中source-map文件时浏览器解析之后再展示到控制台的，它是先展示原始
的错误栈信息，解析完source-map之后再展示解析后的错误栈信息。页面中通过错误监听拿到的错误栈信息是原始的错误信息。所以
页面使用source-map对于错误上报来说是没有用的。我觉得在 错误信息入库前 或者 在错误信息出库展示 前使用source-map解析
转换一下比较合理一些，这样子的话就需要在服务器保存一份最新的source-map文件。


## [开发中 Server(devServer)](https://www.webpackjs.com/configuration/dev-server/#devserver)

> webpack-dev-server 这个插件是非常使用的，可以启动一个服务器，修改代码保存后可以自动刷新，不需要手动在build一下。
> 可以设置devServer.hot:true来启动webpack的模块热替换功能，就是修改代码之后，在页面不刷新的状态下就展示新的效果,
> 适合调试css和js的时候。


### 启动devServer

启动一个服务很简单。`npm install -D webpack-dev-server`，然后在 `webpack.config.js` 中配置devServer，在 `package.json` 中添加 `webpack-dev-server` 命令。

``` javascript
devServer: {
    contentBase: path.join(__dirname, "dist"), // 告诉服务器从哪里提供内容。一般就是你的html文件的目录
    compress: true, // 一切服务都启用gzip 压缩
    port: 9000 // 配置端口
  },
```

改动文件保存之后，自动执行build命令，页面就会自动刷新了。非常方便。


### [开启 HMR 模块热替换特性](https://www.webpackjs.com/configuration/dev-server/#devserver-hot)

可以设置 `devServer.hot:true` 来启动webpack的模块热替换功能，就是修改代码之后，在页面不刷新的状态下就展示新的效果,适合调试css和js的时候。

需要在 `webpack.config.js` 中添加一个插件 `new webpack.HotModuleReplacementPlugin()`，这样调样式就更方便啦。

这种功能是得益我们使用的 插件 或者 loader 中内部实现了HMR功能的，
一般css相关的loader都实现了HMR功能，像 `style-loader` 就实现了，所以可以直接使用。

有个问题是，如果css是外部引入的话，不是用 `style-loader` 直接写在 `<style>` 中的话，是实现不了HMR的。

还有一个问题是js，像你自己写的js代码可能是不会考虑HMR的，这时就需要你自己进行处理才能实现相应的效果了。

``` javascript
import counter from "./counter";
import number from "./number";

counter();
number();

if (module.hot) {
  module.hot.accept("./number", () => {
      // 需要在这个回调里面手动执行一下js代码
    document.body.removeChild(document.getElementById("number"));
    number();
  });
}
```

## [babel](https://babeljs.io/setup)

> babel 主要是处理js代码兼容性的问题，将高级的js语法转为古董浏览器能够识别的代码。

进入官网： `https://babeljs.io -> setup -> 选择webpack`

安装： `npm install --save-dev babel-loader @babel/core`

webpack.config.js 中 增加一个loader
``` javascript
{
    // 使用babel 
    test: /\.js$/, 
    exclude: /node_modules/, // 忽略依赖中的文件
    use: [
        {
            loader: "babel-loader",
            options: {
                presets: ["@babel/preset-env"]
            }
        }
    ]
}
```

这样下来只是将基本的语法转换了(let、const、箭头函数等改了)，但是没有做方法的兼容处理，比如 `Promise\map` 等都没有变化。

安装 `npm install --save @babel/polyfill`，使用 `useBuiltIns: 'usage'` 按需引入的话，
就不需要再页面中 `import/require @babel/polyfill` 了。不使用按需引入的话，打包后的js体积将会非常大。
``` javascript
{
    "presets": [
        ["@babel/preset-env", {
            "useBuiltIns": "usage", // 按需引入
            "corejs": 2
        }]
    ]
}
```

### [@babel/plugin-transform-runtime](https://babeljs.io/docs/en/babel-plugin-transform-runtime)


这个转换器的另一个目的是为您的代码创建一个沙箱环境。如果直接导入core-js或@babel/polyfill及其提供的内置组件(如Promise、Set和Map)，则会污染全局范围。虽然这对于应用程序或命令行工具来说是可以的，但是如果您的代码是一个库，您打算将其发布给其他人使用，或者您不能确切地控制代码运行的环境，那么就会出现问题
