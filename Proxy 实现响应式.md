`Object.defineProperty`的缺点：
* 深度监听需要一次性递归，如果data 层级太深的话可能会有一段时间的卡顿
* 无法监听新增属性/删除属性
* 无法原生监听数组

`proxy `深度监听在 `get `时再递归，需要获取到某一层时才触发这一层的响应式

而`Object.defineProperty() `在 `get `之前就一次性递归完,进来就深度递归

```
function reactive(target={}){
   if(typeof target !== 'object' || target === null ){
     // target 非对象或数组直接返回
     return target
   }
   // 代理配置
   const proxyConf = {
      get(target, key, receiver) {
         const ownkeys = Reflect.ownKeys(target)
         // 只处理本身的属性
         if(ownkeys.includes(key)){
            console.log('get:', key)
         }
         const result = Reflect.get(target, key, receiver)
         // 深度监听
         return reactive(result)
      },
      set(target, key, val, receiver) {
         // 重复设置不处理
         if(val === target[key]) {
           return true
         }
         
         const ownKeys = Reflect.ownKeys(target)
         if(ownKeys.includes[key]) {
           console.log('已有的 key', key)
         }else {
            console.log('新增的 key', key)
         }
         
         const result = Reflect.set(target, key, val, receiver)
         console.log('set', key, val)
         return result  // 是否设置成功
      }，
      deleteProperty(target, key) {
         const result = Reflect.deleteProperty(target, key)
         console.log('delete property:', key)
         return result
      }
   }
   
   // 生成代理对象
   const observed = new Proxy(target, proxyConf)
   return observed
}

// 测试实例数据
const data = {
   name: 'zhangsan',
   age: 20,
   info: {
      city: 'beijing',
      a{
        b{
          c{
           d: 100
          }
        }
      }
   }
}

const proxyData = reactive(data)

```
![proxy.png](https://i.loli.net/2020/05/08/fA7driWZNhbn54K.png)
![proxy.png](https://i.loli.net/2020/05/08/VpG9j5AIuJWwCfP.png)

总结： 

proxy 实现响应式能深度监听，且性能更好，可监听新增/删除粗属性，可监听数组变化

proxy 能规避 Object.defineProperty的问题，但无法兼容所有浏览器，无法 polyfill
