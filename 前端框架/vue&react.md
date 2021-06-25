# React和vue异同

## 1. Vue和React相同点
- 都使用Virtural DOM
- 都使用组件化思想，流程基本一致
- 都是响应式，推崇单向数据流
- 都有成熟的社区，都支持服务端渲染

_Vue和React实现原理和流程基本一致，都是使用Virtual DOM + Diff算法_。不管是Vue的template模板+options api写法，还是React的Class或者Function写法，底层最终都是为了生成render函数，render函数执行返回VNode（虚拟DOM的数据结构，本质上是棵树）。

Vue和React通用流程：vue template/react jsx -> render函数 -> 生成VNode -> 当有变化时，新老VNode diff -> diff算法对比，并真正去更新真实DOM。

Virtual DOM优点：
- 减少直接操作DOM。框架给我们提供了屏蔽底层dom书写的方式，减少频繁的整更新dom，同时也使得数据驱动视图
- 为函数式UI编程提供可能（React核心思想）
- 可以跨平台，渲染到DOM（web）之外的平台。比如ReactNative，Weex

## 2. Vue和React区别
1. 核心思想不同
- Vue：早期定位是尽可能的降低前端开发的门槛，推崇灵活易用（渐进式开发体验），数据可变，双向数据绑定（依赖收集）






---------------
[返回主页](https://github.com/Marilynlee/interview-note)
