# PostCSS
PostCSS是一个允许使用JS插件转换css样式的工具

###### install
```sh
npm i postcss -D


# 安装自动补齐前缀的插件
npm i autofixer -D 
```

###### 命令行使用
```sh
# 安装postcss的命令行工具, 安装后才能在命令行使用
npm i postcss-cli -D 

npx post --use autoprefixer -o result.css ./src/css/index.css
```

###### 插件
 - autofixer 自动添加兼容性的浏览器前缀
 - postcss-preset-env(预设插件) 将现代css转换成大部门浏览器可以识别的css,包含autofixer


###### webpack.config.js
```js
{
  test: /\.css$/i,
  use: [
    MiniCssExtractPlugin.loader,
    {
      loader: "css-loader",
      options: {
        // 如果css中通过@import导入,则导入的css没有被postcss处理,因此需要这个配置
        importLoaders: 1, // css-loader处理过的还需要前面1个loader处理
      }
    },
    {
      loader: "postcss-loader",
      options: {
        postcssOptions: {
          plugins: [
            'autoprefixer', // 简写
            require('postcss-preset-env')
          ]
        }
      }
    }
  ],
},
```

###### postcss.config.js
当多个文件解析使用到postcss-loader时, 为了避免重复定义,可以将postcss的配置单独拎出来作为配置文件
```js
module.exports = {
  plugins: [
    // 插件写法一
    // require('autoprefixer'),
    // 插件写法二
    "autoprefixer": {
      // 和项目配置的browserslist会冲突
      browsers: ["last 5 version", "Android >= 4.0"], 
      //是否美化属性值 默认：true
      cascade: true,
      //是否去掉不必要的前缀 默认：true
      remove: true
    }
  ]
}
```