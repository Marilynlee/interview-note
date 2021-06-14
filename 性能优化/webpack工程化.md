# 前端工程化

## 1. webpack构建流程
 - 初始化流程：从配置文件和 Shell 语句中读取与合并参数，并初始化需要使用的插件和配置插件等执行环境所需要的参数
 - 编译构建流程：从 Entry 发出，针对每个 Module 串行调用对应的 Loader 去翻译文件内容，再找到该 Module 依赖的 Module，递归地进行编译处理
 - 输出流程：根据入口和模块之间的依赖关系，对编译后的 Module 组合成 Chunk，把 Chunk 转换成文件，根据配置确定输出的路径和文件名，把文件内容写入到文件系统

- 初始化参数：从配置文件和 Shell 语句中读取与合并参数，得出最终的参数；
- 开始编译：用上一步得到的参数初始化 Compiler 对象，加载所有配置的插件，执行对象的 run 方法开始执行编译；
- 确定入口：根据配置中的 entry 找出所有的入口文件；
- 编译模块：从入口文件出发，调用所有配置的 Loader 对模块进行翻译，再找出该模块依赖的模块，再递归本步骤直到所有入口依赖的文件都经过了本步骤的处理；
- 完成模块编译：经过Loader翻译完所有模块后，得到了每个模块被翻译后的最终内容以及它们之间的依赖关系；
- 输出资源：根据入口和模块之间的依赖关系，组装成一个个包含多个模块的 Chunk，再把每个 Chunk 转换成一个单独的文件加入到输出列表，这步是可以修改输出内容的最后机会；
- 输出完成：在确定好输出内容后，根据配置确定输出的路径和文件名，把文件内容写入到文件系统。

## 2. 常用的loader和plugin，怎么优化？
> Loader是文件加载器，能够加载资源文件，并对这些文件进行一些处理，诸如编译、压缩等，最终一起打包到指定的文件中webpack原生只能解析js文件，Loader让webpack拥有了加载和解析非JavaScript文件的能力。
> Plugin可以扩展webpack的功能，让webpack具有更多的灵活性。 在Webpack 运行的生命周期中会广播出许多事件，Plugin可以监听这些事件，在合适的时机通过 Webpack 提供的 API 改变输出结果。

loader运行在打包文件之前（loader为在模块加载时的预处理文件），plugins在整个编译周期都起作用。
 - loader： 
    - 样式：css-loader,style-loader, sass/less-loader
    - 编译：babel-loader,vue-loader, ts-loader
    - 图片字体：file-loader,url-loader
    - 校验：jshint-loader eslint-loader
 - plugin：
    - html-webpack-plugin可以根据模板自动生成html代码，并自动引用css和js文件
    - DefinePlugin 编译时配置全局变量，这对开发模式和发布模式的构建允许不同的行为非常有用。
    - HotModuleReplacementPlugin 热更新
    
    - optimize-css-assets-webpack-plugin 不同组件中重复的css可以快速去重 
    - extract-text-webpack-plugin 将js文件中引用的样式单独抽离成css文件
    - uglifyjs-webpack-plugin 压缩和混淆代码
    - CommonsChunkPlugin抽离公共模块
    
    - copy-webpack-plugin 复制文件插件
    - compression-webpack-plugin 生产环境可采用gzip压缩JS和CSS
    - webpack-bundle-analyzer 一个webpack的bundle文件分析工具，将bundle文件以可交互缩放的treemap的形式展示
    - happypack：通过多进程模型，来加速代码构建
    - clean-wenpack-plugin 清理每次打包下没有使用的文件

## 3.webpack本地热更新怎么实现的？
 - webpack-dev-server通过express启动本地服务，当浏览器访问资源时做出回应，webpack开始构建，在编译期间会向 entry 文件注入热更新代码
 - 首次打开客户端时，服务端和客户端建立起websocket长连接
 - webpack内部compiler.watch默认会监听文件修改，当文件被编辑保存后webpack会重新打包编译到内存中
    - 每次编译完会生产hash值、已改动模块的json文件和js文件
    - 编译完成后通过socket向客户端推送此次编译的hash值
 - 客户端的websocket收到新的hash值，和上一次文件进行对比，一致则走缓存，不一致通过ajax和jsonp获取最新资源文件
 - 使用内存文件系统去替换有修改的内容，实现局部更新

## 4.webpack的tree shaking和scope hoisting
- tree shaking：
  - Tree-shaking只对ES Module起作用，对于commonjs无效，对于umd亦无效。因为tree-shaking是针对静态结构进行分析，只有import和export是静态的导入和导出。而commonjs有动态导入和导出的功能，导出的变量都挂载在exports上，没法由静态分析得知是否被引用。
  - babel编译默认将模块转换为commonjs，需要配置.babelrc的{module:false} 和 package.json的{sideEffects: false}才可以进行tree-shaking
  - 作用： 剪除无用代码
> treeShaking依赖于对模块导出providedExports和被导入usedExports的分析, webpack对代码进行标记，把import & export标记

- 开启ScopeHoisting：所有代码打包到一个作用域内，然后使用压缩工具根据变量是否被引用进行处理，删除未被引用的代码；
- 未开启ScopeHoisting：每个模块保持自己的作用域，由webpack的treeShaking对export打标记，未被使用的导出不会被webpack链接到exports（即被引用数为0），然后使用压缩工具将被引用数为0的变量清除。（类似于垃圾回收的引用计数机制？）

- scope hoisting作用域提升，把引入的 js 文件“提升到”它的引入者顶部。webpack打包出来模块是一个一个的IIFE（立即执行函数），构建后大量作用域包裹函数，导致闭包增多内存开销大
  
作用：代码量明显减少，减少多个函数后内存占用减少，不用多次使用 __webpack_require__ 调用模块，运行速度也会得到提升

## 5.sourcemap作用
 源码经过构建工具的转换，与源码差别较大，无法排查问题。sourceMap是一个JSON文件，存储着转换后代码和转化前代码的位置对应，以及转换前代码的信息。当执行出错或debugger时，相关工具（比如google的开发者工具）可以根据sourceMap中的信息直接显示原始代码并定位到出错点。

## 6.AST抽象语法树
- CST：解析树是包含代码所有语法信息的树型结构，又叫具象语法树CST
- AST：实际上只是一个解析树(parse tree)的一个精简版本
- 词法分析（扫描scanner）：编译的第一个阶段是扫描源代码文本，scanner从左到右扫描文本，把文本拆成一些单词。然后将单词传入分词器，经过识别器确定这些单词的词性（关键字识别器、标识符识别器、常量识别器、操作符识别器等），产出用<type, value>形似的二元组的token序列，type表示一个单词种类，value为属性值
- 语法分析（解析器）：分词阶段完成以后，token序列会经过我们的解析器，由解析器识别出代码中的各类短语，会根据语言的文法规则(rules of grammar)输出解析树，这棵树是对代码的树形描述。

## 7.babel原理
1. Parse(解析)：生成抽象语法树的过程，包括词法分析和语法分析
2. Transform(转换)：接收AST并对其进行遍历，在此过程中对节点进行添加、更新及移除等操作，让它符合编译器的期望。
> bable是一个工具链，babel是依赖于它的插件的, 没有插件babel只是会将源码生成AST，然后在通过生成器生成和原来的源码一摸一样的代码。比如@babel/plugin-transform-react-jsx是将react中的jsx转换为react的节点对象
3. Generate(代码生成)：将第二步经过转换过的（抽象语法树）生成新的代码。  AST转换成字符串形式的代码，同时还会创建源码映射（source maps）。代码生成其实很简单：深度优先遍历整个 AST，然后构建可以表示转换后代码的字符串。

### 8.Typescript编译
   - 语法分析器（Parser）：从原文件开始, 根据语言的语法生成抽象语法树（AST）
   - 联合器（Binder）：遍历并处理AST，并将AST中的声明结合放到一个Symbol中，生成带有Symbol的SourceFile
   - 类型解析器与检查器（Type resolver/Checker）：解析每种类型的构造，检查读写语义并生成适当的诊断信息
   - 生成器（Emitter）：生成输出结果，可以是以下形式之一：JavaScript（.js），声明（.d.ts），或者是source maps（.js.map）
