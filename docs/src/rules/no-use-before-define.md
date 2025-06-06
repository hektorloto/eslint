---
title: no-use-before-define
rule_type: problem
---


In JavaScript, prior to ES6, variable and function declarations are hoisted to the top of a scope, so it's possible to use identifiers before their formal declarations in code. This can be confusing and some believe it is best to always declare variables and functions before using them.

In ES6, block-level bindings (`let` and `const`) introduce a "temporal dead zone" where a `ReferenceError` will be thrown with any attempt to access the variable before its declaration.

## Rule Details

This rule will warn when it encounters a reference to an identifier that has not yet been declared.

Examples of **incorrect** code for this rule:

::: incorrect

```js
/*eslint no-use-before-define: "error"*/

alert(a);
var a = 10;

f();
function f() {}

function g() {
    return b;
}
var b = 1;

{
    alert(c);
    let c = 1;
}

{
    class C extends C {}
}

{
    class C {
        static x = "foo";
        [C.x]() {}
    }
}

{
    const C = class {
        static x = C;
    }
}

{
    const C = class {
        static {
            C.x = "foo";
        }
    }
}

export { foo };
const foo = 1;
```

:::

Examples of **correct** code for this rule:

::: correct

```js
/*eslint no-use-before-define: "error"*/

var a;
a = 10;
alert(a);

function f() {}
f(1);

var b = 1;
function g() {
    return b;
}

{
    let c;
    c++;
}

{
    class C {
        static x = C;
    }
}

{
    const C = class C {
        static x = C;
    }
}

{
    const C = class {
        x = C;
    }
}

{
    const C = class C {
        static {
            C.x = "foo";
        }
    }
}

const foo = 1;
export { foo };
```

:::

## Options

```json
{
    "no-use-before-define": ["error", {
        "functions": true,
        "classes": true,
        "variables": true,
        "allowNamedExports": false
    }]
}
```

* `functions` (`boolean`) -
  This flag determines whether or not the rule checks function declarations.
  If this is `true`, the rule warns on every reference to a function before the function declaration.
  Otherwise, the rule ignores those references.
  Function declarations are hoisted, so it's safe to disable this option (note that some idiomatic patterns, such as [mutual recursion](https://en.wikipedia.org/wiki/Mutual_recursion), are incompatible with enabling this option).
  Default is `true`.
* `classes` (`boolean`) -
  This flag determines whether or not the rule checks class declarations of upper scopes.
  If this is `true`, the rule warns on every reference to a class before the class declaration.
  Otherwise, the rule ignores such references, provided the declaration is in an upper function scope.
  Class declarations are not hoisted, so it might be dangerous to disable this option.
  Default is `true`.
* `variables` (`boolean`) -
  This flag determines whether or not the rule checks variable declarations in upper scopes.
  If this is `true`, the rule warns on every reference to a variable before the variable declaration.
  Otherwise, the rule ignores a reference if the declaration is in an upper scope, while still reporting the reference if it's in the same scope as the declaration.
  Default is `true`.
* `allowNamedExports` (`boolean`) -
  If this flag is set to `true`, the rule always allows references in `export {};` declarations.
  These references are safe even if the variables are declared later in the code.
  Default is `false`.

This rule additionally supports TypeScript type syntax. The following options enable checking for the references to `type`, `interface` and `enum` declarations:

* `enums` (`boolean`) -
  If it is `true`, the rule warns every reference to an `enum` before it is defined.
  Defult is `true`.
* `typedefs` (`boolean`) -
  If it is `true`, this rule warns every reference to a type `alias` or `interface` before its declaration. If `false`, the rule allows using type `alias`es and `interface`s before they are defined.
  Default is `true`.
* `ignoreTypeReferences` (`boolean`) -
  If it is `true`, rule will ignore all type references, such as in type annotations and assertions.
  Default is `true`.

This rule accepts `"nofunc"` string as an option.
`"nofunc"` is the same as `{ "functions": false, "classes": true, "variables": true, "allowNamedExports": false }`.

### functions

Examples of **correct** code for the `{ "functions": false }` option:

::: correct

```js
/*eslint no-use-before-define: ["error", { "functions": false }]*/

f();
function f() {}
```

:::

This option allows references to function declarations. For function expressions and arrow functions, please see the [`variables`](#variables) option.

### classes

Examples of **incorrect** code for the `{ "classes": false }` option:

::: incorrect

```js
/*eslint no-use-before-define: ["error", { "classes": false }]*/

new A();
class A {
}

{
    class C extends C {}
}

{
    class C extends D {}
    class D {}
}

{
    class C {
        static x = "foo";
        [C.x]() {}
    }
}

{
    class C {
        static {
            new D();
        }
    }
    class D {}
}
```

:::

Examples of **correct** code for the `{ "classes": false }` option:

::: correct

```js
/*eslint no-use-before-define: ["error", { "classes": false }]*/

function foo() {
    return new A();
}

class A {
}
```

:::

### variables

Examples of **incorrect** code for the `{ "variables": false }` option:

::: incorrect

```js
/*eslint no-use-before-define: ["error", { "variables": false }]*/

console.log(foo);
var foo = 1;

f();
const f = () => {};

g();
const g = function() {};

{
    const C = class {
        static x = C;
    }
}

{
    const C = class {
        static x = foo;
    }
    const foo = 1;
}

{
    class C {
        static {
            this.x = foo;
        }
    }
    const foo = 1;
}
```

:::

Examples of **correct** code for the `{ "variables": false }` option:

::: correct

```js
/*eslint no-use-before-define: ["error", { "variables": false }]*/

function baz() {
    console.log(foo);
}
var foo = 1;

const a = () => f();
function b() { return f(); }
const c = function() { return f(); }
const f = () => {};

const e = function() { return g(); }
const g = function() {}

{
    const C = class {
        x = foo;
    }
    const foo = 1;
}
```

:::

### allowNamedExports

Examples of **correct** code for the `{ "allowNamedExports": true }` option:

::: correct

```js
/*eslint no-use-before-define: ["error", { "allowNamedExports": true }]*/

export { a, b, f, C };

const a = 1;

let b;

function f () {}

class C {}
```

:::

Examples of **incorrect** code for the `{ "allowNamedExports": true }` option:

::: incorrect

```js
/*eslint no-use-before-define: ["error", { "allowNamedExports": true }]*/

export default a;
const a = 1;

const b = c;
export const c = 1;

export function foo() {
    return d;
}
const d = 1;
```

:::

### enums (TypeScript only)

Examples of **incorrect** code for the `{ "enums": true }` option:

::: incorrect

```ts
/*eslint no-use-before-define: ["error", { "enums": true }]*/

const x = Foo.FOO;

enum Foo {
  FOO,
}
```

:::

Examples of **correct** code for the `{ "enums": true }` option:

::: correct

```ts
/*eslint no-use-before-define: ["error", { "enums": true }]*/

enum Foo {
  FOO,
}

const x = Foo.FOO;
```

:::

### typedefs (TypeScript only)

Examples of **incorrect** code for the `{ "enums": true }` with `{ "ignoreTypeReferences": false }` option:

::: incorrect

```ts
/*eslint no-use-before-define: ["error", { "typedefs": true, "ignoreTypeReferences": false }]*/

let myVar: StringOrNumber;

type StringOrNumber = string | number;

const x: Foo = {};

interface Foo {}
```

:::

Examples of **correct** code for the `{ "typedefs": true }` with `{ "ignoreTypeReferences": false }` option:

::: correct

```ts
/*eslint no-use-before-define: ["error", { "typedefs": true, "ignoreTypeReferences": false }]*/

type StringOrNumber = string | number;

let myVar: StringOrNumber;

interface Foo {}

const x: Foo = {};
```

:::

### ignoreTypeReferences (TypeScript only)

Examples of **incorrect** code for the `{ "ignoreTypeReferences": false }` option:

::: incorrect

```ts
/*eslint no-use-before-define: ["error", { "ignoreTypeReferences": false }]*/

let var1: StringOrNumber;

type StringOrNumber = string | number;

let var2: Enum;

enum Enum {}
```

:::

Examples of **correct** code for the `{ "ignoreTypeReferences": false }` option:

::: correct

```ts
/*eslint no-use-before-define: ["error", { "ignoreTypeReferences": false }]*/

type StringOrNumber = string | number;

let myVar: StringOrNumber;

enum Enum {}

let var2: Enum;
```

Examples of **correct** code for the `{ "ignoreTypeReferences": false }` with `{ "typedefs": false }` option:

::: correct

```ts
/*eslint no-use-before-define: ["error", { "ignoreTypeReferences": false, "typedefs": false, }]*/

let myVar: StringOrNumber;

type StringOrNumber = string | number;

const x: Foo = {};

interface Foo {}
```

:::
