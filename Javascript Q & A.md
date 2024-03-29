### Q: 何时使用 `===` 和 `==` ？

A: 使用 `==` 会发生**隐式类型转换**, 例如 `5 == '5'`, 避免隐式类型转换可使用恒等 `===`。`0 == ''` 为 true，因为 0 和 '' 都是逻辑
false，`0 === ''` 为 false。

### Q: null 和 undefinded 的区别 ？

A： 作用于变量时 undefined 为变量已声明，但未初始化， null 为变量的值为 null，变量不可用，当变量未声明时，引用该变量将会报错。

用 `==` 比较时，两者相等，`null == undeinded` 为 true，因为 null 和 undefined 都是逻辑的 false。

```
> typeof null
> "object"
```

```
> typeof undefined
> "undefined"
```

两者的类型不同，null 的类型为 object，这是一个设计缺陷，但是涉及面太广，这个缺陷在未来不会被修复，如果需要严格区分 undefined 类型，可以使用
`typeof a === 'undefined'` 来判断，如果不需要区分可以使用 `a == null` 来判断一个变量是否是 null 或者 undefined。

JSON标准支持编码 null 值，不支持 undefined，当 JSON 序列化时，null 值的属性将会包含 null 值，undefined 值的属性将会被过滤掉。

### Q：严格模式的作用 ？

A: 启用严格模式：`"use strict"`, 作用：

1. 严格模式通过抛出错误来消除了一些原有静默错误。
2. 严格模式修复了一些导致 JavaScript引擎难以执行优化的缺陷：有时候，相同的代码，严格模式可以比非严格模式下运行得更快。
3. 严格模式禁用了在ECMAScript的未来版本中可能会定义的一些语法。

> 建议：始终使用严格模式

### Q：双精度浮点运算的缺陷,如何避免 ？

A: 示例：

```
0.1 + 0.2 = 0.30000000000000004
0.7 + 0.1 = 0.7999999999999999
0.2 + 0.4 = 0.6000000000000001
```

1. 将浮点数转换为整数计算，例如价格计算，可精确到“分”，计算完成以后对分之后的数值进行圆整，满足精度要求。
2. 使用 bignumber.js 第三方库进行精确的十进制计算，使用 bignumber 进行大量计算有可能会有性能问题，所以不建议在动画或者 webgl 编程中使用 bignumber。

### Q：falsy 的值有哪些？

`false`, `""`, `0`, `NaN`, `null`, `undefined`, 其中 NaN 是 Not a Number 的意思，但是它的类型是 `typeof NaN === 'number'`, 当它与 false 相比较时 `NaN == false` 为 false，NaN 与 NaN 比较也为 false， 在条件判断中 NaN 为 falsy：


```
> let aa = NaN;
> undefined
> if (aa) {console.log("!!!!!")}
> undefined
> if (!aa) {console.log("!!!!!")}
> !!!!! 
```

当需要判断一个变量是否为 NaN 时，使用 `Number.isNaN()` 或 `isNaN()`。空数组和空对象（[],{}）始终是 truthy。

### Q: for...in 和 for...of 的区别 ？

A: for...in 主要用于遍历对象的属性，也可用于数组，数组遍历的是索引:

```
> var obj = {a:1, b:2, c:3};
> for(let it in obj) {console.log(it)}
> a 
> b 
> c
```

for...of 遍历数组或者可迭代对象，包含 Array，Set，String和生成器:

```
> let arr = [12, 4, 4,134, 65]
> for(let it of arr) {console.log(it)}
> 12 
> 4 
> 4 
> 134 
> 65
```

### Q: 什么是作用域 ？

A: 作用域就是定义的一套规则，这套规则用来管理引擎如何在当前作用域以及嵌套的子作用域中根据标识符名称进行变量查找。
作用域共有两种主要的工作模型：**1. 词法作用域；2. 动态作用域**。

词法作用域包含**函数作用域**和**块作用域**，通常由大括号（{}）标识出来。

**函数作用域**

```js
const a = "hello";

function foo() {
    const a = "world";
    console.log(a);
}

console.log(a);
foo();
```

每个函数的定义都会有自己的作用域，因此在函数 foo 内可重复定义变量 a，并且处在自己的作用域当中。由于函数作用域的存在，
可以实现对函数内部实现细节的隐藏，只暴露出对外提供的方法。

**块作用域**

```js
const a = "hello";

for(let i = 0; i <= 10; i++) {
    const a = "world";
    console.log(a);
}

console.log(a);
```

```js
const a = "hello";
if (a) {
    const a = "world";
    console.log(a);
}
console.log(a);
```

```js
const a = "hello";
try {
    const a = "world";
    console.log(a);
    throw Error(a);
} catch (err) {
    const a = "!!!!";
    console.log(a);
}
console.log(a);
```
### Q: 解释通过 `new` 运算符创建对象的行为和所执行的操作？

A: `new` 关键字会进行如下的操作：

1. 创建一个空的简单JavaScript对象（即{}）；
2. 为步骤1新创建的对象添加属性`__proto__`，将该属性链接至构造函数的原型对象 ；
3. 将步骤1新创建的对象作为`this`的上下文 ；
4. 如果该函数没有返回对象，则返回`this`。

当代码 `new Foo(...)` 执行时，会发生以下事情：

1. 一个继承自 `Foo.prototype` 的新对象被创建。
2. 使用指定的参数调用构造函数 Foo，并将 `this` 绑定到新创建的对象。`new Foo` 等同于 `new Foo()`，也就是没有指定参数列表，Foo 不带任何参数调用的情况。
3. 由构造函数返回的对象就是 `new` 表达式的结果。如果构造函数没有显式返回一个对象，则使用步骤1创建的对象。（一般情况下，构造函数不返回值，但是用户可以选择主动返回对象，来覆盖正常的对象创建步骤）

参考链接：https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/new

### Q: `this` 关键字在不同环境下的指向？
A: *全局上下文*

在全局执行环境中（在任何函数体外部）`this` 都指向全局对象。 示例：
在浏览器中全局对象为 `window` 对象
```shell
> var a = 123
undefined
> a
123
> window.a
123
```
在 `nodejs` 环境中全局对象为 `globalThis` 对象

```shell
> var a = 123
undefined
> a
123
> globalThis.a
123
```

*函数上下文*

在单独的函数定义的时候，`this` 指向全局对象，在严格模式下，`this` 会保持
为 `undefined`，示例：

```js
var a = 1234;
function f1() {
    return this.a;  // this 指向 window 对象
}
f1() // 输出 1234
```
当函数作为对象里的方法被调用时，`this` 被设置为调用该函数的对象，示例：

```js
var obj = {
  prop: 37,
  f: function() {
    return this.prop;
  }
};
obj.f(); // 37
```
这样的行为不会受函数定义方式或位置的影响:
```js
let obj = {prop: 37};

function independent() {
  return this.prop;
}

obj.f = independent;
obj.f(); // 37
```

函数在复杂对象中定义，`this` 指向最近的调用实例对象，示例：

```js
let obj = {
    a: 1234,
    foo: {
        a: 4567,
        zoo: function() {
            return this.a;
        }
    }
}

obj.foo.zoo(); // `this` 指向最近的对象即：foo，输出：4567
```

通过 `.call`, `.apply` 等函数调用，`this` 指向调用者本身实例，示例：

```js
var obj = {
    a: 12345,
}
var a = 'abcde'; // 全局变量
function foo() {
    return this.a;
}
foo(); // 指向全局，输出：abcde
foo.call(obj); // this 被设置为 obj， 输出：12345
foo.apply(obj); // this 被设置为 obj， 输出：12345
```

ES2015 引入了箭头函数，箭头函数不提供自身的 `this` 绑定（`this` 的值将保持为闭合词法上下文的值），在全局代码中，它将被设置为全局对象。

```js
var a = 'abcd'
let obj = {
    a: 1234,
    foo: {
        a: 4567,
        zoo: () => {
            return this.a;  // this 指向全局变量，输出：abcd
        }
    }
}
let f1 = (() => this); // this 指向全局变量
f1() === window // // 输出： true

let obj2 = {
    a: '123abc',
    bar: function() {
        var x = (() => this);
        return x;
    }
};
let f2 = obj2.bar(); // 返回 x 函数
f2() === obj2 // x 函数中的 this 指向 obj2
f2().a; // 输出：123abc
```

*类上下文*

在类中 `this` 指向类实例本身，示例：

```js
let a = 'abcde';
class Foo {
    a = 12345;
    bar() {
        return this.a;
    }
}
let foo = new Foo();
foo.bar() // bar 中的 this 指向 foo, 输出：12345
```

参考链接：https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/this