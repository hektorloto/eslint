---
title: no-restricted-globals
rule_type: suggestion
related_rules:
- no-restricted-properties
- no-restricted-syntax
---


Disallowing usage of specific global variables can be useful if you want to allow a set of global
variables, but still want to disallow some of those.

For instance, early Internet Explorer versions exposed the current DOM event as a global variable
`event`, but using this variable has been considered as a bad practice for a long time. Restricting
this will make sure this variable isn't used in browser code.

## Rule Details

This rule allows you to specify global variable names that you don't want to use in your application.

## Options

This rule takes a list of strings, where each string is a global to be restricted:

```json
{
    "rules": {
        "no-restricted-globals": ["error", "event", "fdescribe"]
    }
}
```

Alternatively, the rule also accepts objects, where the global name and an optional custom message are specified:

```json
{
    "rules": {
        "no-restricted-globals": [
            "error",
            {
                "name": "event",
                "message": "Use local parameter instead."
            },
            {
                "name": "fdescribe",
                "message": "Do not commit fdescribe. Use describe instead."
            }
        ]
    }
}
```

Examples of **incorrect** code for sample `"event", "fdescribe"` global variable names:

::: incorrect

```js
/*global event, fdescribe*/
/*eslint no-restricted-globals: ["error", "event", "fdescribe"]*/

function onClick() {
    console.log(event);
}

fdescribe("foo", function() {
});
```

:::

Examples of **correct** code for a sample `"event"` global variable name:

::: correct

```js
/*global event*/
/*eslint no-restricted-globals: ["error", "event"]*/

import event from "event-module";
```

:::

::: correct

```js
/*global event*/
/*eslint no-restricted-globals: ["error", "event"]*/

const event = 1;
```

:::

Examples of **incorrect** code for a sample `"event"` global variable name, along with a custom error message:

::: incorrect

```js
/*global event*/
/* eslint no-restricted-globals: ["error", { name: "event", message: "Use local parameter instead." }] */

function onClick() {
    console.log(event);    // Unexpected global variable 'event'. Use local parameter instead.
}
```

:::

Restricted globals used in TypeScript type annotations—such as type references, interface inheritance, or class implementations—are ignored by this rule.

Examples of **correct** TypeScript code for "Promise", "Event", and "Window" global variable names:

::: correct

```ts
/*eslint no-restricted-globals: ["error", "Promise", "Event", "Window"]*/

const fetchData: Promise<string> = fetchString();

interface CustomEvent extends Event {}

class CustomWindow implements Window {}

function handleClick(event: Event) {
  console.log(event);
}
```

:::
