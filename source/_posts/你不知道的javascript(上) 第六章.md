---
title: 你不知道的javascript(上) 第六章
date: 2017-08-08 11:03:09
tags: 你不知道的javascript
---

# 数组

## 数组遍历与属性

虽然在 JavaScript 中数组是对象，但是没有好的理由去使用 for in 循环 遍历数组。 相反，有一些好的理由不去使用 for in 遍历数组。

>注意: JavaScript 中数组不是 关联数组。 JavaScript 中只有对象 来管理键值的对应关系。但是关联数组是保持顺序的，而对象不是。

由于 for in 循环会枚举原型链上的所有属性，唯一过滤这些属性的方式是使用 hasOwnProperty 函数， 因此会比普通的 for 循环慢上好多倍。

**遍历**

为了达到遍历数组的最佳性能，推荐使用经典的 for 循环。
```javascript
var list = [1, 2, 3, 4, 5, ...... 100000000];
for(var i = 0, l = list.length; i < l; i++) {
    console.log(list[i]);
}
```
上面代码有一个处理，就是通过 l = list.length 来缓存数组的长度。

虽然 length 是数组的一个属性，但是在每次循环中访问它还是有性能开销。 可能最新的 JavaScript 引擎在这点上做了优化，但是我们没法保证自己的代码是否运行在这些最近的引擎之上。

实际上，不使用缓存数组长度的方式比缓存版本要慢很多。

**`length` 属性**

`length`属性的 `getter` 方式会简单的返回数组的长度，而 `setter` 方式会截断数组。
```javascript
var foo = [1, 2, 3, 4, 5, 6];
foo.length = 3;
foo; // [1, 2, 3]

foo.length = 6;
foo; // [1, 2, 3]
```
译者注： 在 Firebug 中查看此时 foo 的值是： [1, 2, 3, undefined, undefined, undefined] 但是这个结果并不准确，如果你在 Chrome 的控制台查看 foo 的结果，你会发现是这样的： [1, 2, 3] 因为在 JavaScript 中 undefined 是一个变量，注意是变量不是关键字，因此上面两个结果的意义是完全不相同的。
```
// 译者注：为了验证，我们来执行下面代码，看序号 5 是否存在于 foo 中。
5 in foo; // 不管在 Firebug 或者 Chrome 都返回 false
foo[5] = undefined;
5 in foo; // 不管在 Firebug 或者 Chrome 都返回 true
为 length 设置一个更小的值会截断数组，但是增大 length 属性值不会对数组产生影响。
```
**结论**

为了更好的性能，推荐使用普通的 for 循环并缓存数组的 length 属性。 使用 for in 遍历数组被认为是不好的代码习惯并倾向于产生错误和导致性能问题。

## Array 构造函数

由于 Array 的构造函数在如何处理参数时有点模棱两可，因此总是推荐使用数组的字面语法 - [] - 来创建数组。

```javascript
[1, 2, 3]; // 结果: [1, 2, 3]
new Array(1, 2, 3); // 结果: [1, 2, 3]

[3]; // 结果: [3]
new Array(3); // 结果: []
new Array('3') // 结果: ['3']


// 译者注：因此下面的代码将会使人很迷惑
new Array(3, 4, 5); // 结果: [3, 4, 5]
new Array(3) // 结果: []，此数组长度为 3
```
>译者注：这里的模棱两可指的是数组的两种构造函数语法
由于只有一个参数传递到构造函数中（译者注：指的是 new Array(3); 这种调用方式），并且这个参数是数字，构造函数会返回一个 length 属性被设置为此参数的空数组。 需要特别注意的是，此时只有 length 属性被设置，真正的数组并没有生成。

>译者注：在 Firebug 中，你会看到 [undefined, undefined, undefined]，这其实是不对的。在上一节有详细的分析。

```javascript
var arr = new Array(3);
arr[1]; // undefined
1 in arr; // false, 数组还没有生成
```
这种优先于设置数组长度属性的做法只在少数几种情况下有用，比如需要循环字符串，可以避免 for 循环的麻烦。

```
new Array(count + 1).join(stringToRepeat);
```
>译者注： new Array(3).join('#') 将会返回 ##

**结论**

应该尽量避免使用数组构造函数创建新数组。推荐使用数组的字面语法。它们更加短小和简洁，因此增加了代码的可读性。


# 类型

## 相等与比较

JavaScript 有两种方式判断两个值是否相等。

**等于操作符**

等于操作符由两个等号组成：`==`

JavaScript 是弱类型语言，这就意味着，等于操作符会为了比较两个值而进行强制类型转换。
```javascript
""           ==   "0"           // false
0            ==   ""            // true
0            ==   "0"           // true
false        ==   "false"       // false
false        ==   "0"           // true
false        ==   undefined     // false
false        ==   null          // false
null         ==   undefined     // true
" \t\r\n"    ==   0             // true
```
上面的表格展示了强制类型转换，这也是使用`==`被广泛认为是不好编程习惯的主要原因， 由于它的复杂转换规则，会导致难以跟踪的问题。

此外，强制类型转换也会带来性能消耗，比如一个字符串为了和一个数字进行比较，必须事先被强制转换为数字。

**严格等于操作符**

严格等于操作符由三个等号组成：`===`

不像普通的等于操作符，严格等于操作符不会进行强制类型转换。
```javascript
""           ===   "0"           // false
0            ===   ""            // false
0            ===   "0"           // false
false        ===   "false"       // false
false        ===   "0"           // false
false        ===   undefined     // false
false        ===   null          // false
null         ===   undefined     // false
" \t\r\n"    ===   0             // false
```
上面的结果更加清晰并有利于代码的分析。如果两个操作数类型不同就肯定不相等也有助于性能的提升。

**比较对象**

虽然 `==` 和 `===` 操作符都是等于操作符，但是当其中有一个操作数为对象时，行为就不同了。

```javascript
{} === {};                   // false
new String('foo') === 'foo'; // false
new Number(10) === 10;       // false
var foo = {};
foo === foo;                 // true
```
这里等于操作符比较的不是值是否相等，而是是否属于同一个身份；也就是说，只有对象的同一个实例才被认为是相等的。 这有点像 Python 中的 is 和 C 中的指针比较。

**结论**

强烈推荐使用严格等于操作符。如果类型需要转换，应该在比较之前显式的转换， 而不是使用语言本身复杂的强制转换规则。

## `typeof` 操作符

`typeof` 操作符（和 `instanceof` 一起）或许是 JavaScript 中最大的设计缺陷， 因为几乎不可能从它们那里得到想要的结果。

尽管 `instanceof` 还有一些极少数的应用场景，`typeof` 只有一个实际的应用（译者注：这个实际应用是用来检测一个对象是否已经定义或者是否已经赋值）， 而这个应用却不是用来检查对象的类型。

>注意: 由于 `typeof` 也可以像函数的语法被调用，比如 `typeof(obj)`，但这并不是一个函数调用。 那两个小括号只是用来计算一个表达式的值，这个返回值会作为 `typeof` 操作符的一个操作数。 实际上不存在名为 `typeof` 的函数。

**JavaScript 类型表格**
```javascript
Value               Class      Type
-------------------------------------
"foo"               String     string
new String("foo")   String     object
1.2                 Number     number
new Number(1.2)     Number     object
true                Boolean    boolean
new Boolean(true)   Boolean    object
new Date()          Date       object
new Error()         Error      object
[1,2,3]             Array      object
new Array(1, 2, 3)  Array      object
new Function("")    Function   function
/abc/g              RegExp     object (function in Nitro/V8)
new RegExp("meow")  RegExp     object (function in Nitro/V8)
{}                  Object     object
new Object()        Object     object
```
上面表格中，Type 一列表示 `typeof` 操作符的运算结果。可以看到，这个值在大多数情况下都返回 "object"。

Class 一列表示对象的内部属性 [[Class]] 的值。

>JavaScript 标准文档中定义: [[Class]] 的值只可能是下面字符串中的一个： Arguments, Array, Boolean, Date, Error, Function, JSON, Math, Number, Object, RegExp, String.

为了获取对象的 [[Class]]，我们需要使用定义在 Object.prototype 上的方法 toString。

**对象的类定义**

JavaScript 标准文档只给出了一种获取 `[[Class]]` 值的方法，那就是使用`Object.prototype.toString`。

```javascript
function is(type, obj) {
    var clas = Object.prototype.toString.call(obj).slice(8, -1);
    return obj !== undefined && obj !== null && clas === type;
}

is('String', 'test'); // true
is('String', new String('test')); // true
```
上面例子中，`Object.prototype.toString`方法被调用，`this` 被设置为了需要获取 `[[Class]]` 值的对象。

译者注：`Object.prototype.toString` 返回一种标准格式字符串，所以上例可以通过 `slice` 截取指定位置的字符串，如下所示：
```
Object.prototype.toString.call([])    // "[object Array]"
Object.prototype.toString.call({})    // "[object Object]"
Object.prototype.toString.call(2)    // "[object Number]"
```
>ES5 提示: 在 ECMAScript 5 中，为了方便，对 null 和 undefined 调用 Object.prototype.toString 方法， 其返回值由 Object 变成了 Null 和 Undefined。

译者注：这种变化可以从 IE8 和 Firefox 4 中看出区别，如下所示：

```
// IE8
Object.prototype.toString.call(null)    // "[object Object]"
Object.prototype.toString.call(undefined)    // "[object Object]"

// Firefox 4
Object.prototype.toString.call(null)    // "[object Null]"
Object.prototype.toString.call(undefined)    // "[object Undefined]"
```
**测试为定义变量**

```javascript
typeof foo !== 'undefined'
```
上面代码会检测 foo 是否已经定义；如果没有定义而直接使用会导致 ReferenceError 的异常。 这是 typeof 唯一有用的地方。

**结论**

为了检测一个对象的类型，强烈推荐使用 Object.prototype.toString 方法； 因为这是唯一一个可依赖的方式。正如上面表格所示，typeof 的一些返回值在标准文档中并未定义， 因此不同的引擎实现可能不同。

除非为了检测一个变量是否已经定义，我们应尽量避免使用 typeof 操作符。

** `instanceof` 操作符**

instanceof 操作符用来比较两个操作数的构造函数。只有在比较自定义的对象时才有意义。 如果用来比较内置类型，将会和 typeof 操作符 一样用处不大。

**比较自定义对象**
```javascript
function Foo() {}
function Bar() {}
Bar.prototype = new Foo();

new Bar() instanceof Bar; // true
new Bar() instanceof Foo; // true

// 如果仅仅设置 Bar.prototype 为函数 Foo 本身，而不是 Foo 构造函数的一个实例
Bar.prototype = Foo;
new Bar() instanceof Foo; // false
```

**`instanceof` 比较内置类型**

```javascript
new String('foo') instanceof String; // true
new String('foo') instanceof Object; // true

'foo' instanceof String; // false
'foo' instanceof Object; // false
```
有一点需要注意，instanceof 用来比较属于不同 JavaScript 上下文的对象（比如，浏览器中不同的文档结构）时将会出错， 因为它们的构造函数不会是同一个对象。

**结论**

instanceof 操作符应该仅仅用来比较来自同一个 JavaScript 上下文的自定义对象。 正如 typeof 操作符一样，任何其它的用法都应该是避免的。

## 类型转换

JavaScript 是弱类型语言，所以会在任何可能的情况下应用强制类型转换。
```javascript
// 下面的比较结果是：true
new Number(10) == 10; // Number.toString() 返回的字符串被再次转换为数字

10 == '10';           // 字符串被转换为数字
10 == '+10 ';         // 同上
10 == '010';          // 同上
isNaN(null) == false; // null 被转换为数字 0
                      // 0 当然不是一个 NaN（译者注：否定之否定）

// 下面的比较结果是：false
10 == 010;
10 == '-10';
```
>ES5 提示: 以 0 开头的数字字面值会被作为八进制数字解析。 而在 ECMAScript 5 严格模式下，这个特性被移除了。
为了避免上面复杂的强制类型转换，强烈推荐使用严格的等于操作符。 虽然这可以避免大部分的问题，但 JavaScript 的弱类型系统仍然会导致一些其它问题。

**内置类型的构造函数**

内置类型（比如 Number 和 String）的构造函数在被调用时，使用或者不使用 new 的结果完全不同。

```javascript
new Number(10) === 10;     // False, 对象与数字的比较
Number(10) === 10;         // True, 数字与数字的比较
new Number(10) + 0 === 10; // True, 由于隐式的类型转换
```
使用内置类型 Number 作为构造函数将会创建一个新的 Number 对象， 而在不使用 new 关键字的 Number 函数更像是一个数字转换器。

另外，在比较中引入对象的字面值将会导致更加复杂的强制类型转换。

最好的选择是把要比较的值显式的转换为三种可能的类型之一。

**转换为字符串**
```javascript
'' + 10 === '10'; // true
```
将一个值加上空字符串可以轻松转换为字符串类型。

**转换为数字**
```javascript
+'10' === 10; // true
```
使用一元的加号操作符，可以把字符串转换为数字。

译者注：字符串转换为数字的常用方法：
```javascript
+'010' === 10
Number('010') === 10
parseInt('010', 10) === 10  // 用来转换为整数

+'010.2' === 10.2
Number('010.2') === 10.2
parseInt('010.2', 10) === 10
```
**转换为布尔型**

通过使用 否 操作符两次，可以把一个值转换为布尔型。
```javascript
!!'foo';   // true
!!'';      // false
!!'0';     // true
!!'1';     // true
!!'-1'     // true
!!{};      // true
!!true;    // true
```


# 核心

## 为什么不用`eval`
`eval` 函数会在当前作用域中执行一段 JavaScript 代码字符串。
```javascript
var foo = 1;
function test() {
    var foo = 2;
    eval('foo = 3');
    return foo;
}
test(); // 3
foo; // 1
```
但是 `eval` 只在被直接调用并且调用函数就是 `eval` 本身时，才在当前作用域中执行。

```javascript
var foo = 1;
function test() {
    var foo = 2;
    var bar = eval;
    bar('foo = 3');
    return foo;
}
test(); // 2
foo; // 3
```

译者注：上面的代码等价于在全局作用域中调用 eval，和下面两种写法效果一样：
```javascript
// 写法一：直接调用全局作用域下的 foo 变量
var foo = 1;
function test() {
    var foo = 2;
    window.foo = 3;
    return foo;
}
test(); // 2
foo; // 3

// 写法二：使用 call 函数修改 eval 执行的上下文为全局作用域
var foo = 1;
function test() {
    var foo = 2;
    eval.call(window, 'foo = 3');
    return foo;
}
test(); // 2
foo; // 3
```

在任何情况下我们都应该避免使用 `eval` 函数。99.9% 使用 `eval` 的场景都有不使用 `eval` 的解决方案。

**伪装的 `eval`**

定时函数 setTimeout 和 setInterval 都可以接受字符串作为它们的第一个参数。 这个字符串总是在全局作用域中执行，因此 eval 在这种情况下没有被直接调用。

**安全问题**

eval 也存在安全问题，因为它会执行任意传给它的代码， 在代码字符串未知或者是来自一个不信任的源时，绝对不要使用 eval 函数。

**结论**

绝对不要使用 eval，任何使用它的代码都会在它的工作方式，性能和安全性方面受到质疑。 如果一些情况必须使用到 eval 才能正常工作，首先它的设计会受到质疑，这不应该是首选的解决方案， 一个更好的不使用 eval 的解决方案应该得到充分考虑并优先采用。

## `undefined`和`null`

JavaScript有两个表示空的值，其中比较游泳的是undefined。

**`undefined`的值**

`undefined`是一个值为`undefined`的类型
这个语言也定义了一个全局变量，它的值是 `undefined，这个变量也被称为` `undefined`。 但是这个变量不是一个常量，也不是一个关键字。这意味着它的值可以轻易被覆盖。

下面的情况会返回 `undefined` 值：

*访问未修改的全局变量 `undefined。`
*由于没有定义 return *表达式的函数隐式返回。
`undefined`*访问不存在的属性。
*函数参数没有被显式的传递值。
*任何被设置为 `undefined` 值的变量。
*处理 `undefined` 值的改变

由于全局变量 `undefined` 只是保存了 `undefined` 类型实际值的副本， 因此对它赋新值不会改变类型 `undefined` 的值。

`undefined`
为了避免可能对 `undefined` 值的改变，一个常用的技巧是使用一个传递到匿名包装器的额外参数。 在调用时，这个参数不会获取任何值。
```jaascript
var undefined = 123;
(function(something, foo, undefined) {
    // 局部作用域里的 undefined 变量重新获得了 `undefined` 值

})('Hello World', 42);
```
另外一种达到相同目的方法是在函数内使用变量声明。
```javascript
var undefined = 123;
(function(something, foo) {
    var undefined;
    ...

})('Hello World', 42);
```
这里唯一的区别是，在压缩后并且函数内没有其它需要使用 var 声明变量的情况下，这个版本的代码会多出 4 个字节的代码。

>译者注：这里有点绕口，其实很简单。如果此函数内没有其它需要声明的变量，那么 var 总共 4 个字符（包含一个空白字符） 就是专门为 `undefined` 变量准备的，相比上个例子多出了 4 个字节。
**`null` 的用处**

JavaScript 中的 `undefined` 的使用场景类似于其它语言中的 `null` ，实际上 JavaScript 中的 `null` 是另外一种数据类型。

它在 JavaScript 内部有一些使用场景（比如声明原型链的终结 `Foo.prototype = null`，但是大多数情况下都可以使用 `undefined` 来代替。

## 自动分号插入

尽管 JavaScript 有 C 的代码风格，但是它不强制要求在代码中使用分号，实际上可以省略它们。

JavaScript 不是一个没有分号的语言，恰恰相反上它需要分号来就解析源代码。 因此 JavaScript 解析器在遇到由于缺少分号导致的解析错误时，会自动在源代码中插入分号。

```
var foo = function() {
} // 解析错误，分号丢失
test()
```

自动插入分号，解析器重新解析。
```
var foo = function() {
}; // 没有错误，解析继续
test()
```
自动的分号插入被认为是 JavaScript 语言最大的设计缺陷之一，因为它能改变代码的行为。

**工作原理**

下面的代码没有分号，因此解析器需要自己判断需要在哪些地方插入分号。

```javascript
(function(window, undefined) {
    function test(options) {
        log('testing!')

        (options.list || []).forEach(function(i) {

        })

        options.value.test(
            'long string to pass here',
            'and another long string to pass'
        )

        return
        {
            foo: function() {}
        }
    }
    window.test = test

})(window)

(function(window) {
    window.someLibrary = {}
})(window)
```
下面是解析器"猜测"的结果。

```javascript
(function(window, undefined) {
    function test(options) {

        // 没有插入分号，两行被合并为一行
        log('testing!')(options.list || []).forEach(function(i) {

        }); // <- 插入分号

        options.value.test(
            'long string to pass here',
            'and another long string to pass'
        ); // <- 插入分号

        return; // <- 插入分号, 改变了 return 表达式的行为
        { // 作为一个代码段处理
            foo: function() {}
        }; // <- 插入分号
    }
    window.test = test; // <- 插入分号

// 两行又被合并了
})(window)(function(window) {
    window.someLibrary = {}; // <- 插入分号
})(window); //<- 插入分号
```

>注意: JavaScript 不能正确的处理 return 表达式紧跟换行符的情况， 虽然这不能算是自动分号插入的错误，但这确实是一种不希望的副作用。

解析器显著改变了上面代码的行为，在另外一些情况下也会做出错误的处理。

**前置括号**

在前置括号的情况下，解析器不会自动插入分号。

>log('testing!')

>(options.list || []).forEach(function(i) {})

上面代码被解析器转换为一行。

>log('testing!')(options.list || []).forEach(function(i) {})

log 函数的执行结果极大可能不是函数；这种情况下就会出现 TypeError 的错误，详细错误信息可能是 undefined is not a function。

**结论**

建议绝对不要省略分号，同时也提倡将花括号和相应的表达式放在一行， 对于只有一行代码的 if 或者 else 表达式，也不应该省略花括号。 这些良好的编程习惯不仅可以提到代码的一致性，而且可以防止解析器改变代码行为的错误处理。

## delete操作符
总之，删除全局变量是不可能的，功能和一些其他的东西在JavaScript中有一个dontdelete属性集。

**全局代码和函数代码**
当一个变量或函数是在全球或一个函数的范围是可以激活对象或全局对象的属性定义。这种性质的一组属性，其中之一是dontdelete。变量和函数在全球和功能的代码总是创建属性与dontdelete声明，因此不能被删除。
```javascript
// global variable:
var a = 1; // DontDelete is set
delete a; // false
a; // 1

// normal function:
function f() {} // DontDelete is set
delete f; // false
typeof f; // "function"

// reassigning doesn't help:
f = 1;
delete f; // false
f; // 1
```
**显式属性**
显式设置的属性可以正常删除。
```javascript
// explicitly set property:
var obj = {x: 1};
obj.y = 2;
delete obj.x; // true
delete obj.y; // true
obj.x; // undefined
obj.y; // undefined
```
在上面的例子，`obj.x` 和 `obj.y`可以删除对象。因为他们没有`DontDelete`属性。这就是为什么下面的例子也行。
```javascript
// this works fine, except for IE:
var GLOBAL_OBJECT = this;
GLOBAL_OBJECT.a = 1;
a === GLOBAL_OBJECT.a; // true - just a global var
delete GLOBAL_OBJECT.a; // true
GLOBAL_OBJECT.a; // undefined
```
在这里，我们用诡计来删除这在这里指的是全局对象，我们显式声明变量的性质允许我们删除它。

IE（至少6-8）有一些错误，所以上面的代码不工作。

**函数的参数和内置插件**
函数正常的参数，参数对象和内置的属性也有DontDelete集。
```javascript
// function arguments and properties:
(function (x) {

  delete arguments; // false
  typeof arguments; // "object"

  delete x; // false
  x; // 1

  function f(){}
  delete f.length; // false
  typeof f.length; // "number"

})(1);
```
**宿主对象**

删除操作的行为是不可预知的托管对象。由于规范，宿主对象可以实现任何一种行为。

**结论**

`delete`删除操作符通常会带来意想不到的行为，只能被安全用于删除标准对象的显式设置。


# 其他

## `setTimeout` 和 `setInterval`

由于 JavaScript 是异步的，可以使用 `setTimeout` 和 `setInterval` 来计划执行函数。

>注意: 定时处理不是 ECMAScript 的标准，它们在 DOM (文档对象模型) 被实现。

```javascript
function foo() {}
var id = setTimeout(foo, 1000); // 返回一个大于零的数字
```
当 `setTimeout` 被调用时，它会返回一个 ID 标识并且计划在将来大约 1000 毫秒后调用 foo 函数。 foo 函数只会被执行一次。

基于 JavaScript 引擎的计时策略，以及本质上的单线程运行方式，所以其它代码的运行可能会阻塞此线程。 因此没法确保函数会在 `setTimeout` 指定的时刻被调用。

作为第一个参数的函数将会在全局作用域中执行，因此函数内的 this 将会指向这个全局对象。

```javascript
function Foo() {
    this.value = 42;
    this.method = function() {
        // this 指向全局对象
        console.log(this.value); // 输出：undefined
    };
    setTimeout(this.method, 500);
}
new Foo();
```

>注意: `setTimeout` 的第一个参数是函数对象，一个常犯的错误是这样的 `setTimeout`(foo(), 1000)， 这里回调函数是 foo 的返回值，而不是foo本身。 大部分情况下，这是一个潜在的错误，因为如果函数返回 `undefined，setTimeout` 也不会报错。

**setInterval 的堆调用**

`setTimeout` 只会执行回调函数一次，不过 setInterval - 正如名字建议的 - 会每隔 X 毫秒执行函数一次。 但是却不鼓励使用这个函数。

当回调函数的执行被阻塞时，setInterval 仍然会发布更多的回调指令。在很小的定时间隔情况下，这会导致回调函数被堆积起来。

function foo(){
    // 阻塞执行 1 秒
}
setInterval(foo, 1000);
上面代码中，foo 会执行一次随后被阻塞了一分钟。

在 foo 被阻塞的时候，setInterval 仍然在组织将来对回调函数的调用。 因此，当第一次 foo 函数调用结束时，已经有 10 次函数调用在等待执行。

**处理可能的阻塞调用**

最简单也是最容易控制的方案，是在回调函数内部使用 `setTimeout` 函数。
```javascript
function foo(){
    // 阻塞执行 1 秒
    setTimeout(foo, 1000);
}
foo();
```
这样不仅封装了 `setTimeout` 回调函数，而且阻止了调用指令的堆积，可以有更多的控制。 foo 函数现在可以控制是否继续执行还是终止执行。

**手工清空定时器**

可以通过将定时时产生的 ID 标识传递给 clearTimeout 或者 clearInterval 函数来清除定时， 至于使用哪个函数取决于调用的时候使用的是 `setTimeout` 还是 setInterval。
```
var id = setTimeout(foo, 1000);
clearTimeout(id);
```
**清除所有定时器**

由于没有内置的清除所有定时器的方法，可以采用一种暴力的方式来达到这一目的。
```
// 清空"所有"的定时器
for(var i = 1; i < 1000; i++) {
    clearTimeout(i);
}
```
可能还有些定时器不会在上面代码中被清除（译者注：如果定时器调用时返回的 ID 值大于 1000）， 因此我们可以事先保存所有的定时器 ID，然后一把清除。

**隐藏使用 `eval`**

`setTimeout` 和 `setInterval` 也接受第一个参数为字符串的情况。 这个特性绝对不要使用，因为它在内部使用了 eval。

>注意: 由于定时器函数不是 ECMAScript 的标准，如何解析字符串参数在不同的 JavaScript 引擎实现中可能不同。 事实上，微软的 JScript 会使用 Function 构造函数来代替 eval 的使用。

```javascript
function foo() {
    // 将会被调用
}

function bar() {
    function foo() {
        // 不会被调用
    }
    setTimeout('foo()', 1000);
}
bar();
```

由于 `eval` 在这种情况下不是被直接调用，因此传递到 `setTimeout` 的字符串会自全局作用域中执行； 因此，上面的回调函数使用的不是定义在 `bar` 作用域中的局部变量 `foo`。

建议不要在调用定时器函数时，为了向回调函数传递参数而使用字符串的形式。

```javascript
function foo(a, b, c) {}

// 不要这样做
setTimeout('foo(1,2, 3)', 1000)

// 可以使用匿名函数完成相同功能
setTimeout(function() {
    foo(a, b, c);
}, 1000)
```
>注意: 虽然也可以使用这样的语法 `setTimeout(foo, 1000, a, b, c)`， 但是不推荐这么做，因为在使用对象的属性方法时可能会出错。 （译者注：这里说的是属性方法内，this 的指向错误）
结论

绝对不要使用字符串作为 `setTimeout` 或者 `setInterval` 的第一个参数， 这么写的代码明显质量很差。当需要向回调函数传递参数时，可以创建一个匿名函数，在函数内执行真实的回调函数。

另外，应该避免使用 `setInterval` ，因为它的定时执行不会被 JavaScript 阻塞。