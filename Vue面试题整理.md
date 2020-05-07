**v-show  和 v-if 的区别:**
 * v-show 体现的是 CSS 的 display 属性的显示与隐藏，页面中会一直存在这个元素；
 * v-if  则是组件真正的渲染和销毁，而不是显示与隐藏
 * 频繁切换显示状态用 v-show
 
 **v-for 中的 key :**
 * 必须使用 key 且不能是 index 和 random 随机数
 * diff 算法通过 tag 和 key 来判断是否是 sameNode ，是就不用diff了 
 * 减少渲染次数，提升渲染性能
 
**描述 Vue 组件生命周期（父子组件）:**
 * 单组件生命周期 
 `beforeCreate,created,beforeMount,beforeMounted,beforeUpdate,updated,beforeDestroy,destroyed`
 * 父子组件生命周期关系
 创建先父后子： `父 created -->  子 created --> 子 mounted --> 父 mounted`
 更新先父后子： `父 beforeUpdate --> 子 beforeUpdate --> 子 updated --> 父 updated`
 销毁先子后父： `父 beforeDestryy --> 子 beforeDestryy --> 子 destroyed --> 父 destroyed`
 
 **Vue 组件通讯（常见）**
 
* 父子组件 `props` 和 `this.$emit`
* 自定义事件` event.$on`,`event.$off`,`event.$emit`
* `uex `

**描述组件渲染和更新过程**

![看懂这个就够了](https://cn.vuejs.org/images/data.png)

解析模板为 `render`函数后，执行` render `函数后生成` vnode` ，这个过程会触发响应式监听` data `属性的` getter` ，`getter` 进行依赖收集，当依赖项的 `setter`触发时，会通知` watcher`，从而使它关联的组件重新渲染。

**双向数据绑定 v-model 的实现原理**

* `input`元素的`value = this.name`
* 绑定`input `事件 `this.name = $event.target.value`
* `data `更新触发 `re-render`

**computed 的特点**
* 它有缓存，data不变不会重新计算
* 提高性能

**为何组件data 必须是一个函数**
```
export default {
  namer: 'app',
  data(){
    return{
    ...
    }
  }
}
```
导出的组件看似是一个对象，但在`vue`中其实是一个`class`，而在其他地方使用这个组件就相当于对这个类进行实例化，`data`如果不是函数，每个实例化组件内的属性就是共享的了，牵一发而动全身。`data` 如果是函数，那他内部的属性被外部使用时就自然而然地形成了闭包，闭包中的属性在某一个组件修改属性时不会影响另一个组件对属性的引用，所以`data `必须是以函数返回一个对象。

**ajax请求应该放在那个生命周期**

* 推荐放在` mounted `中,此时DOM 加载完成。
* `JS `是单线程的，`ajax `是异步获取数据，页面没有渲染完是不会去发起请求的，所以即使放在`mounted`之前发请求也是处于排队阶段，没啥好效果，只会让逻辑更混乱

**如何讲组件所有 props 传递给子组件**
`$props <User v-bind="$props" />`

**如何自己实现 v-model**
```
<body>
<div id="app1">
   <input type="text"
          :value="message"
          @input="message=$event.target.value"
   >
   <p>{{message}}</p>
   <input type="text" v-model="message">
 </div>
  <script src="https://cdn.jsdelivr.net/npm/vue@2.5.16/dist/vue.js"></script>
 </body>
 <script>
 var app =new Vue({
  el: '#app1',
  data() {
    return {
      message: 'hi' 
    }
  }
})
</script>
```
![v-model.png](https://i.loli.net/2020/05/07/R4y2w1xokOmBsFa.png)
  
**多个组件有相同逻辑应如何处理**
* mixin
* 缺点：不利于阅读，多mixin 可能会造成命名冲突，可能会出现多对多关系，复杂化

**何时使用异步组件**
* 加载大组件时，如编辑器、图表
* 路由异步加载时

**何时使用keep-alive**
* 缓存组件，不需要重复渲染时
* 如多个静态 tab 页切换时
* 能优化性能

**何时使用 beforeDestory**
* 解绑自定义事件`event.$off`
* 清除定时器
* 解绑自定义的` DOM `事件， 如` window scroll`等

**什么是作用域插槽**

**Vuex 中action 和 mutation 有何区别**
* `action `中可以处理异步，`mutation` 不可以
* `mutation `做原子操作，一次只处理一个
* `action`可以整合都多个`mutation` 
* `mutation `可以直接修改`store`中的`state` ，而`action `必须显式地`commit mutation `才能间接操作`state`

**Vue-router 常用路由模式**
* 默认为 `hash` 模式
* H5 history （需要服务端支持）
* hash 通过 window.onhashchange 监听 hash 变化
* H5 history 通过 history.pushState 和 window.onpopstate 实现跳转和监听，需要server 支持

**hash 或者 history 两者如何让选择?**
* to B 的系统(后台管理系统)推荐使用 hash ，简单易用， 对 url 规范不敏感
* to C 的系统，可以考虑选择 H5 history ,但需要服务端支持，如果有 SEO 要求考虑使用
* 考虑成本和收益，尽量选择简单的

**监听` data `变化的核心 API 是`Object.defineProperty`，`observer(value)`深度监听，其优点是兼容性好，支持IE 9。**

**如何监听数组?**
* `Object.defineProperty`不能监听数组变化
* 必须重新定义原型，重写 `push 、pop`等方法来实现监听
* Proxy 可以原生支持监听数组变化
```
// 重新定义数组原型 ， 以免污染全局 Array 原型
const oldArrayProperty =  Array.prototype
// 创建新对象，原型指向 oldArrayProperty , 再扩展新的方法不会影响原型
const arrProto = Object.create(oldArrayProperty)
['push','pop','shift','unshift','splice'].forEach(methodName => {
   arrProto[methodName]  = function () {
    updateView()  // 触发视图更新
    oldArrayProperty[methodName].call(this, ...arguments)
   }
})
```
**Object.defineProperty 缺点：**

* 深度监听需要递归到底，一次性计算量大；

* 无法监听新增或删除属性；

* 无法监听数组，需要特殊处理

**Proxy 的优势:**
* `Proxy `可以直接监听对象而非属性；
* `Proxy `可以直接监听数组的变化；
* `Proxy `有多达 13 种拦截方法,不限于` apply、ownKeys、deleteProperty、has `等等是 `Object.defineProperty `不具备的；
* `Proxy `返回的是一个新对象,我们可以只操作新的对象达到目的,而 `Object.defineProperty`只能遍历对象属性直接修改；
* `Proxy `作为新标准将受到浏览器厂商重点持续的性能优化，也就是传说中的新标准的性能红利

**描述响应式原理**
* 监听data 变化
* 组件渲染和更新过程 [组件渲染和更新过程](https://github.com/WUXIAOd/Vue-learning/blob/master/%E7%BB%84%E4%BB%B6%E6%B8%B2%E6%9F%93%E6%9B%B4%E6%96%B0%E8%BF%87%E7%A8%8B.md)

**diff 算法时间复杂度**
* O(n)
* 基于O(n^3)优化而来 [diff 算法优化](https://github.com/WUXIAOd/Vue-learning/blob/master/%E8%99%9A%E6%8B%9FDOM%E5%92%8Cdiff.md)

**简述 diff 算法过程**
* `patch(elem,vnode)和patch(vnode, newVnode)`
* `patchVnode 和addVnodes和removeVnodes`
* `updateChildren(key 的重要性)`

**Vue 为何是异步渲染，$nextTick **

* 异步渲染（以及合并data 修改）可以提高渲染性能
* $nextTick在DOM 更新完之后触发回调

**Vue 常见性能优化**

vue 层面的优化：
* 合理使用 V-show v-if
* 合理使用computed
* v-for 加 key ,以及避免和v-if 同时使用
* 自定义事件、DOM事件及时销毁
* 合理使用异步组件
* 合理使用缓存组件keep-alive
* data 层级不要太深
* 使用 vue-loader 在开发环境做模板编译（预编译）
* 服务端渲染SSR

webpack 层面的优化：
* Webpack 对图片进行压缩减少
* ES6 转为 ES5 的冗余代码
* 提取公共代码
* 模板预编译
* 提取组件的 CSS
* 优化 SourceMap
* 构建结果输出分析

前端通用的性能优化： [前端通用性能优化](http://pigxw.top/web%E6%80%A7%E8%83%BD%E4%BC%98%E5%8C%96/)


