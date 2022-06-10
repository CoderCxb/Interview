# Terser
用于压缩JS代码的工具

###### 做了什么
 - 删除不会被执行的代码,比如if(false){}


###### webpack配置
首先, webpack在生产模式默认开启了terser,不过并没有对css进行优化

```js
const CssMinimizerPlugin = require("css-minimizer-webpack-plugin");
const TerserPlugin = require("terser-webpack-plugin");
module.exports = {
  // minimize必须设置为true才会开启优化
  minimize: true,
  // 自定义minimizer时,会覆盖默认的minimizer
  minimizer: [
    new CssMinimizerPlugin(),
    new TerserPlugin()
  ],
}
```
