+++
layout = "post"
title = "React 组件化之路"
date = "2014-04-12"
categories = ["js"]
tags = ["doc"]
draft = true
+++

个人接触 React 的时间不长，属于个新手用户，表述有误的话，欢迎指正。

### 什么是 React ?

React 起源于 Facebook 的内部项目, 因为对现有 MVC 框架不满意，于是自己重写了一套，用于架设 Instagram, 后来感觉很好，就开源了。其架构的设计很值得前端的同学去研究一下，或者说，颠覆了 MVC 的思维方式。

前段时间又发布了 React Native， 两个都在 github 上受到了很大的关注。 React Native 我目前还没有怎么去了解，所以在这里不太好做评价。不过他们想要 Native App 和 Web App 上的体验一致，跟我们（公司）现阶段准备的 H5 框架的出发点是一样的。

这个想要强调一下的是，目前 React 还没有发布 1.0 版本，所以想要将项目使用到实际开发环境中的同学可以权衡一下。因为现在 React API 也还在不断变动之中。或者可以使用一个较稳定版本，后期统一升级。

### 概述
React 标榜自己是 MVC 里面的 V 部分，那个 Flux(Facebook 使用的一套前端应用架构模式，用于推进应用中的数据单向流动) 相当于 M 和 C 部分。

其核心思想是，封装组件，各个组件维护自己的状态和 UI，当状态变更的时候，自动重新渲染整个组件。

？？ 组件， JSX , 虚拟 Dom , 单向数据流 ？？

### 基本语法
时间关系，这个部分简单提一提好了，保证下面的内容能够顺利讲下去就好了。

* JSX
    React 模板语法。与 HTML 类似，里面可穿插 script。

* 初始页面结构
    - 引入 react.js(add-ons版本还集成了 React 的工具类)
    - react 并不一定依赖与 JSX (相关语法可以移步官网查阅)
    - JSXTransformer.js (JSX 语法解析, 解析的速度可能会有些慢，建议在本地或者服务器上转化)
    - react js 代码。注意 script 的 type 类型是 `text/jsx`

        ```
        <!DOCTYPE html>
        <html>
          <head>
            <script src="https://fb.me/react-0.13.1.js"></script>
            <script src="https://fb.me/JSXTransformer-0.13.1.js"></script>
          </head>
          <body>
            <div id="example"></div>
            <script type="text/jsx">
              React.render(
                <h1>Hello, world!</h1>,
                document.getElementById('example')
              );
            </script>
          </body>
        </html>
        ```

* React.render 渲染页面
* React.createClass 构建组件函数
  - render 渲染页面
* ...

### 构建
这里偷懒了，拿了开源项目[TODO list]()的代码做分析。

#### 0. 准备初始化数据
这里使用 localStorage 对 todo list 数据进行存储, 暂不考虑和服务器做交互。数据格式为 JSON 。自定义的 Model 和 Control 层不做特别展开。

#### 1. 划分 UI 层
首先上图，对一个 Todo list 的功能需求进行功能划分。将一个功能组件切分成尽可能小的子组件，保持每一个子组件功能的单一性(尽量只做一件事情)。

为了压缩文字，移除了部分功能点的讨论。

图中，将 Todo list 分为

* 整个组件容器
  - 用户输入框
  - 展示列表
    + 单条列表（包含可编辑功能）
  - 底部功能选项集合

#### 2. 创建静态版本
      ```
      var TodoApp = React.createClass({
        render: function(){
          var main, footer;
          var todos = this.props.model.todos;
          var todoItems = todos.map(function (todo) {
            return (
              <TodoItem
                key={todo.id}
              />
            );
          }, this);
          footer =
            <TodoFooter
              count={activeTodoCount}
              completedCount={completedCount}
              nowShowing={this.state.nowShowing}
              onClearCompleted={this.clearCompleted}
            />;
          if(todo.length){
            main = (
                <ul id="todo-list">
                  {todoItems}
                </ul>
              );
          }
          return (
              <div>
                <header id="header">
                  <h1>todos</h1>
                  <input
                    ref="newField"
                    id="new-todo"
                    placeholder="What needs to be done?"
                    onKeyDown={this.handleNewTodoKeyDown}
                    autoFocus={true}
                  />
                </header>
                {main}
                {footer}
              </div>
            );
        }
      });
      React.render(
        <TodoApp model={model}/>,
        document.getElementById('todoapp')
      );
      ```

构建组件和可重用的组件。并通过 props 传递数据。 state 一般多用于状态的变更，如交互上。可以采用从上往下的方法构建静态页面。`React.render()` 用于实例化组件，渲染 UI。 `React.createClass` 创建组件类， `render` 方法返回页面的 HTML 部分。

##### 3. 识别最小且完整代表 UI 的 state
React 中页面的渲染是通过底层数据模型的变化来实现的。所以在 UI 的交互上，只需要触发改变 **state** 的状态即可。

为了正确构建应用，首先需要考虑应用需要的最小的可变 state 数据模型集合。state 的关键点在于精简。不存储重复的数据。可以依据以前条件来判断：
* 是否可以从父级通过 props 传入？是的话，可能不是 state;
* 是否随时间的改变而改变？如果不是，可能不是 state;
* 能否依据组件中其它 state 或 props 计算得出？如果是，不是 state;

思路相同，我们不对 Footer 组件做讨论。
这里，我们设定一个 Model 用于存放所有 list 的数据集合。当用户新增或者编辑的时候去更新 Model 数据。

根据上面的规则，可分析出：
* 初始数据列表(props， Model 数据)
* 新增时用户输入文本 (state， 随用户的行为改变)
* 编辑时的输入文本(state，同上)
* 复选框的值(props，初始化的时候设一个完成度标记，checked 的时候改变标记值)
* 删除，双击等也都属于是属性上的变更(props)

##### 4. 确认 state 的生命周期
接下来需要确定的是那一层组件上使用该 state 数据模型。
可以按如下的方法来找出每个 state 数据（有点复杂）：
* 找出每个基于哪个 state 渲染界面的组件
* 找出一个共同的祖先组件（如果是单组件的话，则为需要这个 state 的所有组件的上一层）
* 要么是共同的祖先组件， 要么是另一个组件树中位于更高层级的组件应该拥有这个 state
* 如果找不到拥有这个的合适组件，创建一个新的组件来维护这个 state，并添加到所有拥有这个组件的上面。

则：
* 新增和编辑拥有 state, 他们共同拥有的组件 TodoApp 上
* 整个 Todo list 组件需要传入列表数据(model)
* 复选框和删除等是一个状态的变更
* 使用`getInitialState` 初始化 state 数据

##### 5. 添加方向数据流
上面已经基本按着单向数据流的方式构建好应用了。剩下的是用户交互时子组件的变更引起的祖先组件的更新（如编辑单条内容的时候）。
当你想要改变单条内容保存的时候，需要更新 state 的状态，调用`setState()`来重置。props 的话，可以使用 `setProps()`。否则的话，所有的用户输入都将会被忽略。因为列表所接收的数据并未改变。

React 有提供一个 ReactLink 的插件来实现数据的双向绑定功能，这里不做讨论。


#### 参考文献
* [Thinking in React](https://facebook.github.io/react/docs/thinking-in-react.html)
* [【翻译】深入理解 React](http://www.html-js.com/article/2782)



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













