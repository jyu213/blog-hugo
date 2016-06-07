+++
draft = true
layout = "post"
title = "React 组件化之路（下）"
date = "2014-04-21"
categories = ["article"]
tags = ["doc"]
+++

#### 比较？
不同于 MVC 视图-数据-控制器分离的思维方式，React 的组件化之路是将每个组件的 UI 和逻辑都定义在组件的内部，通过 API（state, props）来和外部实现交互。这样，组件可以被切割成多个功能单一的小模块，然后通过组合和嵌套来实现功能的快速开发。

#### 优缺点
很明显，这样的好处在于
* 可组合
* 可重用
* 可维护
* 可测试

我还没有在比较复杂的项目中大面积的使用过，所以可能很多不足都还没有体验到，欢迎留言反馈。
* 打破了传统的已事件绑定的开发模式。在构建项目的时候可能会有很大不适。
* 单向数据绑定和双向数据绑定，各有优缺点吧，看具体的使用场景。如在用户交互频繁的地方，就可能需要不断重置 state ，单向数据绑定就并不一定显得友好。
* 如何实现类似插件化的组件，即可传递参数之类？

有兴趣的朋友可以看一下 [StackOverflow](http://programmers.stackexchange.com/questions/225400/pros-and-cons-of-facebooks-react-vs-web-components-polymer)上的问答。

#### 未来Web组件化？
之前有看到网上讨论过这个话题，其实还是挺期待的。目前市场上拥有各种的框架，有很多存在功能相似，但是在代码上并不能完全复用。这导致我们从一个框架迁移到另一个的成本其实是非常高的。

这使得我们在想，是否有必要去统一底层组件，而不必关系依赖，兼容等等的问题了，这样大家就可以更专注于业务上的开发了。

目前比较流行的框架如 Angular, Polymer, React， 大家可以去了解了解。

最后，脑动大开一下，也许以后的图片滚动的组件类似于这样呢：

    <imgScroll>
      <img src="xxx.jpg" />
      <img src="xxx.jpg" />
      <img src="xxx.jpg" />
    </imgScroll>


### 附录

#### JSX
不同于 MVC 中的 View 模板把 HTML 单独的抽出来，React 采用的组件的思维模式，认为组件和模板是紧密关联，模板与组件的分离会使得问题复杂化，而模板中往往又会含有大量的判断逻辑在其中。虽然一度曾认为把 HTML 写在 JS 中好像回到了以前的字符串拼接上。

简单来说，JSX 就是一种把 HTML 嵌入到 JS 中。需要将 JSX 编译成 JS 代码才能使用。编译会有些消耗，建议本地处理。另外，React 中并不一定是需要 JSX 的，它也支持 JS 的写法。

JSX中遇到 HTML 标签`<`开头的就按 HTML 规则解析。用法基本一样。除了其中如 class 转换为 className，for 转化成 htmlfor 之外（保留关键字），自定义属性会被过滤（可使用 data- 或 aria-）。

遇到代码块已 `{`开头的内容则按 JS 表达式来解析。

#### 组件(component)
React 应用就是构建在组件之上的。
通过 props 和 state 属性在 render 中生成对应的 HTML 结构。

##### props 和 state
props 一般为组件的属性，为外部传入，可以认为是不可更改的。不建议随便重新设置 props 中的值。
state 为组件的当前状态。依据不同的状态去呈现不同的 UI。一旦状态的变更就会重新渲染 UI。可通过 setState 触发。

保持数据的精简。

##### 生命周期
组件由 React.createClass 创建，并提供 render 方法及其它相关方法。
getInitialState: 初始化 this.state。 只在组件转载前调用一次。
。。。

componentWillMount:
只会在装载之前调用一次。 // ???

##### 事件绑定
React 拥有一套自定义事件。
同时 `componentDidMount` 方法里也支持 `addEventListener` 的事件绑定。不过，建议能使用 React 的事件的话就不要使用原生事件了。

##### DOM
getDOMNode , refs

##### 组件间的通信
父子组件间通信：通过 props 属性传递。
非父子组件间的通信： 使用订阅模式。在 componentDidMount 里订阅事件，componentWillUnmount 里取消订阅。有事件触发的时候调用 setState 更新 UI。


#### 虚拟 DOM
当组件状态 state 有变更的时候，React 就会自动调用组件的 render 方法重新渲染整个组件的 UI。
频繁的操作 Dom 元素一直是浏览器上的一个瓶颈。React 采用了一个虚拟 DOM 机制。使用 JS 在内部实现了一套虚拟 DOM 元素，把页面中的 DOM 结构映射到了这个虚拟的 DOM 上。当组件更新的时候，会通过比较两者元素，寻找出变更的节点，再把这个改动的数据更新到浏览器 DOM 节点上。这样在性能上就会比原生 DOM 好很多。同时，也方便 React 迁移到其它平台（如 app）上。

#### 单向数据流
这是 React 的又一大亮点。









http://www.ruanyifeng.com/blog/2015/03/react.html






