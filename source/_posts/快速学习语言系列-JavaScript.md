---
title: 快速学习语言系列--JavaScript
date: 2022-10-02 11:05:23
tags: 前端
---
<meta name="referrer" content="no-referrer"/>

引言：本文方便一些刚入门前端的同学快速了解JavaScript这个语言。

阅读本篇之前，我希望你先去了解下[一个网页从拿到到展示出来经历了什么-CSDN博客](https://blog.csdn.net/Grace_Luo99/article/details/134180132?spm=1001.2014.3001.5501 "一个网页从拿到到展示出来经历了什么-CSDN博客")
<!--more-->

一，JavaScript语言介绍
----------------

### 1，JavaScript设计用来做什么

JavaScript诞生于1993年，当时网页还只能是静态网页，缺乏在浏览器中加载网页后的动态行为能力。最初的设想是需要一种可以插在HTML的胶水语言，动态组装图片和插件之类的组件，于是JavaScript应运而生。

至于为什么叫这个名字，是因为当时有人认为用Java更合适，争执过程中新的语言取名为JavaScript。但是两种语言之间没有任何关系，就像雷锋和雷峰塔的关系，哈哈。

不同浏览器厂家打造了不同的JS运行时，最著名的莫过于谷歌chrome的V8。“JS运行时”可以简单理解为在JS解释器基础上增加一层跟浏览器操作相关的定制化API的打包体，有了这个东西，你就可以通过其API动态修改网页了。

我们继续来扩展，后续大家发现JS用在服务端也可以加速开发，于是就有个工程师给服务器程序员们写了一个服务器端JS运行时，这就是大名鼎鼎的Node.js。

所以你现在知道了，我们经常听到的V8和Node.js都是JS运行时，当前不止这两种，甚至还有很多自定义的JS Runtime，毕竟作为胶水语言，JS的跨语言基因决定了他的使用场景有很多。

### 2，JavaScript特征

#### 1）JS定义

根据维基百科的定义，

**JavaScript**（通常缩写为**JS**）是一门[基于原型](https://zh.wikipedia.org/wiki/%E5%9F%BA%E4%BA%8E%E5%8E%9F%E5%9E%8B%E7%BC%96%E7%A8%8B "基于原型")和[头等函数](https://zh.wikipedia.org/wiki/%E5%A4%B4%E7%AD%89%E5%87%BD%E6%95%B0 "头等函数")的多范式[高级](https://zh.wikipedia.org/wiki/%E9%AB%98%E7%BA%A7%E8%AF%AD%E8%A8%80 "高级")[解释型](https://zh.wikipedia.org/wiki/%E7%9B%B4%E8%AD%AF%E8%AA%9E%E8%A8%80 "解释型")[编程语言](https://zh.wikipedia.org/wiki/%E7%BC%96%E7%A8%8B%E8%AF%AD%E8%A8%80 "编程语言")，它支持[面向对象](https://zh.wikipedia.org/wiki/%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E7%A8%8B%E5%BA%8F%E8%AE%BE%E8%AE%A1 "面向对象")程序设计、[指令式编程](https://zh.wikipedia.org/wiki/%E6%8C%87%E4%BB%A4%E5%BC%8F%E7%BC%96%E7%A8%8B%E8%AF%AD%E8%A8%80 "指令式编程")和[函数式编程](https://zh.wikipedia.org/wiki/%E5%87%BD%E6%95%B0%E5%BC%8F%E7%BC%96%E7%A8%8B%E8%AF%AD%E8%A8%80 "函数式编程")。

我们可以看到JS语言的一些基础特征：**解释性语言，基于原型和头等函数，支持面向对象编程**。

后续我们将重点关注这些部分。

补充一些：

a）因为JS语言是非常短时间内被设计出来的，所以决定了它有不完善的部分，后面我们先忽略这些部分。

#### 2）JS标准

JS设计背景说明它很长一段时间都是运行在浏览器中的脚本语言，浏览器大战的历史不再赘述，但是由此我们可知JS一定得符合某种设计标准，这就是[ECMAScript](https://zh.wikipedia.org/wiki/ECMAScript "ECMAScript")标准，简称ES标准。JavaScript也是ECMAScript最著名的实现之一。目前最新的ES标准是 2015 年ECMAScript 6，简称ES6，目前主流浏览器基本都支持**ES6**。

![](https://img-blog.csdnimg.cn/1bf18d7b10674e19a677429693820f20.png)

二，JavaScript是怎么运行起来的
--------------------

上文可知，JS Runtime是我们和JS打交道的大合集，不同的JS使用场景对应不同的JS Runtime，比如客户端场景的V8和服务端场景的Node.js的JS Runtime API肯定是不用的，其实其内部的JS解释器也有很多种，他们针对不同的针对场景有不同的优化策略。我们这里以V8 JS Runtime为例子讲解： ![](https://img-blog.csdnimg.cn/b81848bb57ae4c308f37e214a4b9106b.png)

一个客户端的运行时一般包括用户API，事件系统，JS引擎（JS解释器+HTML解释器+CSS解释器）。
具体来说，

1）JS引擎内部分为堆（存Object）和调用栈（存执行上下文）。

2）Web API提供操作Dom的接口，如Window对象，documents对象等；提供一些方便使用的系统接口，如Fetch，Timer等；提供一些方便调试的接口，如console.log

3）事件Loop，JS Runtime里是用了两个队列Callback和Microstack Queue，两者的区别就是优先级不同。但都是队列有内容就唤醒处理，没有就挂起的策略。

![](https://img-blog.csdnimg.cn/ba6a5e69da8e4adf9b8fa6ee9aedbaf3.png)

那扩展下，Node.js的JS Runtime有什么不同呢? 首先肯定是没有Web API这一趴，那你想想Node.js的使用场景，他作为后台的胶水语言，肯定会涉及跟后台语言的跨语言通信。所以会有一个C++ Bindings和Thread Pool。后面我们会专门开文讲讲跨语言，这里先挖个坑。

![](https://img-blog.csdnimg.cn/5806704f804043c3b2597de9747fefab.png)

到这里我们总结下：

1.  JS是一种最初被设计用来动态更新网页的胶水语言。
2.  JS是运行在JS Runtime（JS运行时）中的，最著名的JS Runtime是V8和Node.js，前者使用场景主要是浏览器（或者说客户端），后者是服务端。
3.  JS Runtime的构成是JS引擎（JS解释器不太完善是因为现在浏览器也支持JIT了）+ Web API（v8） + 事件队列。

三，JavaScript语言的重要用法
-------------------

虽然到目前为止，你已经知道JS是干啥的，以及怎么干的了。但是你还不会写或者看懂一段JS脚本，这就需要了解JS的用法，以下不涉及过多细节，更希望你能在10min内对这个语言有个系统认知。具体使用中推荐到官方文档查询[W3Schools Online Web Tutorials](https://www.w3schools.com/ "W3Schools Online Web Tutorials")

### 1，数据类型

JS的数据类型分为基本类型和复杂类型，两者的区别是存储位置不同。基本类型存储在栈上，引用类型存储在堆上。

#### 1）基本类型

基本类型主要为以下6种：

*   Number
*   String
*   Boolean
*   Undefined
*   Null
*   Symbol

##### Number

整形，浮点数，都属于Number类型。在数值类型中，存在一个特殊数值`NaN`，意为“不是数值”，用于表示本来要返回数值的操作失败了（而不是抛出错误）。
```
console.log(0/0); // NaN
console.log(-0/+0); // NaN
```
##### String

字符串可以使用双引号（"）、单引号（'）或反引号（`）标示，多行必须要用反引号。字符串是不可变的，意思是一旦创建，它们的值就不能变了。
```
 let lang = "Java";
 lang = lang + "Script";  // 先销毁再创建
```
##### Boolean

Boolean（布尔值）类型有两个字面值： true 和false  
通过Boolean可以将其他类型的数据转化成布尔值  
规则如下：
```
数据类型      				转换为 true 的值      				转换为 false 的值
 String        				 非空字符串          					"" 
 Number 				非零数值（包括无穷值）						0 、 NaN 
 Object 					 任意对象 							   null
Undefined 					N/A （不存在） 						undefined
```

##### Undefined和Null

`Undefined` 类型只有一个值，就是特殊值 `undefined`。当使用 `var`或 `let`声明了变量但没有初始化时，就相当于给变量赋予了 `undefined`值。

`Null`类型同样只有一个值，即特殊值 `null。用来给变量赋初值。`

`两者的区别在于一个是定义了没初始化的状态，一个是用来给初始化变量的。`

```
let car = null;
console.log(typeof car); 
object"console.log(null == undefined); // true
```
##### Symbol

Symbol （符号）是原始值，且符号实例是唯一、不可变的。符号的用途是确保对象属性使用唯一标识符，不会发生属性冲突的危险。了解下即可。

#### 2）引用类型

复杂类型统称为`Object`，我们这里主要讲述下面三种：

*   Object
*   Array
*   Function

##### Object

一组键值对，值可以是任意类型，包括函数。
```
let person = {
    name: "Nicholas",
    "age": 29,
    5: true
};
```
##### Array

相当于默认key是0,1,2..的只有value的Obejct, value可以是任意类型。

Array是动态大小的，会随着数据添加而自动增长。
```
let colors = ["red", 2, {age: 20 }];
colors.push(2)
```
##### Function

函数也是一种Object，存在三种常见的表达方式：

函数存在三种常见的表达方式：

*   函数声明
```
// 函数声明
function sum (num1, num2) {
    return num1 + num2;
}

```
*   函数表达式
```
let sum = function(num1, num2) {
    return num1 + num2;
};
```
*   箭头函数

函数声明和函数表达式两种方式
```
let sum = function(num1, num2) {
    return num1 + num2;
};
```
### 2，Array的常用方法

![](https://img-blog.csdnimg.cn/e29505119b1b43d8970d4bf01d1cb13a.png)

### 3，JS的类型转换

JS是弱类型语言，运行的时候才能确认类型。这时各种运算符对数据类型是有要求的，如果运算子的类型与预期不符合，就会触发类型转换机制

常见的类型转换有：

*   强制转换（显示转换）
*   自动转换（隐式转换）

#### 1）显示转换

*   Number()
*   parseInt()
*   String()
*   Boolean()

#### 2）隐式转换

比较运算（==、!=、>、<）、if、while需要布尔值地方  
算术运算（+、-、*、/、%）  
除了上面的场景，还要求运算符两边的操作数不是同一类型

### 4，JS的this指针

#### 1) 什么是this指针

`this` 关键字是函数运行时自动生成的一个内部对象，只能在函数内部使用，总指向调用它的对象。this指向的是运行时的调用对象，而不是定义时的上下文，运行时才会真正的绑定this，一旦被绑定，就不能再修改了。this绑定发生在常见函数上下文的时候，下文会涉及。

#### 2）this的绑定方式

this的绑定方式分为默认绑定，隐式绑定，硬绑定和构造函数绑定

##### a)默认绑定

全局环境中定义`person`函数，内部使用`this`关键字.
```
var name = 'Jenny';
function person() {
    return this.name;
}
console.log(person());  //Jenny
```

上述代码输出`Jenny`，原因是调用函数的对象在游览器中位`window`，因此`this`指向`window`，所以输出`Jenny`

注意：

严格模式下，不能将全局对象用于默认绑定，this会绑定到`undefined`，只有函数运行在非严格模式下，默认绑定才能绑定到全局对象

##### b)隐式绑定

函数还可以作为某个对象的方法调用，这时`this`就指这个上级对象。
```
function test() {
  console.log(this.x);
}

var obj = {};
obj.x = 1;
obj.m = test;

obj.m(); // 1
```

这个函数中包含多个对象，尽管这个函数是被最外层的对象所调用，`this`指向的也只是它上一级的对象

    var o = {    a:10,    b:{        fn:function(){            console.log(this.a); //undefined        }    }}o.b.fn();

上述代码中，`this`的上一级对象为`b`，`b`内部并没有`a`变量的定义，所以输出`undefined`

    var o = {    a:10,    b:{        a:12,        fn:function(){            console.log(this.a); //undefined            console.log(this); //window        }    }}var j = o.b.fn;j();

此时`this`指向的是`window`，这里的大家需要记住，`this`永远指向的是最后调用它的对象，虽然`fn`是对象`b`的方法，但是`fn`赋值给`j`时候并没有执行，所以最终指向`window。这个跟后文讲到的作用域链有关。`

##### c)硬绑定

`apply()、call()、bind()`是函数的一个方法，作用是改变函数的调用对象。它的第一个参数就表示改变后的调用这个函数的对象。因此，这时`this`指的就是这第一个参数。

    var x = 0;function test() {　console.log(this.x);} var obj = {};obj.x = 1;obj.m = test;obj.m.apply(obj) // 1

##### d)构造函数绑定

 通过构建函数`new`关键字生成一个实例对象，此时`this`指向这个实例对象。

    function test() {　this.x = 1;} var obj = new test();obj.x // 1

上述代码之所以能过输出1，是因为`new`关键字改变了`this`的指向。

如果构造函数返回的是一个对象，则this绑定到返回的这个对象上。（返回非对象，如简单类型，null等，则依然默认绑定在这个实例对象上）

### 5，JS执行上下文和执行栈

#### 1）什么是执行上下文Execution Context

执行上下文是一种对`Javascript`代码执行环境的抽象概念，也就是说只要有`Javascript`代码运行，那么它就一定是运行在执行上下文中。

执行上下文的类型分为三种：

*   全局执行上下文：只有一个，浏览器中的全局对象就是 `window`对象，`this` 指向这个全局对象
*   函数执行上下文：存在无数个，只有在函数被调用的时候才会被创建，每次调用函数都会创建一个新的执行上下文
*   Eval 函数执行上下文： 指的是运行在 `eval` 函数中的代码，很少用而且不建议使用

下面给出全局上下文和函数上下文的例子：

![](https://img-blog.csdnimg.cn/img_convert/c606a5592bb46c94663e3cde0a4115e3.png)

紫色框住的部分为全局上下文，蓝色和橘色框起来的是不同的函数上下文。只有全局上下文（的变量）能被其他任何上下文访问

可以有任意多个函数上下文，每次调用函数创建一个新的上下文，会创建一个私有作用域，函数内部声明的任何变量都不能在当前函数作用域外部直接访问。

#### 2）执行上下文的声明周期

执行上下文的生命周期包括三个阶段：创建阶段 → 执行阶段 → 回收阶段

##### 创建阶段

创建阶段即当函数被调用，但未执行任何其内部代码之前

创建阶段做了三件事：

*   确定 this 的值，也被称为 `This Binding`
*   LexicalEnvironment（词法环境） 组件被创建
*   VariableEnvironment（变量环境） 组件被创建

伪代码如下：

    ExecutionContext = {    ThisBinding = <this value>,     // 确定this   LexicalEnvironment = { ... },   // 词法环境  VariableEnvironment = { ... },  // 变量环境}

##### This Binding

确定`this`的值我们前面讲到，`this`的值是在执行的时候才能确认，定义的时候不能确认

##### 词法环境

词法环境有两个组成部分：

*   全局环境：是一个没有外部环境的词法环境，其外部环境引用为`null`，有一个全局对象，`this` 的值指向这个全局对象
    
*   函数环境：用户在函数中定义的变量被存储在环境记录中，包含了`arguments` 对象，外部环境的引用可以是全局环境，也可以是包含内部函数的外部函数环境
    

伪代码如下：

    GlobalExectionContext = {  // 全局执行上下文  LexicalEnvironment: {       // 词法环境    EnvironmentRecord: {     // 环境记录      Type: "Object",           // 全局环境      // 标识符绑定在这里       outer: <null>           // 对外部环境的引用  }  } FunctionExectionContext = { // 函数执行上下文  LexicalEnvironment: {     // 词法环境    EnvironmentRecord: {    // 环境记录      Type: "Declarative",      // 函数环境      // 标识符绑定在这里      // 对外部环境的引用      outer: <Global or outer function environment reference>    }  }

##### 变量环境

变量环境也是一个词法环境，因此它具有上面定义的词法环境的所有属性

在 ES6 中，词法环境和变量环境的区别在于前者用于存储函数声明和变量（ `let` 和 `const` ）绑定，而后者仅用于存储变量（ `var` ）绑定

举个例子

    let a = 20;  const b = 30;  var c; function multiply(e, f) {   var g = 20;   return e * f * g;  } c = multiply(20, 30);

执行上下文如下：

    GlobalExectionContext = {   ThisBinding: <Global Object>,   LexicalEnvironment: {  // 词法环境    EnvironmentRecord: {        Type: "Object",        // 标识符绑定在这里        a: < uninitialized >,        b: < uninitialized >,        multiply: < func >      }      outer: <null>    },   VariableEnvironment: {  // 变量环境    EnvironmentRecord: {        Type: "Object",        // 标识符绑定在这里        c: undefined,      }      outer: <null>    }  } FunctionExectionContext = {       ThisBinding: <Global Object>,   LexicalEnvironment: {      EnvironmentRecord: {        Type: "Declarative",        // 标识符绑定在这里        Arguments: {0: 20, 1: 30, length: 2},      },      outer: <GlobalLexicalEnvironment>    },   VariableEnvironment: {      EnvironmentRecord: {        Type: "Declarative",        // 标识符绑定在这里        g: undefined      },      outer: <GlobalLexicalEnvironment>    }  }

留意上面的代码，`let`和`const`定义的变量`a`和`b`在创建阶段没有被赋值，但`var`声明的变量从在创建阶段被赋值为`undefined`

这是因为，创建阶段，会在代码中扫描变量和函数声明，然后将函数声明存储在环境中

但变量会被初始化为`undefined`(`var`声明的情况下)和保持`uninitialized`(未初始化状态)(使用`let`和`const`声明的情况下)

这就是变量提升的实际原因。

#### 执行阶段

在这阶段，执行变量赋值、代码执行

如果 `Javascript` 引擎在源代码中声明的实际位置找不到变量的值，那么将为其分配 `undefined` 值

#### 回收阶段

执行上下文出栈等待虚拟机回收执行上下文

#### 3）执行栈

执行栈，也叫调用栈，具有 LIFO（后进先出）结构，用于存储在代码执行期间创建的所有执行上下文

![](https://img-blog.csdnimg.cn/img_convert/6062ad0536bd55e9cf9410f32d506d58.png)

当`Javascript`引擎开始执行你第一行脚本代码的时候，它就会创建一个全局执行上下文然后将它压到执行栈中

每当引擎碰到一个函数的时候，它就会创建一个函数执行上下文，然后将这个执行上下文压到执行栈中

引擎会执行位于执行栈栈顶的执行上下文(一般是函数执行上下文)，当该函数执行结束后，对应的执行上下文就会被弹出，然后控制流程到达执行栈的下一个执行上下文

### 6，JS作用域链

#### 1）js的作用域

js的作用域分为块作用域，函数作用域，全局作用域。

其中浏览器中的全局作用域可以理解为window的Object作用域，ES6引入的let和const是存在于块作用域的（var针对的是函数作用域，严格模式下，全局变量也必须声明var，否则会报错）。

#### 2）js的作用域链

首先作用域链的目的是为了方便查找变量。

当在`Javascript`中使用一个变量的时候，首先`Javascript`引擎会尝试在当前作用域下去寻找该变量，如果没找到，再到它的上层作用域寻找，以此类推直到找到该变量或是已经到了全局作用域

如果在全局作用域里仍然找不到该变量，它就会在全局范围内隐式声明该变量(非严格模式下)或是直接报错。

函数定义的时候生成每个函数的\[\[scope\]\]记录当前的词法环境，生成一组全局的作用域链。当函数执行的时候会根据函数的执行上下文去调整作用域链。

### 7，JS闭包

#### 1）闭包是什么，怎么实现

一个函数和对其周围状态（lexical environment，词法环境）的引用捆绑在一起（或者说函数被引用包围），这样的组合就是闭包（closure）

也就是说，闭包让你可以在一个内层函数中访问到其外层函数的作用域

在 `JavaScript`中，每当创建一个函数，闭包就会在函数创建的同时被创建出来，作为函数内部与外部连接起来的一座桥梁

下面给出一个简单的例子

    function init() {    var name = "Mozilla"; // name 是一个被 init 创建的局部变量    function displayName() { // displayName() 是内部函数，一个闭包        alert(name); // 使用了父函数中声明的变量    }    displayName();}init();

`displayName()` 没有自己的局部变量。然而，由于闭包的特性，它可以访问到外部函数的变量

#### 2）js闭包的应用场景

任何闭包的使用场景都离不开这两点：

*   创建私有变量
*   延长变量的生命周期

注意闭包的使用会影响外层函数词法环境（静态作用域链）的及时释放。要注意评估影响。

### 8，JS原型和原型链

#### 1）原型

每个函数都有一个特殊的属性：原型，原型指向一个原型对象。

当试图访问一个对象的属性时，它不仅仅在该对象上搜寻，还会搜寻该对象的原型对象，以及该对象的原型对象的原型对象，依次层层向上搜索，直到找到一个名字匹配的属性或到达原型链的末尾。

    function Person(name) {    this.name = name;    this.age = 18;    this.sayName = function() {        console.log(this.name);    }}// 第二步 创建实例var person = new Person('person')

首先搞清楚几个概念：构造函数，实例化，对象。

Person是一个函数，构造函数。new Person叫实例化，person叫Person实例化出来的对象。

下面看一个函数的原型指向的原型对象长什么样：

    function doSomething(){}console.log( doSomething.prototype );

控制台输出

    {    constructor: ƒ doSomething(),    __proto__: {        constructor: ƒ Object(),        hasOwnProperty: ƒ hasOwnProperty(),        isPrototypeOf: ƒ isPrototypeOf(),        propertyIsEnumerable: ƒ propertyIsEnumerable(),        toLocaleString: ƒ toLocaleString(),        toString: ƒ toString(),        valueOf: ƒ valueOf()    }}

上面这个对象，就是大家常说的原型对象

可以看到，原型对象有一个自有属性`constructor`，这个属性指向该函数，如下图关系展示

![](https://img-blog.csdnimg.cn/img_convert/c4d4d52fbab8b6f39423d0380537acdb.png)

#### 2）原型链

几个概念：

1）对象的\_\_proto\_\_属性和函数.prototype都指向原型对象。

2）一个方法的\_\_proto\_\_指向的是Function anonymous，他对应的构造函数是Function

![](https://img-blog.csdnimg.cn/5052c705dfe34790986e038fdb96bd88.png)

现在来看继承链是什么，以及他的作用：

原型对象也可能拥有原型对象，并从中继承方法和属性，一层一层、以此类推。这种关系常被称为原型链 (prototype chain)，它解释了为何一个对象会拥有定义在其他对象中的属性和方法，也就是继承关系。

![](https://img-blog.csdnimg.cn/307633f0a40a417ea810c0ff5f2e06fc.png)

### 9，JS继承

JS的继承是基于原型链实现的，在ES6的Class关键字出来之前，有几种经典的实现方式：

*   原型链继承
    
*   构造函数继承（借助 call）
    
*   组合继承
    
*   原型式继承
    
*   寄生式继承
    
*   寄生组合式继承
    

作者不再展开。ES6的Class关键字一出，就让继承变的非常简单，写法跟java，c++非常类似。Class内部的extends实现其实还是基于原型链，感兴趣的同学可以自己Babel把代码转换到ES5看看。

    // 货车class Truck extends Car{    constructor(color,speed){        super(color,speed)        this.Container = true // 货箱    }}

### 10，JS的内存管理

JS是自动内存管理，同时因为他胶水语言的特征，一个脚本完成之后就释放了，所以一般不太需要关注他的内存管理问题。

JS内部的GC算法跟java类似，也是引用计数+标记清除。

常见的一些内存泄露或者说良好的编程风格是：

1.  使用stick严格模式，避免意外的全局变量
    
2.  谨慎闭包的使用（不是说不用，是要关注下会不会有引用问题）
    
3.  `DOM`元素的引用不用的时候及时清理，包括addListener和removeListener成对使用。
    

### 三，浏览器的基础应用

### 1，JS事件循环

[一个网页从拿到到展示出来经历了什么-CSDN博客](https://blog.csdn.net/Grace_Luo99/article/details/134180132 "一个网页从拿到到展示出来经历了什么-CSDN博客")讲到了浏览器中的事件系统是由两个事件队列组成的，区别是优先级不同。

我们把这两个队列分别微任务队列与宏任务队列，前者优先级高，间隔时间短，后者优先级低，间隔时间长。比如常见的Promise.then会被放到微任务队列中，而setTimeout/setInterval，UI rendering/UI事件会被放到宏任务队列中。

### 2，操作DOM

因为Jquery库，`vue`，`Angular`，`React`等框架的出现，我们很少需要直接去操作DOM，会有封装好的API，但是我们要了解一点，就是直接操作DOM都是通过操作DOM的根节点documents对象来实现的。

### 3，BOM

`BOM` (Browser Object Model)，浏览器对象模型，提供了独立于内容与浏览器窗口进行交互的对象

其作用就是跟浏览器做一些交互效果,比如如何进行页面的后退，前进，刷新，浏览器的窗口发生变化，滚动条的滚动，以及获取客户的一些信息如：浏览器品牌版本，屏幕分辨率

浏览器的全部内容可以看成`DOM`，整个浏览器可以看成`BOM`。区别如下：

![](https://img-blog.csdnimg.cn/img_convert/d425c48d5afb50d46bbb6feec0f375aa.png)

常见的几个BOM对象：`window，`location，navigator，history

### 3，Ajax

#### 1，什么是Ajax

`AJAX`全称(Async Javascript and XML)

即异步的`JavaScript` 和`XML`，是一种创建交互式网页应用的网页开发技术，可以在不重新加载整个网页的情况下，与服务器交换数据，并且更新部分网页。

Ajax是一种技术的通用说法，我们可以通过`XmlHttpRequest`对象（或者ES6提供的Fetch API）来向服务器发异步请求，从服务器获得数据，然后用`JavaScript`来操作`DOM`而更新页面。

#### 2，跨域问题

Ajax会带来一个同源限制的问题，就是说js脚本不能访问非同源的url（协议，域名，端口都一样叫同源地址）。经典的解决方案是JsonP和Cors，前者的优势是兼容性好，在一些老旧的浏览器种也可以运行，但它的缺点也非常明显，那就是只能进行 GET 请求；后者使用更方便，但是需要浏览器支持Html5。

### 4，常用的前端框架

`经典的三驾马车，vue`，`Angular`，`React`框架。

React是这三个最早发展起来的，Facebook 设计的，Twitter、Instagram使用的就是React。React的使用者最多，然后生态环境也比较好。

`Angular(V2版本)是`由 Google 开发，生态环境是typescript。

Vue是个人开发者尤雨溪大神开发出来的，三个里面最轻量的，基于MVVM设计模式。根据github数据，目前React和Vue的热度是最高的，开发者最多。

我们可以根据团队需求和风格选择一个来上手。

四，如何优化你的网页性能
------------

### 1，优化网络资源

#### 1）控制资源大小和下载次数

*   使用webpack(一个`JavaScript`应用程序的静态模块打包工具),将资源都优化打包到一起，减少下载次数。
    
*   cssSprite，将用到的很多小图片打包成一个图集，一次性下载后再切割。图集的思想在很多客户端场景都会用到。

#### 2）使用缓存

对一些频繁使用的资源存储到本地，使用cookie等等。

### 2，优化js逻辑

#### 1）异步js

一些耗时的逻辑async/await异步执行，不阻塞主线程; 

#### 2）防抖和节流

这两者本质上都是优化高频率执行代码的一种手段。

*   节流: n 秒内只运行一次，若在 n 秒内重复触发，只有一次生效。如付款时连续点击付款按钮。
*   防抖: n 秒后在执行该事件，若在 n 秒内被重复触发，则重新计时。如统计用户滚动页面的行为，避免用户不断滚动，频繁发送网络请求的行为。

### 3，优化css

#####  a) 内联首屏关键CSS

在打开一个页面，页面首要内容出现在屏幕的时间影响着用户的体验，而通过内联`css`关键代码能够使浏览器在下载完`html`后就能立刻渲染

而如果外部引用`css`代码，在解析`html`结构过程中遇到外部`css`文件，才会开始下载`css`代码，再渲染

所以，`CSS`内联使用使渲染时间提前

注意：但是较大的`css`代码并不合适内联（初始拥塞窗口、没有缓存），而其余代码则采取外部引用方式

##### b) 异步加载CSS

##### c) 合理使用选择器

*   不要嵌套使用过多复杂选择器，最好不要三层以上
*   使用id选择器就没必要再进行嵌套
*   通配符和属性选择器效率最低，避免使用

##### d) 减少使用昂贵的属性

如`box-shadow`/`border-radius等`

##### e) @import和link，优选link 

##### f) 优选不引起重绘重排的动画，如transform动画

### 4，优化html

减少DOM层数

五，总结和学习资源
---------

### 总结：

1，本文介绍了JS的产生背景和重要语言特性。

2，介绍了JS Runtime及v8和nodejs的区别。

3，介绍了JS的重要用法，如数据类型及类型转换，执行栈，this，作用域链，闭包，原型链，继承，内存管理。

4，介绍了浏览器的基础应用，如JS事件系统，DOM操作，BOM，Ajax，主流前端框架。

5，介绍了优化网页的一些点，如优化网络资源，优化js逻辑，优化css和html。目的是让网页展示和交互更流程。

### 学习资源：

[W3Schools Online Web Tutorials](https://www.w3schools.com/ "W3Schools Online Web Tutorials")

[JavaScript教程 - 廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/1022910821149312 "JavaScript教程 - 廖雪峰的官方网站")