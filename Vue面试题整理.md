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
 
 
