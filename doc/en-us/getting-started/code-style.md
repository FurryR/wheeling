# How to improve your code

## Why

**Readability.** High readability can make the code easier to read (so the extension can be merged faster) and avoid some possible problems.

Here's an example:

```js
// what does "d" mean?
var d = 0
function a(s) {
  a = ''
  var a
  for (d = 0; d < s['length']; d++) {
    a += s[d].toString() // what if s[d] is undefined?
  }
  return a
}
```

And a better implementation:

```js
/**
 * Join an array.
 * @param {any[]} arr The array to join.
 * @returns {string} The result string.
 */
function join(arr) {
  let result = ''
  for (let index = 0; index < arr.length; index++) {
    result += `${arr[index]}`
  }
  return result
}
```

Now you can see the difference.

## How

Here are some tips to improve your code.

<table>
<tr><td>

### Replace `var` with `let` or `const`

`var` and `let` have some differences. For example:

```js
a = '' // fine
var a
var a // also fine
b = '' // error
let b
let b // error
{
  var c
}
c = '' // fine
{
  let c
}
c = '' // error
```

As you see, `var` allows you to use variable before initalization, and allows you to redefine (which has no effect), while `let` won't allow you to do that.

So, it is recommended to use `let` or `const` instead of `var` in order to avoid variable usages before initalization.

</td></tr>

<tr><td>

### Avoid global variables

Avoiding global variables can improve the code's reusability in some situations. For example:

```js
let a = 1
class A {
  query() {
    return a // All instances share the same variable.
  }
}
a = 2 // All instances are affected.
```

```js
class A {
  query() {
    return this.a // Each instance has its own `a` variable.
  }
  constructor() {
    this.a = 1
  }
}
const instance = new A()
instance.a = 2 // Only `instance` will be affected.
```

When you have to define a global variable, use [Singleton](https://www.digitalocean.com/community/tutorials/js-js-singletons) instead.

</td></tr>
<tr><td>

### Get everything documented, or at least type hinted

For example:

```js
class A {
  func(args) {
    return args.a + args.b
  }
}
```

Nobody except you knows what does this function do, and they don't know the arguments either.

Let's see a better version of this example:

```js
/**
 * This class implements the extension.
 */
class MyExtension {
  /**
   * Append b to a.
   * @param {{a: string, b: string}} param0 arguments.
   * @returns {string} the appended string.
   */
  append({ a, b }) {
    return a + b // If you are using Visual Studio Code, you can enjoy auto completion.
  }
}
```

The comments like `@param` can be recognized by the editor, and then they can produce better auto completions. We call this kind of comment `JSDoc`.

For example:

```js
/**
 * Adjust the extension.
 * @param {string} a some settings.
 * @param {string} b some settings.
 * @returns {string} return value.
 */
function adjust(a, b) {}

/// Some basic syntaxes:
/// @param {Type} param_name description
/// @returns {Type} description

/// The basic types are: `number`, `boolean`, `string`, `object`, `bigint`. You can also use a literal value as a type. For example, `5` means the value (param, return value, etc.) can only be 5. If `ts-check` is enabled, the editor will require you to perform some extra validations to ensure the value is 5.
/// You can use `|` to describe "union" types. For example, `string | symbol` means the type of the value can be either a string or a symbol.
/// You can also use `&` to describe "intersection" types. For example, `number & 1` requires the type of the value to satisfy the constraint of both `number` and `1`. It is equal to `1`.
```

</td></tr>
<tr><td>

### Avoid using `eval`, `Function` or `innerHTML` directly

Let's see this example (Fun fact: Gandi Data Utils did this):

```js
class MyExtension {
  /**
   * Parses a string into JSON.
   * @param {{str: string}} param0 The string to parse.
   * @returns {any} Result of parsing.
   */
  parse({ str }) {
    return eval(str)
  }
  /**
   * Style the div element.
   * @param {{style: string}} param0 The style.
   */
  style({ str }) {
    document.body.innerHTML += `<div style="${str}">Hello World</div>`
  }
}
```

The user can use the following Scratch code to perform a CSRF attack:

```
parse "window.alert(1)"
style '"onmouseover="window.alert(1)"a="'
```

So, avoid using the methods above. If you have to do that, make sure you have validated the parameters.

</td></tr>
</table>
