# ES6数值的扩展

## 二进制和八进制表示法

使用前缀`0b`（或`0B`）和`0o`（或`0O`）表示二进制和八进制。

```javascript
0b111110111 === 503 // true
0o767 === 503 // true
```

使用`Number`方法转为十进制

```javascript
Number('0b111') // 7
Number('0o10') // 8
```

## `Number.isFinite()`, `Number.isNaN()`

- `Number.isFinite()` 检查一个数值是否为有限的，即不是`Infinity`
- `Number.isNaN()` 检查一个值是否为`NaN`

对比全局`isFinite()`和`isNaN()`全局会先调用`Number()`将非数值转换为数值。`Number.isFinite()`对于非数值一律返回`false`,`Number.isNaN()`只对与`NaN`才返回`true`。

## `Number.parseInt()`,`Number.parseFloat()`

与全局方法行为保持一致，逐步减少全局方法，使得语言逐步模块化。

## `Number.isInteger()`

判断一个数值是否是整数

```javascript
Number.isInteger(25) // true
Number.isInteger(25.0) // true
```

参数不是负值返回`false`

由于js采用IEEE 754标准，数值存储为64位双精度格式，数值精度最多可以达到53个二进制位（1个隐藏位与52个有效位）。如果数值超过这个限度，第54位后面的位就会被丢弃，导致`Number.isInteger`误判

> 64位双精度浮点数，1个符号位，11位指数部分，剩下52位有效位

## `Number.EPSILON`

极小常亮，表示1与大于1的最小浮点数之间的差。

大于1的最小浮点数`1+2e-52`

```javascript
Number.EPSILON === Math.pow(2, -52)
// true
```

引入最小常量的目的，为浮点数计算，设置一个误差范围

```javascript
function withinErrorMargin (left, right) {
    return Math.abs(let - right) < Number.EPSILON * Math.pow(2, 2)
}

0.1 + 0.2 === 0.3 // false
withinErrorMargin(0.1 + 0.2, 0.3) // true
```

## 安全数和`Number.isSafeInteger()`

js能表示的整数范围在`-2^53`到`2^53`之间

超过这个范围，无法精确表示这个值。

ES6引入`Number.MAX_SAFE_INTEGER`和`Number.MIN_SAFE_INTEGER`表示这两个范围的上下限

`Number.isSafeInteger()`是用来判断一个整数是否落在这个范围之内。

## Math对象的扩展

新增17个与数学相关的静态方法

**`Math.trunc()`**

去除小数部分，返回整数部分

```javascript
Math.trunc(4.1) // 4
Math.trunc(-4.9) // -4
```

非数值使用`Number`方法将其转化为数值。

```javascript
Math.trunc('123.456') // 123
Math.trunc(true) // 1
Math.trunc(null) // 0
```

空值和无法截取整数的值，返回`NaN`

```javascript
Math.trunc() // NaN
Math.trunc('foo') // NaN
Math.trunc(NaN) // NaN
Math.trunc(undefined) // NaN
```

模拟

```javascript
Math.trunc = Math.trunc || function (x) {
    return x < 0 ? Math.ceil(x) : Math.floor(x)
}
```

**`Math.sign()`**

判断一个数到底是正数、负数、还是零。对于非数值，会先将其转换为数值。

它会返回五种值

    - 参数为正数，返回+1
    - 参数为负数，返回-1
    - 参数为0，返回0
    - 参数为-0，返回-0
    - 其他值，返回NaN

**`Math.cbrt()`**

计算一个数的立方根

非数值内部调用`Number`方法转换为数值

**`Math.clz32()`**

javascript的整数使用32位二进制形式表示，`Math.clz32`方法返回一个数的32位无符号整数形式有多少个前导0

非数值调用`Number`方法转化为数值，小数只考虑整数部分

```javascript
Math.clz32(0) // 32
Math.clz32(1) // 31
Math.clz32(3.2) // 30
Math.clz32() // 32
Math.clz32(NaN) // 32
Math.clz32(Infinity) // 32
Math.clz32(null) // 32
Math.clz32([]) // 32
Math.clz32({}) // 32
Math.clz32(true) // 31
```

**`Math.hypot()`**

返回所有参数的平方和的平方根

如果参数不是数值，则转换为数值，有一个参数不能转换就返回NaN

## 对数方法

4个对数相关方法

**`Math.expm1()`**

`Math.expm1(x)`返回e<sup>x</sup> - 

等同于`Math.exp(x) - 1`

**`Math.log1p()`**

返回1+x的自然对数

等同于`Math.log(1 + x)`

x小于-1返回NaN

**`Math.log10()`**

返回以10为低的`x`的对数。如果x小于0返回NaN

**`Math.log2()`**

返回以2为低的`x`的对数。如果x小于0返回NaN

**指数运算符**

```javascript
2**2 = 4
2**3 = 8
```