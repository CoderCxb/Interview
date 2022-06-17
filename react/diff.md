# diff
 - 同级比较
 - 节点类型不同，直接创建新的节点,即使子节点相同也不会复用


### 单节点diff
 - key相同, type相同直接复用
 - key相同, type不同,删除旧fiber及其所有兄弟节点
 - key不同, 删除fiber

解析: 由于更新的fiber是单节点,所以如果key相同,type不同, 唯一一个key相同的type还不同,那其他兄弟节点就更不可能能复用,所以全部删除

### 多节点diff
 - 第一轮遍历
  - 遍历newChildren, index
    - 能复用, index++, 继续遍历
    - 不能复用
      - key不同导致不能复用, 跳出循环(key如果不同就说明顺序发生了改变)
      - key相同,type不同,导致不可复用, oldFiber标记为DELETION,继续遍历
    - 直到newChildren遍历结束或oldFiber遍历结束
 - 第二轮遍历
  - newChildren和oldFiber同时遍历完, 只需要进行更新
  - newChildren没遍历完, oldFiber遍历完, 需要将新节点进行插入
  - newChildren遍历完, oldFiber没有遍历完, 标记oldFiber剩余节点为DELETION
  - 都没有遍历完
   - 记录newChildren在旧节点中的最大索引(lastPlacedIndex), 如果当前索引值>lastPlacedIndex,则说明位置不需要移动,否则需要进行移动