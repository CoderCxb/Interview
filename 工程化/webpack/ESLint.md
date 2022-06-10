# ESLint
ESLint是一个JS检测工具,官网 https://eslint.bootcss.com/

### 起步
 - 安装: npm i eslint -D
 - 初始化(根据命令行生成): npx eslint --init
 - 检测: npx eslint ./src/index.js

### 配置文件
```javascript
module.exports = {
  // 支持的环境
  env: {},
  extends: [
    "plugin:vue/essential",
    "airbnb-base"
  ],
  // 编译选项
  parserOptions: {
    ecmaVersion : 12, // 指定ECMA版本
    parser: '@typescript-eslint/parser',
  },
  // 使用插件
  plugins: [],
  // 编写规则, 优先级大于extends继承来的
  rules: {
    "no-unused-vars": "off", // 0 - off关闭、1 - warn警告、2 - error报错
  }
}
```