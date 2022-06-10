# CLI
无论是webpack-cli、vue-cli还是create-react-app, 本质上都是通过导入webpack以及配置文件, 然后进行编译

```js
const webpack = require('webpack');

// CommonJS
// // 如果导出的是函数, 还需要进行调用
// // const config = require('./webpack.config.js')({
// //   pruduction: true
// // });

// ESM导出, 导出的内容在default属性上
const configFn = require('./webpack.config.js').default;

// 导出函数还需要调用一次
const config = configFn();

// // 获取webpack编译器
const compiler = webpack(config);

compiler.run((err, stats) => {
  if(err){
    console.error(err);
  } else {
    console.log(stats);
  }
});
```