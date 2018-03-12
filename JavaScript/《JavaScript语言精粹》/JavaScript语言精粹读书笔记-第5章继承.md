# 第5章 继承
## 1 伪类
> JavaScript的原型存在诸多矛盾。它不直接让对象从其他对象继承，反而插入了一个多余的间接层：通过构造器函数产生对象。

当采用构造器调用模式，即用new前缀去调用一个函数时，函数的执行方式会被修改。如果new运算符是一个方法而不是一个运算符，它可能会这样执行。
```
Function.method('new',function(){
    //创建一个新对象，它继承自构造函数的原型对象
    var that = Object.create(this.prototype);
    //调用构造函数，绑定-this-到新对象上
    var other = this.apply(that,arguments);
    //如果它的返回值不是一个新对象，就返回该新对象
    return (typeof other === 'object' && other)|| that;
});
```

#### 伪类模式继承
```
//定义一个构造器并扩充它的原型
var Mammal = function (name) {
    this.name = name;
};
Mammal.prototype.get_name = function (){
    return this.name;
};
Mammal.prototype.says = function(){
    return this.saying || '';
}

```
```
//构造实例
var myMammal = new Mammal('Herb the Mammal');
var name = myMammal.get_name();// 'Herb the Mammal'
```
> 构造另一个伪类来继承Mammal,这是通过定义它的constructor函数并替换它的prototype为另一个Mammal的实例来实现的
```
var Cat = function (name) {
    this.name = name;
    this.saying = 'meow';
};
//替换Cat.prototype为一个新的Mammal实例
Cat.prototype = new Mammal();
//扩充原型对象，增加purr和get_name方法
Cat.prototype.purr = function(n){
    var i,s = '';
    for(i = 0; i < n; i += 1){
        if(s){
            s += '-';
        }
        s += 'r';
    }
    return s;
}
Cat.prototype.get_name = function() {
    return this.says() + '' + this.name + '' + this.says();
};

var myCat = new Cat('Henrietta');
var says = myCat.says(); //'meow'
var purr = myCat.purr(5); //'r-r-r-r-r'
var name = myCat.get_name();// 'meow Henrietta meow'
```

## 2 对象说明符

## 3 原型
```
var myMammal = {
    name: 'Herb the Mammal',
    get_name: function () {
        return this.name;
    },
    says: function () {
        return this.saying || '';
    }
};
```
> 使用object.create方法构造出跟多实例

```
var myCat = object.create(myMammal);
myCat.name = 'Henrietta';
mayCat.saying = 'meow';
myCat.purr = function(n){
    var i,s = '';
    for(i = 0; i < n; i += 1){
        if(s){
            s += '-';
        }
        s += 'r';
    }
    return s;
};
myCat.get_name = function () {
    return this.says + '' + this.name + '' + this.syays;
};

```

## 4 函数化
