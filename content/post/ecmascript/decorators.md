+++
categories = ["一起学 ES6"]
date = "2017-06-18T22:10:05+08:00"
description = ""
tags = []
title = "ES7 Decorators"

+++

Decorators 的概念来源于 Python，可以称为**装饰器**。简单的理解其实它就是一个 `wrapper`，**对目标函数或对象进行修改并返回**。如果从 Java 的角度的话，可以看作是一个 `filter`。

它只是一种语法糖，本质上是对函数的变换。先来看看经典的日志系统吧。

```
class Person {
  @log
  getFullName(firstname, lastname) {
    return `${firstname}${lastname}`
  }
}

function log(target, key, descriptor){
  const value = descriptor.value
  descriptor.value = (...args) => {
    console.log(`Calling ${key} with`, args)
    return value.apply(null, arguments)
  }
  return descriptor
}

const person = new Person()
person.getFullName('LeBron', 'James')
```

`@` 声明的 `@log` 即为 `decorator`，它是一个函数，接收三个参数：

* `target`，需要定义属性的对象
* `key`，属性名
* `descriptor`， 属性描述符，同 `Object.defineProperty` 的 `decorator`

发现了么，其实 `decorators` 底层用的也是 `Object.defineProperty` 的属性来写的。

如果单纯通过外面包的一层函数来实现的话，会是怎么样的？其实也挺简单的。

```
function log(target){
  console.log(target.name, typeof target, arguments)
  return (...args) => {
   console.log(`Calling ${target.name} with`, args)
    target.call(this, [](#).slice.call(arguments))
  }
}

class Person{
  getFullName(firstname, lastname) {
    // …
  }
}
Person.prototype.getFullName = log(Person.prototype.getFullName)
// …
```

实际项目开发中，我们可以通过使用 `babel-plugin-syntax-decorators` 或 `babel-plugin-transform-decorators-legacy` 来转码。让我们来看看 babel 中的实现方式。以下只是一些伪代码。

```
function _applyDecoratedDescriptor(target, property, decorators, descriptor, context) {
  // descriptor 赋值逻辑以及默认属性
  Object['keys'](#)(descriptor).forEach(function (key) {
    desc[key](#) = descriptor[key](#);
  });
  desc.enumerable = !!desc.enumerable;
  desc.configurable = !!desc.configurable;

  desc = decorators.slice().reverse().reduce(function (desc, decorator) {
    return decorator(target, property, desc) || desc;
  }, desc);

  if (desc.initializer === void 0) {
    Object['defineProperty'](#)(target, property, desc);
    desc = null;
  }

  return desc;
}

let Person = (_class = class Person {
  getFullName(firstname, lastname) {
    return `${ firstname }${ lastname }`;
  }
}, (_applyDecoratedDescriptor(_class.prototype, 'getFullName', [log](#), Object.getOwnPropertyDescriptor(_class.prototype, 'getFullName'), _class.prototype)), _class);

```

### 作用 & 结尾
* 用于函数或类的修饰
* 由于用的 Object.defineProperty 特性，所以 descriptor 还可以对对象属性层级的操作
* 实现 `mixin`
* 用于工厂函数

几个有趣的库，对 decorator 做了一些加强。有兴趣的同学可以翻翻。
* [core-decorators.js](https://github.com/jayphelps/core-decorators.js)
* [core-wrappers](https://github.com/akira-cn/core-wrappers)

