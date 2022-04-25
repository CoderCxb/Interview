###### 1. vue3中存在openBlock方法用于优化
静态节点会被提升,之后不会参与diff算法的比较,但是如果说根元素包含了动态节点,会导致所有children都无法提升,因此引入了block的概念,在block上记录动态节点, 非动态子节点照常提升。
v-if也会创建block,因此v-if可能既包含动态节点,也包含静态节点,所以需要block
```html
<div>
  <div>标题</div>
  <div>{{msg}}</div>
</div>
```