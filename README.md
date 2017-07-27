
# Vue源码解读

分析的版本是2.1.7。文档持续更新

## 主线

目前关注的是数据绑定以及Vdom这两条方向。

```
new Vue()=>this._init()=>initState=>observe(data)=>initRender=>new Watcher()以及Vdom的实现
```

## 生命周期

`this._init()`执行的过程中回调了以下函数
```
initLifecycle(vm)
initEvents(vm)
callHook(vm, 'beforeCreate')
initState(vm)
callHook(vm, 'created')
initRender(vm)
```
在最后`initRender(vm)`的过程中又回调`beforeMount`和`mounted`这两个钩子函数

在`initState(vm)`的时候才执行数据监听，在`initRender(vm)`挂载DOM。

```
beforeCreate $data:undefined $el:undefined
created      $data:初始化     $el:undefined
beforeMount  $data:初始化     $el:已挂载，但此时挂载的节点是虚拟DOM
mounted      $data:初始化     $el:真实DOM
```

## 分析模块

### 双向数据绑定

待补充

维护一个Dep对象保存数据与模板之间的依赖关系。Observe函数监听数据，在之后的Render函数中会实例化Watcher，此时会把模板中的依赖添加到对应数据的Dep中。当数据或者模板发生变化时会回调Dep中函数实现更新。

### Vdom的实现

研究中

## 其它记录

### 原型上的属性以及方法
```
function Vue (options) {
  if (process.env.NODE_ENV !== 'production' &&
    !(this instanceof Vue)) {
    warn('Vue is a constructor and should be called with the `new` keyword')
  }
  this._init(options)
}

initMixin(Vue)
stateMixin(Vue)
eventsMixin(Vue)
lifecycleMixin(Vue)
renderMixin(Vue)
```

```
// initMixin(Vue)	src/core/instance/init.js **************************************************
Vue.prototype._init = function (options?: Object) {}

// stateMixin(Vue)	src/core/instance/state.js **************************************************
Vue.prototype.$data
Vue.prototype.$set = set
Vue.prototype.$delete = del
Vue.prototype.$watch = function(){}

// renderMixin(Vue)	src/core/instance/render.js **************************************************
Vue.prototype.$nextTick = function (fn: Function) {}
Vue.prototype._render = function (): VNode {}
Vue.prototype._s = _toString
Vue.prototype._v = createTextVNode
Vue.prototype._n = toNumber
Vue.prototype._e = createEmptyVNode
Vue.prototype._q = looseEqual
Vue.prototype._i = looseIndexOf
Vue.prototype._m = function(){}
Vue.prototype._o = function(){}
Vue.prototype._f = function resolveFilter (id) {}
Vue.prototype._l = function(){}
Vue.prototype._t = function(){}
Vue.prototype._b = function(){}
Vue.prototype._k = function(){}

// eventsMixin(Vue)	src/core/instance/events.js **************************************************
Vue.prototype.$on = function (event: string, fn: Function): Component {}
Vue.prototype.$once = function (event: string, fn: Function): Component {}
Vue.prototype.$off = function (event?: string, fn?: Function): Component {}
Vue.prototype.$emit = function (event: string): Component {}

// lifecycleMixin(Vue)	src/core/instance/lifecycle.js **************************************************
Vue.prototype._mount = function(){}
Vue.prototype._update = function (vnode: VNode, hydrating?: boolean) {}
Vue.prototype._updateFromParent = function(){}
Vue.prototype.$forceUpdate = function () {}
Vue.prototype.$destroy = function () {}
```

### 实例上的属性以及方法

执行`this._init()`的过程中会执行


```
initLifecycle(vm)
initEvents(vm)
callHook(vm, 'beforeCreate')
initState(vm)
callHook(vm, 'created')
initRender(vm)
```
vm即是this,指向当前实例。添加了以下实例属性以及方法
```
// 在 Vue.prototype._init 中添加的属性 		**********************************************************
this._uid = uid++
this._isVue = true
this.$options = {
    components,
    directives,
    filters,
    _base,
    el,
    data: mergedInstanceDataFn()
}
this._renderProxy = this
this._self = this

// 在 initLifecycle 中添加的属性		**********************************************************
this.$parent = parent
this.$root = parent ? parent.$root : this
 
this.$children = []
this.$refs = {}

this._watcher = null
this._inactive = false
this._isMounted = false
this._isDestroyed = false
this._isBeingDestroyed = false

// 在 initEvents	 中添加的属性	 	**********************************************************
this._events = {}
this._updateListeners = function(){}

// 在 initState 中添加的属性		**********************************************************
this._watchers = []
    // initData
    this._data

// 在 initRender	 中添加的属性 	**********************************************************
this.$vnode = null // the placeholder node in parent tree
this._vnode = null // the root of the child tree
this._staticTrees = null
this.$slots
this.$scopedSlots
this._c
this.$createElement
```
