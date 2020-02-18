---
title: 简单入门 Webpack
date: 2020-02-09 22:56:06
tags: [Webpack, 项目配置, 原理]
categories:
- 项目配置
---

简单入门对项目中 Webpack 的配置。<!-- more -->

在实习中接触了很多项目结构、配置的东西，当时只是简单解决并没有了解原理，最近学习了一下 Webpack 相关内容，做一个总结。

# 简介

Webpack 是一个开源的 JavaScript 模块打包工具，解决模块之间的依赖，把各个模块按照特定的规则和顺序组织在一起，最终合并成一个或多个 JS 文件，即 bundle。

## 对比

| 引用 script | 使用 webpack |
| :------| :------ |
| 需要手动维护 JS 文件加载顺序 | 通过导入/导出语句加载模块，依赖关系清晰 |
| 加载每个 script 标签需要请求一次 | 加载合并后的资源，减少网络开销 |
| 顶层作用域即全局作用域 | 各模块独立，不会有命名冲突 |

## 配置文件

Webpack 的默认配置文件为 webpack.config.js，比如

```JavaScript
// webpack.config.js
module.export = {
    entry: './src/index.js',
    output: {
        filename: 'bundle.js'
    },
    mode: 'development'
}
```

当打包项目时，需要指定如上所示的一系列参数。如果配置了这个文件，在 `package.json` 中再添加一个简短的命令：

```JavaScript
  "scripts": {
    "build": "webpack"
  }
```

此时使用 `yarn build` 或者 `npm run build` 就可以完成打包操作。
打包后产生的文件默认在 `/dist` 目录下。

如果没有配置文件，可以直接在 `package.json` 中添加完整命令：

```JavaScript
  // 默认的入口为 `./src/index.js`，如果只有一个入口可以不用配置
  "scripts": {
    "build": "webpack --output-filename=bundle.js --mode=development"
  }
```

# 模块打包

## 模块标准

CommonJS 和 ES6 Module 是目前使用较为广泛的模块标准。

### 对比

| CommonJS | ES6 Module |
| :------| :------ |
| 运行时建立模块依赖关系 | 编译时建立模块依赖关系 |
| 模块导入是值拷贝 | 模块导入是变量映射 |
| 不支持循环依赖 | 支持循环依赖 |

## 模块打包原理

举例：

```JavaScript
// index.js
const calculator = require('./calculator');
const sum = calculator.add(2, 3);
console.log('sum', sum);

// calculator.js
module.exports = {
  add: function(a, b) {
    return a + b;
  }
}
```

上面的代码经 webpack 打包后会产生一个 `bundle.js`：

```JavaScript
// 最外层立即执行函数，包裹整个 bundle，并构成自身的作用域
(function(modules) {
  // 模块缓存，installedModules 对象，储存第一次加载时的模块，模块再次加载将直接从这个对象取值
  var installedModules = {};
  // 模块加载 require 的实现
  function __webpack__require__(moduleId) {
    ...
  }
  // 加载入口模块
  return __webpack__require__(__webpack__require__.s = 0);
})({
  // modules：以 key-value 的形式储存所有被打包的模块
  // key 可以理解为一个模块的 id，由数字或一个很短的 hash 字符串构成
  // value 是一个匿名函数包裹的模块实体，参数表示导入导出的能力。
  0: function(module, exports, __webpack__require__){
    // 打包入口
    //执行模块代码，将执行权交给 __webpack__require__ 的模块，结束后返回，是一个递归过程
    module.exports = __webpack__require__('3qiv');
  },
  '3qiv': function(module, exports, __webpack__require__) {
    // index.js 内容
  },
  jkzz: function(moudle, exports) {
    //calculator.js 内容
  }
});
```

# 打包相关资源的输入输出

## 配置资源入口

1. entry：入口，可以有多个。类型可以是字符串、数组、对象（多入口使用）、函数。传入一个数组的作用是将多个资源预先合并，在打包时 Webpack会将数字中的最后一个元素作为实际的入口路径。传入一个函数的话可以在函数体内添加动态逻辑来获取入口或返回一个 Promise 对象来进行异步操作。
2. chunk：Webpack 从入口文件开始检索，将具有依赖关系的模块生产一棵依赖树。chunk 就是对它的抽象和包装。
3. bundle：打包产物，由 chunk 打包得到。
4. chunk name：工程只有一个入口，chunk name 默认为 `main`。有多个入口时 entry 需要使用对象形式，每个 chunk name各自定义，作为 chunk 的唯一标识。
5. vendor：一般指过程中所使用的库、框架等第三方模块集中打包而产生的 bundle。在 entry 中设置 vendor 可以将第三方模块抽取出来，利用客户端缓存加快整体渲染速度。

```JavaScript
module.exports = {
  entry: {
    app: './src/app.js',
    vendor: ['react', 'react-dom', 'react-router']
  }
}
```

## 配置资源出口

所有与出口相关的配置都在 output 对象里。

```JavaScript
module.exports = {
  entry: './src/app.js',
  output: {
    filename: 'bundle.js'
  }
}
```

1. filename：输出资源的文件名或相对路径。如果多入口，可以使用 `[name].js`。

> | 变量名称 | 功能描述 |
> | :------| :------ |
> | [name] | 指代 chunk name |
> | [hash] | 指代 Webpack 此次打包所有资源生成的 hash |
> | [chunkhash] | 指代当前 chunk 的 hash |
> | [id] | 指代当前 chunk 的 id |

2. path：资源输出的位置，绝对路径。默认 dist 目录，一般不用设置。

3. publicPath： 资源请求的位置。

# 预处理器

每个预处理器（loader）本质上都是一个函数。

```JavaScript
module.exports = {
  // ...
  module: {
    rules: [{
      test: /\.css$/,
      use: ['style-loader', 'css-loader']
    }]
  }
}
```

与 loader 相关的配置都在 module 对象中，其中 module.rules 代表模块的处理规则。test 和 use 都可以接收数组。

Webpack 在打包时是按照数组从后往前的顺序将资源交给 loader 处理的，因此要把最后生效的放在最前面。

例子中是将 css 文件先用 css-loader 处理，再用 style-loader 将样式字符串包装成 style 标签插入页面。

# 插件

插件目的在于解决 loader 无法实现的其他事。

使用的话是在 plugins 属性传入 new 实例：

```JavaScript
const HtmlWebpackPlugin = require('html-webpack-plugin');
const webpack = require('webpack'); //访问内置的插件

module.exports = {
  plugins: [
    new webpack.optimize.UglifyJsPlugin(),
    new HtmlWebpackPlugin({template: './src/index.html'})
  ]
}
```

参考资料：
[Webpack 中文网](https://www.webpackjs.com/concepts/)
[Webpack实战：入门、进阶与调优](https://read.douban.com/ebook/116888545/)