+++
title = "ES6 - 字符串模板"
date = "2015-12-14"
tags = ["es6"]
+++

## ES6 - 字符串模板

### 写在前面
关于 ES6, 也终于在 2015 年的 7 月 18 号尘埃落定了。虽然说各大浏览器还没有全面的支持，不过这并不妨碍我们一颗想要撸一把的心。在后端，可以使用 Node.js(0.12+)或 io.js, 前端的话，也可以使用[Babel](http://babeljs.io/) 或[Traceur](https://github.com/google/traceur-compiler#what-is-traceur) 进行语法预转义成 ES5使用 。

关于该系列（不知道能不能成为一个系列，总是各种懒），会没有规律的挑选一些内容来学习。欢迎大家积极纠错，留言探讨。

### 模板字符串(template strings)
ES6 中引进的一种新型的字符串字面量语法 - 模板字符串。书面上来解释，模板字符串是一种能在字符串文本中内嵌表示式的字符串字面量。简单来讲，就是增加了变量功能的字符串。

先来看一下以前我们对字符串的使用：

        ```
        /**
         * Before ES6 字符串拼接
         */
        var name = '丁香医生';
        var desc = '丁香医生是面向大众的科普性健康类网站';
        var html = function(name, desc){
            var tpl = '公司名：' + name + '\n'+
                    '简介：'+ desc;
            return tpl;
        }
        ```

而现在：

        ```
        var html = `公司名：${name}
            简介：${desc}`;
        ```

很简洁吧。
引一段 MDN 对于模板字符串的定义：

        模板字符串使用反引号 (` `) 来代替普通字符串中的用双引号和单引号。模板字符串可以包含特定语法(${expression})的占位符。占位符中的表达式和周围的文本会一起传递给一个默认函数，该函数负责将所有的部分连接起来。

而占位符`${}`，可以是任意的 js 表达式（函数或者运算），甚至是另一个模板字符串，会将其计算的结果作为字符串输出。如果模板中需要使用`$,{`等字符串，则需要进行转义。

看个例子就明白了。

        ```
        var x = 1;
        var y = 2;
        `${ x } + ${ y } = ${ x + y}`  // "1 + 2 = 3"
        ```

不同于普通字符串，模板字符串还可以多行书写，模板字符串中所有的空格，新行，缩进都会原样的输出在生成的字符串中。

而单纯的模板字符串还存在着很多的局限性。如：
* 不能自动转义特殊的字符串。（这样很容易引起注入攻击）
* 不能很好的和国际化库配合（即不会格式化特定语言的数字，日期，文字等）
* 没有内建循环语法，（甚至连条件语句都不支持， 只可以使用模板套构的方法）

### 标签模板(tagged template)
为此，引出了标签模板的概念。标签模板则是在反引号前面引入一个标签（tag）。该标签是一个函数，用于处于定制化模板字符串后返回值。就拿上面对特殊字符串举例。

        ```
        /**
         * HTML 标签转义
         * @param {Array.<DOMString>} templateData 字符串类型的tokens
         * @param {...} ..vals 表达式占位符的运算结果tokens
         *
         */
        function SaferHTML(templateData) {
          var s = templateData[0];
          for (var i = 1; i < arguments.length; i++) {
            var arg = String(arguments[i]);
            // Escape special characters in the substitution.
            s += arg.replace(/&/g, "&amp;")
                    .replace(/</g, "&lt;")
                    .replace(/>/g, "&gt;");
            // Don't escape special characters in the template.
            s += templateData[i];
          }
          return s;
        }
        // 调用
        var html = SaferHTML`<p>这是关于字符串模板的介绍</p>`;
        ```

标签函数会接收多个参数。
* 第一个参数是包含了字符串字面量(即那些没有变量替换的值)的数组
* 后面的参数是已经替换后的变量值

改一下例子1

        ```
        var name = '丁香医生';
        var desc = '丁香医生是面向大众的科普性健康类网站';
        tag`公司名：${name}简介：${desc}`
        ```

tag 的参数则分别为 ['公司名：','简介：'], '丁香医生', '丁香医生是面向大众的科普性健康类网站'。

有了此类方法，就大大的增加了控制的权利。如上面说的国际化库甚至循环语句。

### 浏览器兼容性
* 服务器端， io.js 支持
* 浏览器端， FF34+ , chrome 41+
* 移动端 IOS 8, Android 4.4
* IE Tech Preview

### 扩展阅读
* [ECMAScript 6 入门](http://es6.ruanyifeng.com/#docs/string)
* [Getting Literal With ES6 Template Strings](https://developers.google.com/web/updates/2015/01/ES6-Template-Strings)
* [HTML templating with ES6 template strings](http://www.2ality.com/2015/01/template-strings-html.html)
* [模板字符串 - MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/template_strings)
* [ES6 Features系列：Template Strings & Tagged Template Strings](http://segmentfault.com/a/1190000002950341)


