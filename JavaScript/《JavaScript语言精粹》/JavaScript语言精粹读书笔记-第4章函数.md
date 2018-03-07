# 第4章 函数

## 4.4方法调用模式
```
var add = function(a,b){
    return a+b;
}
var myObject = {
    value : 0,
    increment: function (inc) {
        this.value += typeof inc === 'number' ? inc : 1;
    }
};

myObject.increment();
document.writeln(myObject.value); //1

myObject.increment(2);
document.writeln(myObject.value); //3
```
## 4.4函数调用模式
> 以函数形式调用函数时，this绑定到全局对象

```
myObject.double = function(){
var that = this
    var helper = function (){
        that.value = add(that.value,that.value);
    };
    helper();//以函数形式调用helper
};

myObject.double();//以方法形式调用double
document.writeln(myObject.value);

```
## 4.5 构造器调用模式
> 如果在函数前面带上new来调用，那么背地里将会创建一个连接到该函数的prototype成员的新对象，同时this会绑定到新对象上。不推荐使用

## 4.6 Apply调用模式
> apply 方法接收两个参数，第1个要绑定给this的值，第2个就是一个参数数组

```
var array = [3,4];
var sum = add.apply(null,array);//7
```

## 4.7参数
> 函数被调用时，会得到一个免费配送的参数arguments数组.arguments拥有一个length属性，但它没有任何数组的方法。

```
var sum = function () {
    var i,sum = 0;
    for(i=0;i<arguments.length;i+=1){
        sum +=arguments[i];
    }
    return sum;
};
document.writeln(sum(4,8,15,16,23,42)); //108
```
## 4.11递归


## 4.12作用域
> 定义在函数中的参数和变量在函数外部是不可见的，函数内部任何位置定义的变量，在该函数内部任何地方都可见。JavaScript缺少块作用域。所以，最好的做法是在函数体的顶部声明函数中可能用到的所有变量。

## 4.13 闭包
> 内部函数能访问外部函数的实际变量而无须复制。
```
//当点击一个节点时，按照预期，应该弹出一个对话框显示节点的序号
var add_the_handlers = function(nodes){
    var helper = function(i){
        return function(e){
            alert(i);
        };
    };
    var i;
    for(i = 0;i<nodes.length;i+=1){
        node[i].onclick = helper(i);
    }
};
```
## 4.14 回调
## 4.15 模块
## 4.16 级联
> 有一些方法没有返回值，如果我们让这些方法返回this而不是undefined，就可以启用级联
## 4.17 柯里化
## 4.18 记忆