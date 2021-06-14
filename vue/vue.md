## vue面试题

## 1. vue组建通信方式
  - 父子之间：props/$emit
  - 父子、兄弟、跨级：eventBus
  - 父子、兄弟、跨级： vuex
  - 父子、跨级：$attrs/$listeners
  - 父子、跨级： provide/inject
  - 父子间： $parent / $children ref

## 2. v-model双向绑定的原理
   vue2通过Object.defineProperty()来实现数据代理，vue3通过proxy实现数据代理.  
   当数据改变时，在setter中更新dom，当dom改变时，使用事件监听回调把最新的值赋给data相应的属性

## 3. vuex和全局变量的区别
   - Vuex 的状态存储是响应式的。当Vue组件从store中读取状态的时候，若store中的状态发生变化，那么相应的组件也会相应地得到高效更新。
   - 不能直接改变store中的状态。改变store中的状态的唯一途径就是显式地提交(commit)mutation。这样使得我们可以方便地跟踪每一个状态的变化，更好地调试应用。

## 4. computed和watch区别和场景？
   **computed：** 是计算属性，依赖其它属性值，并且computed的值有缓存，只有它依赖的属性值发生改变，下一次获取computed的值时才会重新计算。不支持异步，当computed内有异步操作时无效，无法监听数据的变化

运用场景：当需要进行数值计算，并且依赖于其它数据时，可以利用 computed 的缓存特性，避免每次获取值时，都要重新计算

   **watch：** 是「观察」的作用，类似于某些数据的监听回调 ，每当监听的数据变化时都会执行回调进行后续操作；不支持缓存，支持异步

运用场景：当需要在数据变化时执行异步或开销较大的操作时，使用watch允许我们执行异步操作(访问一个API)，限制我们执行该操作的频率，并在我们得到最终结果前，设置中间状态。这些都是计算属性无法做到的。

## 5. vue生命周期及各阶段
  ![生命周期示意图](https://user-gold-cdn.xitu.io/2019/8/19/16ca74f183827f46?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

## 6. Vue父子组件生命周期执行顺序
  - 加载渲染过程: 父 beforeCreate -> 父 created -> 父 beforeMount -> 子 beforeCreate -> 子 created -> 子 beforeMount -> 子 mounted -> 父 mounted
  - 子组件更新过程: 父 beforeUpdate -> 子 beforeUpdate -> 子 updated -> 父 updated 
  - 父组件更新过程: 父 beforeUpdate -> 父 updated
  - 销毁过程: 父 beforeDestroy -> 子 beforeDestroy -> 子 destroyed -> 父 destroyed

## 7. 父组件如何监听子组件生命周期
   可以在父组件引用子组件时通过@hook来监听，子组件生命周期事件，例如：created，mounted， updated 等

## 8. vue中v-html会导致哪些问题
   - 可能会导致xss攻击；
   - V-html更新的是元素的 innerHTML 。内容按普通 HTML 插入， 不会作为 Vue 模板进行编译 。
   - 在单文件组件里，scoped 的样式不会应用在 v-html 内部，因为那部分 HTML 没有被 Vue 的模板编译器处理。

## 9. Vue的Object.defineProperty和Vue3.0的Proxy区别
   - Object.defineProperty只能劫持对象的属性，从而需要对每个对象，每个属性进行遍历，如果，属性值是对象，还要递归
   - Object.defineProperty不能监控到数组下标的变化，Object.defineProperty本身是支持的，但vue考虑到性能体验问题舍弃了这个特性
   - Object.defineProperty兼容性好，支持 IE9，而 Proxy 的存在浏览器兼容性问题,而且无法用 polyfill 磨平
   
   - Proxy可以劫持整个对象，并返回一个新的对象，不仅可以代理对象，还可以代理数组。还可以代理动态增加的属性。
   - Proxy有多达13种拦截方法,不限于apply、ownKeys、deleteProperty、has等等是Object.defineProperty不具备的
   - Proxy返回的是一个新对象,我们可以只操作新的对象达到目的,而 Object.defineProperty 只能遍历对象属性直接修改
   - Proxy作为新标准将受到浏览器厂商重点持续的性能优化，也就是传说中的新标准的性能红利
## 10.Vue怎么用vm.$set()解决对象新增属性不能响应的问题
   - 如果目标是数组，直接使用数组的 splice 方法触发相应式；
   - 如果目标是对象，会先判读属性是否存在、对象是否是响应式，最终如果要对属性进行响应式处理，则是通过调用defineReactive方法进行响应式处理（defineReactive方法就是Vue初始化对象时，给对象属性采用Object.defineProperty动态添加getter和setter的功能所调用的方法）

## 11.Vue.nextTick的原理和用途
   Vue在修改数据后，视图不会立刻更新，而是等同一事件循环中的所有数据变化完成之后，再统一进行视图更新。 nextTick在下次 DOM 更新循环结束之后执行延迟回调。在修改数据之后立即使用这个方法，获取更新后的 DOM。
   用途：需要在视图更新之后，基于新的视图进行操作

## 12.vue模板编译原理
   vue 对模板编译的整体流程分为三个部分：解析器（parser），优化器（optimizer）和代码生成器（code generator）。
   - 解析器（parser）：将模板字符串转换成element ASTs。原理是一小段一小段的去截取字符串，然后维护一个stack用来保存DOM深度，每截取到一段标签的开始就push到stack中，当所有字符串都截取完之后也就解析出了一个完整的 AST
   - 优化器（optimizer）：找出那些静态节点和静态根节点并打上标记。用递归的方式将所有节点打标记，表示是否是一个静态节点，然后再次递归一遍把'静态根节点'也标记出来。
   - 代码生成器（code generator）：使用element ASTs生成render函数代码。原理也是通过递归去拼一个函数执行代码的字符串，递归的过程根据不同的节点类型调用不同的生成方法，如果发现是元素节点就拼一个_c(tagName, data, children) 的函数调用字符串，然后data和children也是使用AST中的属性去拼字符串。

## 13.router hash和history区别
   - hash： 兼容低版本和IE，url带有#，刷新页面可以正常加载到hash对应的页面。

   \# 后面hash发生变化时，浏览器不会向服务器发出请求，所以不会刷新页面。通过监听hashChange事件根据hash值变化更新页面部分内容

   - history： H5新增API，兼容性没有hash广泛，url干净没有#号，刷新时如果没有处理就会404，需要服务端将所有页面路由重定向回首页路由
     history根据H5发布的API：pushState和replaceState可以做到改变路由但是不会发出请求，达到监听url变化实现页面更新

   - abstract : 支持所有 JavaScript 运行环境，如 Node.js 服务器端。如果发现没有浏览器的 API，路由会自动强制进入这个模式.

## 14. 虚拟 DOM 的优缺点
**优点：**
 - 无需手动操作DOM： 只需要写好View-Model的代码逻辑，框架会根据虚拟DOM和数据双向绑定更新视图，提高开发效率
 - 可以借助DOM diff算法，省略多余的操作，例如重复添加同一个节点
 - 跨平台： 虚拟DOM本质上是JavaScript对象,可以进行更方便地跨平台操作，例如服务器渲染、weex 开发等，而DOM与平台强相关

**缺点:**
 - 无法进行极致优化： 虽然虚拟DOM+合理的优化，足以应对绝大部分应用的性能需求，但在一些性能要求极高的应用中虚拟DOM无法进行针对性的极致优化。首次渲染大量DOM时，由于多了一层虚拟DOM的计算，会比innerHTML插入慢。

## 15. virtual Dom diff及key作用
virtual Dom就是用js中的对象 作为基础树来描述一个节点node，利用dom diff算法避免没有必要的dom操作从而提高性能。响应式数据更新后，触发了`渲染Watcher`的回调函数`vm._update(vm._render())`去驱动视图更新，`vm._render()`其实生成的就是`vnode`，而`vm._update`就会带着新的`vnode`去走触发`__patch__`过程：

- 找到对应的真实dom，称为el
- 判断Vnode和oldVnode是否指向同一个对象，如果是，那么直接return
- 如果他们都有文本节点并且不相等，那么将el的文本节点设置为Vnode的文本节点。
- 如果oldVnode有子节点而Vnode没有，则删除el的子节点
- 如果oldVnode没有子节点而Vnode有，则将Vnode的子节点真实化之后添加到el
- 如果两者都有子节点，则执行updateChildren函数比较子节点，采用diff算法（只会同层比较, 不会跨层级）进行比较

updateChildren过程（核心diff的比较过程）：
  - 将Vnode的子节点Vch和oldVnode的子节点oldCh提取出来， oldCh和vCh各有两个头尾的变量StartIdx和EndIdx，它们的2个变量相互比较
  - 一共有4种比较方式。如果4种比较都没匹配，如果设置了key，就会用key进行比较，在比较的过程中，变量会往中间靠，一旦StartIdx>EndIdx表明oldCh和vCh至少有一个已经遍历完了，就会结束比较。

diff（采用双端比较）的过程：
![示意图](https://user-gold-cdn.xitu.io/2018/5/19/163783eb58bfdb34?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

- 分别对oldS、oldE、S、E两两做sameVnode比较，有四种比较方式，当其中两个能匹配上那么真实dom中的相应节点会移到Vnode相应的位置（比如：oldS和E是sameVnode,真实dom中的第一个节点会移到最后）
- 如果四种匹配没有一对是成功的，分为两种情况:
    - 如果新旧子节点都存在key，那么会根据`oldChild`的`key`生成一张hash表，用`S`的`key`与hash表做匹配，匹配成功就判断S和匹配节点是否为sameNode。  如果是，就在真实dom中将成功的节点移到最前面，否则，将S生成对应的节点插入到dom中对应的oldS位置，S指针向中间移动，被匹配old中的节点置为null。
    - 如果没有key,则直接将S生成新的节点插入真实DOM（ps：v-for设置key原因，没有key就只会做四种匹配，即使指针中间有可复用的节点都不能被复用了）

## 16. vue响应式原理
![viewmodel.png](http://ww1.sinaimg.cn/large/008bQ0fSly1grbtswspz1j30rw0bs3z4.jpg)

## 17. vue项目做过的优化
- 减少data数据，这些数据都要被代理增加getter和setter
- v-for和v-if禁止连用，有需要可以采用computed先过滤掉无用数据
- v-for给每个元素绑定事件的时候可以使用事件代理， key值保持唯一不能用index
- SPA开发的时候，对复杂组件采取keep-alive缓存组件
- 使用路由懒加载，使用异步组件
- 防抖、节流函数的使用
- 第三方模块按需倒入
- 长列表等滚动到可视区域再动态加载
- 图片进行懒加载

体验：
- SEO优化的进行预渲染和SSR渲染
- 骨架屏、缓存、开启gzip等

打包优化：

- 压缩代码、抽离公共文件（splitchunks）
- cdn加载第三方模块
- sourcemap优化
- 多线程打包happypack
- tree shaking

## 18. vue3的优化
- 使用createApp，而不是new Vue(); Template 支持多个根标签，Vue 2不支持多标签
- vue3生命周期函数从destroy变成了unmount，增加了setup替换beforeCreate和create，增加了用于调试的钩子函数onRenderTriggered和onRenderTricked
- 新增composition组合式API，使业务逻辑不会分散到各个option中，允许用户像编写函数一样自由地表达、组合和重用有状态的组件逻辑，易于后期维护
- setup()为组合API的入口，reactive（把一个对象响应式）、ref（把基本数据响应式）、toRef（把对象的一个属性响应式）、toRefs（把一个对象所有属性响应式）都是定响应式数据，使用颗粒度不一样
- Teleport可以使dom结构应该完全剥离Vue顶层组件挂载的DOM；同时还可以使用到Vue组件内的状态（data或者props）的值
- Suspense内置组件，提供两个template slot, 刚开始会渲染一个fallback 状态下的内容， 直到到达某个条件后才会渲染default状态的正式内容，通过使用Suspense组件进行展示异步渲染就更加的简单
- 更有好的支持tree-shaking：重构了全局和内部API，nextTick、observe、compile、set、delete等可以按需引入
