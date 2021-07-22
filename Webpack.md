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
- cache-loader : 在性能开销大的loader前使用(因此loader是从后往前执行)，目的是将结果缓存进磁盘

#### 2. Webpack常见Plugin

- html-webpack-plugin: 简化html创建过程,自动创建文件并将打包后的js导入html文件
- mini-css-extract-plugin：提取chunk的css代码到独立文件
- terser-webpack-plugin : 对JS代码进行压缩混淆
- webpack-parallel-uglify-plugin：多线程执行代码压缩混淆，提高构建性能
- webpack-bundle-analyzer ：可视化 Webpack 输出文件的体积 (业务组件、依赖第三方模块)
- define-plugin: 用于在编译阶段定义全局变量

#### 3. Loader 和 Plugin 的区别

- 首先,Webpack只认识Javascript文件和 Json文件，因此需要对别的类型的文件进行操作的时候，就需要使用loader，loader本质上是一个函数，将接收到的内容进行转换后返回给下一个loader。
- Plugin就是插件，基于Tapable事件流框架，用于扩展Webpack的功能，Webpack的生命周期中会广播很多事件，Plugin可以监听这些事件并在合适的时机通过Webpack提供的API改变输出的结果。



#### 4. 如何优化构建体验

- loader使用缓存,如babel-loader可以直接添加?cacheDirectory开启,如果loader不支持,可以使用cache-loader进行缓存

- 使用`HappyPack`开启多进程打包

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

- `ParallelUglifyPlugin`开启多进程压缩混淆代码,亦或是直接使用`TerserWebpackPlugin`中包含配置选项可以开启多线程

- 使用```HotModuleReplacementPlugin```插件开启模块热更新

- 按需加载代码

- 优化 `SourceMap`模式

- 使用 `DllPlugin` 和`DllReferencePlugin` 将更改不频繁的代码进行单独编译(第三方的库，如react、lodash、axios)

- hard-source-webpack-plugin用于缓存模块

- 使用插件对构建的结果进行分析


#### 5. 优化性能

- 小图片使用base64编码
- bundle使用hash,为了更好的缓存
- 懒加载
- 提取公共代码
- IgnorePlugin，忽略第三方包指定目录，让这些指定目录不要被打包进去





- hash
  - Hash:和整个项目构建相关，只要项目文件发生改变，整个项目的hash都改变。
  - ChunkHash: 和Webpack打包的Chunk有关，不同的入口出来不同的Chunk。
  - ContentHash：根据文件内容hash,只有该文件发生改变的时候才会改变。
  
- 模块热更新: 运行时在本地服务器 dev-server 上在挂载一个websocket，本地服务器和浏览器之间通过websocket进行通信，当代码发生改变时，dev-server向浏览器发送一个包含hash的websocket消息，浏览器接收到后通过网络请求获取改变后的代码。

