# resolve
resolve用于设置模块如何被解析
 - resolve帮助webpack从每个require/import语句中,找到需要引入的合适的模块
 - webpack使用 enhanced-resolve解析文件路径

### 解析方式
 - 绝对路径
 - 相对路径
 - 模块路径
    - 在resolve.modules中指定的所有目录检索模块
      - 默认值: \['node_modules']
    -  可以通过设置alias来替换模块路径

```js
// webpack.config.js
module.exports = {
  resolve: {
    alias: {
      'Profile': path.resolve(__dirname, 'src/profile/')
    },
    extensions: ['js'], // 当后缀名不存在时默认添加什么后缀名
  }
}

// src/index.js
import Profile from 'Profile/index';

// src/profile/index.js
const Profile = {};
export default Profile;
```


```js
//  path.js 可用于简化路径,代替 path.resolve(__dirname, 'xxx')
const path = require('path');

const appDir = process.cwd();
const resolveApp = (relativePath) => path.resolve(appDir, relativePath);

module.exports = resolveApp;

// webpack.config.js中使用
const resolveApp = require('path.js');
module.exports = {
  output: {
    path: resolveApp("./dist"),
  }
}
```