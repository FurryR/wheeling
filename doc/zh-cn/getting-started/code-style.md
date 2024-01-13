# 如何改善代码

## 为什么？

因为**可读性**。高可读性可以让代码更加简单易懂（这样你的扩展就能更快地被合并），并且还能避免一些可能的问题。

举个例子：

```js
// "d" 是干嘛的？
var d = 0
function a(s) {
  a = ''
  var a
  for (d = 0; d < s['length']; d++) {
    a += s[d].toString() // 如果 s[d] 是 undefined 会怎么样呢？
  }
  return a
}
```

下面是一个更好的例子：

```js
/**
 * 将数组合成为 string。
 * @param {any[]} arr 将加入的数组。
 * @returns {string} 结果字符串。
 */
function join(arr) {
  let result = ''
  for (let index = 0; index < arr.length; index++) {
    result += `${arr[index]}`
  }
  return result
}
```

区别一目了然。

## 怎么办？

以下是一些用于改善代码的小技巧。

<table>
<tr><td>

### 将 `var` 换为 `let` 或者 `const`

`var` 和 `let` 有一些区别。

举个例子：

```js
a = '' // 提前使用没问题
var a
var a // 重新定义没问题
b = '' // 提前使用报错
let b
let b // 重新定义报错
{
  var c
}
c = '' // 不在块内也能用
{
  let c
}
c = '' // 不在作用域内导致报错
```

如你所见，`var` 允许你在初始化之前就使用变量，并且还允许你重新定义（无实际效果）变量，而 `let` 不允许你那样做。

故，推荐使用 `let` 或 `const` 而不是 `var` 来避免未初始化先使用的情况。

</td></tr>

<tr><td>

### 避免全局变量

在一些情况下，不使用全局变量可以提高代码的复用性。

举个例子：

```js
let a = 1
class A {
  query() {
    return a // 所有实例都共享一个变量 a。
  }
}
a = 2 // 所有实例都受到影响。
```

```js
class A {
  query() {
    return this.a // 每个实例有单独的变量 a。
  }
  constructor() {
    this.a = 1
  }
}
const instance = new A()
instance.a = 2 // 只有 instance 受影响。
```

如果你不得不定义全局变量，请使用 [单例模式](https://www.digitalocean.com/community/tutorials/js-js-singletons) 来代替。

</td></tr>
<tr><td>

### 给所有东西加上文档，或者至少提供类型注释

举个例子：

```js
class A {
  func(args) {
    return args.a + args.b
  }
}
```

没人知道这个函数在做什么，并且他们同样不知道参数。

让我们来看上述代码的一个更好的版本：

```js
/**
 * 这个类实现了扩展。
 */
class MyExtension {
  /**
   * 向 a 追加 b。
   * @param {{a: string, b: string}} param0 参数。
   * @returns {string} 追加后的参数。
   */
  append({ a, b }) {
    return a + b // 如果你正使用 Visual Studio Code，你可以享受自动补全。
  }
}
```

像 `@param` 这样的注释可以被编辑器自动识别，这样他们就可以提供更好的自动补全。我们把这种类型的注释称作 `JSDoc`。

举个例子：

```js
/**
 * 调整插件。
 * @param {string} a 一些设置。
 * @param {string} b 一些设置。
 * @returns {string} 返回值。
 */
function adjust(a, b) {}

/// 一些基本语法：
/// @param {类型} 参数名 对参数的注释
/// @returns {类型} 对返回值的注释

/// 可用的基本类型有：`number`, `boolean`, `string`, `object`, `bigint`。你还可以将字面量当作类型用。举个例子，`5` 意味着对象的值 (参数，返回值，等等) 只能为 5。若启用 `ts-check`，编辑器将会要求你做一些额外检查来确保值确实为 5。
/// 你可以使用 `|` 来描述“联合”类型。举个例子，`string | symbol` 意味着值的类型可以为 string 或者 symbol 二者中的一个。
/// 你还可以使用 `&` 来描述“交叉”类型。举个例子，`number & 1` 要求值的类型同时满足 `number` 和 `1` 的约束。它等价于 `1`。
```

</td></tr>
<tr><td>

### 避免直接使用 `eval`, `Function` 或 `innerHTML`

让我们来看看这个例子（有趣的是，Gandi 数据助手真的这样做了）：

```js
class MyExtension {
  /**
   * 将字符串解析为 JSON。
   * @param {{str: string}} param0 将要解析的字符串。
   * @returns {any} 解析的结果。
   */
  parse({ str }) {
    return eval(str)
  }
  /**
   * 给 div 加上 style 装饰。
   * @param {{style: string}} param0 CSS 装饰。
   */
  style({ str }) {
    document.body.innerHTML += `<div style="${str}">Hello World</div>`
  }
}
```

用户将可以使用以下 Scratch 代码来实施 CSRF 攻击：

```
parse "window.alert(1)"
style '"onmouseover="window.alert(1)"a="'
```

故，请避免使用上述提到的方法。如果你不得不这样做，请确保你已经验证入参。

</td></tr>
</table>
