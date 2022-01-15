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

