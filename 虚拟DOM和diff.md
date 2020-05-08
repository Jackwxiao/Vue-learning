
**vdom 是实现 Vue 和 React 的重要基石**

**diff 算法是 vdom 中最核心，最关键的部分**

* DOM 操作是非常耗费性能的

* jQuery 可以自行控制 DOM 操作的时机，手动调整

* Vue 和 React 数据驱动视图，如何有效控制 DOM 操作？

解决方案 - vdom

* 有了一定的复杂度，想减少计算次数比较难

* 能不能把计算转换为JS 计算？ 因为JS 执行速度很快

* vdom - 用 JS 模拟 DOM 结构，计算初最小的变更，操作 DOM

```
<div id="div1" class="container">
   <p>vdom</p>
   <ul style="font-size: 20px">
     <li>a</li>
   </ul>
 </div>
```
JS 模拟以上DOM 结构
```
{
  tag: 'div',
  props: {
    className: 'container',
    id: 'div1'
  }
  children: [
    {
      tag: 'p',
      children: 'vdom'
    },
    {
      tag: 'ul',
      props: { style: 'font-size: 20px' },
      children: [
        {
          tag: 'li',
          children: 'a'
        }
      ]
    }
  ]
}
```

[snabbdom](https://github.com/snabbdom/snabbdom/blob/master/src/snabbdom.ts) 一个虚拟`DOM` 库，` Vue`的虚拟` DOM `设计参照过这个库，比较简单适合学习 VDOM 

主要可以看一下`h 函数`，`patch 函数`， `patchVNode 函数`以及 `updateChildren 函数`

### diff 算法概述
diff - 对比

树 `diff `的事件复杂度为 O(n^3)
遍历 `tree1` ， 遍历 `tree2` , 排序，1000 个节点要计算一亿次，算法不可取

优化： 时间复杂优化到O(n)
* 只比较同一层级，不跨级比较

* `tag` 不相同，则直接删掉，不再深度比较

* `tag` 和 `key` 两者都相同，则认为是相同节点（sameVnode），不再深度比较

`v-for `中有` key `的话会使 `updateChildren`算法更高效

`updateChildren`: 

```
// 开始和开始对比
else if(sameVnode(oldStartVnode, newStartVnode)) {
   patchVnode(oldStartVnode, newStartVnode, insertVnodeQueue)
   // ...
}
// 结束和结束对比
else if(sameVnode(oldEndVnode, newEndVnode)) {
   patchVnode(oldEndVnode, newEndVnode, insertVnodeQueue)
   // ...
}
 // 开始和结束对比
else if(sameVnode(oldStartVnode, newEndVnode)) {
   patchVnode(oldStartVnode, newEndVnode, insertVnodeQueue)
   // ...
}
// 结束和开始对比
else if(sameVnode(oldEndVnode, newStartVnode)) {
   patchVnode(oldEndVnode, newStartVnode, insertVnodeQueue)
   // ...
}
// 四个都没命中
else{
    // 看新节点 key 能否对应上 oldCh 中的某个节点的 key
    idxInOld = oldKeyToIdx[newStartVnode.key as string]
    // 没对应上，直接插入
    // ... insertBefore
    // 对应上了
    // 对应上 key 的节点
    // sel 是否相等 （sameVnode 的条件）
    // 不等就插入
    // else sel 相等， key 相等，就 patchVnode()
}
```

### 总结

* diff 过程 ：`path --> patchVnode --> addVnode --> removeVnode --> updateChildren` 

* vdom 核心概念很重要： h 函数的参数 、vnode 结构 、 patch 、diff 过程 、 key 的重要性等

* vdom 存在的价值： 数据驱动视图，控制 DOM 操作，高效


