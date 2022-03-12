[TOC]

# 基本使用

# 原理

## 组件化

## 响应式

## vdom 和 diff

## 模板编译

## 渲染过程

## 前端路由

# Vue3

# 相关问题

### v-show 和 v-if 的区别

- v-show 通过 CSS display 控制显示和隐藏
- v-if 组件是真正的渲染和销毁
- 频繁切换显示状态使用 v-show，否则用 v-if

### 为何在 v-for 中用 key

- 必须用 key，且不能是 index 和 random
- diff 算法中通过 tag 和 key 来判断是否是 sameNode
- 减少渲染次数，提升渲染性能

### 描述 Vue 组件生命周期

- 单组件生命周期图
- 父子组件生命周期关系

### Vue 组件如何通讯

- 父子组件 props 和 this.$emit
- 自定义事件 event.$on, event.$off,event.$emit
- vuex

### 描述组件渲染和更新的过程

### 双向数据绑定 v-model 的实现原理

- input 元素的 value = this.name
- 绑定 input 事件 this.name = $event.target.value
- data 更新触发 re-render

### 对 MVMM 的理解

### computed 有何特点

- 缓存，data 不变不会重新计算
- 提高性能

### 为何组件 data 必须是一个函数

- 一个 vue 组件就是一个 vue 实例
- 使用对象的话，每个实例（组件）上使用的 data 数据是相互影响的
- vue 组件为了保证每个实例上的 data 数据的独立性，规定了必须使用函数，而不是对象。

### Ajax 请求应该放在哪个生命周期

- mounted
- JS 是单线程的，Ajax 异步获取数据
- 放在 mounted 之前没有用，只会让逻辑更加混乱

### 如何将组件所有 props 传递给子组件

- `$props`
- `<User v-bind="$props">`

### 如何自己实现 v-model

### 多个组件有相同的逻辑，如何抽离？

- mixin
- mixin 的缺点

### 何时要使用异步组件

- 加载大组件
- 路由异步加载

### 何时使用 keep-alive

- 缓存组件，不需要重复渲染
- 如多个静态 tab 页的切换
- 优化性能

### 何时需要使用 beforeDestroy

- 解绑自定义事件 event.$off
- 清除定时器
- 解绑自定义的 DOM 事件，`addEventListener()`加的事件

### 什么是作用域插槽

### Vuex 中 action 和 mutation 有何区别

- action 中处理异步，mutation 不可以
- mutation 做原子操作
- action 可以整合多个 mutation

### Vue-router 常用的路由模式

- hash 默认
- H5 history（需要服务端支持）
- 两者比较

### 如何配置 Vue-router 异步加载

```js
export default new VueRounter({
  routes: [
    {
      path: "/**",
      component: () => import("../../components/List"),
    },
  ],
});
```

### 请用 vnode 描述一个 DOM 结构

### 监测 data 变化的核心 API 是什么

- `Object.defineProperty`
- 如何深度监听、监听数组
- 缺点

### Vue 如何监听数组变化

- Object.defineProperty 不能监听数组变化
- 重新定义原型，重写 push pop 等方法，实现监听
- Proxy 可以原生支持监听数组变化

### 请描述响应式原理

- 监听 data 变化
- 组件渲染和更新的流程

### diff 算法的时间复杂度

- O(n)
- 在 O(n^3) 基础上做了一些调整

### 简述 diff 算法过程

- `patch(elem,vnode)`和`patch(vnode,newVnode)`
- `patchVnode`和`addVnodes`和`removeVnodes`（什么时候调用谁）
- `updateChildren`（key 的重要性）

### Vue 为何是异步渲染，$nextTick 何用？

- 异步渲染(以及合并 data 修改)，以提高渲染性能
- $nextTick 在 DOM 更新完之后，触发回调

### Vue 常见性能优化方式

- 合理使用 v-show 和 v-if
- 合理使用 computed（缓存）
- v-for 时加 key，以及避免和 v-if 同时使用
- 自定义事件、DOM 事件及时销毁（内存泄漏问题，页面会越来越卡）
- 合理使用异步组件（eg 比较大的第三方组件中）
- 合理使用 keep-alive（不需要重复渲染时）
- data 层级不要太深（因为深度监听需要一次性遍历完成，diff 次数多，会导致卡）
- 使用 vue-loader 在开发环境下做模板编译（预编译）
- webpack 层面的优化
- 前端通用的性能优化，如图片懒加载
- 使用 SSR
