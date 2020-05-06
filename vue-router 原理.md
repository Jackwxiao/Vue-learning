### vue-router 的两种路由模式

#### 一. hash 模式
1.网页 URL 组成部分：

`http://127.0.0.1:8081/wtf.html?a=100&b=200#/aaa/sss`

location.protocol          // 'http:'

location.hostname         // '127.0.0.1'

location.host            // '127.0.0.1:8081'

location.port           // '8081'

location.pathname      // 'wtf.html'

location.search       // '?a=100&b=200'

location.hash        // '#/aaa/sss'

2.hash 的特点：
* hash 变化会触发网页跳转，即浏览器的前进、后退
* hash 变化不会刷新页面 ，这是 SPA 应用所必需的
* hash 永远不会提交到 server 端

测试代码：
```
<body>
<p>hash test</p>
<button id="btn1">点击修改 hash</button>

<script>
   window.onhashChange = (event) => {
      console.log('old hash', event.oldURL)
      console.log('new hash', event.newdURL)
      console.log('hash', location.hash)
   }
   // 页面初次加载，获取 hash 
   document.addEventListener('DOMContentLoaded',() => {
      console.log('hash:', location.hash)
   })
   // JS 修改 hash ,还可以浏览器前进后退、手动修改hash
   document.getElementById('btn1').addEventListener('click',
      location.href = '#/user'
   )
</script>
</body>
```
#### 二. H5 history 模式
* URL 规范的路由，跳转时不刷新页面
* 主要通过history.pushState 和 window.onpopstate API 来实现

1.正常的网页浏览如 : 

从 `https://github.com/xxx `

到 `https://github.com/xxx/yyy`  会刷新页面

再到 `https://github.com/xxx/yyy/zzz` 会刷新页面

2.改造成 H5 history 模式

效果如下： 

从 `https://github.com/xxx `

到 `https://github.com/xxx/yyy`  前端跳转，不刷新页面

再到 `https://github.com/xxx/yyy/zzz` 前端跳转，不刷新页面

怎么实现？
```
<body> 
<p>H5 history </p>
<button id= "btn2">点击切换路径</button>

<script>
   // 页面初次加载获取 path 
   document.addEventListener('DOMContentLoaded', () => {
      console.log(location.pathname)
   })
   
   // 打开一个新的路由
   document.getElementById('btn2').addEventListener('click', ()=> {
      const state = {name : 'page1'}
      console.log('切换路由到：', 'page1')
      history.pushState(state, '', 'page1')
      // pushState 方法不刷新浏览器
   })
   
   // 监听浏览器的前进后退
   window.onpopstate = (event) => {
      console.log('onpopstate', event.state, location.pathname)
   }
   
   // 需要 server 端配合，后端配置可参考
   // https://router.vuejs.org/zh/guide/essentials/history-mode.html#%E5%90%8E%E7%AB%AF%E9%85%8D%E7%BD%AE%E4%BE%8B%E5%AD%90
</script>
</body>
```

### 总结

* hash 通过 window.onhashchange 监听 hash 变化
* H5 history 通过 history.pushState 和 window.onpopstate 实现跳转和监听，需要server 支持

两者如何让选择：

* to B 的系统(后台管理系统)推荐使用 hash ，简单易用， 对 url 规范不敏感
* to C 的系统，可以考虑选择 H5 history ,但需要服务端支持，如果有 SEO 要求考虑使用
* 考虑成本和收益，尽量选择简单的








