###### commonjs和ESM的区别
 - commonjs是运行时, 而ESM是静态编译时
 - commonjs是同步的, ESM是异步的
 - commonjs在导出时进行了一次浅拷贝(基本数据类型浅拷贝后,外部使用的是拷贝后的值,原文件中的值不受影响), 而ESM则是直接导出
 - commonjs可以通过某些库转换成ESM,从而进行Tree-Shaking


###### Proxy应用场景
 - 数据不可变


###### ES6新增特性
 - let、const
 - Proxy、Reflect
 - Set、WeakSet、Map、WeakMap
 - Symbol
 - Promise
 - 箭头函数
 - 扩展运算符、剩余参数、解构赋值
 - ES Moudle
 - Array: of、from、fill、find
 - Object: is、assign、getPrototypeof、setPrototypeof、\__proto__
 - String: 模版字符串
 - class 
