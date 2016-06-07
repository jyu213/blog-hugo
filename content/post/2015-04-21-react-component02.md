+++
layout = "post"
title = "React 组件化之路（中）"
date = "2014-04-21"
categories = ["article"]
tags = ["doc"]
draft = true
+++

接上一篇。这里我们以开源项目[TODO list](https://github.com/tastejs/todomvc)的代码做分析。这里为了简化逻辑，对代码进行了一定的修改与删除。

### 构建

#### 0. 准备初始化数据
这里使用 localStorage 对 todo list 数据进行存储, 暂不考虑和服务器做交互。数据格式为 JSON 。自定义的 Model 和 Control 层不做特别展开。

#### 1. 划分 UI 层
首先上图，对一个 Todo list 的功能需求进行功能划分。将一个功能组件切分成尽可能小的子组件，保持每一个子组件功能的单一性(尽量只做一件事情)。

=== 图啊图啊 ===

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








