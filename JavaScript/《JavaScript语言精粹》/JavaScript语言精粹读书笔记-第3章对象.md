# 第3章对象
> JavaScript的简单类型包括数字，字符串，布尔值（true 和false）、null 值和undefined值，其他所有的值都是对象。

```
var stooge= {
"first-name" : "Jerome",
"last-name": "Howard"
};

var flight = {
  airline: "Oceanic",
  number: 815,
  departure:{
    IATA:"SYD",
    time:"2004",
    city:"Sydeny"
  },
  arrival:{
    IATA:"LAX",
    time:"2004-09-23",
    city: "Los Angeles"
  }
};

```
## 3.2检索
> 检索不存在的成员元素将返回undefined值。检索一个undefined值将会导致typeError异常。
可以采用[]后缀中括住一个字符串表达式的方式。优先考虑使用. 方法。

```
stooge["first-name"] //"Joe"
flight.departure.IATA //"SYD"
```

![image](https://github.com/xpchen-fa/XP-Blog/blob/master/JavaScript/img/%E3%80%8Ajs%E8%AF%AD%E8%A8%80%E7%B2%BE%E7%B2%B9%E3%80%8B-3.2.png?raw=true)

## 3.3更新
> 使用赋值语句。如果之前没有拥有那个属性，该属性被扩充到对象中。

![image](https://github.com/xpchen-fa/XP-Blog/blob/master/JavaScript/img/%E3%80%8Ajs%E8%AF%AD%E8%A8%80%E7%B2%BE%E7%B2%B9%E3%80%8B-3.3.png?raw=true)

## 3.4引用
> 对象通过引用来传递。他们永远不会被拷贝

![image](https://github.com/xpchen-fa/XP-Blog/blob/master/JavaScript/img/%E3%80%8Ajs%E8%AF%AD%E8%A8%80%E7%B2%BE%E7%B2%B9%E3%80%8B-3.4.png?raw=true)

```
var a = {}, b = {}, c = {};
//a、b、c 每个都引用一个不同的空对象
a = b = c = {};
// a、b、c都引用同一个对象

```
## 3.5原型
> 每个对象都连接到一个原型对象，并且可以从中继承属性。所有通过对象字面量创建的对象都连接到Object.prototype,它
是Javascript的标配对象。

> 创建一个新对象时，可以选择一个对象作为它的原型。使用Object增加的create方法，这个方法创建一个使用原对象作为其
 原型的新对象

 ```
 var stooge= {
"first-name" : "Jerome",
"last-name": "Howard"
};
var another_stooge = Object.create(stooge)
stooge.profession = 'actor';
another_stooge.profession;  //'actor'
 ```

## 3.6反射
```
typeof flight.number   //'number'
typeof flight.status   //'string'
typeof flight.arrival  //'object'
typeof flight.manifest //'undefined'
```
原型链中的任何一个属性也会产生值
```
typeof flight.toString     //'functiton'
typeof flight.constructor  //'function'
```
![image](https://github.com/xpchen-fa/XP-Blog/blob/master/JavaScript/img/%E3%80%8Ajs%E8%AF%AD%E8%A8%80%E7%B2%BE%E7%B2%B9%E3%80%8B-3.6.png?raw=true)
> hasOwnProperty方法，如果对象拥有独有的属性，它将返回true。hasOwnProperty方法不会检查原型链。
```
flight.hasOwnProperty('number') //true
flight.hasOwnProperty('constructor')  //false
```
## 3.7枚举

## 3.8删除
> delete运算符会移除对象中确定包含的属性。它不会触及原型链中的任何对象。删除对象的属性可能会让来自原型链中的属性浮现出来

## 3.9减少全局变量污染

