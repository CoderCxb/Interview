
### AST的生成和应用
AST(Abstract Syntax Tree - 抽象语法树),是对源代码结构的抽象表示。

###### 应用
 - 如何将 Typescript 转化为 Javascript (typescript)
 - 如何将 SASS/LESS 转化为 CSS (sass/less)
 - 如何将 ES6+ 转化为 ES5 (babel)
 - 如何将 Javascript 代码进行格式化 (eslint/prettier)
 - 如何识别 React 项目中的 JSX (babel)
 - GraphQL、MDX、Vue SFC 等等

**转换过程:**
 - Code -> AST(Parse)
 - AST  -> New AST(Transform)
 - AST  -> Code(Generate)

###### 生成AST
 - 词法分析(Lexical Analysis), 将Code转换成 Tokens
   - 代码检查, eslint判断是否以分号结尾等
   - 语法高亮, highlight/prism 使之代码高亮
   - 模板语法，如 ejs 等模板也离不开
```js
// Code
a = 3

// Token
[
  { type: { ... }, value: "a", start: 0, end: 1, loc: { ... } },
  { type: { ... }, value: "=", start: 2, end: 3, loc: { ... } },
  { type: { ... }, value: "3", start: 4, end: 5, loc: { ... } },
  ...
]
```
 - 语法分析(Syntactic Analysis),将tokens转化成AST
```json
{
  "type": "Program",
  "start": 0,
  "end": 5,
  "body": [
    {
      "type": "ExpressionStatement",
      "start": 0,
      "end": 5,
      "expression": {
        "type": "AssignmentExpression",
        "start": 0,
        "end": 5,
        "operator": "=",
        "left": {
          "type": "Identifier",
          "start": 0,
          "end": 1,
          "name": "a"
        },
        "right": {
          "type": "Literal",
          "start": 4,
          "end": 5,
          "value": 3,
          "raw": "3"
        }
      }
    }
  ],
  "sourceType": "module"
}
```

###### 简易编译器
https://github.com/jamiebuilds/the-super-tiny-compiler
