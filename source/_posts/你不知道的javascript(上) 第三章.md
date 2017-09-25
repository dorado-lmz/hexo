---
title: 你不知道的javascript(上) 第三章
date: 2017-08-08 11:03:09
tags: 你不知道的javascript
---

## 理解闭包
> 闭包就是一个作用域的外部变量保持着对这个作用域的引用，这个引用就叫做闭包。

```javascript
function foo() {
     var a = 2;
     function bar() {
         console.log( a );
     }
     return bar;
}
var baz = foo();
baz(); // 2 这就是闭包
```
因为baz本身是foo作用域之外的变量，根据作用域的规则，baz本身是不可以访问到foo作用域内的变量的。

本来在通常的情况下，在函数执行完后，如果函数在后面不再使用的时候，会进行垃圾回收机制，把foo函数内的作用域销毁，把不再使用到的内存释放掉。

但是正因为闭包，这个作用域没有被销毁。原因是foo()函数执行之后的返回值bar，就是bar内部的函数即function bar(){console.log( a );}，把这个赋值给了baz，baz在被调用时（baz()）因为baz中用到了变量a，而变量a是在foo的作用域中，所以baz必须得拥有foo函数的作用域闭包才能够正常运行，所以foo的作用域不会被销毁会一直存在，以便baz之后调用的时候能正常运行。

这么说还是很绕，简单讲就是foo函数外面的变量baz要用到foo作用域里面的东西，这就叫baz拥有foo的闭包。

在定时器、时间监听器、Ajax请求、跨窗口通信、或者任何其他的异步（或者同步）任务中，只要是用了回调函数，实际上就是在使用闭包。

## 理解闭包
```javascript
for (var i=1; i<=5; i++) {
     setTimeout( function timer() {
         console.log( i );
     }, i*1000 );
}
```
本来这段代码的预期是，分别输出1~5，每秒一次，每次一个。但是实际上他会每秒一个的频率输出五次6。

造成这样的原因，书上讲的是循环中的五个函数是在各个迭代中分别定义的，但是他们都被封闭在一个共享的全局作用域当中，实际只有一个i。

解决办法是运用IIFE，并在每次循环中的IIFE内保存i的值。
```javascript
for (var i=1; i<=5; i++) {
     (function() {
         var j = i;
         setTimeout( function timer() {
             console.log( j );
         }, j*1000 );
     })();
}
```
再改进一下代码：
```javascript
for (var i=1; i<=5; i++) {
     (function(j) {
         setTimeout( function timer() {
             console.log( j );
         }, j*1000 );
     })( i );
}
```
如果运用ES6就会更加简单，只要运用let声明的块作用域：
```javascript
or (let i=1; i<=5; i++) {
     setTimeout( function timer() {
         console.log( i );
     }, i*1000 );
}
```

## 模块
```javascript
function CoolModule() {
     var something = "cool";
     var another = [1, 2, 3];
     function doSomething() {
         console.log( something );
     }
     function doAnother() {
         console.log( another.join( " ! " ) );
     }
     return {
         doSomething: doSomething,
         doAnother: doAnother
     };
}
var foo = CoolModule();
foo.doSomething(); // cool
foo.doAnother(); // 1 ! 2 ! 3
```

这样的模式就是最常见的模块的实现方法。返回值是一个包含内部的函数的对象。因为CoolModule()是一个函数，必须调用这个外部的函数后才能创建一个包含内部作用域的闭包。并且返回对象含有的是内部函数而不是内部变量的引用，内部变量是隐藏且私有的状态。

模块也是函数，也可以接受参数：

```javascript
function CoolModule(id) {
     function identify() {
         console.log( id );
     }
     return {
         identify: identify
     };
 }
 var foo1 = CoolModule( "foo 1" );
 var foo2 = CoolModule( "foo 2" );
 foo1.identify(); // "foo 1"
 foo2.identify(); // "foo 2"
```
ES6为模块添加了语法的支持，ES6可以把文件当做模块来加载，但是要注意ES6的模块没有行内格式，就是每个模块必须在一个单独的文件中。

* bar.js

```javascript
function hello(who) {
    return "Let me introduce: " + who;
}
export hello;
```

* foo.js

```javascript
//仅从bar模块导入hello()
import hello from "bar";
 var hungry = "hippo";
 function awesome() {
     console.log(
         hello( hungry ).toUpperCase()
     );
 }
 export awesome;
```

* baz.js

```javascript
//导入完整的foo和bar模块
module foo from "foo";
module bar from "bar";
 console.log(
     bar.hello( "rhino" )
 ); // Let me introduce: rhino
 foo.awesome(); // LET ME INTRODUCE: HIPPO

```

import可以将一个模块中的一个或多个API引入到当前作用域，并分别绑定在一个变量上。module会将整个模块的API引入并绑定到一个变量上。export会将当前模块的变量或函数导出为公共的API。