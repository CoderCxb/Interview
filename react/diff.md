# diff
 - 同级比较
 - 节点类型不同，直接创建新的节点,即使子节点相同也不会复用


### 单节点diff
 - key相同, type相同直接复用
 - key相同, type不同,删除旧fiber及其所有兄弟节点
 - key不同, 删除fiber

解析: 由于更新的fiber是单节点,所以如果key相同,type不同, 唯一一个key相同的type还不同,那其他兄弟节点就更不可能能复用,所以全部删除

### 多节点diff
 - 遍历newChildren, index
  - 能复用, index++, 继续遍历
  - 不能复用
    - key不同导致不能复用, 