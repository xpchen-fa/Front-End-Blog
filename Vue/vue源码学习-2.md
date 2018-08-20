---
title: Vue
date: 2018-08-13 10:07:30
tags: vue
description:  vue学习
---
#（一）

##学前须知
> vue的数据响应使用了ES5的[Object.defineProperty()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/prototype)

> ECMAScript对象中目前存在的属性描述符主要有两种，数据描述符(数据属性)和存取描述符(访问器属性)，数据描述符是一个拥有可写或不可写值的属性。存取描述符是由一对 getter-setter 函数功能来描述的属性。

数据描述符和存取描述符的属性代表的含义
1. 数据(数据描述符)属性(data descriptors)
    - Configurable: 表示能否通过delete删除此属性，能否修改属性的特性，或能否修改把属性修改为访问器属性，如果直接使用字面量定义对象，默认值为true
    - Enumerable: 表示该属性是否可枚举，即是否通过for-in循环或Object.keys()返回属性，如果直接使用字面量定义对象，默认值为true
    - Writable: 能否修改属性的值，如果直接使用字面量定义对象，默认值为true
    - Value: 该属性对应的值，默认为undefined
2. 访问器(存取描述符)属性(accessor descriptors)
    - Configurable: 和数据属性的[[Configurable]]一样，表示能否通过delete删除此属性，能否修改属性的特性，或能否修改把属性修改为访问器属性，如果直接使用字面量定义对象，默认值为true
    - Enumerable: 和数据属性的[[Configurable]]一样，表示该属性是否可枚举，即是否通过for-in循环或Object.keys()返回属性，如果直接使用字面量定义对象，默认值为true
    - Get: 一个给属性提供 getter 的方法(访问对象属性时调用的函数,返回值就是当前属性的值)，如果没有 getter 则为 undefined。该方法返回值被用作属性值。默认为 undefined
    - Set: 一个给属性提供 setter 的方法(给对象属性设置值时调用的函数)，如果没有 setter 则为 undefined。该方法将接受唯一参数，并将该参数的新值分配给该属性。默认为 undefined

###Object.defineProperty()
> 方法会直接在一个对象上定义一个新属性，或者修改一个对象的现有属性， 并返回这个对象。如果不指定configurable, writable, enumerable ，则这些属性默认值为false，如果不指定value, get, set，则这些属性默认值为undefined

> 语法
```
Object.defineProperty(obj, prop, descriptor)
//obj: 需要被操作的目标对象
//prop: 目标对象需要定义或修改的属性的名称
//descriptor: 将被定义或修改的属性的描述符
```

```
// using __proto__
var obj = {};
Object.defineProperty(obj, 'key', {
  __proto__: null, // no inherited properties
  value: 'static'  // not enumerable
                   // not configurable
                   // not writable
                   // as defaults
});
```
###Eg
> writable为false的时候，对"key"属性重新赋值，它的属性仍然保持不变。
```
var obj = {};
undefined
Object.defineProperty(obj, 'key', {
  __proto__: null, // no inherited properties
  value: 'static'  // not enumerable
                   // not configurable
                   // not writable
                   // as defaults
});
//返回{key: "static"}

/* writable为false的时候，对"key"属性重新赋值，它的属性仍然保持不变 */
obj["key"] = "no static"  //返回"no static"
obj                       //返回{key: "static"}

/* configurable为false的时候，对"key"属性删除失败，它的属性仍然保持不变 */
delete obj["key"]         //false
obj                       //{key: "static"}

/* 代表这个属性能够通过for in或者Object.keys来遍历。 */
Object.keys(obj)         //[]

/* 抛出错误，value只能在数据描述符，get只能出现在存取描述符；不能混用
Object.defineProperty(o, 'conflict', {
  value: 0x9f91102,
  get: function() { return 0xdeadbeef; }
});
// throws a TypeError: value appears only in data descriptors, get appears only in accessor descriptors

```

> 
**********
##Vue构造函数
> 在我们弄清 Vue 构造函数的过程中主要看两个主要的文件，分别是：core/instance/index.js 文件以及 core/index.js 文件，前者是 Vue 构造函数的定义文件，我们一直都叫其 Vue 的出生文件，主要作用是定义 Vue 构造函数，并对其原型添加属性和方法，即实例属性和实例方法。后者的主要作用是，为 Vue 添加全局的API，也就是静态的方法和属性。
    - core/instance 每个 *Mixin 方法的作用其实就是包装 Vue.prototype，在其上挂载一些属性和方法，参考大神的总结[Vue 构造函数整理-原型](http://hcysun.me/vue-design/appendix/vue-prototype.html)
    - core/global-api 它的作用就像它的名字一样，是在 Vue 构造函数上添加全局的API参考总结[Vue 构造函数整理-全局API](http://hcysun.me/vue-design/appendix/vue-global-api.html)
