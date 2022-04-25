#### 1. v-if和v-show的区别

- v-show是通过设置元素的display:none实现的，并不会真的移除元素，如果组件是元素的话，也不会触发销毁等生命周期。
- v-if是在页面上直接移除元素，因此会触发生命周期。

当需要频繁切换元素的显示与隐藏时，建议使用v-show。

<!-- #### 2. v-for为什么要有key?为什么index不能作为key？

- 默认情况下，使用v-for渲染的列表采用的是就地更新原则，即数据项的顺序发生改变时(比如头部添加),vue不会去移动DOM元素来匹配每一项，而是就地更新每一个元素，使他们在每个索引位置匹配。
- Vue更新的最小单位是元素(标签)。
- 当使用index作为key的时候，数据发生改变，只会改变这个item中和数据相关的元素(标签)，其他没有改变的元素还会复用(可以使用input观察)。因此建议key使用唯一值。
- 避免同时使用v-for和v-if，v-for的优先级比v-if高，即便v-if设置成false了，v-for优先，还是遍历了数组。 -->
#### 2. diff算法和key

###### diff算法
首先要知道的是,通过虚拟DOM进行更新,那么消耗的时间就是 diff找到新旧虚拟DOM差异的时间 + 更新DOM的时间
 - diff算法采取深度优先遍历, 并且仅比较同层级元素,不会进行跨级比较
 - 

###### 使用index作为key有什么问题?

#### 3. computed和watch的区别
- computed:计算属性，依赖其他属性值，具有缓存的功能，只要当数据发生改变时才会重新计算。
- watch:监听,监听某个属性的改变，每一次数据改变都会触发监听。

在数据改变时需要执行异步或者开销较大的操作时，使用watch，而computed用于对其他属性就进行计算。

###### computed实现


#### 4. vue的响应式原理
以vue3的模版形式为例, vue3是通过Proxy对对象进行代理, 模版在编译阶段被转化成了render(渲染函数),render返回虚拟DOM,并且在编译阶段进行依赖收集(track), 因为使用了Proxy对象的属性时触发get代理,在get的代理中可以存储代理的对象、属性以及副作用函数列表(当依赖发生改变的时候应该怎么做,比如文本更新之类的), 当我们修改依赖时, 会触发proxy的set代理, 在set中会执行trigger函数,trriger函数主要是通过代理对象、属性找到依赖对应的副作用函数列表并执行它,这步主要是更新虚拟DOM,然后再通过diff算法找出差异,然后进行更新真实DOM。

#### 5. 为什么vue3使用Proxy而不是Object.definedProperty?
首先要搞清楚, Object.definedproperty做了什么? 这个方法是对对象的某个属性进行配置,如get、set、可枚举性、可配置性以及可读写性。
一、也就是说刚开始的时候配置了哪些属性,那么这些属性就具备相应时,后续添加的属性并没有进行配置,因此vue提供了vue.set进行配置新的属性。数组同理,修改已经存在的元素, 读取和修改已经存在的元素,会触发响应式,而通过方法添加和修改元素默认是不会触发响应式的,因此vue重写了部分方法,使其具备响应式,不过通过索引添加元素依旧不具备响应式。

Proxy可以解决新增属性没有响应式问题,但是通过delete删除属性无论哪种形式都是不会触发set的,因为删除是删除,但是Proxy无法通过转译成ES5,因此无法兼容IE以及部分低版本浏览器。

#### 6. vue3主要的改动
 - 响应式基于Proxy而不是Objec.defindeProperty
 - `<script setup></script>`
 - 组合API
 - teleport
 - `<fragment></fragment>`
 - style中的v-bind使用JS变量
 - v-for和v-if,在2.x中 v-for优先级高, 3.x中v-if优先级高
 - 全局和内部API支持tree-shaking,需要进行导入使用, 不使用则不会进行打包

#### 7. vue中传递函数的优化
```html
<div>
  <!-- inline-function形式,如果父组件重新渲染,会导致这个函数重新创建 -->
  <Son :fn="()=>{}" />
  <!-- fn2如果是methods中的方法,那么有缓存,父组件重新渲染不会导致方法重新创建 -->
  <Son2 :fn="fn2" />
</div>
```
在vue2的版本中,如果props没有发生改变,父组件的重新渲染不会使子组件也重新渲染,但是如果传递是一个inline-function,父组件的重新渲染都会导致inline-function重新创建,导致子组件的更新,和React中的原理一致,这也是React.memo和React.useCallback解决的问题,而methods中的方法都是有缓存,直接传递不会有这个问题。

而vue3对inline-function做了缓存优化, 父组件的重新渲染不会让这些inline-function重新创建了, 因此就不会使子组件重新渲染。

#### 8. scoped原理
为每个元素添data-v-hash的属性(attribute),在编译CSS时为所有选择器附加属性选择器\[data-v-hash],每个vue文件的附加的hash都不同,所以就不会互相影响了
```vue
<style scoped>
  h1{
    color: red;
  }
</style>
<!-- 编译之后 -->
<style>
  h1[data-v-hash]{
    color: red;
  }
</style>
```