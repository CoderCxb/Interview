#### 1. Webpack常见loader
  - html-loader :  将 HTML 导出为字符串。当编译器需要时，将压缩 HTML 字符串。
  - css 相关 loader
    - style-loader ：将 import style from './style.css' 以`<style></style>`插入到 DOM 中
    - css-loader：对 `@import` 和 `url()` 进行处理
    - less-loader:  加载 Less并编译为 CSS 
    - sass-loader: 加载 Sass/SCSS 文件并将他们编译为 CSS
    - postcss-loader: 使用 `PostCSS`处理 CSS 
- 文件相关
  - file-loader :  v5弃用，将一个文件中的 `import`/`require()` 解析为 url，并且将文件发送到输出文件夹。
  - url-loader : v5弃用, 用于将文件转换为 base64 URI 的 loader。
  - raw-loader: v5弃用,用于将文件作为字符串导入
  - file-loader和url-loader一起使用会出问题
- source-map-loader: 从现有的源文件中提取 source maps（从 `sourceMappingURL` 中提取）。
- ts-loader ：加载编译ts文件
- vue-loader :  加载编译vue文件
- babel-loader: 语法编译
- cache-loader : 在性能开销大的loader前使用(因为loader是从后往前执行)，目的是将结果缓存进磁盘

###### loader使用方式
- 内联方式(了解即可): 在import时也可以指定 import"style-loader!css-loader!../css/index.css"
- module配置
```js
module.exports = {
  module: {
    rules: [{
      test: /\*.css$/,
      use: [
        { loader: "style-loader", options: {} }
        { loader: "css-loader", options: {} }
      ]
      // loader: [ 'style-loader', 'css-loader' ]
    }]
  }
}
```

#### 2. Webpack常见Plugin
- html-webpack-plugin: 简化html创建过程,自动创建文件并将打包后的js导入html文件
- copy-webpack-plugin: 拷贝文件/文件夹到构建目录下
- SplitChunksPlugin: 进行模块分离
- mini-css-extract-plugin：提取chunk的css代码到独立文件
- CssMinimizerWebpackPlugin: 对css进行压缩
- terser-webpack-plugin : 对JS代码进行压缩混淆
- webpack-parallel-uglify-plugin：多线程执行代码压缩混淆，提高构建性能
- webpack-bundle-analyzer ：可视化 Webpack 输出文件的体积 (业务组件、依赖第三方模块)
- speed-measure-webpack-plugin: 分析loader和plugins的对于构建速度的影响
- define-plugin: 用于在编译阶段定义全局常量
- DllPlugin 和 DllReferencePlugin: 用某种方法实现了拆分 bundles，同时还大幅度提升了构建的速度

#### 3. Loader 和 Plugin 的区别
- 首先,Webpack只认识Javascript文件和 Json文件，因此需要对别的类型的文件进行操作的时候，就需要使用loader，loader本质上是一个函数，将接收到的内容进行转换后返回给下一个loader, 内容是JS所能识别的,一般是JS代码或者base64。
- Plugin就是插件，基于Tapable事件流框架，用于扩展Webpack的功能，Webpack的生命周期中会广播很多事件，Plugin可以监听这些事件并在合适的时机通过Webpack提供的API改变输出的结果。

#### 4. 如何优化构建体验
- loader层面
  - loader使用缓存,如babel-loader可以直接添加?cacheDirectory开启,如果loader不支持,可以使用cache-loader进行缓存
  - 通过exclude或include控制文件范围
  - module.noParse忽略不需要构建的大型库
  - webpack5 使用thread-loader
    ```javascript
      module: {
        rules: [
          {
            test: /\.js$/,
            include: path.resolve('src'),
            use: [
              "thread-loader",
              // 耗时的 loader （例如 babel-loader）
            ],
          },
        ],
      },
    ```
  - webpack4 使用`HappyPack`开启多进程打包 
    ```javascript
    // @file: webpack.config.js
    const HappyPack = require('happypack');
    module.exports={
        module:{
            rules:[
                {
                  test: /.js$/,
                  use: 'happypack/loader',
                }
            ]
        },
        plugins:[
          new HappyPack({
            loaders: [ 'babel-loader?cacheDirectory' ]
          })
        ]
    }
    ```

- JS文件层面
  - `ParallelUglifyPlugin`开启多进程压缩混淆代码,亦或是直接使用`TerserWebpackPlugin`中包含配置选项可以开启多线程

- 修改频率低的第三方库
  - 使用 `DllPlugin` 和`DllReferencePlugin` 将更改不频繁的代码进行单独编译(第三方的库，如react、lodash、axios)
  - 除了DLL的方式, 还可以通过配置external以及CDN的方式来使用更改不频繁的资源(可以通过HtmlWebpackPlugin配置) 本地可以不安装依赖
  - hard-source-webpack-plugin用于缓存模块

- 体积层面
  - 使用插件对构建的结果进行分析

- 优化 `SourceMap`模式

- webpack5中的缓存机制
  - 缓存webpack模块以及chunk
  - cache: false -  不使用缓存
  - type: filesystem(快) - 将缓存的文件存放在文件系统中 node_modules/.cache/webpack
  ```javascript
    cache: {
      type: 'filesystem', 
    },
  ```
- type: memory(默认,略慢) - 将缓存存放到内存中
  ```javascript
    cache: {
      type: 'memory', 
    },
  ```

#### 5. 优化体积
 - TerserWebpackPlugin压缩JS/CssMinimizerWebpackPlugin压缩CSS
 - gzip压缩
 - webpack-bundle-analyzer
 - 配置库的按需加载,如ant-design这种组件库都支持


#### 6. 优化性能
- 小图片使用base64编码
- bundle使用hash,为了更好的缓存
- 懒加载
- 提取公共代码
- IgnorePlugin，忽略第三方包指定目录，让这些指定目录不要被打包进去
- hash
  - Hash:和整个项目构建相关，只要项目文件发生改变，整个项目的hash都改变。
  - ChunkHash: 和Webpack打包的Chunk有关，不同的入口出来不同的Chunk。
  - ContentHash：根据文件内容hash,只有该文件发生改变的时候才会改变。



#### 6. 什么是SourceMap? 作用是什么？
在开发时, webpack会将代码进行打包, 此时如果报错,报错的位置默认情况下并不是源代码的位置,为了方便调试,引入了source-map,可以理解为源代码的映射

模式规则:  \[ inline- | hidden- | eval- ]\[ nosources- ]\[ cheap- \[ module- ] ]source-map 
 - eval-*: 使用eval执行模块,优化了重新构建时的性能
 - inline-*: 将 SourceMap 内联到原始文件，而不是创建单独的文件
 - hidden-*: 不添加对SourceMap的引用,即SourceMap文件会生成,但是不生效,因为没有引用
 - nosources-*: SourceMap中不包含源代码,也就无法映射到源代码的位置了

 - *-module-*: 默认情况下忽略Loader SourceMap,即只能映射到loader编译之后的代码,而不会源代码,使用 *-module-*的模式,能够对Loader SourceMap进行映射,获取更好的映射效果。
 - *-cheap-*: 不映射列数,仅映射行数

例子: eval-nosources-cheap-module-source-map: 使用eval执行模块,不包含源代码,忽略列数,映射loader的模式

```json
{
  "version": "", // source-map版本
  "sources": [], // 源文件
  "names": [], // 转换前的属性名
  "mappings": ";;;;;;;;AAA;AACA.....", // VLQ(variable length quantity) 记录映射信息
  "file": "", // 打包之后的文件
  "sourcesContent": [], // 源代码
  "sourceRoot": "", // 源代码目录
}
```


#### 7. 什么是tree-shaking? 
tree-shaking是描述移除未引用代码的概念, 依赖ES6的静态模块特性。

Webpack5尝试的时候是默认保留了css,如果css被tree-shaking, 可以配置packag.json中的sideEffects: \["*.css"], sideEffects用于告诉Webpack哪些文件是有副作用的

#### 8. TerserWebpackPlugin压缩JS代码
 - 删除空格
 - 缩写变量
 - 删除注释
 - 预计算,将一些依赖常量的表达式提前计算,直接使用其结果
 

#### 9. 工程化配置
 - prittier
 - eslint 
 - githooks
  - .git/hooks
 - commitlint
 - CI/CD



#### 11. Webpack常见变量(占位符)
 - hash: 和整个项目的构建决定, 其中任何一个资源的改动都会使得hash发生改变
 - chunkhash: 
 - contentHash: 和文件内容决定, 仅内容发生改变时hash改变
 - ext	文件后缀名
 - name	文件名
 - path	文件相对webpack配置文件的路径
 - folder	文件所在文件夹

#### 12. 模块热更新
 - webpack重新构建时, 会生成lastHash.hot-update.json和chunk.lastHash.hot-update.ext,其中lastHash为上一次构建的hash值
 - 代码发生改变, dev-server向浏览器发送WebSocket消息, 告知新构建的hash值用于下次请求
 - 浏览器根据上一次hash发送请求hot-update.json
 - hot-update.json包含新的hash值和需要更新的模块, 根据内容请求对应的资源就完成的热替换


#### 13. 资源模块
 - asset/resource: 发送一个单独的文件并导出 URL。之前通过使用 file-loader 实现。
   - 将文件单独拎出去, js照常拉取, 会更快一点,不过会多发送网络请求
 - asset/inline: 导出一个资源的 data URI。之前通过使用 url-loader 实现。
   - base64打包到bundle.js中, 会是js文件变大,浏览器获取js更慢,而页面需要等bundle获取之后才渲染
 - asset/source: 导出资源的源代码。之前通过使用 raw-loader 实现。
 - asset: 在导出一个 data URI 和发送一个单独的文件之间自动选择。之前通过使用 url-loader，并且配置资源体积限制实现。
    - 大文件通过http请求
    - 小文件转换成base64打包到js
    - 配置asset/resource输出的文件名
```javascript
  output: {
    // 默认情况下输出是 [hash][ext][query] 
    assetModuleFilename: 'images/[name][hash][ext]'
  }
```

```js
 module: {
    rules: [
      {
       test: /\.png/,

       // 一般小文件使用inline
       type: 'asset/inline',

       type: 'asset/resource'
       type: "asset",
       // 配置输出 
       generator: {
         filename: "image/[name][hash][ext]", // 优先级大于assetModuleFilename
       },
       parser: {
         dataUrlCondition: {
           maxSize: 100 * 1024, // 设置文件限,超过则使用resource,否则inline
         }
       }
     },
 }
```
###### scope hoisting 
作用域提升,将变量/函数提升到使用的作用域, 避免了复杂的查找步骤

###### react-dev-utils
 - InlineChunkHtmlPlugin 将chunk直接插入html,减少http请求
 - InterpolateHtmlPlugin 向HTML模版注入变量

###### 13. webpack配置拆分
```js
// webpack.base.js
const merge = require('webpack-merge');
const commonConfig = require('./webpack.common.js')
const prodConfig = require('./webpack.prod.js');
const devConfig = require('./webpack.dev.js');

// 可以导出一个函数, 可以获取到webpack命令传递的参数 --env=production
module.exports =  function(env){
  // --env=production,通过env.production获取,用于判断是否开发环境
  const isProduction = env.production;
  // 用于babel.config.js等配置文件区分开发环境和生产环境
  process.env.NODE_ENV = isProduction ? "production": "development";
  return isProduction 
    ? merge(commonConfig, prodConfig)
    : merge(commonConfig, devConfig)
}
// webpack.common.js
module.exports =  {};

// webpack.dev.js
module.exports =  {
  mode: 'development'
};

// webpack.prod.js
module.exports =  {
  mode: 'production'
};
```

