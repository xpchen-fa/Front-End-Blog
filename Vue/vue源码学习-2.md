---
title: Vue
date: 2018-08-13 10:07:30
tags: vue
description:  vue学习
---

#学前须知
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

##Object.defineProperty()
> 方法会直接在一个对象上定义一个新属性，或者修改一个对象的现有属性， 并返回这个对象。如果不指定configurable, writable, enumerable ，则这些属性默认值为false，如果不指定value, get, set，则这些属性默认值为undefined

> 语法
```
Object.defineProperty(obj, prop, descriptor)
//obj: 需要被操作的目标对象
//prop: 目标对象需要定义或修改的属性的名称
//descriptor: 将被定义或修改的属性的描述符
```
*******