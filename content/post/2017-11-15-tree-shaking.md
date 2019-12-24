+++
title = "Rollup 和 Webpack 之 Tree Shaking"
categories = ["webpack"]
date = "2017-10-18"
tags=["webpack"]
description = ""
+++

这个话题最早源于和同事之前的一次讨论。Webpack 也支持 Tree Shaking 了，同样 Rollup 也是，那么它们之间实现是一致的么？写了一半，中间拖了几个月，然后社区中又有两篇比较不错的文章，然后又拖了好久，才慢慢补齐了文章。拖延症伤不起啊😂。

### 概念
在此之前先简单的谈谈 Tree Shaking 的概念。最早应该是从 Rollup 来的吧，不过其实简单理解的话，可以当成是 `dead code elimination` 的另一种说辞，就是把无用代码剔除掉。它们本质的区别在于，Tree Shaking 是导入有用的代码，而 `dead code elimination` 是排除无用代码。更学术的解释可以看 Rich_Harris 的[官方说明](https://medium.com/@Rich_Harris/tree-shaking-versus-dead-code-elimination-d3765df85c80#.p4izirx8z)。

### Rollup
Rollup 作用于顶级 AST 节点上，依赖于 ES6 模块的静态解析。ES6 模块有个特性很好，就是完全的静态结构，这样导入和导出的声明都放在了模块最顶层，而且都是非动态的。正因这个设计的强大，才使用 Rollup 的 Tree Shaking 得以实现。

我们先从简单的例子开始看。

```javascript
// action.js
export const walk = () => console.log('jianjian is walking')
export const run = () => console.log('jianjian is running')

// index.js
import {walk} from './action.js'
walk()
```

`action.js` 中定义了两个行为函数并导出，在 `index.js` 中使用 `walk` 函数。Rollup 编译后的结果如下：

```javascript
'use strict';
const walk = () => console.log('jianjian is walking');
walk();
```

很明显，未使用到的 `run` 函数则在编译的时候被移除掉了。

> 如果是 CommonJS 写法的话，还需要通过 `rollup-plugin-commonjs` 插件来转换一下。



再来看一看对类的处理。

```javascript
// action.js
class action1 {
  talk() {
    console.log('jianjian is talking')
  }
  walk() {
    console.log('jianjian is walking')
  }
}

class action2 {
  walk() {
    console.log('jianjian is walking')
  }
}
export {
    action1,
    action2
}

// index.js
import {action1, action2} from './class.js'

let act = new action1()
act.talk()
```



输出的结果如下：

```javascript
'use strict';

class action1 {
  talk() {
    console.log('jianjian is talking');
  }
  walk() {
    console.log('jianjian is walking');
  }
}

let act = new action1();
act.talk();

```



类 `action1` 中定义了两个函数，`walk` 未使用到， 但是实际结果还是会输出的，而 `action2` 未使用到则不输出。如果打包的时候加了 babel 将 class 语法转换为 ES5 的实现的话就无能为力了，毕竟对动态语法的分析有可能导致更大的问题，有兴趣的同学可以参看[话题讨论](https://github.com/rollup/rollup/issues/349)。这也导致 Tree shaking 在实际的使用场景中显得有些尴尬，毕竟目前能优化的空间上有限。



Rollup 的主要构建流程做了如下几步：

1. 查找。找到对应 `entry` 模块的所有依赖项并导入(可以是 `plugin` 处理后的内容，上面讲到的 `commonjs` 的处理就是如此)，返回模块包含 `code`, `ast`, `map` 等的 JSON 信息。同时存储相关的依赖 `imports`, `externals` 等等。
2. 绑定。将索引与之相关联，来生成完整的依赖代码。
3. 标记。引用所有需要被包含的申明( Tree Shaking 就是在这一步进行处理)
4. 排序。使用增强的拓扑排序对模块进行排序，解决诸如全局命名冲突等问题。

而  Tree Shaking 实际针对 AST 进行删减处理，主要有以下内容，具体可以查找对应 AST 语法。如果错误，欢迎指正。

- classDeclaration

- conditional express

- ExportDefaultDeclaration

- FunctionDeclaration

- isStatement

- SequenceExpression

- Statement

- VariableDeclaration

- VariableDeclarator

  ​

不对上面语法树替换做进一步说明了，主要展开来篇幅有点长。

### Webpack

再来看看 Webpack 的相关处理。同样的先从函数入手。取了核心的编译后的文件，如下：

```javascript
/***/ }),
/* 1 */
/***/ (function(module, __webpack_exports__, __webpack_require__) {

"use strict";
Object.defineProperty(__webpack_exports__, "__esModule", { value: true });
/* harmony import */ var __WEBPACK_IMPORTED_MODULE_0__action_js__ = __webpack_require__(2);

Object(__WEBPACK_IMPORTED_MODULE_0__action_js__["a" /* walk */])()



/***/ }),
/* 2 */
/***/ (function(module, __webpack_exports__, __webpack_require__) {

"use strict";
const walk = () => console.log('walk')
/* harmony export (immutable) */ __webpack_exports__["a"] = walk;

const run = () => console.log('run')
/* unused harmony export run */
```

`walk` 和 `run` 函数还在，区别在于 `run` 函数处有 `unused harmony export run` 的注释，从而在 `UglifyjsWebpackPlugin` (底层用的是 uglify-js) 的时候做删除处理。



类的结果处理类似，这里不具体展开了。



抛除 Webpack 整个复杂的构建过程，在  Tree Shaking  中主要处理的两步，

* 抓取所有模块合并到一个包文件中，任何未导入的文件都不会被导出
* 在执行压缩的过程中，移除 dead code



> 我个人觉得 Webpack 的这个处理方式还是 DCE 而不该定义做  Tree Shaking。不过也就名字而已。



## TODO

待续😂…