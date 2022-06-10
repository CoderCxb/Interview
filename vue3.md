#### 1. vue3中存在openBlock方法用于优化
静态节点会被提升,之后不会参与diff算法的比较,但是如果说根元素包含了动态节点,会导致所有children都无法提升,因此引入了block的概念,在block上记录动态节点, 非动态子节点照常提升。
v-if也会创建block,因此v-if可能既包含动态节点,也包含静态节点,所以需要block
```html
<div>
  <div>标题</div>
  <div>{{msg}}</div>
</div>
```

#### 2. diff算法

###### unkeyed - 即key不存在时
 - key不存在时, 获取新老节点的长度短的作为length进行遍历
 - 通过patch方法更新虚拟DOM
 - 如果新节点长度 > 老节点长度, 挂载
 - 否则, 卸载多余老节点

###### 2. 快速diff算法
 - 遍历新节点列表,使用对象存储key和index,即索引表
 - 遍历老节点列表, 在索引表中如果能找到对应的,则说明这个节点依旧存在,进行patch更新(此处仅做更新,不考虑移动), 通过数组source存储新节点原来的索引,默认填充-1,如果找不到则进行卸载
 - 此时source中存储了新节点原来的索引, 获取其最长递增子序列(最长递增子序列是不需要移动的最大节点组合)
 - 倒序遍历source数组,索引为index
  - source\[index]为-1, 则创建并挂载
  - source\[index]不为-1并且不在最大递增子序列中, 则移动到上一个index对应的元素前(所以要倒序遍历)
  - 最大子序列中对应的索引不需要移动


#### 3. Vue3的优化
 - 响应式从Object.definedProperty到 Proxy
 - 编译阶段对不同类型的节点打算patchFlag的标记, 有些静态节点后续就不会进行更新
 - 提供了Composition-API,代码可以更好的解耦
 - diff算法从双端diff算法升级为快速diff算法
 - 更好的支持TS


#### 4. nexttick
 - 依赖发生改变并不会立刻触发DOM更新,而是会去修改虚拟DOM, 等全部修改完毕进行判断组件是否需要更新
 - 如果组件需要更新, 将更新组件的操作


#### 5. Vue-Router实现原理

###### hash模式
通过 location.hash对hash进行修改, 会触发onhashchange事件


###### history模式
通过 HTML5 提供的history相关API改变url, 注意pushState和replaceState是不会触发window.popstate事件,需要通过 history.go()、history.back()、history.forward()触发
