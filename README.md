# page-diary
日记笔记

1. vuex mapActions 工具方法

背景： 这是啥？为啥可以这样用？看起来好吊？研究一下？

源码: 

```
// 对外抛出的方法
export const mapActions = normalizeNamespace((namespace, actions) => {
  const res = {}
  // 解析后的actions,并返回函数
  normalizeMap(actions).forEach(({ key, val }) => {
    res[key] = function mappedAction (...args) {
      // 从 store 中 获取 dispatch 方法
      let dispatch = this.$store.dispatch
      // 是否有命名空间
      if (namespace) {
        const module = getModuleByNamespace(this.$store, 'mapActions', namespace)
        if (!module) {
          return
        }
        dispatch = module.context.dispatch
      }
      
      // 根据传入的参数类型进行不同的处理
      return typeof val === 'function'
        ? val.apply(this, [dispatch].concat(args))
        : dispatch.apply(this.$store, [val].concat(args))
    }
  })
  return res
})


// 针对性的对传入参数进行预处理, 一种写法。
function normalizeNamespace (fn) {
  return (namespace, map) => {
    // 处理 命名空间 及 映射
    if (typeof namespace !== 'string') {
      map = namespace
      namespace = ''
    } else if (namespace.charAt(namespace.length - 1) !== '/') {
      namespace += '/'
    }
    return fn(namespace, map)
  }
}


// 规范化map格式
function normalizeMap (map) {
  // 对数组/对象做相应的处理方式
  return Array.isArray(map)
    ? map.map(key => ({ key, val: key }))
    : Object.keys(map).map(key => ({ key, val: map[key] }))
}


// 通过命名空间获取制定的module
function getModuleByNamespace (store, helper, namespace) {
  const module = store._modulesNamespaceMap[namespace]
  if (process.env.NODE_ENV !== 'production' && !module) {
    console.error(`[vuex] module namespace not found in ${helper}(): ${namespace}`)
  }
  return module
}
```
简单的阅读: vuex的辅助函数. normalizeNamespace对传参进行了处理(平时在写前置函数时,是否可以采用此种写法)

normalizeMap: 数据格式化（将数据处理为目标格式,在日常工作时,是否需要这种工具函数）


2. git merge 命令的范围

```
git-merge命令是用于从指定的commit(s)合并到当前分支的操作。

注：这里的指定commit(s)是指从这些历史commit节点开始，一直到当前分开的时候。
```

