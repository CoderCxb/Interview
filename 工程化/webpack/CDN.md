# CDN
参考 常见网络问题 - CDN

### 常见方式 + 配置

###### 将所有打包的资源都存放到CDN服务器, 用户的所有资源都通过CDN服务器加载
publicPath需要进行修改, 由于资源上传到CDN服务器,因此访问资源的路径也是CDN服务器的路径了, 一般修改publicPath
```js
module.exports = {
  output: {
    publicPath: "http://cxb.com/cdn", // 设置为CDN服务器地址
  }
}
```
###### 将不需要参与构建的资源存放到CDN服务器,如第三方库
生产环境: 第三方库的配置 externals + script标签

开发环境直接使用依赖即可
```js
module.exports = {
  externals: {
    lodash: "_"
  }
}

// index.js
import { get } from 'lodash';
// 可以看出从全局对象 _ 解构出get

// html 
// 生产环境时才添加这个CDN资源
<%if (process.env.NODE_ENV === 'production') { %>
<script></script>
<% } %>
```