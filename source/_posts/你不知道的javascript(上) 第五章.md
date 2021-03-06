---
title: 你不知道的javascript(上) 第五章
date: 2017-08-08 11:03:09
tags: 你不知道的javascript
---

# 函数

## 函数的声明与表达式

函数是javascript中的一等对象，这意味着可以函数像其他值一样传递。一个常见的用法是把匿名函数作为回调传递到异步函数中。

**函数声明**
```javascript
function foo(){}
```
上面的方法会在执行前被解析(hoisted),因此它存在于当前上下文的任意一个地方，即使在函数定义体的上面被调用也是对的。
```javascript
foo(); //正常运行，以为foo在代码之前已经被创建
funtion foo() {}
```

**函数赋值表达式**

```javascript
var foo = function() {};
```

这个例子把一个匿名函数赋值给变量foo。
```javascript
foo;//'undefined'
foo(); //出错:TypeError
var foo = function() {};
```
由于var定义了一个声明语句，对变量的解析是在代码运行之前，因此foo变量在代码运行之前已经被定义过了。
但是由于赋值语句只在运行是被执行，因此在相应代码执行之前，foo的值缺省值为undefined。

**命名函数的赋值表达式**

另外一个特殊的情况是将命名函数赋值给一个变量。
```javascript
var foo = function bar(){
	bar(); //正常运行
}
bar();//出错：ReferenceError
```

bar 函数声明外是不可见的，这是因为我们已经把函数赋值给了foo；然而在bar内部依然可见。这是由于JavaScript的命名处理所致，函数名在函数内总是可见的。

## `this`的工作原理
JavaScript 有一套完全不同于其它语言的对 this 的处理机制。 在五种不同的情况下 ，this 指向的各不相同。

**全局范围内**
```javascript
this;
```
当全局范围内使用this，它将指向全局对象。

>浏览器中运行的JavaScript脚本，这个全局对象是window

**函数调用**
```
foo();
```
这里this也会指向全局对象。
>ES5注意：在严格模式下（strict mode），不存在全局变量。这种情况下this将会是undefined。

**方法调用**

```
test.foo();
```
这个例子中，this指向test对象。

**调用构造函数**
```
new foo();
```

如果函数倾向于和new 关键字一起使用，则我们成这个函数为构造函数。在函数内部，this指向一个新创建的对象。

**显式设置的this**
```javascript
function foo (a,b,c) {}

var bar = {};
foo.apply(bar,[1,2,3]); //数组将会扩展如下
foo.call(bar,1,2,3);//结果 a=1,b= 2,c=3
```

当使用 `Function.prototype` 上的 call 或者 apply 方法时，函数内的 this 将会被 显式设置为函数调用的第一个参数。

因此函数调用的规则在上例中已经不适用了，在foo 函数内 this 被设置成了 bar。

>注意: 在对象的字面声明语法中，this 不能用来指向对象本身。 因此 `var obj = {me: this}` 中的 me 不会指向 obj，因为 this 只可能出现在上述的五种情况中。

**常见误解**

尽管大部分的情况都说的过去，不过第一个规则被认为是JavaScript语言另一个错误设计的地方，因为它从来就没有实际的用途。

```javascript
Foo.method = function() {
    function test() {
        // this 将会被设置为全局对象
    }
    test();
}
```
一个常见的误解是 `test` 中的 `this` 将会指向 `Foo` 对象，实际上不是这样子的。

为了在 test 中获取对 Foo 对象的引用，我们需要在 method 函数内部创建一个局部变量指向 Foo 对象。

```javascript
Foo.method = function() {
    var that = this;
    function test() {
        // 使用 that 来指向 Foo 对象
    }
    test();
}
```
`that`只是我们随意起的名字，不过这个名字被广泛的用来指向外部的 `this` 对象。 在 闭包 一节，我们可以看到 `that` 可以作为参数传递。

**方法的赋值表达式**

另一个看起来奇怪的地方是函数别名，也就是将一个方法赋值给一个变量。

```
var test = someObject.methodTest;
test();
```
上例中，`test` 就像一个普通的函数被调用；因此，函数内的 this 将不再被指向到 someObject 对象。

虽然 `this` 的晚绑定特性似乎并不友好，但是这确实基于原型继承赖以生存的土壤。

```
function Foo() {}
Foo.prototype.method = function() {};

function Bar() {}
Bar.prototype = Foo.prototype;

new Bar().method();
```
当 method 被调用时，this 将会指向 Bar 的实例对象。

## 闭包和引用

闭包是 JavaScript 一个非常重要的特性，这意味着当前作用域总是能够访问外部作用域中的变量。 因为 函数 是 JavaScript 中唯一拥有自身作用域的结构，因此闭包的创建依赖于函数。

**模拟私有变量**

```javascript
function Counter(start) {
    var count = start;
    return {
        increment: function() {
            count++;
        },

        get: function() {
            return count;
        }
    }
}

var foo = Counter(4);
foo.increment();
foo.get(); // 5
```
这里，Counter 函数返回两个闭包，函数 `increment` 和函数 `get`。 这两个函数都维持着 对外部作用域 Counter 的引用，因此总可以访问此作用域内定义的变量 count.

为什么不可以在外部访问私有变量

因为 JavaScript 中不可以对作用域进行引用或赋值，因此没有办法在外部访问 count 变量。 唯一的途径就是通过那两个闭包。

var foo = new Counter(4);
foo.hack = function() {
    count = 1337;
};
上面的代码不会改变定义在 Counter 作用域中的 count 变量的值，因为 foo.hack 没有 定义在那个作用域内。它将会创建或者覆盖全局变量 count。

**循环中的闭包**

一个常见的错误出现在循环中使用闭包，假设我们需要在每次循环中调用循环序号

```javascript
for(var i = 0; i < 10; i++) {
    setTimeout(function() {
        console.log(i);
    }, 1000);
}
```
上面的代码不会输出数字 0 到 9，而是会输出数字 10 十次。

当 console.log 被调用的时候，匿名函数保持对外部变量 i 的引用，此时 for循环已经结束， i 的值被修改成了 10.

为了得到想要的结果，需要在每次循环中创建变量 i 的拷贝。

**避免引用错误**

为了正确的获得循环序号，最好使用 匿名包裹器。

```javascript
for(var i = 0; i < 10; i++) {
    (function(e) {
        setTimeout(function() {
            console.log(e);
        }, 1000);
    })(i);
}
```
外部的匿名函数会立即执行，并把 i 作为它的参数，此时函数内 e 变量就拥有了 i 的一个拷贝。

当传递给 setTimeout 的匿名函数执行时，它就拥有了对 e 的引用，而这个值是不会被循环改变的。

有另一个方法完成同样的工作；那就是从匿名包装器中返回一个函数。这和上面的代码效果一样。

```javascript
for(var i = 0; i < 10; i++) {
    setTimeout((function(e) {
        return function() {
            console.log(e);
        }
    })(i), 1000)
}
```
## `arguments` 对象

JavaScript 中每个函数内都能访问一个特别变量 `arguments`。这个变量维护着所有传递到这个函数中的参数列表。

>注意: 由于 arguments 已经被定义为函数内的一个变量。 因此通过 var 关键字定义 arguments 或者将 arguments 声明为一个形式参数， 都将导致原生的 arguments 不会被创建。

arguments 变量不是一个数组（Array）。 尽管在语法上它有数组相关的属性 length，但它不从 Array.prototype 继承，实际上它是一个对象（Object）。

因此，无法对 arguments 变量使用标准的数组方法，比如 push, pop 或者 slice。 虽然使用 for 循环遍历也是可以的，但是为了更好的使用数组方法，最好把它转化为一个真正的数组。

**转化为数组**

下面的代码将会创建一个新的数组，包含所有 arguments 对象中的元素。
```
Array.prototype.slice.call(arguments);
```
这个转化比较慢，在性能不好的代码中不推荐这种做法。

**传递参数**

下面将参数从一个函数传递到另一个函数，是推荐的做法。

```javascript
function foo() {
    bar.apply(null, arguments);
}
function bar(a, b, c) {
    // do stuff here
}
```
另一个技巧是同时使用 call 和 apply，创建一个快速的解绑定包装器。
```javacsript
function Foo() {}

Foo.prototype.method = function(a, b, c) {
    console.log(this, a, b, c);
};

// Create an unbound version of "method"
// 输入参数为: this, arg1, arg2...argN
Foo.method = function() {

    // 结果: Foo.prototype.method.call(this, arg1, arg2... argN)
    Function.call.apply(Foo.prototype.method, arguments);
};

```
**自动更新**

`arguments` 对象为其内部属性以及函数形式参数创建 `getter` 和 `setter` 方法。

因此，改变形参的值会影响到 ``arguments 对象的值，反之亦然。
```
function foo(a, b, c) {
    arguments[0] = 2;
    a; // 2

    b = 4;
    arguments[1]; // 4

    var d = c;
    d = 9;
    c; // 3
}
foo(1, 2, 3);
```
**性能真相**

`arguments` 对象总会被创建，除了两个特殊情况 - 作为局部变量声明和作为形式参数。 而不管它是否有被使用。

`arguments` 的 getters 和 setters 方法总会被创建；因此使用 `arguments` 对性能不会有什么影响。 除非是需要对 `arguments` 对象的属性进行多次访问。

>ES5 提示: 这些 getters 和 setters 在严格模式下（strict mode）不会被创建。

译者注：在 MDC 中对 strict mode 模式下 `arguments` 的描述有助于我们的理解，请看下面代码：
```javascript
// 阐述在 ES5 的严格模式下 `arguments` 的特性
function f(a) {
  "use strict";
  a = 42;
  return [a, arguments[0]];
}
var pair = f(17);
assert(pair[0] === 42);
assert(pair[1] === 17);
```
然而，的确有一种情况会显著的影响现代 JavaScript 引擎的性能。这就是使用 arguments.callee。

```javascript
function foo() {
    arguments.callee; // do something with this function object
    arguments.callee.caller; // and the calling function object
}

function bigLoop() {
    for(var i = 0; i < 100000; i++) {
        foo(); // Would normally be inlined...
    }
}
```
上面代码中，foo 不再是一个单纯的内联函数 inlining， 因为它需要知道它自己和它的调用者。 这不仅抵消了内联函数带来的性能提升，而且破坏了封装，因此现在函数可能要依赖于特定的上下文。

因此强烈建议大家不要使用 `arguments.callee` 和它的属性。

>ES5 提示: 在严格模式下，`arguments.callee` 会报错 `TypeError`，因为它已经被废除了。

## 构造函数

JavaScript 中的构造函数和其它语言中的构造函数是不同的。 通过 new 关键字方式调用的函数都被认为是构造函数。

在构造函数内部 - 也就是被调用的函数内 - this 指向新创建的对象 Object。 这个新创建的对象的 prototype 被指向到构造函数的 prototype。

如果被调用的函数没有显式的 return 表达式，则隐式的会返回 this 对象 - 也就是新创建的对象。
```javascript
function Foo() {
    this.bla = 1;
}

Foo.prototype.test = function() {
    console.log(this.bla);
};

var test = new Foo();
```
上面代码把 `Foo `作为构造函数调用，并设置新创建对象的 `prototype` 为 `Foo.prototype`。

显式的` return` 表达式将会影响返回结果，但仅限于返回的是一个对象。
```javascript
function Bar() {
    return 2;
}
new Bar(); // 返回新创建的对象

function Test() {
    this.value = 2;

    return {
        foo: 1
    };
}
new Test(); // 返回的对象
```

如果 new 被遗漏了，则函数不会返回新创建的对象。
```
function Foo() {
    this.bla = 1; // 获取设置全局参数
}
Foo(); // undefined
```
虽然上例在有些情况下也能正常运行，但是由于 JavaScript 中 this 的工作原理， 这里的 this 指向全局对象。

**工厂模式**

为了不使用 new 关键字，构造函数必须显式的返回一个值。
```
function Bar() {
    var value = 1;
    return {
        method: function() {
            return value;
        }
    }
}
Bar.prototype = {
    foo: function() {}
};

new Bar();
Bar();
```
上面两种对 Bar 函数的调用返回的值完全相同，一个新创建的拥有 method 属性的对象被返回， 其实这里创建了一个闭包。

还需要注意， new Bar() 并不会改变返回对象的原型（注：也就是返回对象的原型不会指向 Bar.prototype）。 因为构造函数的原型会被指向到刚刚创建的新对象，而这里的 Bar 没有把这个新对象返回（译者注：而是返回了一个包含 method 属性的自定义对象）。

在上面的例子中，使用或者不使用 new 关键字没有功能性的区别。

注：上面两种方式创建的对象不能访问 Bar 原型链上的属性，如下所示：
```javascript
var bar1 = new Bar();
typeof(bar1.method); // "function"
typeof(bar1.foo); // "undefined"

var bar2 = Bar();
typeof(bar2.method); // "function"
typeof(bar2.foo); // "undefined"
```

**通过工厂模式创建新对象**

我们常听到的一条忠告是不要使用 new 关键字来调用函数，因为如果忘记使用它就会导致错误。

为了创建新对象，我们可以创建一个工厂方法，并且在方法内构造一个新对象。
```javascript
function Foo() {
    var obj = {};
    obj.value = 'blub';

    var private = 2;
    obj.someMethod = function(value) {
        this.value = value;
    }

    obj.getPrivate = function() {
        return private;
    }
    return obj;
}
```
虽然上面的方式比起 new 的调用方式不容易出错，并且可以充分利用私有变量带来的便利， 但是随之而来的是一些不好的地方。

会占用更多的内存，因为新创建的对象不能共享原型上的方法。
为了实现继承，工厂方法需要从另外一个对象拷贝所有属性，或者把一个对象作为新创建对象的原型。
放弃原型链仅仅是因为防止遗漏 new 带来的问题，这似乎和语言本身的思想相违背。
总结

虽然遗漏 new 关键字可能会导致问题，但这并不是放弃使用原型链的借口。 最终使用哪种方式取决于应用程序的需求，选择一种代码书写风格并坚持下去才是最重要的。

## 作用域与命名空间

尽管 JavaScript 支持一对花括号创建的代码段，但是并不支持块级作用域； 而仅仅支持 函数作用域。
```javascript
function test() { // 一个作用域
    for(var i = 0; i < 10; i++) { // 不是一个作用域
        // count
    }
    console.log(i); // 10
}
```
>注意: 如果不是在赋值语句中，而是在 return 表达式或者函数参数中，{...} 将会作为代码段解析， 而不是作为对象的字面语法解析。如果考虑到 自动分号插入，这可能会导致一些不易察觉的错误。

译者注：如果 return 对象的左括号和 return 不在一行上就会出错。
```javascript
// 译者注：下面输出 undefined
function add(a, b) {
    return
        a + b;
}
console.log(add(1, 2));
```
JavaScript 中没有显式的命名空间定义，这就意味着所有对象都定义在一个全局共享的命名空间下面。

每次引用一个变量，JavaScript 会向上遍历整个作用域直到找到这个变量为止。 如果到达全局作用域但是这个变量仍未找到，则会抛出 ReferenceError 异常。

**隐式的全局变量**
```javascript
// 脚本 A
foo = '42';

// 脚本 B
var foo = '42'
```
上面两段脚本效果不同。脚本 A 在全局作用域内定义了变量 foo，而脚本 B 在当前作用域内定义变量 foo。

再次强调，上面的效果完全不同，不使用 var 声明变量将会导致隐式的全局变量产生。
```javascript
// 全局作用域
var foo = 42;
function test() {
    // 局部作用域
    foo = 21;
}
test();
foo; // 21
```
在函数 test 内不使用 var 关键字声明 foo 变量将会覆盖外部的同名变量。 起初这看起来并不是大问题，但是当有成千上万行代码时，不使用 var 声明变量将会带来难以跟踪的 BUG。
```javascript
// 全局作用域
var items = [/* 数组 */];
for(var i = 0; i < 10; i++) {
    subLoop();
}

function subLoop() {
    // subLoop 函数作用域
    for(i = 0; i < 10; i++) { // 没有使用 var 声明变量
        // 干活
    }
}
```
外部循环在第一次调用 subLoop 之后就会终止，因为 subLoop 覆盖了全局变量 i。 在第二个 for 循环中使用 var 声明变量可以避免这种错误。 声明变量时绝对不要遗漏 var 关键字，除非这就是期望的影响外部作用域的行为。

**局部变量**

JavaScript 中局部变量只可能通过两种方式声明，一个是作为函数参数，另一个是通过 var 关键字声明。
```javascript
// 全局变量
var foo = 1;
var bar = 2;
var i = 2;

function test(i) {
    // 函数 test 内的局部作用域
    i = 5;

    var foo = 3;
    bar = 4;
}
test(10);
```
`foo` 和 `i` 是函数 `test` 内的局部变量，而对 `bar` 的赋值将会覆盖全局作用域内的同名变量。

**变量声明提升（Hoisting）**

JavaScript 会提升变量声明。这意味着 var 表达式和 function 声明都将会被提升到当前作用域的顶部。
```javascript
bar();
var bar = function() {};
var someValue = 42;

test();
function test(data) {
    if (false) {
        goo = 1;

    } else {
        var goo = 2;
    }
    for(var i = 0; i < 100; i++) {
        var e = data[i];
    }
}
```
上面代码在运行之前将会被转化。JavaScript 将会把 var 表达式和 function 声明提升到当前作用域的顶部。

```javascript
// var 表达式被移动到这里
var bar, someValue; // 缺省值是 'undefined'

// 函数声明也会提升
function test(data) {
    var goo, i, e; // 没有块级作用域，这些变量被移动到函数顶部
    if (false) {
        goo = 1;

    } else {
        goo = 2;
    }
    for(i = 0; i < 100; i++) {
        e = data[i];
    }
}

bar(); // 出错：TypeError，因为 bar 依然是 'undefined'
someValue = 42; // 赋值语句不会被提升规则（hoisting）影响
bar = function() {};


test();
```
没有块级作用域不仅导致 var 表达式被从循环内移到外部，而且使一些 if 表达式更难看懂。

在原来代码中，if 表达式看起来修改了全部变量 goo，实际上在提升规则被应用后，却是在修改局部变量。

如果没有提升规则（hoisting）的知识，下面的代码看起来会抛出异常 ReferenceError。
```javascript
// 检查 SomeImportantThing 是否已经被初始化
if (!SomeImportantThing) {
    var SomeImportantThing = {};
}
```
实际上，上面的代码正常运行，因为 var 表达式会被提升到全局作用域的顶部。

```javascript
var SomeImportantThing;

// 其它一些代码，可能会初始化 SomeImportantThing，也可能不会

// 检查是否已经被初始化
if (!SomeImportantThing) {
    SomeImportantThing = {};
}
```

```javascript
// 译者注：来自 Nettuts+ 的一段代码，生动的阐述了 JavaScript 中变量声明提升规则
var myvar = 'my value';

(function() {
    alert(myvar); // undefined
    var myvar = 'local value';
})();
```

**名称解析顺序**

JavaScript 中的所有作用域，包括全局作用域，都有一个特别的名称 this 指向当前对象。

函数作用域内也有默认的变量 arguments，其中包含了传递到函数中的参数。

比如，当访问函数内的 foo 变量时，JavaScript 会按照下面顺序查找：

*当前作用域内是否有 var foo 的定义。
*函数形式参数是否有使用 foo 名称的。
*函数自身是否叫做 foo。
*回溯到上一级作用域，然后从 #1 重新开始。
>注意: 自定义 `arguments` 参数将会阻止原生的 `arguments` 对象的创建。

**命名空间**

只有一个全局作用域导致的常见错误是命名冲突。在 JavaScript中，这可以通过 匿名包装器 轻松解决。

```javascript
(function() {
    // 函数创建一个命名空间

    window.foo = function() {
        // 对外公开的函数，创建了闭包
    };

})(); // 立即执行此匿名函数
```

匿名函数被认为是 表达式；因此为了可调用性，它们首先会被执行。
```javascript
( // 小括号内的函数首先被执行
function() {}
) // 并且返回函数对象
() // 调用上面的执行结果，也就是函数对象
```

有一些其他的调用函数表达式的方法，比如下面的两种方式语法不同，但是效果一模一样。

```javascript
// 另外两种方式
+function(){}();
(function(){}());
```
**结论**

推荐使用匿名包装器（译者注：也就是自执行的匿名函数）来创建命名空间。这样不仅可以防止命名冲突， 而且有利于程序的模块化。

另外，使用全局变量被认为是不好的习惯。这样的代码倾向于产生错误和带来高的维护成本。
