# TypeScript
## 一、认识TypeScript
> TypeScript是JavaScript类型的超集，它可以编译成纯JavaScript。TypeScript可以在任何浏览器、任何计算机和任何操作系统上运行，并且是开源的。

> 总结：TS是对js的优化和升级，弥补一些js语法设计上的缺陷，让Js在工具下的帮助下转化为某种“强”类型的语言，方便开发人员的书写，重构。

特性：[参考](https://juejin.im/post/5d6e1294f265da03dc078b94)
- TS也还是JS 。TS从JS开始，从JS结束。TS采用JS的基础结构，因此你只需要知道JS就能去用TS了。所有TS代码为了能够跑起来都会转成JS等效代码。
- TS支持其它JS库 。 可以从任何JS代码里运行编译后的TS。由TS生成的JS代码能够使用所有已有的JS类库，工具和库。
- JS是TS 。 这个意思是任何有效的.js文件都能够重命名成.ts文件，并且和其他TS文件一起编译。
- TS是可移植的 。TS能够运行在任何JS可以运行的地方（跨浏览器，设备，操作系统）。

## 二、为什么使用TypeScript 
> [参考](https://ts.xcatliu.com/introduction/what-is-typescript)
- 增加了代码的可读性和可维护性
    1. 类型系统实际上是最好的文档，大部分的函数看看类型的定义就可以知道如何使用了
    2. JS是一门解释型语言。因此，需要跑起来后来测试它的正确性。TS可以在编译阶段就发现大部分错误，这有助于在脚本运行前就暴露错误。
    3. 增强了编辑器和 IDE 的功能，包括代码补全、接口提示、跳转到定义、重构等。主流的编辑器都支持 TypeScript。Visual Studio Code效果更佳，它是内置了 TypeScript支持，另外它本身也是用TypeScript 编写的。
    4. TS支持面向对象编程概念，比如类，接口，继承，封装等
- 包容性
    1. TypeScript 是 JavaScript 的超集，.js 文件可以直接重命名为 .ts 即可
    2. 强静态类型。即使不显式的定义类型，也能够自动做出类型推论（TS通过TLS(TypeScript Language Service)提供可选的静态类型和类型推断系统。TLS能够推断出无类型变量的类型。）
    3. 兼容第三方库，即使第三方库不是用 TypeScript 写的，也可以编写单独的类型文件供 TypeScript 读取（描述文件（.d.ts文件），作用是从js中抽象出组件描述。）。
    4. 即使 TypeScript 编译报错，也可以生成 JavaScript 文件
## 三、安装
1. TypeScript 的命令行工具安装方法
``
 npm install -g typescript
``
2. 编译一个 TypeScript
``
 tsc hello.ts
``

## 四、概念巩固
- 类（class），定义事物的抽象特性（属性和方法）；
- 抽象类（Abstract class），可以让其他类继承的基类（不允许实例化），抽象方法必须在子类中实现；
- 接口（interface）：不同类公有属性或方法，可抽象成一个接口，接口可以被类实现（implements）。类的继承只能是一个，但可以被实现多次；
- 存取器（setter and getter）：属性的赋值与读取；
- 修饰符（modifiers）：是一些关键字，限定成员或类型的性质（public、protect、private）,Typescript 支持；
- 对象（object），类的实例，通过 new 生成实现；
- 面向对象（OOP）特性：封装、继承和多态；
- 封装（encapsulation）：将对数据属性的处理细节隐藏起来，对外只暴露接口。外部调用不需要知道细节，就能访问该对象，这也保证了外部无法改变内部数据属性；
- 继承（inheritance）：子类继承父类，子类拥有父类所有特性，还可以有自己具体的特性；
- 多态（polymorphism）：由继承产生不同的类，对同一方法有不同的响应；