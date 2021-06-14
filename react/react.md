# React面试题
## 1. React 组件如何通讯
- 父子组件通过 属性 和 props 通讯
- 通过 context 通讯
- 通过 Redux 通讯

## 2. react生命周期
创建阶段：
- constructor
- getDerivedStateFromProps
- render
- componentDidMount

更新时：
- getDerivedStateFromProps
- shouldComponentUpdate
- render
- getSnapshotBeforeUpdate
- componentDidUpdate

卸载时：
- componentWillUnmount

## 3. react项目的坑点
- jsx做判断渲染不同组件时，需要强制转换为boolean类型，否则页面会输出0
- 父组件更新会强制更新所有的子组件，不管子组件使用的props到底是否改变，class组建可以手动在shouldComponentUpdate中优化，函数组件可以使用memo包裹
- 遍历子组件时不能用index作为组件的key
- 给组件添加ref时候，尽量不要使用匿名函数，因为当组件更新的时候，匿名函数会被当做新的prop处理，让ref属性接受到新函数的时候，react内部会先清空ref，也就是会以null为回调参数先执行一次ref这个props，然后在以该组件的实例执行一次ref，所以用匿名函数做ref的时候，有的时候去ref赋值后的属性会取到null

## 4. react虚拟dom实现
操作真实dom耗费性能代价高，所以react内部使用js实现了一套dom，每次在操作真实dom前，使用diff算法对virtual dom进行比对，递归找出有变化的dom，在对其进行更新操作。

为了实现Virtual DOM需要把每一个节点类型抽象成对象，每一种节点类型有自己的属性props，每次进行diff时react会优先比较该节点的类型，如果类型不一样react会直接删除该节点，然后直接创建新的节点插入其中；如果类型一样，会比较prop属性是否有更新，react发现porp有变化时会重新渲染该节点；然后对其子节点进行比较，一层一层往下，直到没有子节点。

## 5. react fiber的原理
react15（stack reconciler）： 对virtual dom更新和渲染是同步的，就是当一次更新或渲染开始以后，diff virtual dom并且渲染是一次完成的。如果组件层级较深，响应的堆栈也很深，长时间占用浏览器主线程，会出现界面无响应或者掉帧的现象。

react16（fiber reconciler）： 把每一个任务分成很多小片，当分配给这个小片的时间用尽时，就检查任务列表有没有新的，优先级更高的任务，有就先执行这些任务，没有就继续原来的任务。这种渲染方式就是异步渲染。

fiber原理： 通过对象记录组件上需要做或已完成的更新，一个组件可以对应多个fiber。在render函数中创建的react Element树在第一次渲染的时候会创建一个一模一样的fiber节点树，不同的react element类型对应不同的fiber节点类型。一个react element的工作就由它对应的fiber节点负责。

一个react element可以对应多个fiber，因为在fiber update时会从原来fiber（current）复制一个新的fiber（alternate）。两个fiber diff出的变化记录在alternate上，更新结束后alternate会取代current成为新的节点。

更新分为两个阶段：
- reconciliation phase： 找出要更新的工作（diff fiber tree），是一个计算阶段，计算结果可以缓存，可以被打断暂停执行。
- commit phase：需要提交所有的更新并渲染，为了防止页面的抖动，是不可以被打短暂停的。

## 6. react合成事件
react 事件使用驼峰命名，而非使用全小写；传递一个函数作为事件处理程序，不是一个字符串；而且不能通过return false阻止默认行为。必须明确调用preventDefault。

react根据W3C规范定义了每个事件处理函数的参数，即为合成事件。事件处理程序传递syntheticEvent的实例，这是一个跨浏览器原生事件的包装器。具有原生事件相同的接口，在所有浏览器中工作方式都相同。syntheticEvent采用了事件池，来节省内存，从而不会频繁的创建和销毁事件对象。

## 7. setState执行过程
setState是一个异步的过程，多次触发会合并成一次执行，setState不会立即更新state而是放入了队列中，最终合并多个setState一次更新页面。

setState后发生了什么：
    react会将传入的参数与组件当前的状态合并，触发所谓的调和过程。然后以相对高效的方式根据新的状态构建react元素树，计算出新旧树节点的差异，对界面进行最小化的重新渲染。在差异计算中，react能够精准的知道哪个节点变化以及该如何变化，保证按需更新而不是重新渲染。

## 8. react diff算法
   传统 diff 算法通过循环递归对节点进行依次对比，效率低下，算法复杂度达到 O(n^3)，其中 n 是树中节点的总数。diff通过以下优化策略，将复杂度降至O(n)：
    - Web UI 中 DOM 节点跨层级的移动操作特别少，可以忽略不计。
    - 拥有相同类的两个组件将会生成相似的树形结构，拥有不同类的两个组件将会生成不同的树形结构。
    - 对于同一层级的一组子节点，它们可以通过唯一 id 进行区分。 

基于以上三个策略，React分别对tree diff、component diff以及element diff进行了算法优化
    - tree diff： react只会对同一层次的节点进行比较，当发现节点不存在时，就会删除整个节点及其子节点，不会再进行比较，这样就只需要遍历一次，就能完成对整个DOM树的比较
    - component diff： 如果是同一类型的组件则继续比较虚拟dom树；如果不是，则将该组件判断为dirty component替换整个组件下的所有子节点
    - element diff： React diff提供了三种节点操作：插入、移动和删除
        - 插入：新的component类型不在老集合里，对新节点执行插入操作
        - 移动：在老集合里有新component类型，且element是可更新的类型（prevChild=nextChild），就需要做移动操作复用以前的dom节点
        - 删除：老的component类型在新集合中，但element不同则不能直接复用和更新，或者老component不在新集合里，需要执行删除操作





