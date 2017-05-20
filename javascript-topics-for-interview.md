[TOC]

## JavaScript 知识点

### 执行上下文

执行上下文（Execution Context，也称执行环境），指的是 JavaScript 代码的运行环境。一个执行上下文包括：当前上下文中的变量、作用域链上的变量、函数参数，以及 `this` 的值。

JavaScript 的执行上下文分为以下三种：

* 全局上下文 - 默认的代码执行环境；
* 函数上下文 - 函数中代码的执行环境；
* `eval` 上下文 - `eval()` 函数内部代码的执行环境。

> `eval()` 函数将传入的字符串当作代码来执行，返回代码执行的结果。`eval()` 执行速度慢且不安全，不推荐使用。

全局上下文永远只有一个，而函数上下文可以有任意多个。

### 执行上下文栈

JavaScript 控制器一次只能执行一条指令，其他动作或事件被压入执行上下文栈（简称执行栈）。这个栈的栈底总是全局上下文，栈顶则是当前活动的执行上下文。控制器加载 JavaScript 代码时，首先进入全局上下文。控制器进入全局上下文中的函数时，一个函数上下文就被生成并压入栈。若该函数内部存在另一个函数，相同的情况再次发生。

浏览器总是执行当前栈顶的函数上下文中的代码。函数执行完毕后，其执行上下文从栈中弹出，控制器继续执行下一层的代码。看看下面的例子：

```javascript
(function foo(i) {
  if (i === 3) {
    return;
  }
  else {
    foo(++i);
  }
})(0);
```

这是一个立即执行函数，其中又有递归操作，将 0 作为参数传入，自加到 3 时返回。下图展示了这段代码执行时，执行上下文栈的变化：

![Execution Stack](http://davidshariff.com/blog/wp-content/uploads/2012/06/es1.gif)

每次 `foo` 调用自身时，都会生成一个新的执行上下文并压入栈。返回条件达成时，最上层的栈弹出，下层的栈再依次弹出，直至抵达全局上下文。

执行栈的五个特点：

* 单线程；
* 同步执行；
* 一个全局上下文；
* 多个函数上下文；
* 任意函数调用（包括调用自身）都会产生新的执行上下文。

### 执行上下文 - 深入

一个新的执行上下文在生成时，需要经历两个阶段：

1. 创建阶段（函数被调用后，执行其中代码之前的阶段）：
   * 创建作用域链；
   * 创建变量、函数和参数；
   * 决定 `this` 的值。
2. 启动/代码执行阶段：
   * 变量赋值，生成函数引用，运行代码。

我们可以用一个对象来概念性地表示执行上下文，这个对象包含三个子对象：

```javascript
executionContextObj = {
  'scopeChain': {
    // 变量对象以及所有父级执行上下文中的变量对象
  },
  'variableObject': {
    // 函数参数，函数内部的变量及函数声明
  },
  'this': {}
};
```

#### 启动对象和代码对象

上面的代码中， `executionContextObj` 创建于函数被调用，但还未开始执行的时候。此阶段就是第一阶段。在此阶段，控制器扫描函数参数及内部的变量和函数声明，生成上面代码中的 `variableObject` 对象。

**控制器处理代码的过程如下：**

1. 执行一个函数之前，创建 `执行上下文` ；
2. 进入创建阶段：

   * 初始化作用域链；
   * 创建变量对象（variable object）：

     * 形参 - 变量对象的一个属性，其属性名为形参名，属性值为实参的值；若没有传递值，值为 `undefined` ；

     * 函数声明 - 变量对象的一个属性，属性名为函数名，指向对应的函数；若已存在同名函数则覆盖之；

     * 变量声明 - 变量对象的一个属性，属性名为变量名，值为 `undefined` ；若已存在同名函数或参数，则跳过。
   * 决定当前上下文 `this` 的值。
3. 启动阶段/代码执行阶段：
   * 进行变量赋值，运行函数中的代码。

来看一个例子：

```javascript
function foo (n) {
  var a = 'hello';
  var b = function bar () {
    
  };
  function c () {
    
  }
}
foo(5);
```

调用 `foo()` 函数时，创建阶段的 `executionContextObj` 如下：

```javascript
executionContextObj = {
  scopeChain: {...},
  variableObject: {
    arguments: {
      0: 5,
      length: 1
    },
    n: 5,
    a: undefined,
    b: undefined,
    c: pointer to c()
  },
  this: {...}
}
```

可见，此时变量被定义，但尚未被赋值。创建阶段结束后，执行流进入函数上下文，开始代码运行阶段。运行结束后， `executionContextObj` 有了变化：

```javascript
executionContextObj = {
  scopeChain: {...},
  variableObject: {
    arguments: {
      0: 5,
      length: 1
    },
    n: 5,
    a: 'hello',
    b: pointer to bar(),
    c: pointer to c()
  },
  this: {...}
}
```

### 关于变量提升

有了上述知识，我们现在可以更深入的理解变量提升了。考虑下面的例子：

```javascript
(function () {
  console.log(typeof foo);
  console.log(typeof bar);
  var foo = 'hello',
      bar = function () {
        return 'world';
      };
  function foo () {
    return 'hello';
  }
})();
```

我们现在可以回答下列问题：

* 为什么 `foo` 可以在定义前访问？

  函数开始执行前，变量已经在创建阶段中生成了，自然可以访问。

* 为什么 `typeof foo` 显示为 `function`？

  创建阶段中， `foo` 首先被定义为指向一个函数，之后遇到 `foo` 的变量定义时，根据之前所述，控制器将跳过此定义，继续执行后面的操作。因此，当 `foo` 被访问时，其既不是 `undefined` ，也非 `'hello'` ，而是指向一个函数。

* 为什么 `bar` 是 `undefined` ？

  `bar` 是一个赋值为函数的变量，在创建阶段中， `bar` 被创建，但初始值为 `undefined`。

### 作用域链

对于一个执行上下文而言，其作用域链是当前上下文的变量 + 所有父级上下文中的变量。

#### 词法作用域

JavaScript 的词法作用域，通俗而言，即是指函数内部的函数是以静态（词法）的方式绑定在其父级函数上的，从代码上，是真正定义在父级函数内的。经由一条静态绑定的作用域链，子函数可以访问外部函数上下文中的变量。

对许多开发者而言，词法作用域是造成困惑的源头。我们知道，每次函数调用都会产生一个新的执行上下文，当中变量的值是在当前上下文中计算得到的。正是这种动态的、运行时的值运算操作以及静态定义的词法作用域，造成了许多意料之外的结果。考虑下面的例子：

```javascript
var myAlerts = [];

for (var i = 0; i < 5; i++) {
  myAlerts[i] = function inner () {
    alert(i);
  };
}
myAlerts[0](); // 5
myAlerts[1](); // 5
myAlerts[2](); // 5
myAlerts[3](); // 5
myAlerts[4](); // 5
```

JavaScript 初学者大多会认为 `alert(i)` 会得到 `i` 每一次递增的值，依次弹出1，2，3，4，5。

这是最容易导致困惑之处。函数 `inner()` 是在全局上下文中创建的，因此其作用域链是静态地绑定在全局环境上的。使用 `myAlerts[i]()` 调用这个函数时， `inner()` 循作用域链找到位于全局上下文中的 `i` 。此时， `for` 循环已经运行结束了， `i` 已经变成了5，所以，每次调用都得到相同的结果。

#### 作用域链与闭包

一直以来，闭包被认为是只有高级开发者才能精通的概念。然而闭包不过是作用域链的一种简单理解。闭包的定义为：

```
一个内层函数总是可以访问外层函数的变量和参数，即便外层函数已经返回也是这样。
```

比如：

```javascript
function foo () {
  var a = 'private variable';
  return function bar () {
    alert(a);
  }
}

var callAlert = foo();
callAlert(); // "private variable"
```

上面的例子中，函数 `foo()` 和变量 `callAlert` 位于全局上下文， `callAlert` 的值是 `foo()` 的返回值。令许多人惊讶的是，即便 `foo()` 已经结束执行了，其内部的变量 `a` 仍可以被访问。

当我们仔细检查各个上下文时，会看到下面的情况：

```javascript
// 执行时的全局上下文
global.VO = {
  foo: pointer to foo(),
  callAlert: returned value of global.VO.foo,
  scopeChain: [global.VO]
};
// foo 执行时的上下文
foo.VO = {
  bar: pointer to bar(),
  a: 'private variable',
  scopeChain: [foo.VO, global.VO]
};
// bar 执行时的上下文
bar.VO = {
  scopeChain: [bar.VO, foo.VO, global]
}
```

可见， `callAlert` 指向 `foo()` ，其又指向 `bar()` 。所以当执行 `callAlert()` 时，实际上是执行了 `bar()` 。 `bar()` 执行时，控制器循作用域链寻找变量 `a` ，最终在 `foo.VO` 找到了 `a` ，并返回到 `bar` 的上下文中。因此，即便 `foo()` 已经执行完毕， `a` 仍然能被取到。

至此，我们了解了作用域链和词法环境，以及闭包和变量解析的原理。接下来，我们来看几个具体的应用场景。

#### 变量解析和原型链

我们知道， JavaScript 在访问一个对象的属性时，会从对象开始，循着该对象的原型链查找，直至找到该属性或到达原型链末端。

这样就产生了一个问题：控制器在解析一个对象属性时，是先循**作用域链**查找，还是循**原型链**查找？答案是，当解析一个对象属性或标识符时，控制器首先循着作用域链找到相应的对象，然后循着该对象的原型链查找目标属性。例如：

```javascript
var bar = {};
function foo () {
  bar.a = 'Set from foo()';
  return function inner () {
    alert(bar.a);
  }
}

foo()(); // "Set from foo()"
```

这里，控制器从 `inner()` 函数的上下文开始，循作用域链，在全局上下文中找到 `bar` ，进而找到其 `a` 属性。再考虑下面的情况：

```javascript
var bar = {};
function foo () {
  Object.prototype.a = 'Set from prototype';
  return function inner () {
    alert(bar.a);
  }
}

foo()(); // "Set from prototype"
```

此时，控制器依旧从 `inner()` 开始，循作用域链在全局上下文中找到 `bar` 。因为 `bar` 本身没有名为 `a` 的属性，控制器循 `bar` 的原型链查找，最后在其原型上找到了 `a` 。

#### 何时使用闭包？

闭包是 JavaScript 中的一个重要概念，其常见的应用情形如下：

**概括**

利用闭包可以将一段代码包裹在一个函数上下文中，只通过一个公共接口与外部作用域相连。

**回调函数**

回调函数可以推迟一个函数的触发，让其他操作能够继续进行。一个典型例子是，向一个服务器发送 AJAX 呼叫时，运用回调函数来处理服务器的响应。

**闭包作为参数**

闭包作为参数，即函数作为参数，可以为一些问题提供更普适的解决方案。例如，在给数据排序时，我们可以定义针对不同数据类型的不同排序方法，同时复用一个函数体。

#### 何时不要使用闭包？

**大型作用域**

多层函数往往导致性能问题。请牢记，控制器每处理一个变量，都要循作用域链寻找，显然函数层数越多，作用域链就越长，执行速度也越慢。

#### 垃圾收集与循环引用

JavaScript 拥有自动收集垃圾的机制，开发者一般不需要担心内存管理问题。但是，这种自动的垃圾收集机制会导致性能的降低和内存泄漏。一般而言，垃圾回收机会试图释放那些不再被访问的对象所占用的内存资源。

循环引用指的是两个对象互相引用对方的情形。在闭包中，内层函数可以引用到外层函数的变量，即便那个函数已经返回。旧版本的 IE 对此往往处理不当，产生内存泄漏。

### `this` 关键字

JavaScript 中， `this` 是指当前代码所属的对象。在函数中， `this` 指代当前函数所属的对象。例如：

```javascript
// 全局作用域
var foo = 'abc';
alert(foo); // "abc"
this.foo = 'def';
alert(foo); // "def"
```

可见，在全局作用域使用 `this` 时，其指代的是全局对象。再来看看函数里的情况：

```javascript
var boat = {
  size: 'normal',
  info: function () {
    alert(this === boat);
    alert(this.size);
  }
};
boat.info(); // true, "normal"

var largeBoat = {
  size: 'large';
}
largeBoat.info = boat.info;
largeBoat.info(); // false, "large"
```

首先要要知道的是，函数内部 `this` 的指向是动态的，取决于函数调用之时，开始执行之前。事实上，函数内 `this` 的值是由其父级作用域提供的，同时又跟函数的写法有关。

一个函数被调用时，我们可以查看花括号的两侧 - 若花括号的左侧是一个引用，则 `this` 指代拥有这个函数的对象；否则， `this` 指代全局对象。来看一些例子：

```javascript
function bar () {
  console.log(this);
}
bar(); // global，因为 bar() 属于全局对象

var foo = {
  baz: function () {
    console.log(this);
  }
}
foo.baz(); // foo，因为 baz() 被调用时属于 foo
```

现在来看看下面的例子：

```javascript
var foo = {
  baz: function () {
    console.log(this);
  }
}
foo.baz(); // foo
var anotherBaz = foo.baz;
anotherBaz(); // global - 因为 anotherBaz() 被调用时属于全局对象
```

在上面的例子中， `this` 的值发生了变化，这是由于函数执行时所处的位置不同而引起的。接着我们再来看看下面这个对象：

```javascript
var num = 0;
var foo = {
  num: 10,
  baz: {
    num: 20,
    bar: function () {
      console.log(this.num);
    }
  }
}
foo.baz.bar(); // 20 - 因为 bar() 被调用时属于 baz
var anotherBar = foo.baz.bar;
anotherBar(); // 0 - 因为 anotherBar() 被调用时属于全局对象
```

弄懂了之前所说的 `this` 的决定机制，上面几个例子就很好理解了。现在有一个新问题：一个事件处理程序中的 `this` 指向又是怎样的呢？答案是：事件处理程序中的 `this` 总是指向触发该事件的元素。来看一个例子：

```html
<div id="test">Some text.</div>
```

```javascript
function fn () {
  alert(this.innerHTML);
}
fn(); // undefined

var e = document.getElementById('test');
e.onclick = fn;
e.onclick(); // "Some text."
```

这里，我们将函数 `fn()` 复制一份赋给 `onclick()` 方法，而 `onclick` 是绑定在 `e` 上面的，因而 `this` 指向 `e` ，输出了其中的文本。

#### 改变 `this` 的指向

我们可以用 `call()` 或 `apply()` 这两个函数来改变 `this` 的指向。它们都是用给定的 `this` 指向和参数去调用一个函数：

**语法**

```javascript
function.call(thisArg, arg1, arg2, ...)
function.apply(thisArg, argArray)
```

**例子**

```javascript
function add (a, b) {
  console.log(a + b);
}
add.call(null, 3, 4); // 7
add.apply(null, [3, 4]); // 7

function greet () {
  var reply = `${this.name} is an awesome ${this.role.toLowerCase()}!`;
  console.log(reply);
}
var me = {
  name: 'Sean Zhou',
  role: 'Developer'
};
greet.call(me); // Sean Zhou is an awesome developer!
```

除了 `call()` 和 `apply()` ，我们还可以使用 `bind()` 。 `bind()` 与前两者不同，其返回一个新的函数，这个函数内 `this` 的指向被设为一个给定值，在调用时优先使用一组给定的参数（置于调用时实参的前面）。

**语法**

```javascript
fn.bind(thisArg[, arg1[, arg2[, ...]]]);
```

`bind()` 实际上创建了一个**绑定函数**，该函数套在原函数 `fn` 的外面，调用绑定函数相当于执行了原函数，并为原函数提供了 `this` 指向和初始参数。

一个绑定函数可视为下面的对象：

```javascript
BoundFunction = {
  BoundTargetFunction: 被绑定的函数,
  BoundThis: 作为 this 传递给被绑定函数的值,
  BoundArguments: 作为绑定函数初始参量的一串值,
  Call: 执行绑定函数的方法
}
```

绑定函数最简单的应用，就是创建一个 `this` 指向固定不变的函数。例如：

```javascript
var me = {
  name: 'Sean Zhou',
  sayName: function () {return this.name;}
};
me.sayName(); // "Sean Zhou"
var foo = me.sayName;
foo(); // undefined

var boundSayName = foo.bind(me);
boundSayName(); // "Sean Zhou"
```

### `undefined` 和 `null`

`undefined` 和 `null` 都是 JavaScript 的内建对象。

`undefined` 是全局对象的一个属性，表示 `undefined` 这个原始值。 `undefined` 同时也是：

* 已声明但尚未赋值的变量的值；
* 访问对象中不存在的属性得到的值；
* 没有 `return` 语句的函数的返回值。

> 访问没有声明的变量会抛出 ReferenceError 错误，显示该变量未定义，这与 `undefined` 不同。不过，对尚未声明的变量使用 `typeof` 则会显示 “undefined”。

`null` 表示空值。其是一个字面量，并非是标识符或全局对象的属性。若一个变量或属性的值为 `null` ，其一定是被赋值成了 `null` 。

#### 访问对象属性

对象的某个属性值为 `undefined` 时，访问这个属性会得到 `undefined` 。那么问题来了：如何区分一个不存在的属性，和一个值为 `undefined` 的属性？我们可以使用 `in` 运算符或者对象的 `hasOwnProperty()` 方法：

```javascript
var me = {
  name: 'Sean Zhou'
};
// 设定 me 的原型
var student = {
  name: 'stuName',
  gender: 'stuGender'
};
Object.setPrototypeOf(me, student);
// 检查对象及其原型链
'name' in me;   // true
'gender' in me; // true
'age' in me;    // false
// 仅检查对象自身
me.hasOwnProperty('gender'); // false
```

一般的，检查对象是否拥有某个属性时，若仅检查其自身拥有的属性，则使用 `hasOwnProperty()` 方法；若同时检查其原型链上是否拥有该属性，则使用 `in` 操作符。

> 上述代码中的 `Object.setPrototype(me, student)` 会降低运行性能。以下是一个替代方案：
>
> ```javascript
> var student = {
>   name: 'stuName',
>   gender: 'stuGender'
> };
> // 基于 student 原型，创建 me
> var me = Object.create(student);
> me.name = 'Sean Zhou';
> ```

让我们用一些例子来回顾：

检查一个变量是否存在：

```javascript
if (typeof foo !== 'undefined') {}
```

检查一个属性是否存在，以及是否赋值：

```javascript
if ('bar' in foo && typeof foo.bar !== 'undefined') {
  // foo.bar 存在，且已赋值
}
```

### 函数声明与函数表达式

```javascript
// 函数声明
function fn () {
  ...
}
// 函数表达式
var fn = function () {
  ...
}
```

函数声明与函数表达式的主要区别在于，函数表达式所定义的函数，定义发生在执行这段代码之时，而由声明定义的函数，其定义会被提升至当前作用域的顶部。这意味着用 `if` 语句来控制函数的定义是行不通的：

```javascript
if (expression) {
  function fn () {...}
}
```

上面的代码不论 `expression` 是真是假， `fn()` 函数都会被定义。在严格模式下，这将抛出一个错误。

函数声明与函数表达式可以结合起来使用：

```javascript
// 将一个名为 bar 的函数赋给 foo
var foo = function bar () {...};
```

在 ES6 中，我们看到了一种新的函数类型：箭头函数。

```javascript
var a = [1, 2, 3];
var b = a.map(n => n ** 2);
console.log(b.join(' ')); // "1 4 9"
```

使用箭头函数时需要留意，箭头函数中的 `this` 是词法绑定的，其指向确定于函数定义的时候。这与一般的函数不同，它们的 `this` 是在调用时确定的。

> 更确切的说法是：箭头没有自己的 `this` ，箭头函数中的 `this` 取决于外层上下文的 `this` 。

