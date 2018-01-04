+++
title = "ES6 - 解构赋值"
date = "2015-12-14"
categories = ["js"]
tags = ["es6"]
+++

在解释什么是解构赋值前，我们先来看一下， ES5 中对变量的声明和赋值。

```javascript
var str = 'hello word';
```

左边一个变量名，右边可以是字符串，数组或对象。

ES6 中增加了一种更为便捷的赋值方式。称为 **Destructuring** 。好像大家普遍翻译为解构。解构赋值允许我们将数组或对象的属性赋予给任何变量，该变量的定义语法与数组字面量或对象字面量很相似。举个例子可以直观的说明。

```javascript
let [speak, name] = ['hello', 'destructuring'];
console.log( speak + ' ' + name ); // hello destructuring
```

### 数组的解构赋值
用更加直白的话来描述就是，等号两边保持相同的形式（数组对应数组，对象对应对象），则左边的变量就会被赋予对应的值。如果对应的右边值缺失，缺失部分变量值为 `undefined` ,如果右边值多余，依旧能够正常解构。

```javascript
// ES6 中
let arr = [1,2,3,4,5];
let [el1, el2] = [arr];
// 或者
let [el1, el2] = [1,2,3,4,5];
// el1 => 1, el2 => 2
```

解构赋值也是可嵌套的：

```javascript
let value = [1, 2, [3, 4, 5]];
let [el1, el2, [el3, el4]] = value;
```

同样可以通过简单地在指定位置省略变量来忽略数组中的某个元素：

```javascript
let value = [1, 2, 3, 4, 5];
let [el1, , el3, , el5] = value;
```

更进一步，默认值同样也可以被指定：

```javascript
let [firstName = "John", lastName = "Doe"] = [];
```

ES6中，提供了一种将右侧多余的值以数组的形式赋值给左侧变量的语法——“rest“模式：

```javascript
let [head, ...tail] = [1, 2, 3, 4];
console.log(tail);  // [2, 3, 4]
```

当默认值不存在，变量值就等于 `undefined`

```javascript
let [missing] = [];
console.log(missing);  // undefined
```

这里的数组，指的是部署了 `[Iterator](http://www.2ality.com/2015/02/es6-iteration.html)` 接口的数据结构。

### 对象的解构赋值
和数组的用法相类似，一个主要的不同点是，对象是无次序排列的，所以变量必须和属性名相同。

```javascript
let person = {firstName: "John", lastName: "Doe"};
let {firstName, lastName} = person;
```

解构另一个特性是，变量 keys 可以使用[计算后的属性名](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Object_initializer#Computed_property_names)，但是如果你对这容易混淆的话，不建议使用。

```javascript
let person = {firstName: "John", lastName: "Doe"};
let {firstName: name, lastName} = person;
// `name`变量将会被声明为 `person.firstName` 的值
```

### 函数参数的解构赋值
函数的参数也可以解构赋值。

```javascript
function add({x: 0, y: 0}){
    return x+y;
}
add({1,2}); // 3
```

### null 和 undefined
当尝试解构 `null` 和 `undefined` 的时候会报错。这是因为使用对象赋值模式时，被解构的值必需能够转换成一个对象（object）。大多数的类型都可以转换为一个对象，但null和undefined却并不能被转换。

### 其它
解构赋值的好处有很多，比如

* 两个值的交换
* 函数参数默认值
* 函数返回值
* 正则匹配的返回值
* 快速处理 JSON 数据
* 遍历Map结构

ES6 的解构赋值给 JS 的书写带来了很大的便利性，同时也提高了代码的可读性。但是也需要注意一些问题，避免适得其反。
* 关于圆括号的问题，欢迎翻看[阮一峰老师的博客](http://es6.ruanyifeng.com/#docs/destructuring#圆括号问题)
* 解构的嵌套尽量不要太深
* 函数有多个返回值时，尽量使用对象解构而不用数组解构，避免出现顺序对应问题
* 已声明过的变量不能用于解构

###  浏览器支持
http://kangax.github.io/compat-table/es6/#destructuring
不完全统计：
* IE NO
* FF 38+ (大部分)
* CH/OP NO
* Webkit
* SF 9+, 8部分
* IOS8 部分


PS: 整理的时候发现以前还保留着这样一份文章，无新，基本参考下述文章内容。仅供学习。

### 参考文章
* [变量的解构赋值 - 阮一峰](http://es6.ruanyifeng.com/#docs/destructuring)
* [ES6 In Depth: Destructuring](https://hacks.mozilla.org/2015/05/es6-in-depth-destructuring/)
* [Getting Started with JavaScript ES6 Destructuring](https://strongloop.com/strongblog/getting-started-with-javascript-es6-destructuring/)