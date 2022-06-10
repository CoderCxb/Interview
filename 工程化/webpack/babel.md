# Babel
Babel 是一个 JavaScript 编译器
 - 语法转换
 - 源代码转换
 - Polyfill

###### JS代码执行流程
JS --Parsing--> AST(Abstract Syntax Tree) --Transformation--> ByteCode -> V8


###### Babel执行阶段
源代码 -> 词法分析 -> tokens数组 -> 语法分析 -> AST -> 遍历 + 访问 AST -> 通过插件修改AST -> 新的AST -> 生成新代码

###### 常见预设
 - @babel/preset-env 预设了部分插件,默认通过browserslist对浏览器进行支持
 - @babel/preset-react 预设了jsx等React所需的插件
 - @babel/preset-typescript 预设了ts相关的插件

**小问题: 既然使用Babel-loader也可以对ts进行编译,那么ts-loader和babel-loader如何选择?**
 - babel-loader不会对ts进行代码检测
 - ts-loader不会进行polyfill

```javascript
// 一般采用 先tsc检测, 检测通过再使用babel-loader编译的方式
// package.json
{
  "scripts": {
    "type-check": "tsc --noEmit", // --noEmit 不生成js文件
    "type-check-watch": "tsc --noEmit --watch", // 实时检测文件
    "build": "npm run type-check && webpack",
  }
}
```

###### 配置文件
 - babel.config.json(js/cjs/mjs): 直接作用于Monorepos项目的子包,推荐
 - .babelrc(js/cjs/mjs/json)

```js
module.exports = {
  presets: [
    // 使用预设兼容浏览器
    ["@babel/preset-env", {
      // false: 不使用任何polyfill相关代码
      // usage: 代码中需要哪些就用哪些
      // entry: 需要手动导入core-js/stable和regenerator-runtime/runtime
      // 为了避免polyfill和第三方库的polyfill冲突,babel-loader需要排除node_modules
      useBuiltIns: "usage", // 默认添加到全局
      corejs: 3, // 指定corejs阶段
    }]
  ],
  plugins: [
    // 避免Polyfill污染全局, 一般写第三方包时使用
    ["@babel/plugin-transform-runtime", {
      // corejs为false, npm install --save @babel/runtime
      // corejs为2, npm install --save @babel/runtime-corejs2
      // corejs为3, npm install --save @babel/runtime-corejs3

      corejs: false, // 安装
    }],
  ]
}
```

###### Polyfill
补丁: 当浏览器不支持部分语法特性时,可以通过Polyfill进行填充, 填充和该特性基本一致的特性 

早期使用(babel7前)@babel/polyfill,现在使用core-js/stable和regenerator-runtime/runtime


###### 支持react模块热替换
```js
// 需要在webpack serve中才能使用
// 安装 
// npm install -D @pmmmwh/react-refresh-webpack-plugin react-refresh

// babel.config.js
module.exports = {
  plugin: [
    // 使react支持模块热替换
    // webpack.config.js需要使用 react-refresh-webpack-plugin
    ["react-refresh/babel"],
  ]
}

// webpack.config.js
const ReactRefreshWebpackPlugin = require('@pmmmwh/react-refresh-webpack-plugin');

module.exports = {
  devServer: {
    hot: true
  },
  plugins: [
    new ReactRefreshWebpackPlugin()
  ]
}
```


###### 支持vue模块热替换
```js
// 安装
// vue-loader就支持了模块热替换
// npm i vue-loader vue-template-compiler -D 

// webpack.config.js
const VueLoadPlugin = require('vue-loader/lib/plugin');

module.exports = {
  devServer: {
    hot: true, // 可能需要
  },
  module: {
    rules: [
      {
        test: /\.vue$/,
        use: 'vue-loader',
      }
    ] 
  },
  plugins: [
    new VueLoadPlugin()
  ]
}

```