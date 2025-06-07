# JavaScript Closures - Comprehensive Notes

## What is a Closure?

A **closure** is the combination of a function bundled together (enclosed) with references to its surrounding state (the lexical environment). Closures give functions access to their outer scope and are created every time a function is created, at function creation time.

## Lexical Scoping

**Lexical scoping** describes how a parser resolves variable names when functions are nested. The word "lexical" refers to the fact that lexical scoping uses the location where a variable is declared within the source code to determine where that variable is available.

### Basic Example

```jsx
function init() {
  var name = "Mozilla"; // local variable created by init
  function displayName() {
    // inner function that forms a closure
    console.log(name); // uses variable from parent function
  }
  displayName();
}
init();

```

**Key Points:**

- Inner functions have access to variables of outer scopes
- `displayName()` has no local variables but can access `name` from its parent function
- This demonstrates lexical scoping in action

## Scoping with let and const

### Traditional var Behavior (Pre-ES6)

- JavaScript variables only had **function scope** and **global scope**
- Variables declared with `var` are either function-scoped or global-scoped
- **Blocks with curly braces do NOT create scopes for `var`**

```jsx
if (Math.random() > 0.5) {
  var x = 1;
} else {
  var x = 2;
}
console.log(x); // Works! x is accessible (should throw error in other languages)

```

### ES6 Block Scoping

- `let` and `const` create **block-scoped variables**
- Blocks are finally treated as scopes in ES6, but only with `let` or `const`
- ES6 also introduced **modules**, which introduced another kind of scope

```jsx
if (Math.random() > 0.5) {
  const x = 1;
} else {
  const x = 2;
}
console.log(x); // ReferenceError: x is not defined

```

## Closure Fundamentals

### Basic Closure Example

```jsx
function makeFunc() {
  const name = "Mozilla";
  function displayName() {
    console.log(name);
  }
  return displayName; // Return the inner function
}

const myFunc = makeFunc();
myFunc(); // Still works! Prints "Mozilla"

```

**Why this works:**

- Functions in JavaScript form closures
- A closure is the combination of a function and the lexical environment within which that function was declared
- `myFunc` maintains a reference to its lexical environment, where `name` exists
- When `myFunc` is invoked, `name` remains available for use

### Function Factory Example

```jsx
function makeAdder(x) {
  return function (y) {
    return x + y;
  };
}

const add5 = makeAdder(5);
const add10 = makeAdder(10);

console.log(add5(2)); // 7
console.log(add10(2)); // 12

```

**Key Points:**

- `makeAdder` is a **function factory**
- `add5` and `add10` both form closures
- They share the same function body definition but store different lexical environments
- In `add5`'s environment, `x` is 5; in `add10`'s environment, `x` is 10

## Practical Applications of Closures

### Event-Based Programming

Closures are particularly useful in front-end JavaScript for event-based code where you define behavior and attach it to user-triggered events.

### Font Size Adjuster Example

```jsx
function makeSizer(size) {
  return () => {
    document.body.style.fontSize = `${size}px`;
  };
}

const size12 = makeSizer(12);
const size14 = makeSizer(14);
const size16 = makeSizer(16);

// Attach to buttons
document.getElementById("size-12").onclick = size12;
document.getElementById("size-14").onclick = size14;
document.getElementById("size-16").onclick = size16;

```

### Emulating Private Methods

JavaScript (prior to classes) didn't have native private methods, but closures can emulate them following the **Module Design Pattern**.

### Counter Example (Shared Lexical Environment)

```jsx
const counter = (function () {
  let privateCounter = 0;
  function changeBy(val) {
    privateCounter += val;
  }

  return {
    increment() {
      changeBy(1);
    },
    decrement() {
      changeBy(-1);
    },
    value() {
      return privateCounter;
    },
  };
})();

console.log(counter.value()); // 0
counter.increment();
counter.increment();
console.log(counter.value()); // 2

```

**Key Points:**

- Uses an **IIFE** (Immediately Invoked Function Expression)
- Single lexical environment shared by three functions
- `privateCounter` and `changeBy` are private members
- Public functions form closures that share the same lexical environment

### Counter Factory (Independent Closures)

```jsx
function makeCounter() {
  let privateCounter = 0;
  function changeBy(val) {
    privateCounter += val;
  }
  return {
    increment() { changeBy(1); },
    decrement() { changeBy(-1); },
    value() { return privateCounter; },
  };
}

const counter1 = makeCounter();
const counter2 = makeCounter();
// Each counter maintains independence

```

**Benefits:**

- **Data hiding** and **encapsulation** (normally associated with OOP)
- Each closure references a different version of the private variables

## Closure Scope Chain

Nested functions have access to **all outer scopes**, creating a chain of function scopes.

### Multi-Level Nesting Example

```jsx
// global scope
const e = 10;
function sum(a) {
  return function (b) {
    return function (c) {
      // outer functions scope
      return function (d) {
        // local scope
        return a + b + c + d + e;
      };
    };
  };
}

console.log(sum(1)(2)(3)(4)); // 20

```

### Block Scope Closures

```jsx
function outer() {
  let getY;
  {
    const y = 6;
    getY = () => y; // Closure over block-scoped variable
  }
  console.log(typeof y); // undefined
  console.log(getY()); // 6
}

```

### Module Scope Closures

```jsx
// myModule.js
let x = 5;
export const getX = () => x;
export const setX = (val) => { x = val; };

// other file
import { getX, setX } from "./myModule.js";
console.log(getX()); // 5
setX(6);
console.log(getX()); // 6

```

**Live Bindings:** Closures can close over imported values, which are **live bindings** - when the original value changes, the imported one changes accordingly.

## Common Mistakes: Closures in Loops

### The Problem (with var)

```jsx
function setupHelp() {
  var helpText = [
    { id: "email", help: "Your email address" },
    { id: "name", help: "Your full name" },
    { id: "age", help: "Your age (you must be over 16)" },
  ];

  for (var i = 0; i < helpText.length; i++) {
    var item = helpText[i]; // Culprit: var creates function scope
    document.getElementById(item.id).onfocus = function () {
      showHelp(item.help); // Always shows last item's help!
    };
  }
}

```

**Why it fails:**

- Three closures created by the loop share the **same single lexical environment**
- Variable `item` is declared with `var` (function scope due to hoisting)
- By the time callbacks execute, `item` points to the last entry

### Solution 1: Function Factory

```jsx
function makeHelpCallback(help) {
  return function () {
    showHelp(help);
  };
}

// In the loop:
document.getElementById(item.id).onfocus = makeHelpCallback(item.help);

```

### Solution 2: IIFE (Immediately Invoked Function Expression)

```jsx
for (var i = 0; i < helpText.length; i++) {
  (function () {
    var item = helpText[i];
    document.getElementById(item.id).onfocus = function () {
      showHelp(item.help);
    };
  })(); // Immediate execution with current value preserved
}

```

### Solution 3: let/const (Block Scoping)

```jsx
for (let i = 0; i < helpText.length; i++) {
  const item = helpText[i]; // Block-scoped
  document.getElementById(item.id).onfocus = () => {
    showHelp(item.help);
  };
}

```

### Solution 4: forEach

```jsx
helpText.forEach(function (text) {
  document.getElementById(text.id).onfocus = function () {
    showHelp(text.help);
  };
});

```

## Performance Considerations

### The Problem

- Each function instance manages its own scope and closure
- Creating functions within other functions unnecessarily affects performance (processing speed and memory consumption)

### Bad Practice

```jsx
function MyObject(name, message) {
  this.name = name.toString();
  this.message = message.toString();
  this.getName = function () { // Methods reassigned for every object creation
    return this.name;
  };
  this.getMessage = function () {
    return this.message;
  };
}

```

### Better Practice - Prototype Methods

```jsx
function MyObject(name, message) {
  this.name = name.toString();
  this.message = message.toString();
}

MyObject.prototype.getName = function () {
  return this.name;
};
MyObject.prototype.getMessage = function () {
  return this.message;
};

```

**Benefits:**

- Inherited prototype can be shared by all objects
- Method definitions don't occur at every object creation
- Better memory efficiency

**Note:** Redefining the entire prototype is not recommended - instead append to the existing prototype as shown above.

## Memory Management and Garbage Collection

### Memory Implications of Closures

```jsx
function createHeavyFunction() {
  const heavyData = new Array(1000000).fill('data'); // Large array

  return function() {
    // This closure keeps heavyData in memory even if we don't use it
    console.log('Function called');
  };
}

const func = createHeavyFunction();
// heavyData stays in memory as long as func exists

```

### Avoiding Memory Leaks

```jsx
function createOptimizedFunction() {
  const heavyData = new Array(1000000).fill('data');
  const neededValue = heavyData[0]; // Extract only what you need

  return function() {
    console.log(neededValue); // Only keeps neededValue, not entire heavyData
  };
}

```

### Proper Cleanup

```jsx
function createTimer() {
  let count = 0;
  const timer = setInterval(() => {
    count++;
    console.log(count);
  }, 1000);

  return {
    getCount: () => count,
    stop: () => {
      clearInterval(timer); // Proper cleanup to prevent memory leaks
      return count;
    }
  };
}

```

## Advanced Closure Patterns

### Memoization Pattern

```jsx
function memoize(fn) {
  const cache = new Map();

  return function(...args) {
    const key = JSON.stringify(args);

    if (cache.has(key)) {
      return cache.get(key);
    }

    const result = fn.apply(this, args);
    cache.set(key, result);
    return result;
  };
}

const expensiveFunction = memoize((n) => {
  console.log('Computing...');
  return n * n;
});

console.log(expensiveFunction(5)); // Computing... 25
console.log(expensiveFunction(5)); // 25 (from cache)

```

### Curry Function Pattern

```jsx
function curry(fn) {
  return function curried(...args) {
    if (args.length >= fn.length) {
      return fn.apply(this, args);
    }

    return function(...nextArgs) {
      return curried.apply(this, args.concat(nextArgs));
    };
  };
}

const add = (a, b, c) => a + b + c;
const curriedAdd = curry(add);

console.log(curriedAdd(1)(2)(3)); // 6
console.log(curriedAdd(1, 2)(3)); // 6
console.log(curriedAdd(1)(2, 3)); // 6

```

### Throttle and Debounce Patterns

```jsx
// Throttle - Execute at most once per specified time
function throttle(func, delay) {
  let lastExecTime = 0;

  return function(...args) {
    const currentTime = Date.now();

    if (currentTime - lastExecTime > delay) {
      lastExecTime = currentTime;
      return func.apply(this, args);
    }
  };
}

// Debounce - Execute only after specified time of inactivity
function debounce(func, delay) {
  let timeoutId;

  return function(...args) {
    clearTimeout(timeoutId);
    timeoutId = setTimeout(() => func.apply(this, args), delay);
  };
}

// Usage examples
const throttledScroll = throttle(() => console.log('Scrolling'), 100);
const debouncedSearch = debounce((query) => console.log('Searching:', query), 300);

```

## Closure vs Other Concepts

### Closure vs Callback

```jsx
// Callback - function passed as argument
function processData(data, callback) {
  const result = data.map(x => x * 2);
  callback(result);
}

// Closure - function with access to outer scope
function createProcessor(multiplier) {
  return function(data) {
    return data.map(x => x * multiplier); // Accesses multiplier from outer scope
  };
}

```

### Closure vs Arrow Functions

```jsx
// Regular function closure
function createCounter() {
  let count = 0;
  return function() {
    return ++count;
  };
}

// Arrow function closure (same behavior)
const createCounterArrow = () => {
  let count = 0;
  return () => ++count;
};

// Both create closures, but arrow functions don't have their own 'this'

```

## Real-World Examples

### State Management Pattern

```jsx
function createStore(initialState = {}) {
  let state = { ...initialState };
  const listeners = [];

  return {
    getState: () => ({ ...state }), // Return copy to prevent mutation

    setState: (newState) => {
      state = { ...state, ...newState };
      listeners.forEach(listener => listener(state));
    },

    subscribe: (listener) => {
      listeners.push(listener);
      return () => {
        const index = listeners.indexOf(listener);
        if (index > -1) listeners.splice(index, 1);
      };
    }
  };
}

const store = createStore({ count: 0 });
const unsubscribe = store.subscribe(state => console.log('State:', state));
store.setState({ count: 1 }); // State: { count: 1 }

```

### API Request Cache

```jsx
function createApiCache(ttl = 5 * 60 * 1000) { // 5 minutes default TTL
  const cache = new Map();

  return async function cachedFetch(url) {
    const now = Date.now();
    const cached = cache.get(url);

    if (cached && (now - cached.timestamp) < ttl) {
      return cached.data;
    }

    try {
      const response = await fetch(url);
      const data = await response.json();

      cache.set(url, {
        data,
        timestamp: now
      });

      return data;
    } catch (error) {
      // Return stale data if available
      return cached ? cached.data : Promise.reject(error);
    }
  };
}

const cachedFetch = createApiCache(10000); // 10 second cache

```

### Plugin System

```jsx
function createPluginSystem() {
  const plugins = [];
  const hooks = new Map();

  return {
    register: (plugin) => {
      plugins.push(plugin);

      // Register plugin hooks
      Object.keys(plugin.hooks || {}).forEach(hookName => {
        if (!hooks.has(hookName)) {
          hooks.set(hookName, []);
        }
        hooks.get(hookName).push(plugin.hooks[hookName]);
      });
    },

    execute: (hookName, data) => {
      const hookFunctions = hooks.get(hookName) || [];
      return hookFunctions.reduce((result, fn) => fn(result), data);
    },

    getPlugins: () => [...plugins] // Return copy
  };
}

const pluginSystem = createPluginSystem();
pluginSystem.register({
  name: 'logger',
  hooks: {
    beforeRequest: (data) => {
      console.log('Request:', data);
      return data;
    }
  }
});

```

## Debugging Closures

### Common Debugging Techniques

```jsx
// 1. Use console.dir to inspect closure scope
function createDebugClosure() {
  const secretValue = 'hidden';

  function innerFunction() {
    debugger; // Breakpoint here will show closure scope
    return secretValue;
  }

  console.dir(innerFunction); // Shows [[Scopes]] in browser console
  return innerFunction;
}

// 2. Name your functions for better stack traces
const namedClosure = (function createNamedClosure() {
  const value = 42;

  return function namedInnerFunction() {
    return value;
  };
})();

// 3. Use closure to create debugging helpers
function createLogger(prefix) {
  return function log(message) {
    console.log(`[${prefix}] ${new Date().toISOString()}: ${message}`);
  };
}

const apiLogger = createLogger('API');
const dbLogger = createLogger('DB');

```

## Browser Compatibility and Edge Cases

### IE and Legacy Browser Considerations

```jsx
// IE has issues with named function expressions in closures
var problematicInIE = function() {
  // This can cause issues in old IE versions
  return function namedFunction() {
    return 'value';
  };
};

// Safer approach for legacy browsers
var saferApproach = function() {
  var namedFunction = function() {
    return 'value';
  };
  return namedFunction;
};

```

### Temporal Dead Zone with Closures

```jsx
function temporalDeadZoneExample() {
  // This will throw ReferenceError
  const getClosure = () => {
    return function() {
      return value; // value is in temporal dead zone
    };
  };

  const closure = getClosure();
  const value = 'initialized'; // let/const are hoisted but not initialized

  return closure;
}

```

## Testing Closures

### Unit Testing Patterns

```jsx
// Testing private state through public interface
function testableCounter() {
  let count = 0;

  return {
    increment: () => ++count,
    decrement: () => --count,
    getValue: () => count,
    reset: () => { count = 0; } // Provide reset for testing
  };
}

// Test example (using Jest syntax)
test('counter maintains private state', () => {
  const counter = testableCounter();

  expect(counter.getValue()).toBe(0);
  counter.increment();
  expect(counter.getValue()).toBe(1);
  counter.reset();
  expect(counter.getValue()).toBe(0);
});

```

## Summary

### Key Concepts

1. **Closures** = function + lexical environment where function was declared
2. **Lexical scoping** uses location of variable declaration to determine availability
3. **Block scoping** with `let`/`const` vs **function scoping** with `var`
4. Closures can capture variables in **function scopes**, **block scopes**, and **module scopes**

### Use Cases

- **Event handling** and callback functions
- **Data privacy** and encapsulation (emulating private methods)
- **Function factories** and **module patterns**
- **Maintaining state** across function calls
- **Memoization** and **caching** patterns
- **Throttling** and **debouncing**
- **State management** systems
- **Plugin architectures**

### Common Pitfalls

- **Loop closures** with `var` (solved with `let`/`const`, IIFE, or function factories)
- **Performance issues** from unnecessary closure creation
- **Memory leaks** from unintended variable retention
- **Temporal dead zone** issues with `let`/`const`
- **Browser compatibility** with named function expressions

### Best Practices

- Use `let`/`const` instead of `var` for block scoping
- Use prototype methods instead of closure-based methods for objects
- Be mindful of performance implications when creating closures in loops or frequently called functions
- **Clean up** event listeners and timers to prevent memory leaks
- **Extract only needed values** from large objects in closures
- **Name your functions** for better debugging and stack traces
- **Provide reset methods** for testing closure-based modules
- **Use modern patterns** like memoization and debouncing appropriately