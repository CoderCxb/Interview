# 开发环境
https://webpack.docschina.org/guides/development

### 不使用时开发服务器 Watch Mode
webpack --watch + vscode的live-server插件 或 http-server库

弊端: 
 - 代码修改,所有源代码重新编译(webpack5 测试感觉不明显)
 - 依赖打包后的文件, 频繁进行文件读写
 - 依赖第三方库或者插件
 - live-server和http-server需要刷新才能更新代码

###### 使用 
 - 安装: npm i webpack-dev-server -D
 - 使用: npx webpack serve


### webpack-dev-server
 - express

###### webpack.config.js
```js
// 注意: 如果配置output.publicPath可能会导致资源路径访问不到
module.exports = {
  output: {
    // publicPath: '', // 可能会导致devServer访问不到
  },
  devServer: {
    // 监听目录, 默认public
    static: [ 'public', 'assets' ], // 也可以是false 
    liveReload: false, // 开启live load
    hot: true, // 开启模块热替换
    open: true, // 打开浏览器
    // 配置跨域
    // 所有/api开头的请求都会代理到
    proxy: {
      '/api': 'http://localhost:3000',
      '/api2': {
        target: 'http://localhost:3000',
        pathRewrite: { '^/api2': '' }, // 不希望传递/api2
        secure: false, // 是否必须是https, 默认为true
        changeOrigin: true, // 是否保留原主机
      }
    },
    historyApiFallback: true, // 当使用 HTML5 History API 时，任意的 404 响应都可能需要被替代为index.html
    allowedHosts: [], // 可访问域名, all即跳过host检测
    compress: true, // 开启gzip压缩
    http2: true, // 开启http2
    https: true, // 开启https
    headers: {}, // 为所有响应添加响应头
    host: '0.0.0.0', // 指定host
    port: 8080, // 指定端口号
  }

}
```


###### HMR 
模块热更新(Hot Module Replacement): 代码发生改变,不刷新页面即可更新代码

React和Vue的模块热替换配置参考babel中

```javascript
// 开启模块热更新
if(module.hot){
  module.hot.accept("./math.js", ()=>{
    // 更新完成的回调函数
  })
}

// webpack.config.js
{
  devServer: {
    hot: true
  }
}
```

 - webpack重新构建时, 会生成lastHash.hot-update.json和chunk.lastHash.hot-update.ext,其中lastHash为上一次构建的hash值
 - 代码发生改变, dev-server向浏览器发送WebSocket消息, 告知新构建的hash值用于下次请求
 - 浏览器根据上一次hash发送请求hot-update.json
 - hot-update.json包含新的hash值和需要更新的模块, 根据内容请求对应的资源就完成的热替换


### webpack-dev-middleware
自定义开发服务器