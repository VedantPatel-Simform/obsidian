# JavaScript 'this' Keyword - Complete Deep Dive

## Core Concept

`this` is a runtime binding that refers to the context in which a function is called, NOT where it's defined. The value of `this` is determined by **how** a function is invoked.

## The Four Binding Rules (in order of precedence)

### 1. New Binding (Highest Precedence)

When a function is called with `new`, `this` binds to the newly created object.

```jsx
function Person(name) {
    this.name = name;
    this.greet = function() {
        console.log(`Hello, I'm ${this.name}`);
    };
}

const person = new Person("Alice");
person.greet(); // "Hello, I'm Alice"
// 'this' inside Person constructor refers to the new person object

```

### 2. Explicit Binding

Using `call()`, `apply()`, or `bind()` to explicitly set `this`.

```jsx
function greet() {
    console.log(`Hello, I'm ${this.name}`);
}

const person = { name: "Bob" };

// call() - arguments passed individually
greet.call(person); // "Hello, I'm Bob"

// apply() - arguments passed as array
greet.apply(person, []); // "Hello, I'm Bob"

// bind() - returns new function with bound 'this'
const boundGreet = greet.bind(person);
boundGreet(); // "Hello, I'm Bob"

```

### Hard Binding with bind()

Once bound, `this` cannot be overridden:

```jsx
function sayName() {
    console.log(this.name);
}

const obj1 = { name: "Object 1" };
const obj2 = { name: "Object 2" };

const boundFn = sayName.bind(obj1);
boundFn(); // "Object 1"

// Even explicit binding can't override hard binding
boundFn.call(obj2); // Still "Object 1"

```

### 3. Implicit Binding

When a function is called as a method of an object, `this` binds to that object.

```jsx
const user = {
    name: "Charlie",
    greet: function() {
        console.log(`Hello, I'm ${this.name}`);
    }
};

user.greet(); // "Hello, I'm Charlie"
// 'this' refers to 'user' object

```

### Implicit Binding Loss - Critical Interview Topic

The most common source of bugs:

```jsx
const user = {
    name: "David",
    greet: function() {
        console.log(`Hello, I'm ${this.name}`);
    }
};

// Direct call - works
user.greet(); // "Hello, I'm David"

// Assignment causes loss of binding
const greetFn = user.greet;
greetFn(); // "Hello, I'm undefined" (or error in strict mode)

// Callback scenarios
setTimeout(user.greet, 1000); // 'this' is lost
[1, 2, 3].forEach(user.greet); // 'this' is lost

// Solutions:
setTimeout(() => user.greet(), 1000); // Arrow function preserves context
setTimeout(user.greet.bind(user), 1000); // Explicit binding

```

### 4. Default Binding (Lowest Precedence)

When no other rule applies, `this` defaults to global object (window in browsers, global in Node.js) or `undefined` in strict mode.

```jsx
function defaultThis() {
    console.log(this);
}

defaultThis(); // Window object (browser) or undefined (strict mode)

// Strict mode behavior
"use strict";
function strictThis() {
    console.log(this); // undefined
}
strictThis();

```

## Arrow Functions - Special Case

Arrow functions don't have their own `this`. They inherit `this` from the enclosing lexical scope.

```jsx
const obj = {
    name: "Eve",
    regularFunction: function() {
        console.log("Regular:", this.name); // "Eve"

        const arrowFunction = () => {
            console.log("Arrow:", this.name); // "Eve" - inherits from regularFunction
        };

        function nestedRegular() {
            console.log("Nested:", this.name); // undefined - loses binding
        }

        arrowFunction();
        nestedRegular();
    }
};

obj.regularFunction();

```

### Arrow Functions Cannot Be Bound

```jsx
const arrowFn = () => console.log(this.name);
const obj = { name: "Test" };

// These don't change 'this' in arrow functions
arrowFn.call(obj); // Still uses lexical 'this'
arrowFn.bind(obj)(); // Still uses lexical 'this'

```

## Class Context

In ES6 classes, `this` refers to the instance:

```jsx
class MyClass {
    constructor(name) {
        this.name = name;
    }

    regularMethod() {
        console.log(`Regular: ${this.name}`);
    }

    arrowMethod = () => {
        console.log(`Arrow: ${this.name}`);
    }
}

const instance = new MyClass("Frank");
instance.regularMethod(); // "Regular: Frank"

// Method extraction
const extracted = instance.regularMethod;
extracted(); // undefined - loses binding

const extractedArrow = instance.arrowMethod;
extractedArrow(); // "Arrow: Frank" - maintains binding

```

## Advanced Scenarios

### Nested Objects

```jsx
const parent = {
    name: "Parent",
    child: {
        name: "Child",
        greet: function() {
            console.log(this.name); // "Child" - 'this' refers to immediate parent object
        }
    }
};

parent.child.greet(); // "Child"

```

### Event Handlers

```jsx
// DOM event context
button.addEventListener('click', function() {
    console.log(this); // Refers to the button element
});

// Arrow function in event handler
button.addEventListener('click', () => {
    console.log(this); // Refers to global object or undefined
});

```

### Array Methods with thisArg

```jsx
const numbers = [1, 2, 3];
const multiplier = {
    factor: 10,
    multiply: function(num) {
        return num * this.factor;
    }
};

// Using thisArg parameter
const results = numbers.map(function(num) {
    return this.multiply(num);
}, multiplier); // Pass multiplier as 'this'

console.log(results); // [10, 20, 30]

```

## Common Interview Questions & Answers

### Q: What will this code output?

```jsx
var name = "Global";
const obj = {
    name: "Object",
    getName: function() {
        return this.name;
    }
};

const getName = obj.getName;
console.log(getName()); // ?
console.log(obj.getName()); // ?

```

**Answer:**

- `getName()` outputs "Global" (implicit binding lost)
- `obj.getName()` outputs "Object" (implicit binding preserved)

### Q: Arrow Function Lexical Binding - What's the output?

```jsx
const obj = {
    name: "vedant",
    foo: function(){
        return () => {
            console.log(this.name)
        }
    }
}
obj.foo()
const foo2 = obj.foo()
foo2()

```

**Answer:**

- `obj.foo()` - No output (just returns the arrow function)
- `foo2()` - Outputs "vedant"

**Explanation:**

1. `obj.foo()` calls the method where `this` refers to `obj`
2. Inside `foo()`, an arrow function is created and returned (but not executed)
3. The arrow function captures `this` from its lexical scope (which is `obj`)
4. `foo2` stores this returned arrow function
5. When `foo2()` is called, the arrow function still remembers the original `this` (obj)
6. Therefore it prints "vedant" even though `foo2` is called without object context

**Key Insight:** Arrow functions "freeze" the `this` value from when they were created, not when they're called.

### Q: Fix the setTimeout issue

```jsx
const user = {
    name: "User",
    greet: function() {
        setTimeout(function() {
            console.log(`Hello ${this.name}`);
        }, 1000);
    }
};

user.greet(); // "Hello undefined"

```

**Solutions:**

```jsx
// Solution 1: Arrow function
greet: function() {
    setTimeout(() => {
        console.log(`Hello ${this.name}`);
    }, 1000);
}

// Solution 2: Store reference
greet: function() {
    const self = this;
    setTimeout(function() {
        console.log(`Hello ${self.name}`);
    }, 1000);
}

// Solution 3: Bind
greet: function() {
    setTimeout(function() {
        console.log(`Hello ${this.name}`);
    }.bind(this), 1000);
}

```

## Performance Considerations

- Arrow functions are slightly faster for lexical `this` scenarios
- `bind()` creates new functions, consider memory implications
- Method binding in render methods (React) can cause unnecessary re-renders

## Debugging Tips

```jsx
function debugThis() {
    console.log("Function name:", debugThis.name);
    console.log("'this' value:", this);
    console.log("'this' constructor:", this.constructor.name);
}

// Use in different contexts to understand binding

```

## Key Takeaways for Interviews

1. **Rule Priority:** New > Explicit > Implicit > Default
2. **Arrow functions inherit `this` lexically**
3. **Binding loss is the most common bug**
4. **`bind()` creates permanent binding**
5. **Strict mode affects default binding**
6. **Event handlers bind `this` to the element**
7. **Class methods need careful handling when extracted**

# JavaScript Constructor Functions and 'new' Operator - Complete Notes

## Purpose and Problem Statement

When we need to create multiple similar objects (like users, menu items, etc.), using regular `{...}` object literal syntax becomes repetitive and inefficient. Constructor functions with the `new` operator solve this by providing reusable object creation code.

## Constructor Function Basics

### Definition and Conventions

Constructor functions are technically regular functions but follow specific conventions:

1. **Named with capital letter first** (PascalCase)
2. **Should be executed only with `new` operator**

```jsx
function User(name) {
  this.name = name;
  this.isAdmin = false;
}

let user = new User("Jack");
alert(user.name); // Jack
alert(user.isAdmin); // false

```

### What Happens When Using 'new'

When a function is executed with `new`, it performs these steps automatically:

1. **A new empty object is created and assigned to `this`**
2. **The function body executes** (usually modifies `this`, adds properties)
3. **The value of `this` is returned**

### Behind the Scenes Visualization

```jsx
function User(name) {
  // this = {};  (implicitly created)

  // add properties to this
  this.name = name;
  this.isAdmin = false;

  // return this;  (implicitly returned)
}

```

### Equivalent Manual Creation

```jsx
// Using constructor
let user = new User("Jack");

// Is equivalent to:
let user = {
  name: "Jack",
  isAdmin: false
};

```

### Key Benefits

- **Reusable**: Can create multiple objects with `new User("Ann")`, `new User("Alice")`
- **Shorter than literals**: Less code repetition
- **Easy to read**: Clear intent of object creation
- **Flexible**: Can accept parameters to customize each instance

## Advanced Constructor Concepts

### Any Function Can Be a Constructor

**Important**: Any function (except arrow functions, as they don't have `this`) can be used as a constructor. It can be run with `new` and will execute the algorithm above.

```jsx
function regularFunction() {
  this.property = "I'm a constructor now!";
}

let obj = new regularFunction(); // Works!
console.log(obj.property); // "I'm a constructor now!"

```

**Note**: The "capital letter first" is just a common agreement to indicate a function should be run with `new`.

### Immediately Invoked Constructor Function

For complex single-use objects, you can create and immediately call a constructor:

```jsx
let user = new function() {
  this.name = "John";
  this.isAdmin = false;

  // ...other code for user creation
  // maybe complex logic and statements
  // local variables etc
};

```

**Purpose**: Encapsulates complex object creation code without future reuse possibility.

## Constructor Mode Detection: new.target

### Basic Usage

The `new.target` property allows checking if a function was called with `new`:

```jsx
function User() {
  alert(new.target);
}

// without "new":
User(); // undefined

// with "new":
new User(); // function User { ... }

```

### Flexible Constructor Pattern

Make functions work both with and without `new`:

```jsx
function User(name) {
  if (!new.target) { // if called without new
    return new User(name); // automatically add new
  }

  this.name = name;
}

let john = User("John"); // redirects call to new User
alert(john.name); // John

```

**Note**: This approach is sometimes used in libraries for flexibility, but may make code less obvious. Using `new` explicitly shows clear intent of object creation.

## Return Statement in Constructors

### Default Behavior

Usually, constructors don't have a `return` statement. They automatically return `this` after writing properties to it.

### Custom Return Rules

If a `return` statement is present:

1. **Return with an object**: The object is returned instead of `this`
2. **Return with a primitive**: It's ignored, `this` is returned

### Example: Returning an Object

```jsx
function BigUser() {
  this.name = "John";

  return { name: "Godzilla" };  // <-- returns this object instead of this
}

alert(new BigUser().name);  // Godzilla

```

### Returning an Function

```jsx
function User(name){
    this.name = name;
    return function(){
        console.log("Hello")
    }
}

console.log(new User("abc")) // will return the anonymous function 
// as function is also object
```

### Example: Returning a Primitive

```jsx
function SmallUser() {
  this.name = "John";

  return; // <-- ignored, returns this
  // return "something"; // <-- also ignored, returns this
}

alert(new SmallUser().name);  // John

```

**Note**: This behavior is mentioned for completeness. Usually constructors don't have return statements.

## Syntax Details

### Parentheses with 'new'

Parentheses after `new` are optional when no arguments are passed:

```jsx
let user = new User; // <-- no parentheses (valid)

// same as
let user = new User(); // <-- with parentheses (preferred style)

```

**Style Note**: Omitting parentheses is permitted by specification but not considered "good style".

## Methods in Constructor Functions

### Adding Methods

Constructor functions can add both properties and methods:

```jsx
function User(name) {
  this.name = name;

  this.sayHi = function() {
    alert("My name is: " + this.name);
  };
}

let john = new User("John");
john.sayHi(); // My name is: John

/*
john = {
   name: "John",
   sayHi: function() { ... }
}
*/

```

### Method Context

Methods added in constructors maintain proper `this` binding to the instance:

```jsx
function Calculator(initialValue) {
  this.value = initialValue || 0;

  this.add = function(n) {
    this.value += n;
    return this; // Enable chaining
  };

  this.multiply = function(n) {
    this.value *= n;
    return this;
  };

  this.getResult = function() {
    return this.value;
  };
}

let calc = new Calculator(5);
let result = calc.add(3).multiply(2).getResult(); // 16

```

## Built-in Constructor Functions

JavaScript provides constructor functions for many built-in objects:

- `Date` for dates
- `Set` for sets
- `Array` for arrays
- `Object` for objects
- And many others

```jsx
let now = new Date();
let numbers = new Set([1, 2, 3]);
let arr = new Array(5); // Array with 5 empty slots

```

## Common Patterns and Best Practices

### Factory Pattern Alternative

Instead of constructors, you might see factory functions:

```jsx
// Constructor approach
function User(name) {
  this.name = name;
  this.isAdmin = false;
}
let user1 = new User("John");

// Factory approach
function createUser(name) {
  return {
    name: name,
    isAdmin: false
  };
}
let user2 = createUser("John");

```

### Validation in Constructors

```jsx
function User(name, age) {
  if (!name) {
    throw new Error("Name is required");
  }
  if (age < 0) {
    throw new Error("Age cannot be negative");
  }

  this.name = name;
  this.age = age;
  this.isAdmin = false;
}

```

### Private Variables Pattern

```jsx
function User(name) {
  // Private variable (closure)
  let id = Math.random();

  // Public properties
  this.name = name;

  // Public method with access to private variable
  this.getId = function() {
    return id;
  };
}

let user = new User("John");
console.log(user.name); // "John"
console.log(user.getId()); // Random number
console.log(user.id); // undefined (private)

```

## Key Takeaways

1. **Constructor functions are regular functions** with naming convention (PascalCase)
2. **`new` operator creates empty object, assigns to `this`, executes function, returns `this`**
3. **Any function can be constructor** (except arrow functions)
4. **`new.target` detects constructor mode** (rarely used)
5. **Return statement can override default behavior** (object returns override `this`, primitives are ignored)
6. **Parentheses with `new` are optional** but recommended for style
7. **Methods can be added** directly in constructor
8. **Built-in constructors exist** for many JavaScript objects
9. **Constructor pattern enables reusable object creation**

## Evolution Note

This chapter covers constructor function basics. More advanced syntax (classes) provides modern alternatives to constructor functions while maintaining similar functionality under the hood.

# JavaScript Optional Chaining '?.' - Complete Notes

## Overview and Browser Support

Optional chaining `?.` is a **recent addition** to JavaScript that provides a safe way to access nested object properties, even if an intermediate property doesn't exist.

**Browser Support**: Old browsers may need polyfills for this feature.

## The Problem: Non-Existing Properties

### Common Scenarios

The problem occurs frequently in real-world JavaScript development:

### Scenario 1: User Objects with Optional Address

```jsx
let user = {}; // a user without "address" property

alert(user.address.street); // Error!
// TypeError: Cannot read property 'street' of undefined

```

**Expected behavior**: We'd prefer to get `undefined` instead of an error.

### Scenario 2: DOM Element Query

```jsx
// document.querySelector('.elem') returns null if no element found
let html = document.querySelector('.elem').innerHTML; // Error if element doesn't exist
// TypeError: Cannot read property 'innerHTML' of null

```

**Expected behavior**: We want `html = null` when element doesn't exist, not an error.

### Traditional Solutions and Their Problems

### Solution 1: Conditional Operator

```jsx
let user = {};

alert(user.address ? user.address.street : undefined);

```

**Problems**:

- Code duplication: `user.address` appears twice
- Inelegant and verbose

### Solution 2: Multiple Conditional Checks

```jsx
let html = document.querySelector('.elem') ? document.querySelector('.elem').innerHTML : null;

```

**Problems**:

- `document.querySelector('.elem')` called twice (performance issue)
- Redundant code

### Solution 3: Deeply Nested Properties

```jsx
let user = {}; // user has no address

alert(user.address ? user.address.street ? user.address.street.name : null : null);

```

**Problems**:

- Extremely ugly and hard to read
- Difficult to understand and maintain
- Prone to errors

### Solution 4: Logical AND Operator

```jsx
let user = {}; // user has no address

alert(user.address && user.address.street && user.address.street.name); // undefined (no error)

```

**Problems**:

- Still has property name duplication
- `user.address` appears three times
- Not ideal for complex nested structures

## Optional Chaining Solution

### Basic Syntax and Behavior

The optional chaining `?.` stops evaluation if the value before `?.` is `undefined` or `null` and returns `undefined`.

**Definition**: Something "exists" if it's not `null` and not `undefined`.

### Core Mechanism

```jsx
value?.prop

```

**Behavior**:

- **If `value` exists**: Works as `value.prop`
- **If `value` is `undefined/null`**: Returns `undefined`

### Basic Examples

### Safe Property Access

```jsx
let user = {}; // user has no address

alert(user?.address?.street); // undefined (no error)

```

**Benefits**:

- Short and clean code
- No duplication
- Safe execution

### DOM Query Example

```jsx
let html = document.querySelector('.elem')?.innerHTML; // undefined if no element

```

### Null Object Handling

```jsx
let user = null;

alert(user?.address); // undefined
alert(user?.address.street); // undefined

```

### Important Limitations

### Scope of Optional Chaining

```jsx
user?.address.street.name

```

**Key Point**: The `?.` only makes the value **before** it optional, not further properties.

- `user` can safely be `null/undefined`
- `address.street.name` are accessed normally after `user?.address`
- If you want `street` to be optional too: `user?.address?.street?.name`

## Best Practices and Guidelines

### Don't Overuse Optional Chaining

```jsx
// GOOD: address is optional, but user must exist
user.address?.street

// BAD: overusing optional chaining
user?.address?.street

```

**Principle**: Use `?.` only where it's acceptable that something doesn't exist according to your code logic.

**Why avoid overuse**:

- Can silence programming errors
- Makes debugging more difficult
- Hides bugs that should be caught

### Variable Declaration Requirement

```jsx
// ERROR: ReferenceError: user is not defined
user?.address;

// CORRECT: Variable must be declared first
let user = null;
user?.address; // Works fine

```

**Rule**: The variable before `?.` must be declared (using `let/const/var` or as function parameter).

## Short-Circuiting Behavior

### Evaluation Stops Immediately

When the left part doesn't exist, `?.` immediately stops ("short-circuits") the evaluation.

```jsx
let user = null;
let x = 0;

user?.sayHi(x++); // no "user", so execution doesn't reach sayHi call and x++

alert(x); // 0, value not incremented

```

**Important**: Function calls and operations to the right of `?.` won't be executed if the left part is `null/undefined`.

## Advanced Variants

### 1. Optional Method Calls: ?.()

### Syntax and Usage

```jsx
obj.method?.()

```

**Purpose**: Call a function that may not exist.

### Example

```jsx
let userAdmin = {
  admin() {
    alert("I am admin");
  }
};

let userGuest = {};

userAdmin.admin?.(); // "I am admin"
userGuest.admin?.(); // nothing happens (no such method)

```

**Process**:

1. First use dot (`userAdmin.admin`) to get the property
2. Then `?.()` checks if the function exists
3. If exists, it runs; otherwise, evaluation stops silently

### 2. Optional Bracket Notation: ?.[]

### Syntax and Usage

```jsx
obj?.[prop]

```

**Purpose**: Safely access properties using bracket notation when object may not exist.

### Example

```jsx
let key = "firstName";

let user1 = {
  firstName: "John"
};

let user2 = null;

alert(user1?.[key]); // John
alert(user2?.[key]); // undefined

```

### 3. Optional Chaining with Delete

```jsx
delete user?.name; // delete user.name if user exists

```

**Usage**: Safe property deletion when object may not exist.

## Usage Restrictions

### Cannot Use on Left Side of Assignment

```jsx
let user = null;

user?.name = "John"; // Error, doesn't work
// because it evaluates to: undefined = "John"

```

**Rule**: Optional chaining `?.` can be used for safe **reading** and **deleting**, but **not writing**.

## Complete Syntax Summary

### Three Main Forms

1. **Property Access**: `obj?.prop`
    - Returns `obj.prop` if `obj` exists, otherwise `undefined`
2. **Bracket Access**: `obj?.[prop]`
    - Returns `obj[prop]` if `obj` exists, otherwise `undefined`
3. **Method Call**: `obj.method?.()`
    - Calls `obj.method()` if `obj.method` exists, otherwise returns `undefined`

## Real-World Examples

### API Response Handling

```jsx
// Traditional approach
if (response && response.data && response.data.user && response.data.user.profile) {
  console.log(response.data.user.profile.name);
}

// With optional chaining
console.log(response?.data?.user?.profile?.name);

```

### Configuration Objects

```jsx
// Settings with optional nested properties
function initializeApp(config) {
  const theme = config?.ui?.theme || 'light';
  const apiUrl = config?.api?.baseUrl || '<https://api.example.com>';
  const debug = config?.development?.debug || false;
}

```

### Event Handling

```jsx
// Safe event listener removal
element?.removeEventListener?.('click', handler);

// Safe method execution
window?.gtag?.('event', 'page_view');

```

### Array and Object Combinations

```jsx
// Accessing array elements safely
const firstUser = users?.[0]?.name;

// Method chaining with optional steps
const result = data?.filter?.(item => item.active)?.map?.(item => item.name);

```

## Performance Considerations

### Efficient Property Access

```jsx
// GOOD: Single query with optional chaining
const element = document.querySelector('.elem');
const content = element?.innerHTML;
const classes = element?.className;

// BAD: Multiple queries
const content = document.querySelector('.elem')?.innerHTML;
const classes = document.querySelector('.elem')?.className;

```

### Memory and Execution

- Optional chaining has minimal performance overhead
- Short-circuiting prevents unnecessary evaluations
- More efficient than multiple conditional checks

## Error Handling Patterns

### Graceful Degradation

```jsx
// Fallback values with optional chaining
const userName = user?.profile?.name ?? 'Anonymous';
const userEmail = user?.contact?.email ?? 'No email provided';

```

### Combining with Nullish Coalescing

```jsx
// Using ?? with optional chaining for default values
const config = {
  timeout: settings?.api?.timeout ?? 5000,
  retries: settings?.api?.retries ?? 3,
  baseUrl: settings?.api?.baseUrl ?? '<https://api.example.com>'
};

```

## Key Takeaways

1. **Optional chaining provides safe property access** without throwing errors
2. **Use sparingly** - only where properties are genuinely optional
3. **Cannot be used for assignment** - only for reading and deleting
4. **Requires variable declaration** - cannot be used with undeclared variables
5. **Short-circuits evaluation** - stops immediately when encountering null/undefined
6. **Three main forms**: `?.`, `?.[]`, and `?.()`
7. **Combines well with nullish coalescing** (`??`) for default values
8. **Prevents code duplication** and makes nested property access cleaner
9. **Don't overuse** - can hide programming errors and make debugging difficult
10. **Modern feature** - may need polyfills for older browsers

# JavaScript Symbols - Complete Study Notes

## Object Property Keys - Fundamental Rules

**Only two primitive types can serve as object property keys:**

- String type
- Symbol type

**Auto-conversion behavior:**

- If you use another type (like number), it's automatically converted to string
- `obj[1]` is the same as `obj["1"]`
- `obj[true]` is the same as `obj["true"]`

## What is a Symbol?

A **symbol** represents a **unique identifier**.

### Creating Symbols

**Basic creation:**

```jsx
let id = Symbol();

```

**With description (symbol name):**

```jsx
let id = Symbol("id"); // Description is mainly for debugging

```

### Key Characteristics

**Guaranteed uniqueness:**

- Even symbols with identical descriptions are different values
- Example:

```jsx
let id1 = Symbol("id");
let id2 = Symbol("id");
alert(id1 == id2); // false

```

**Important note:** JavaScript symbols are different from symbols in other languages like Ruby.

**Summary definition:** A symbol is a "primitive unique value" with an optional description.

## Symbols Don't Auto-Convert to String

**The rule:** Most JavaScript values support implicit string conversion, but symbols are special - they don't auto-convert.

**This will cause an error:**

```jsx
let id = Symbol("id");
alert(id); // TypeError: Cannot convert a Symbol value to a string

```

**This is a "language guard"** - prevents accidental conversion between fundamentally different types (strings vs symbols).

### How to Display Symbols

**Method 1 - Using .toString():**

```jsx
let id = Symbol("id");
alert(id.toString()); // Symbol(id), now it works

```

**Method 2 - Using .description property:**

```jsx
let id = Symbol("id");
alert(id.description); // id (shows description only)

```

## "Hidden" Properties

Symbols allow creation of "hidden" object properties that other code can't accidentally access or overwrite.

### Use Case Example

**Problem scenario:** Working with user objects from third-party code

```jsx
let user = { // belongs to another code
  name: "John"
};

```

**Solution using symbols:**

```jsx
let id = Symbol("id");
user[id] = 1;
alert(user[id]); // we can access the data using the symbol as the key

```

### Benefits of Symbol("id") over String "id"

1. **Safety:** Adding fields to third-party objects is unsafe with strings (might affect pre-defined behavior)
2. **No accidental access:** Symbols cannot be accessed accidentally by third-party code
3. **No conflicts:** Multiple scripts can use their own symbols without conflicts

**Example of conflict-free usage:**

```jsx
// Script 1
let id = Symbol("id");
user[id] = "Our id value";

// Script 2
let id = Symbol("id");
user[id] = "Their id value";
// No conflict! Both symbols are different despite same description

```

**Example of string conflict:**

```jsx
let user = { name: "John" };

// Our script
user.id = "Our id value";

// Another script
user.id = "Their id value"; // Boom! Overwritten!

```

## Symbols in Object Literals

**To use a symbol in object literal, use square brackets:**

```jsx
let id = Symbol("id");

let user = {
  name: "John",
  [id]: 123 // not "id": 123
};

```

**Why square brackets?** We need the value from the variable `id` as the key, not the string "id".

## Symbols are Skipped by for…in

**Symbolic properties do not participate in for...in loops:**

```jsx
let id = Symbol("id");
let user = {
  name: "John",
  age: 30,
  [id]: 123
};

for (let key in user) alert(key); // name, age (no symbols)
alert("Direct: " + user[id]); // Direct: 123 (direct access works)

```

**Object.keys(user) also ignores symbols** - part of the "hiding symbolic properties" principle.

**Libraries and scripts won't unexpectedly access symbolic properties** when looping over objects.

### Exception: Object.assign

**Object.assign copies both string AND symbol properties:**

```jsx
let id = Symbol("id");
let user = {
  [id]: 123
};

let clone = Object.assign({}, user);
alert(clone[id]); // 123

```

**This is by design:** When cloning or merging objects, we usually want ALL properties copied (including symbols).

## Global Symbols

**Problem:** Sometimes we want same-named symbols to be the same entities across different parts of an application.

**Solution:** Global symbol registry

### Using Global Symbol Registry

**Symbol.for(key) method:**

- Reads (creates if absent) a symbol from the global registry
- Checks global registry for symbol described as `key`
- If exists: returns it
- If doesn't exist: creates new Symbol(key) and stores it in registry

**Example:**

```jsx
// Read from global registry
let id = Symbol.for("id"); // if symbol didn't exist, it's created

// Read again (maybe from another part of code)
let idAgain = Symbol.for("id");

// Same symbol
alert(id === idAgain); // true

```

**Global symbols:** Symbols inside the registry are called global symbols - for application-wide, everywhere-accessible symbols.

**Note:** This is similar to Ruby, where there's a single symbol per name. In JavaScript, this is true for global symbols.

## Symbol.keyFor

**Purpose:** Return a name by global symbol (opposite of Symbol.for)

**Usage:**

```jsx
// Get symbol by name
let sym = Symbol.for("name");
let sym2 = Symbol.for("id");

// Get name by symbol
alert(Symbol.keyFor(sym)); // name
alert(Symbol.keyFor(sym2)); // id

```

**Important limitations:**

- Symbol.keyFor uses global symbol registry to look up the key
- **Doesn't work for non-global symbols**
- Returns `undefined` for non-global symbols

**Example:**

```jsx
let globalSymbol = Symbol.for("name");
let localSymbol = Symbol("name");

alert(Symbol.keyFor(globalSymbol)); // name, global symbol
alert(Symbol.keyFor(localSymbol)); // undefined, not global

alert(localSymbol.description); // name (all symbols have description property)

```

## System Symbols

**Definition:** Many "system" symbols that JavaScript uses internally to fine-tune various aspects of objects.

**Location:** Listed in specification in the "Well-known symbols" table.

**Examples of system symbols:**

- Symbol.hasInstance
- Symbol.isConcatSpreadable
- Symbol.iterator
- Symbol.toPrimitive
- ...and more

**Usage example:** Symbol.toPrimitive allows describing object-to-primitive conversion.

## Summary

### What Symbols Are

- **Symbol is a primitive type for unique identifiers**
- Created with `Symbol()` call with optional description (name)
- **Always different values, even with same name**
- For same-named symbols to be equal: use global registry with `Symbol.for(key)`
- Multiple calls of `Symbol.for` with same key return exactly the same symbol

### Two Main Use Cases

### 1. "Hidden" Object Properties

- Add properties to objects that "belong" to other scripts/libraries
- Create symbol and use as property key
- Symbolic properties don't appear in `for...in` loops
- Won't be accidentally processed with other properties
- Can't be accessed directly by other scripts without the symbol
- Property protected from accidental use or overwrite
- Can "covertly" hide something in objects that we need but others shouldn't see

### 2. System Symbols

- Many system symbols used by JavaScript (accessible as `Symbol.*`)
- Used to alter built-in behaviors
- Examples: `Symbol.iterator` for iterables, `Symbol.toPrimitive` for object-to-primitive conversion

### Technical Note on "Hiding"

**Symbols are not 100% hidden:**

- `Object.getOwnPropertySymbols(obj)` - gets all symbols of an object
- `Reflect.ownKeys(obj)` - returns all keys including symbolic ones
- **However:** Most libraries, built-in functions, and syntax constructs don't use these methods