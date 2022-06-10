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


###### 数组与链表
1. 数组
 - 优势
    - 随机访问性强，查找速度快，时间复杂度为O(1)
 - 劣势   
    - 插入效率低
    - 内存空间要求高，必须有足够的连续的内存空间，空间利用率不高 
    - 数组空间的大小固定，不能动态拓展，改变数组长度本质上是将数组往新的数组填充

2. 链表
 - 优势
    - 插入效率高
    - 分散存储,空间利用率高
    - 无大小限制, 可动态扩展
 - 劣势
    - 访问效率低

###### setTimeout / setInterval
 - setTimeout / setInterval 最小间隔为4ms,一般是由于函数嵌套导致
 - 通过 postMessage和onmessage事件模拟零延时的setTimeout


###### 0.1 + 0.2 !== 0.3 为什么?
进行计算时先转换成二进制,除了2的幂次方以外的浮点数,都会

###### 为什么使用 void 0？
 - ES5时,window.undefined是可写的, 而表达式void 0固定返回undefined


###### 判断空对象
 - Object.keys()
 - JSON.stringify() 当值为函数、undefined、Symbol时被忽略,BigInt报错


###### Proxy
Proxy没有prototype属性,没有原型也可以new