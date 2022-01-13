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

A：启用严格模式：`"use strict"`, 作用：

1. 严格模式通过抛出错误来消除了一些原有静默错误。
2. 严格模式修复了一些导致 JavaScript引擎难以执行优化的缺陷：有时候，相同的代码，严格模式可以比非严格模式下运行得更快。
3. 严格模式禁用了在ECMAScript的未来版本中可能会定义的一些语法。

> 建议：始终使用严格模式

### Q：双精度浮点运算

