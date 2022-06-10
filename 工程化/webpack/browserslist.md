# browserslist
在不同前端工具之间共享目标浏览器和 Node.js 版本的配置, 即工具需要兼容哪些浏览器和node版本

github: https://github.com/browserslist/browserslist
 - autoprefixer
 - babel
 - postcss-preset-env
 - postcss-normalize
 - eslint-plugin-compat

###### 默认配置
 - package.json写法
```json
{
  "browserslist": [
    "defaults" // 默认值为 > 0.5%, last 2 versions, Firefox ESR, not dead
  ],
  // 等价于
  "browserslist": [
    "> 0.5%", // 市场占有率,可以通过caniuse的usage-table查看
    "last 2 versions", // 浏览器的最新两个版本
    "Firefox ESR",  //最新的Firefox 扩展支持版本
    "not dead", // 24个月内有官方支持更新的浏览器(IE10已经dead了)
   ]
}
```

 - .browserslistrc写法
 ```
    > 0.5%
    last 2 versions 
    Firefox ESR
    not dead
 ```



###### 查看条件对应的版本
npx browserslist "defaults, node > 12, last 2 version2"


###### 条件关系
 - , 或者 or 为 取并集
 node > 12 or last 2 versions: node版本>12以及浏览器最新的两个版本的并集
 - and 取交集
 last 2 versions and > 0.5%: 浏览器的最新2个版本并且市场占有率>0.5%
 - not 取反 
 dead: 24个月内没有官方支持和更新,即为dead, 如IE10
 no dead: 24个月内有官支持和更新