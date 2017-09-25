---
title: Javascript编码规范
date: 2017-09-05 10:03:06
tags: javascript
---

## 严格模式
ECMAScript 5 严格模式可在整个脚本或独个方法内被激活。它对应不同的 javascript 语境会做更加严格的错误检查。严格模式也确保了 javascript 代码更加的健壮，运行的也更加快速。

严格模式会阻止使用在未来很可能被引入的预留关键字。

你应该在你的脚本中启用严格模式，最好是在独立的 IIFE 中应用它。避免在你的脚本第一行使用它而导致你的所有脚本都启动了严格模式，这有可能会引发一些第三方类库的问题。


## 理解 JavaScript 的定义域和定义域提升
在 JavaScript 中变量和方法定义会自动提升到执行之前。JavaScript 只有 function 级的定义域，而无其他很多编程语言中的块定义域，所以使得你在某一 function 内的某语句和循环体中定义了一个变量，此变量可作用于整个 function 内，而不仅仅是在此语句或循环体中，因为它们的声明被 JavaScript 自动提升了。

我们应该手动地显示地去声明变量与方法。也就是说，所有的变量以及方法，应当定义在 function 内的首行。
不推荐
```javascript
(function(log){
  'use strict';

  var a = 10;
  var b = 10;

  for(var i = 0; i < 10; i++) {
    var c = a * b * i;
  }

  function f() {

  }

  var d = 100;
  var x = function() {
    return d * d;
  };
  log(x());

}(window.console.log));
```
推荐
```javascript
(function(log){
  'use strict';

  var a = 10,
      b = 10,
      i,
      c,
      d,
      x;

  function f() {

  }

  for(i = 0; i < 10; i++) {
    c = a * b * i;
  }



  d = 100;
  x = function() {
    return d * d;
  };
  log(x());

}(window.console.log));
```
把赋值尽量写在变量申明中。
不推荐
```javascript
var a,
    b,
    c;

a = 10;
b = 10;
c = 100;
```
推荐
```javascript
var a = 10,
    b = 10,
    c = 100;
```

## 总是使用带类型判断的比较判断
总是使用 === 精确的比较操作符，避免在判断的过程中，由 JavaScript 的强制类型转换所造成的困扰。

如果你使用 === 操作符，那比较的双方必须是同一类型为前提的条件下才会有效。

如果你想了解更多关于强制类型转换的信息，你可以读一读 [Dmitry Soshnikov](http://dmitrysoshnikov.com/notes/note-2-ecmascript-equality-operators/) 的这篇文章。

在只使用 == 的情况下，JavaScript 所带来的强制类型转换使得判断结果跟踪变得复杂，下面的例子可以看出这样的结果有多怪了：
```javascript
(function(log){
  'use strict';

  log('0' == 0); // true
  log('' == false); // true
  log('1' == true); // true
  log(null == undefined); // true

  var x = {
    valueOf: function() {
      return 'X';
    }
  };
  log(x == 'X');
}(window.console.log));
```

## 明智地使用真假判断
当我们在一个 if 条件语句中使用变量或表达式时，会做真假判断。if(a == true) 是不同于 if(a) 的。后者的判断比较特殊，我们称其为真假判断。这种判断会通过特殊的操作将其转换为 true 或 false，下列表达式统统返回 false：false, 0, undefined, null, NaN, ''（空字符串）.

这种真假判断在我们只求结果而不关心过程的情况下，非常的有帮助。

以下示例展示了真假判断是如何工作的：
```javascript
(function(log){
  'use strict';

  function logTruthyFalsy(expr) {
    if(expr) {
      log('truthy');
    } else {
      log('falsy');
    }
  }

  logTruthyFalsy(true); // truthy
  logTruthyFalsy(1); // truthy
  logTruthyFalsy({}); // truthy
  logTruthyFalsy([]); // truthy
  logTruthyFalsy('0'); // truthy

  logTruthyFalsy(false); // falsy
  logTruthyFalsy(0); // falsy
  logTruthyFalsy(undefined); // falsy
  logTruthyFalsy(null); // falsy
  logTruthyFalsy(NaN); // falsy
  logTruthyFalsy(''); // falsy
}(window.console.log));
```

## 变量赋值时的逻辑操作
逻辑操作符 || 和 && 也可被用来返回布尔值。如果操作对象为非布尔对象，那每个表达式将会被自左向右地做真假判断。基于此操作，最终总有一个表达式被返回回来。这在变量赋值时，是可以用来简化你的代码的。
不推荐：
```javascript
if(!x) {
  if(!y) {
    x = 1;
  } else {
    x = y;
  }
}
```
推荐：
```javascript
x = x || y || 1;
```
这一小技巧经常用来给方法设定默认的参数。
```javascript
(function(log){
  'use strict';

  function multiply(a, b) {
    a = a || 1;
    b = b || 1;

    log('Result ' + a * b);
  }

  multiply(); // Result 1
  multiply(10); // Result 10
  multiply(3, NaN); // Result 3
  multiply(9, 5); // Result 45
}(window.console.log));
```
## 语句块内的函数声明
切勿在语句块内声明函数，在 ECMAScript 5 的严格模式下，这是不合法的。函数声明应该在定义域的顶层。但在语句块内可将函数申明转化为函数表达式赋值给变量。
不推荐
```javascript
if (x) {
  function foo() {}
}
```
推荐
```javascript
if (x) {
  var foo = function() {};
}
```

## 异常
基本上你无法避免出现异常，特别是在做大型开发时（使用应用开发框架等等）。

在没有自定义异常的情况下，从有返回值的函数中返回错误信息一定非常的棘手，更别提多不优雅了。不好的解决方案包括了传第一个引用类型来接纳错误信息，或总是返回一个对象列表，其中包含着可能的错误对象。以上方式基本上是比较简陋的异常处理方式。适时可做自定义异常处理。

在复杂的环境中，你可以考虑抛出对象而不仅仅是字符串（默认的抛出值）。
```javascript
if(name === undefined) {
  throw {
    name: 'System Error',
    message: 'A name should always be specified!'
  }
}
```

## 修改内建对象的原型链
修改内建的诸如 Object.prototype 和 Array.prototype 是被严厉禁止的。修改其它的内建对象比如 Function.prototype，虽危害没那么大，但始终还是会导致在开发过程中难以 debug 的问题，应当也要避免。

## 自定义 toString() 方法
你可以通过自定义 toString() 来控制对象字符串化。这很好，但你必须保证你的方法总是成功并不会有其它副作用。如果你的方法达不到这样的标准，那将会引发严重的问题。如果 toString() 调用了一个方法，这个方法做了一个断言[3] ，当断言失败，它可能会输出它所在对象的名称，当然对象也需要调用 toString()。
## 圆括号
一般在语法和语义上真正需要时才谨慎地使用圆括号。不要用在一元操作符上，例如 delete,  typeof 和 void，或在关键字之后，例如 return, throw, case, new 等。
## 分号
总是使用分号，因为隐式的代码嵌套会引发难以察觉的问题。
## 字符串
统一使用单引号(‘)，不使用双引号(“)。这在创建 HTML 字符串非常有好处：
```javascript
var msg = 'This is some HTML <div class="makes-sense"></div>';
```
三元条件判断（if 的快捷方法）
用三元操作符分配或返回语句。在比较简单的情况下使用，避免在复杂的情况下使用。没人愿意用 10 行三元操作符把自己的脑子绕晕。
