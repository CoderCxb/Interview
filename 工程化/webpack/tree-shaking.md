# tree-shaking
tree-shaking是描述移除未引用代码的概念, 依赖ES6的静态模块特性。

Webpack5尝试的时候是默认保留了css,如果css被tree-shaking, 可以配置package.json中的sideEffects: \["*.css"], sideEffects用于告诉Webpack哪些文件是有副作用的


## JS的tree-shaking

### usedExports + Terser
通过标记某些函数是否被使用, 之后通过Terser来进行优化(删除的并不彻底,优化力度没有sideEffects大)

```js
module.exports = {
  optimization: {
    // 会标注哪些函数没有被使用, 之后会被Terser解析并优化
    usedExports: true,
    minimize: true, // 设置为true, 不然不会调用Terser
    // 默认的minimizer就有Terser
    minimizer: [
      new TerserPlugin(),
    ],
  }
}
```


### sideEffects 
跳过整个文件/模块, 直接查看文件是否有副作用

###### sideEffects: false
```json
// package.json
{
  // 告诉webpack 所有导入但是没有引用的模块都没有副作用
  // 无论是以下哪种形式,只要导入没有被引用,则都会被视为dead-code而被删除
  // import 'index.css'
  // import { max } from 'math.js'
  "sideEffects": false,
}
```

###### 常见设置
```js
// package.json
{
  // 告诉webpack,哪些文件有副作用
  "sideEffects": [
    "**.css", // 所有css都有副作用
  ],
}

// css除了可以通过sideEffects设置,还可以在rule配置时添加
// webpack.config.js
module.exports = {
  module: {
    rules: [{
      test: /\.css$/i,
      use: [
        'style-loader',
        "css-loader",
      ],
      sideEffects: true, // 表示css文件都是有副作用的
    }],
  }
}
```

## CSS的tree-shaking
通过PurgeCSS插件实现, 开发环境感觉可以不用

```js
// weback.config.js
const PurgeCssPlugin = require('purgecss-webpack-plugin');s
const path = require('path');
const glob = require('glob');
const src = path.resolve(__dirname, "./src");
module.exports = {
  plugins: [
    new PurgeCssPlugin({
      paths: glob.sync(`${src}/**/*`, {
        nodir: true, // 匹配文件
      }),
      safelist: function (){
        return {
          // 默认情况下,html会被移除
          standard: ["html"， "body "]
        }
      }
    })
  ]
}

```