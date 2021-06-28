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
- react: 推崇函数式编程（纯组件），数据不可变以及单向数据流。函数式编程最大的好处是其稳定性（无副作用）和可测试性（输入相同，输出一定相同），React适合大型应用，根本原因还是在于其函数式编程。

    1.1 写法和API的差异
  - Vue：推崇template模板（简单易懂，从传统前端转过来易于理解） + options API、单文件vue。模板中要理解slot、filter、指令等概念和api，options中要理解watch、computed（依赖收集）等概念和api。
  - React:推崇JSX、HOC、all in js。本质上核心只有一个Virtual DOM + Diff算法，所以API非常少，明白setState就能开始开发。
    1.2 社区及升级方向差异
  - Vue: 很多常见的解决方案，是Vue官方主导开发和维护。如Vuex、Vue-Router、Vue-CLI、Vite工具等。属于那种大包大揽，遇到某类通用问题，只需要使用官方给出的解决方案即可。 Vue未来方向依然是考虑通过依赖收集来实现数据可变。
  - React: 只关注底层，上层应用解决方案基本不插手，大部分都是社区去解决。比如redux、mobx、redux-sage、dva等，所以React社区也非常繁荣。 React的函数式编程基本盘不会变。从React Hooks可以看出，React团队致力于组件函数式编程（纯组件，无class组件），尽量减少副作用（减少this引起副作用）。

2. 组件实现不同
- vue：把options挂载到Vue核心类上，然后再new Vue({options})拿到实例，对用户是不透明。options api中的this指向内部Vue实例，Vue插件是基于Vue原型类基础之上，插件使用Vue.install要确保第三方库的Vue和当前应用的Vue对象是同一个。
- React: 内部实现比较简单，直接定义render函数来生成VNode，而React内部使用了四大组件类包装VNode，不同类型的VNode使用相应的组件类处理，职责划分清晰明了（Diff算法也非常清晰）。React类组件都是继承自React.Component类，其this指向用户自定义的类，对用户是透明的。

3. 响应式原理不同
- Vue：赖收集，自动优化，数据可变。递归监听data的所有属性,直接修改。 当数据改变时，自动找到引用组件重新渲染。
- React: 基于状态机，手动优化，数据不可变，需要setState驱动新的State替换老的State。 当数据改变时，以组件为根目录，默认全部重新渲染

4. diff算法不同
- Vue：Diff使用双向指针，边对比，边更新DOM。
- React: 主要使用diff队列保存需要更新哪些DOM，得到patch树，再统一操作批量更新DOM。

5. 事件机制不同
- Vue：Vue原生事件使用标准Web事件， Vue组件自定义事件机制，是父子组件通信基础。
- React: React原生事件被包装，所有事件都冒泡到顶层document监听，然后合成事件下发。可以跨端使用事件机制，不与Web DOM强绑定。React组件上无事件，父子组件通信使用props

---------------
[返回主页](https://github.com/Marilynlee/interview-note)
