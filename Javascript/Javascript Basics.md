
## Hoisting in js

Hoisting is JavaScript’s default behavior of moving declarations — not initializations — to the top of their scope during the compilation phase, before code is executed.

Hoisting is a _compiler-phase optimization_ that helps JavaScript know what variables and functions exist before it runs line by line.

## Var Let and Const

|Feature|`var`|`let`|`const`|
|---|---|---|---|
|**Scope**|Function-scoped|Block-scoped|Block-scoped|
|**Hoisted**|Yes|Yes|Yes|
|**Initialized on Hoist**|Yes (to `undefined`)|No (Temporal Dead Zone)|No (Temporal Dead Zone)|
|**Access Before Declaration**|Allowed (undefined)|❌ ReferenceError|❌ ReferenceError|
|**Re-declaration in Same Scope**|✅ Allowed|❌ Not Allowed|❌ Not Allowed|
|**Reassignment**|✅ Allowed|✅ Allowed|❌ Not Allowed|
|**Object/Array Mutation**|✅ Allowed|✅ Allowed|✅ Allowed|
|**Best Use Case**|Legacy or function scope use|General-purpose variables|Constants / final references|
|**Common Pitfalls**|Leaks into global scope, hoisting confusion|TDZ if accessed early|Misconception that it's immutable|

**Function declarations are hoisted fully, but expressions aren’t.**

## Temporal Dead Zone (TDZ)

The Temporal Dead Zone (TDZ) is the time between the start of a block scope and the point where a `let` or `const` variable is declared. During this time, the variable exists but cannot be accessed — trying to access it throws a ReferenceError.

## Primitive Types

### Primitive Types (Immutable, Passed by Value)

|Type|Description|Example|
|---|---|---|
|`Number`|Numeric values|`42`, `NaN`|
|`String`|Text|`"Hello"`|
|`Boolean`|Logical value|`true`|
|`undefined`|Declared but not assigned|`let x;`|
|`null`|Intentional absence of value|`let y = null`|
|`Symbol`|Unique and immutable identifier|`Symbol("id")`|
|`BigInt`|Arbitrary-length integers|`123n`|

### Non-Primitive Types (Mutable, Passed by Reference)

|Type|Description|Example|
|---|---|---|
|`Object`|Key-value pairs|`{ name: "Boss" }`|
|`Array`|Ordered list|`[1, 2, 3]`|
|`Function`|Callable object|`function() {}`|
|`Date`|Date and time|`new Date()`|
|`RegExp`|Regular expression|`/abc/`|
|`Map`|Key-value pairs with any key types|`new Map()`|
|`Set`|Unique values|`new Set([1, 2, 3])`|

---

## `typeof` Operator

### Usage

```
typeof 42           // "number"
typeof "hello"      // "string"
typeof true         // "boolean"
typeof undefined    // "undefined"
typeof Symbol()     // "symbol"
typeof 123n         // "bigint"
typeof {}           // "object"
typeof []           // "object"
typeof function(){} // "function"
```

### Edge Case

|Expression|Result|Notes|
|---|---|---|
|`typeof null`|`"object"`|✅ Legacy bug from 1995, preserved for compatibility|
|`typeof NaN`|`"number"`|✅ NaN is a number, but invalid|
|`typeof []`|`"object"`|✅ Arrays are objects|
|`typeof function(){}`|`"function"`|✅ Only function has its own typeof value|
|`typeof class A{}`|`"function"`|✅ Classes are special kinds of functions|

## Abstract vs Strict Equality`==` vs `===` in JavaScript

### 🧠 Key Differences

|Feature|`==` (Abstract Equality)|`===` (Strict Equality)|
|---|---|---|
|Type coercion|✅ Yes|❌ No|
|Safe|❌ Risky|✅ Recommended|
|Speed|Slightly slower|Slightly faster|
|Use case|Legacy or intentional coercion|Always prefer this|

### `===` Strict Equality (No Type Conversion)

```jsx
1 === 1;           // true
1 === '1';         // false
null === null;     // true
undefined === null; // false

```

### `==` Abstract Equality (With Coercion)

```jsx
1 == '1';             // true
false == 0;           // true
null == undefined;    // true
[] == '';             // true
[] == 0;              // true

```

### Coercion Rules (Simplified)

1. If same type → compare with `===`
2. If one is `null` and the other `undefined` → `true`
3. If one is number, one is string → convert string to number
4. If boolean → convert to number
5. If object → convert to primitive

### Edge Cases and Pitfalls

### `null == undefined`

```jsx
null == undefined;   // true
null === undefined;  // false

```

### `[] == 0`

```jsx
[] == 0;             // true
// [] → '' → 0

```

### `false == []`

```jsx
false == [];         // true
// false → 0, [] → '' → 0 → 0 == 0

```

### `[] == ![]`

```jsx
[] == ![];           // true
// ![] → false → 0; [] → '' → 0 → 0 == 0

```

### `true == '1'`

```jsx
true == '1';         // true
// true → 1, '1' → 1 → 1 == 1

```

### `null == 0`

```jsx
null == 0;           // false

```

### `undefined == 0`

```jsx
undefined == 0;      // false

```

### Default Parameters with `undefined` vs `null`

```jsx
function greet(name = "Boss") {
  console.log(name);
}

greet();             // "Boss"
greet(undefined);    // "Boss"
greet(null);         // null

```

### Best Practices

```jsx
if (typeof val === "undefined") {}

if (val == null) {} // checks both null and undefined

if (val === undefined) {}
if (val === null) {}

```

### Bonus: `Object.is()`

```jsx
Object.is(NaN, NaN);        // true
Object.is(+0, -0);          // false

0 === -0;                   // true
Object.is(0, -0);           // false

```

### TL;DR Summary

|Operator|Coercion|Safe?|Use Case|
|---|---|---|---|
|`==`|Yes|❌|Legacy, rare intentional coercion|
|`===`|No|✅|Always prefer this|
|`Object.is()`|No|✅|Precise equality w/ edge cases|

## JavaScript Type Coercion Deep Dive

### Implicit vs Explicit Coercion

|Type|Example|Description|
|---|---|---|
|Implicit|`1 + '1'` → `'11'`|javascript coerces automatically|
|Explicit|`Number('42')` → `42`|You explicitly convert type|

### Abstract Operations: `ToNumber()`, `ToString()`, `ToBoolean()`

### `ToNumber()`

```jsx
Number('42');       // 42
Number('');         // 0
Number('abc');      // NaN
Number(null);       // 0
Number(undefined);  // NaN

```

### `ToString()`

```jsx
String(123);        // "123"
String(null);       // "null"
String(undefined);  // "undefined"

```

### `ToBoolean()`

```jsx
Boolean(0);         // false
Boolean('');        // false
Boolean(null);      // false
Boolean(undefined); // false
Boolean([]);        // true
Boolean({});        // true

```

### Falsy Values

```jsx
false, 0, -0, 0n, "", null, undefined, NaN

```

### Truthy Values

Everything else, including:

```jsx
[], {}, "0", "false", function() {}

```

### Coercion with Operators

### `+` (String concatenation or numeric addition)

```jsx
1 + '2';         // "12" (string)
'2' + 1;         // "21" (string)
1 + 2;           // 3 (number)

```

### * , `-`, `/`, `%` (Always Numeric)

```jsx
'10' - 1;        // 9
'10' * 2;        // 20
'10' / 2;        // 5
'10' % 3;        // 1

```

### 🔁 Type Casting

### Using Constructors

```jsx
Number('42');        // 42
String(123);         // "123"
Boolean('true');     // true

```

### Using Unary Operators

```jsx
+'42';               // 42
-'42';               // -42
!!'value';           // true

```

### 🌊 Parsing Numbers

### `parseInt()`

```jsx
parseInt('42px');    // 42
parseInt('101', 2);  // 5

```

### `parseFloat()`

```jsx
parseFloat('3.14abc');  // 3.14

```

### JSON Coercion

### `javascriptON.stringify()`

```jsx
JSON.stringify({ a: 1 });       // '{"a":1}'
JSON.stringify(undefined);      // undefined
JSON.stringify([undefined]);    // [null]

```

### `javascriptON.parse()`

```jsx
javascriptON.parse('{"a":1}');          // { a: 1 }

```

### Tricky Coercion Problems

### `[] + []`

```jsx
[] + [];                // "" (empty string)

```

### `[] + {}` vs `{}` + []

```jsx
[] + {};                // "[object Object]"
{} + [];                // 0 (block + array → coercion to 0)

```

### `true + false`

```jsx
true + false;           // 1

```

### `![] == false`

```jsx
![] == false;           // true

```

### `null + 1`

```jsx
null + 1;               // 1 (null → 0)

```

### `undefined + 1`

```jsx
undefined + 1;          // NaN

```

### `{} + {}`

```jsx
{} + {};                // NaN (block + object → NaN)

```

### ✅ TL;DR Summary

- `+` can mean string or number depending on context.
- , , `/`, `%` always convert to numbers.
- Use `Number()`, `String()`, `Boolean()` for **explicit** conversion.
- Prefer `parseInt`/`parseFloat` for **user input** strings.
- JSON methods have their own quirks with `undefined`, functions, and symbols.

## JavaScript Code Execution & Environment

### How Code Execution Works in JavaScript

JavaScript is a single-threaded, synchronous, and non-blocking language. Code is executed line by line via the JavaScript engine, commonly found in browsers (like V8 in Chrome).

The execution of JavaScript code follows these main steps:

1. Parsing the code
2. Creation of Execution Contexts
3. Execution of code in those contexts

### Call Stack

The call stack is a stack data structure that keeps track of function calls.

- When a function is invoked, it's pushed onto the stack.
- When the function completes, it's popped off the stack.

```jsx
function a() {
  b();
}
function b() {
  console.log('Inside b');
}
a(); // Call stack: [a] → [a, b] → [a] → []

```

### Execution Context

An **Execution Context** is a conceptual environment where the JS code is evaluated and executed.

Types:

- Global Execution Context (GEC)
- Function Execution Context (FEC)
- Eval Execution Context (rarely used)

### Global Execution Context (GEC)

Created when the script starts executing.

- Creates a global object (`window` in browser, `global` in Node.javascript)
- Sets `this` to global object
- Sets up memory for variables and functions declared globally

### Function Execution Context (FEC)

Created whenever a function is invoked.

Each FEC has:

- Variable Environment
- Scope Chain
- `this` binding

### Phases of Execution Context

### Creation Phase

- Setup memory (hoisting)
- `var` variables initialized to `undefined`
- `let` and `const` are hoisted but not initialized (TDZ)
- Functions are stored in memory

### Execution Phase

- Code runs line by line
- Values assigned
- Functions invoked

### Variable Environment vs Lexical Environment

- **Variable Environment**: Holds function-scoped variables.
- **Lexical Environment**: Combination of variable environment and a reference to the outer environment (scope chain).

### What is Lexical Environment?

It’s a structure that holds identifier-variable mappings and a reference to the parent (outer) lexical environment.

### Environment Record

It is a part of the lexical environment that holds the actual variable/function declarations.

### Outer Environment Reference

Each lexical environment holds a reference to its parent environment, forming the scope chain.

### Lexical Scoping Rules

Lexical scope means scope is defined at the time of writing code (not at runtime). Inner functions have access to the variables of their parent functions.

```jsx
function outer() {
  var a = 10;
  function inner() {
    console.log(a);
  }
  inner();
}

```

### Closure Creation and Lexical Environment

A **closure** is formed when a function "remembers" its lexical scope even when executed outside of that scope.

```jsx
function makeCounter() {
  let count = 0;
  return function () {
    return ++count;
  };
}
const counter = makeCounter();
counter(); // 1
counter(); // 2

```

## What is Scope in JS?

Scope determines the accessibility of variables and functions.

### Types of Scope

### Global Scope

Declared outside any function or block.

```jsx
var a = 10; // globally scoped

```

### Function Scope

Accessible only within the function.

```jsx
function test() {
  var a = 5;
}

```

### Block Scope

Accessible only inside a block `{}`. Applies to `let` and `const`.

```jsx
{
  let a = 10;
}
```

### Module Scope

Each ES6 module has its own scope. Variables declared are not global.

```jsx
// in a module file
let a = 10; // scoped to this module
```

### Scope Chain

When resolving a variable, JS looks in the current scope, then moves up to parent scopes until it finds the variable or reaches global.

### Variable Shadowing

When a variable in a local scope has the same name as a variable in an outer scope.

```jsx
let a = 10;
function shadow() {
  let a = 5;
  console.log(a); // 5
}
```

### Scope Pollution

Occurs when too many variables are declared in the global scope, increasing chances of collisions and bugs.

## Lexical Enviornment and Scope chain

> In JavaScript, every execution context — whether it's global, function, or block — creates a Lexical Environment. This environment is a structure that holds all variable and function declarations for that specific context, along with a reference to its parent environment."

> "It consists of two parts: the Environment Record, which stores the actual variables, and the Outer Environment Reference, which points to the lexical parent. This structure is essential for variable resolution and scope creation.

> "When a variable is accessed, JavaScript uses the Scope Chain to resolve it. It starts from the current Lexical Environment and moves up through the outer references until it finds the variable, or until it reaches the global scope. If the variable isn’t found anywhere in the chain, a ReferenceError is thrown."

> "So, the scope chain is essentially the chain of lexical environments connected via their outer references. This mechanism is what enables lexical scoping and closures in JavaScript."

> "In short, Lexical Environments are the internal structures that hold the variables, and the Scope Chain is how JavaScript connects those environments together to resolve variable access — both are fundamental to understanding scoping, closures, and memory behavior in JavaScript."

## JavaScript Strict Mode

### What is Strict Mode?

**Strict Mode** is a way to opt in to a restricted variant of JavaScript that catches common coding bugs and prevents unsafe actions.

Introduced in ES5 using `"use strict"` directive.

### How to Enable Strict Mode

### Global Strict Mode

Add `"use strict";` at the top of a script:

```jsx
"use strict";
x = 10; // ReferenceError: x is not defined

```

### Function-Level Strict Mode

```jsx
function test() {
  "use strict";
  // strict mode only applies here
}

```

### Automatic Strict Mode

- **ES6 Modules** and **Classes** are in strict mode by default, even without `"use strict"`.

### Changes in Behavior Under Strict Mode

### 1. Variable Declaration is Mandatory

```jsx
"use strict";
x = 10; // ❌ ReferenceError: x is not defined

```

### 2. `this` Binding

- In sloppy mode, `this` refers to the global object in functions.
- In strict mode, `this` remains `undefined` if not explicitly bound.

```jsx
"use strict";
function test() {
  console.log(this); // undefined
}

```

### 3. Arguments Object Restrictions

- Arguments no longer reflect parameter changes.
- Modifying `arguments[i]` does not affect the corresponding parameter.

```jsx
"use strict";
function test(a) {
  a = 42;
  console.log(arguments[0]); // remains original value
}

```

### 4. eval() Restrictions

- `eval` cannot create new variables in the surrounding scope.
- Safer and more predictable behavior.

```jsx
"use strict";
eval("var x = 2;");
console.log(x); // ReferenceError

```

### 5. Deleting Variables or Functions Not Allowed

```jsx
"use strict";
var x = 1;
delete x; // ❌ SyntaxError

```

### 6. Duplicate Parameter Names Not Allowed

```jsx
"use strict";
function test(a, a) {} // ❌ SyntaxError

```

### 7. Octal Literals Are Forbidden

```jsx
"use strict";
var x = 010; // ❌ SyntaxError

```

### **Performance Benefits**

- Strict mode simplifies how JS engines optimize code.
- Avoids ambiguity and corner cases, making parsing and execution faster.
- Some engines perform better on strict code due to easier static analysis.

### Summary

|Feature|Strict Mode Behavior|
|---|---|
|Undeclared variables|ReferenceError|
|`this` in functions|`undefined` instead of global object|
|Duplicate parameters|SyntaxError|
|Deleting variables/functions|SyntaxError|
|Octal literals|SyntaxError|
|eval()|No scope leakage|
|arguments object|Decoupled from function parameters|
|Modules/Classes|Strict by default|

Strict mode is highly recommended for writing secure and predictable JavaScript code.