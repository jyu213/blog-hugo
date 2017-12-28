+++
title = "Webpack 和 Rollup 之 Tree Shaking"
draft = true
categories = ["webpack"]
description = ""
+++

这个话题源于和同事之前的一次讨论中。Webpack2 也支持 Tree Shaking 了，那和 Rollup 是不是就一样了。那他们的具体实现又有什么样的差别呢?带着这样的疑问，我们来细细的扒一扒它们之间的区别。

### 概念
在此之前先简单的谈谈 Tree Shaking 的概念。最早应该是从 Rollup 来的吧，不过其实简单理解的话，可以当成是 dead code elimination 的另一种说辞，就是把无用代码剔除掉。有个比较本质的区别在于，Tree Shaking 导入有用的代码，而非排除死代码。更学术的区别可以看 Rich_Harris 的[官方解释](https://medium.com/@Rich_Harris/tree-shaking-versus-dead-code-elimination-d3765df85c80#.p4izirx8z)。

### Rollup
Rollup 在顶级 AST 节点上工作，依赖于 ES6 模块的静态解析。ES6 模块有个很有趣的特性，就是完全的静态结构。正因如此，才使用 rollup 的 Tree Shaking 得以实现。

先从简单的例子开始入手吧。

```javascript
// action.js
export const walk = () => console.log('walk')
export const run = () => console.log('run')

// index.js
import {walk} from './action.js'
walk()
```

`action.js` 中定义了一些行为函数，在 `index.js` 中使用 `walk` 函数。rollup 编译后的结果如下：

```javascript
'use strict';
const walk = () => console.log('walk');
walk();
```

很明显，未使用到的 `run` 则在编译的时候被移除掉了。

如果是 CommonJS 的写法的话，还需要通过 `rollup-plugin-commonjs` 插件来转换一下。

在 Rollup 中主要的构建流程做了一下几步：
1. 查找。找到对应的 entry 模块的所有依赖项并导入(可以是 plugin 处理后的内容，上面讲到的 commonjs 的处理就是如此)，返回模块包含 code, ast,map 等的 JSON 信息。同时存储相关的依赖 imports, externals 等等。
2. 绑定。将索引与之相关联，来生成完整的依赖代码。
3. 标记。引用所用需要被包含的申明(tree-shaking 就是在这一步进行处理)
4. 排序。使用增强的拓扑排序对模块进行排序，解决诸如全局命名冲突等问题。

~~~ ???
~~~

而 tree-shaking 实际针对 AST 进行删减处理，主要有以下内容，具体可以查找对应 AST 语法。如果错误，欢迎指正。

* classDeclaration
* conditional express
* ExportDefaultDeclaration
* FunctionDeclaration
* isStatement
* SequenceExpression
* Statement
* VariableDeclaration
* VariableDeclarator














