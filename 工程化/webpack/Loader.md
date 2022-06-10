# Loader
Loader是将webpack无法识别到模块转换成JS所能识别的模块


### 自定义Loader
**loader.js**
```js
import { validate } from 'webpack/node_modules/schema-utils';
import schema from 'loader-schema.json';
// 内部有个loaderIndex
// PitchLoader对loaderIndex进行++
// NornalLoader对loaderIndex进行--
// PitchLoader 这个是正序,先执行
module.exports.pitch = function (){
  
}

// NormalLoader 这个是逆序,后执行
module.exports = function(content, sourcemap, meta){
  // 获取loader的options参数
  const options = this.getOptions();

  // 通过schema验证options
  validate(schema, options)

  // 同步执行并返回结果的话是同步loader  
  // return content;

  // 异步loader
  const callback = this.async();
  setTimeout(()=>{
    callback(null, content);
  }, 1000)
}
```
**webpack:.config.js**
```js
module.exports = {
  // 配置loader相关
  resolveLoader: {
    modules: [ "node_modules", "./src/loaders" ],
    extensions: [ "js", "json"],
    mainFields: [ "loader", "main" ]
  } 
}
```

**loader-schema:**
```json
{
  "type": "object",
  "properties": {
    "name": {
      "type": "string",
      "description": "请输入名称"
    }
  },
  "additionalProperties": true, // 可以额外附加参数
}
```

###### my-md-loader
```js
const marked = require('marked');
const hljs = require('highlight.js');
module.exports = function(content) {
  marked.setOptions({
    highlight: function(code, lang){
      return hljs.highlight(lang, code);
    }
  })
  const htmlContent = marked(content);
  const innerContent = "`" + htmlContent + "`";
  const moduleCode = `var code=${innerContent}; export default code;`
  return moduleCode; 
}
// 使用的地方导入默认样式
import "highlight.js/styles/default.css";
```

###### my-css-loader
将css插入到head标签末尾
```js
module.exports = function (content) {
  const code = "const styleTag = document.createElement('style'); styleTag.innerHTML = `" 
  + content
  + "`\n document.head.appendChild(styleTag);";
  return code
}
```