###### webpack
- webpack-cli并不是必须品, 主要是对配置文件和命令行参数的处理
- webpack在不使用babel的情况下,只会对import和export做转译,其他语法都不会进行转译



###### resolve
```javascript
resolve: {
  extensions: ['js', 'jsx', 'ts', 'tsx'], // import时不写后缀名则从以下后缀名中取
  // 配置路径别名
  alias: {

  }
}
```



###### CLI 命令行
 - webpack --config configFileName 可以指定webpack的配置文件

###### webpack.config.js
 - 路径需要使用绝对路径, 通过 const path = require('path'); path.resolve(__dirname)获取文件夹绝对路径


###### 多页面配置