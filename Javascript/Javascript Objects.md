# JavaScript Objects

## Overview

- **Objects** store keyed collections of data and complex entities
- JavaScript objects are fundamental - they penetrate almost every aspect of the language
- Think of objects as a cabinet with signed files (key-value pairs)

## Creating Objects

### Two Syntaxes:

```jsx
let user = new Object(); // "object constructor" syntax
let user = {};           // "object literal" syntax (preferred)

```

### Object Literal with Properties:

```jsx
let user = {
  name: "John",  // key "name", value "John"
  age: 30        // key "age", value 30
};

```

## Property Access

### Dot Notation:

```jsx
alert(user.name); // John
alert(user.age);  // 30

```

### Adding/Modifying Properties:

```jsx
user.isAdmin = true;  // add new property

```

### Removing Properties:

```jsx
delete user.age;  // remove property

```

## Square Bracket Notation

### When to Use:

- For multiword property names
- When property name is dynamic/computed
- When property name doesn't meet variable identifier rules

### Examples:

```jsx
let user = {
  "likes birds": true  // multiword property must be quoted
};

// Access multiword properties
user["likes birds"] = true;
alert(user["likes birds"]); // true

// Dynamic property access
let key = "likes birds";
user[key] = true;  // same as user["likes birds"] = true

// Computed from user input
let key = prompt("What do you want to know?", "name");
alert(user[key]); // accesses user.name if "name" entered

```

## Computed Properties

Create properties with dynamic names during object creation:

```jsx
let fruit = prompt("Which fruit?", "apple");
let bag = {
  [fruit]: 5  // property name taken from variable
};
// If fruit="apple", creates bag.apple = 5

// Complex expressions allowed
let bag = {
  [fruit + 'Computers']: 5  // bag.appleComputers = 5
};

```

## Property Value Shorthand

When property name matches variable name:

```jsx
function makeUser(name, age) {
  return {
    name,    // same as name: name
    age,     // same as age: age
  };
}

// Can mix shorthand with normal properties
let user = {
  name,      // shorthand
  age: 30    // normal
};

```

## Property Names - Special Cases

### No Restrictions:

```jsx
let obj = {
  for: 1,      // reserved words OK
  let: 2,
  return: 3
};
```

### Automatic String Conversion:

```jsx
let obj = {
  0: "test"    // number 0 becomes string "0"
};
alert(obj["0"]); // test
alert(obj[0]);   // test (same property)

```

### Special `__proto__` Property:

```jsx
let obj = {};
obj.__proto__ = 5;           // assignment ignored, only object assign allowed
alert(obj.__proto__);        // [object Object]

```

## Property Existence Testing

### Using `undefined` Check:

```jsx
let user = {};
alert(user.noSuchProperty === undefined); // true = doesn't exist

```

### Using `in` Operator:

```jsx
let user = { name: "John", age: 30 };
alert("age" in user);     // true
alert("blabla" in user);  // false

// With variable
let key = "age";
alert(key in user);       // true

```

### When `in` is Better Than `undefined`:

In check for all enumerable properties so if any property is there of parent object then same will be true in child object also

```jsx
let obj = {
  test: undefined  // property exists but stores undefined
};
alert(obj.test);        // undefined
alert("test" in obj);   // true - property exists!

```

## The `for...in` Loop

Iterate over all object enumerable properties(alongside parent one):

```jsx
let user = {
  name: "John",
  age: 30,
  isAdmin: true
};

for (let key in user) {
  alert(key);        // name, age, isAdmin (keys)
  alert(user[key]);  // John, 30, true (values)
}
```

To get the Object’s own property , use the

`object.hasOwnProperty(property)` syntax

```jsx
for(let key in obj2){
  if(obj2.hasOwnProperty(key)){
    console.log(key)
  }
}
```

## Property Ordering Rules

### Integer Properties:

- **Integer properties** are sorted in ascending order
- Integer property = string that converts to integer and back unchanged
- `"49"` is integer property, `"+49"` and `"1.2"` are not

```jsx
let codes = {
  "49": "Germany",
  "41": "Switzerland",
  "1": "USA"
};

for (let code in codes) {
  alert(code); // 1, 41, 49 (sorted ascending)
}

```

### Non-Integer Properties:

- Listed in creation order

```jsx
let user = {
  name: "John",
  surname: "Smith"
};
user.age = 25;

for (let prop in user) {
  alert(prop); // name, surname, age (creation order)
}

```

### Workaround for Integer Property Sorting:

```jsx
let codes = {
  "+49": "Germany",  // "+" makes it non-integer
  "+41": "Switzerland",
  "+1": "USA"
};

for (let code in codes) {
  alert(+code); // 49, 41, 1 (creation order, convert back with +)
}

```

## Key Takeaways

- Objects are key-value collections
- Use `{}` literal syntax for creation
- Dot notation for simple properties, square brackets for complex/dynamic ones
- Properties can have any name (strings/symbols)
- Integer properties are auto-sorted, others follow creation order
- Use `in` operator to test property existence when `undefined` values possible
- `for...in` loop iterates over all enumerable properties

# JavaScript Object References and Copying - Study Notes

## Fundamental Difference: Primitives vs Objects

### Primitives (Copied by Value):

```jsx
let message = "Hello!";
let phrase = message; // Creates independent copy

// Two separate variables with same value
// Changing one doesn't affect the other

```

### Objects (Copied by Reference):

```jsx
let user = { name: "John" };
let admin = user; // Copies reference, NOT the object

admin.name = "Pete";
alert(user.name); // "Pete" - same object modified!

```

## How Object References Work

### Memory Storage:

- **Variable** stores the memory address (reference) to the object
- **Object** is stored separately in memory
- Think of variable as "sheet of paper with object's address"

### Visual Representation:

```
user variable → [memory address] → { name: "John" } (actual object)
admin variable → [same memory address] → (points to same object)

```

## Object Comparison

### Reference Comparison:

```jsx
let a = {};
let b = a; // Same reference
alert(a == b);  // true
alert(a === b); // true

let c = {};
let d = {}; // Different objects
alert(c == d); // false (different references)

```

### Key Points:

- Objects are equal **only if they reference the same object**
- Even identical-looking objects are not equal if they're separate instances

## Const Objects

### Const Allows Property Modification:

```jsx
const user = { name: "John" };
user.name = "Pete"; // ✅ Allowed - modifying property
alert(user.name);   // "Pete"

// user = {}; // ❌ Error - can't reassign reference

```

### Why This Works:

- `const` protects the **reference**, not the object's contents
- Object properties can still be modified
- Only reassignment of the entire object is forbidden

## Object Cloning Methods

### 1. Manual Cloning (Shallow):

```jsx
let user = { name: "John", age: 30 };
let clone = {};

// Copy each property manually
for (let key in user) {
  clone[key] = user[key];
}

clone.name = "Pete";
alert(user.name); // "John" - original unchanged

```

### 2. Object.assign() Method:

### Basic Syntax:

```jsx
Object.assign(destination, ...sources)

```

### Simple Cloning:

```jsx
let user = { name: "John", age: 30 };
let clone = Object.assign({}, user);

alert(clone.name); // "John"
alert(clone.age);  // 30

```

### Merging Objects:

```jsx
let user = { name: "John" };
let permissions1 = { canView: true };
let permissions2 = { canEdit: true };

Object.assign(user, permissions1, permissions2);
// user = { name: "John", canView: true, canEdit: true }

```

### Property Overwriting:

```jsx
let user = { name: "John" };
Object.assign(user, { name: "Pete" }); // Overwrites existing property
alert(user.name); // "Pete"

```

### 3. JSON.parse(JSON.stringify()) Method:

### Deep Cloning with JSON:

```jsx
let user = {
  name: "John",
  sizes: { height: 182, width: 50 }
};

let clone = JSON.parse(JSON.stringify(user));

user.sizes.width = 60;
alert(clone.sizes.width); // 50 - truly independent

```

### JSON Method Limitations:

```jsx
let user = {
  name: "John",
  birthday: new Date(),           // ❌ Becomes string
  sayHi: function() {},           // ❌ Ignored/lost completely
  undefinedProp: undefined,       // ❌ Property removed
  symbol: Symbol("id"),           // ❌ Cannot copy symbols
  mySet: new Set([1, 2, 3]),     // ❌ Becomes empty object {} - literal
  myMap: new Map([['a', 1]]),    // ❌ Becomes empty object {}
};

let clone = JSON.parse(JSON.stringify(user));
console.log(clone);
// Result: { name: "John", birthday: "2023-..." } - Most data lost!

```

**JSON.parse(JSON.stringify()) Limitations:**

- ❌ **Functions**: Completely ignored
- ❌ **undefined**: Properties with undefined values are removed
- ❌ **Symbols**: Cannot copy symbol properties
- ❌ **Date objects**: Converted to string format
- ❌ **Set/Map**: Reduced to empty object literals `{}`
- ❌ **Custom classes**: Lose their prototype, become plain objects
- ❌ **Circular references :** throws error
- ❌ **BigInt :** throws error, cannot serialize it

### 4. structuredClone() Method (Modern):

### Basic Usage:

```jsx
let user = {
  name: "John",
  sizes: { height: 182, width: 50 }
};

let clone = structuredClone(user);
alert(user.sizes === clone.sizes); // false - different objects

```

### Handles Circular References:

```jsx
let user = {};
user.me = user; // Circular reference

let clone = structuredClone(user);
alert(clone.me === clone); // true - circular ref preserved

```

### structuredClone Method:

**Advantages:**

```jsx
let user = {
  name: "John",
  birthday: new Date(),           // ✅ Maintains Date object
  undefinedProp: undefined,       // ✅ Keeps undefined properties
  mySet: new Set([1, 2, 3]),     // ✅ Deep copies Set with elements
  myMap: new Map([['a', 1]]),    // ✅ Deep copies Map with key-value pairs
};

let clone = structuredClone(user);
console.log(clone.birthday instanceof Date); // true - still a Date!
console.log(clone.mySet instanceof Set);     // true - still a Set!
console.log(clone.undefinedProp);            // undefined - preserved

```

**structuredClone Limitations:**

```jsx
let user = {
  sayHi: function() {},     // ❌ Error: functions not supported
  symbol: Symbol("id")      // ❌ Cannot copy symbols
};

// structuredClone(user); // Throws error due to function

```

**structuredClone Pros:**

- ✅ **Date objects**: Maintains as Date instances
- ✅ **undefined**: Preserves undefined key-value pairs
- ✅ **Set/Map**: Deep copies custom classes like Set, Map with their contents
- ✅ **Circular references**: Handles them correctly
- ✅ **TypedArrays**: Copies ArrayBuffer, Uint8Array, etc.
- ✅ **BigInt:** Correctly copies it

**structuredClone Cons:**

- ❌ **Functions**: Throws error when encountered
- ❌ **Symbols**: Cannot copy symbol properties

## Shallow vs Deep Cloning

### Shallow Cloning Problem:

```jsx
let user = {
  name: "John",
  sizes: { height: 182, width: 50 } // Nested object
};

let clone = Object.assign({}, user);
alert(user.sizes === clone.sizes); // true - shared reference!

user.sizes.width = 60;
alert(clone.sizes.width); // 60 - clone affected too!

```

### Deep Cloning Solution:

```jsx
// Using structuredClone
let clone = structuredClone(user);

// Using JSON (with limitations)
let clone = JSON.parse(JSON.stringify(user));

// Manual deep cloning (custom implementation needed)

```

## Cloning Method Comparison

|Method|Type|Pros|Cons|
|---|---|---|---|
|**Object.assign()**|Shallow|Simple, fast, merges objects|No nested object cloning|
|**JSON.parse(JSON.stringify())**|Deep|Simple syntax, handles basic nesting|Loses functions, undefined props, symbols, Date→string, Set/Map→{}|
|**structuredClone()**|Deep|Preserves Date, undefined, Set/Map, circular refs|Errors on functions, can't copy symbols|
|**Manual Loop**|Shallow/Deep|Full control|More code, need custom logic|
|**Lodash _.cloneDeep()**|Deep|Handles all cases including functions|External library required|

## Best Practices

### When to Use Each Method:

1. **Object.assign()**:
    - Simple objects without nesting
    - Merging multiple objects
    - Performance is important
2. **JSON.parse(JSON.stringify())**:
    - Deep cloning of data-only objects
    - No functions or special types
    - Quick and dirty solution
3. **structuredClone()**:
    - Modern deep cloning
    - Complex nested structures
    - Circular references present
4. **Custom Implementation**:
    - Need to handle functions
    - Special business logic required
    - Using libraries like Lodash

### Common Pitfalls:

- Forgetting that objects are copied by reference
- Using shallow cloning when deep cloning is needed
- Assuming `const` prevents object modification
- Not considering function/special type preservation when cloning

## Key Takeaways

- Objects are stored and copied **by reference**
- Multiple variables can point to the same object
- `const` objects can have their properties modified
- **Shallow cloning** copies first level only
- **Deep cloning** copies all nested levels
- Choose cloning method based on data structure and requirements
- Always test cloning behavior with your specific data types

# Object Methods

## Basic Object Inspection

### Object.keys()

Returns an array of an object's own enumerable property names.

```jsx
const obj = { a: 1, b: 2, c: 3 };
Object.keys(obj); // ['a', 'b', 'c']

```

### Object.values()

Returns an array of an object's own enumerable property values.

```jsx
const obj = { a: 1, b: 2, c: 3 };
Object.values(obj); // [1, 2, 3]

```

### Object.entries()

Returns an array of an object's own enumerable key-value pairs.

```jsx
const obj = { a: 1, b: 2, c: 3 };
Object.entries(obj); // [['a', 1], ['b', 2], ['c', 3]]

```

## Advanced Property Inspection

### Object.getOwnPropertyNames()

Returns an array of all properties (including non-enumerable) found directly on an object.

```jsx
const obj = { a: 1 };
Object.defineProperty(obj, 'b', { value: 2, enumerable: false });
Object.getOwnPropertyNames(obj); // ['a', 'b']
Object.keys(obj); // ['a'] - only enumerable

```

### Object.getOwnPropertyDescriptors()

Returns all property descriptors for an object's own properties.

```jsx
const obj = { a: 1 };
Object.getOwnPropertyDescriptors(obj);
// { a: { value: 1, writable: true, enumerable: true, configurable: true } }

```

## Prototype Chain Methods

### Object.getPrototypeOf()

Returns the prototype (internal [[Prototype]] property) of an object.

```jsx
const arr = [];
Object.getPrototypeOf(arr) === Array.prototype; // true

```

### Object.setPrototypeOf()

Sets the prototype of an object to another object or null.

```jsx
const obj = {};
const parent = { x: 10 };
Object.setPrototypeOf(obj, parent);
obj.x; // 10 (inherited from parent)

```

## Object Mutation Control

### Object.freeze()

Freezes an object: prevents new properties from being added and existing properties from being removed or modified.

```jsx
const obj = { a: 1 };
Object.freeze(obj);
obj.a = 2; // Silently fails (throws in strict mode)
obj.b = 3; // Silently fails
console.log(obj); // { a: 1 }

```

### Object.seal()

Seals an object: prevents properties from being added or deleted . but can change the existing ones

```jsx
const obj = { a: 1 };
Object.seal(obj);
obj.a = 2; // Works - can modify existing values
obj.b = 3; // Silently fails - cannot add new properties
delete obj.a; // Silently fails - cannot delete
console.log(obj); // { a: 2 }

```

### Object.preventExtensions()

Prevents new properties from being added to an object, but existing properties can still be modified or deleted.

```jsx
const obj = { a: 1 };
Object.preventExtensions(obj);
obj.a = 2; // Works
delete obj.a; // Works
obj.b = 3; // Silently fails

```

## Object State Checking

### Object.isFrozen()

Determines if an object is frozen.

```jsx
const obj = { a: 1 };
Object.isFrozen(obj); // false
Object.freeze(obj);
Object.isFrozen(obj); // true

```

### Object.isSealed()

Determines if an object is sealed.

```jsx
const obj = { a: 1 };
Object.isSealed(obj); // false
Object.seal(obj);
Object.isSealed(obj); // true

```

### Object.isExtensible()

Determines if an object can have new properties added to it.

```jsx
const obj = { a: 1 };
Object.isExtensible(obj); // true
Object.preventExtensions(obj);
Object.isExtensible(obj); // false

```

## Property Checking Methods

### Object.hasOwnProperty() vs Object.prototype.hasOwnProperty.call()

### obj.hasOwnProperty()

Checks if object has the specified property as its own (not inherited).

```jsx
const obj = { a: 1 };
obj.hasOwnProperty('a'); // true
obj.hasOwnProperty('toString'); // false (inherited from Object.prototype)

```

### Object.prototype.hasOwnProperty.call()

Safer way to check for own properties, avoiding issues when `hasOwnProperty` is overridden.

```jsx
const obj = {
  a: 1,
  hasOwnProperty: 'not a function' // overridden!
};

// obj.hasOwnProperty('a'); // TypeError: not a function
Object.prototype.hasOwnProperty.call(obj, 'a'); // true - works safely

// Modern alternative (ES2022+)
Object.hasOwn(obj, 'a'); // true

```

## Summary

- **Inspection**: `keys()`, `values()`, `entries()` for basic enumerable properties
- **Advanced Inspection**: `getOwnPropertyNames()`, `getOwnPropertyDescriptors()` for all properties
- **Prototype**: `getPrototypeOf()`, `setPrototypeOf()` for prototype chain manipulation
- **Mutation Control**: `freeze()`, `seal()`, `preventExtensions()` with increasing levels of restriction
- **State Checking**: `isFrozen()`, `isSealed()`, `isExtensible()` to check object mutability
- **Property Checking**: Use `Object.prototype.hasOwnProperty.call()` or `Object.hasOwn()` for safe property checking

# JavaScript Garbage Collection - Study Notes

## Overview

- **Memory management** in JavaScript is automatic and invisible
- **Garbage Collection (GC)** discovers and cleans up unused memory
- We create primitives, objects, functions - all take memory
- Engine automatically removes what's no longer needed

## Core Concept: Reachability

### Definition:

**"Reachable" values** = accessible or usable values that are guaranteed to stay in memory

### Root Values (Always Reachable):

```jsx
// These are inherently reachable and cannot be deleted:
function currentFunction() {     // Currently executing function
  let localVar = "data";         // Local variables and parameters
  // Other functions in call chain
}

let globalVar = "global";        // Global variables
// + some internal engine values

```

### Reachability Chain:

- Any value reachable from a **root** by reference or chain of references is considered reachable
- If global variable → object → another object, all are reachable

## Simple Example

### Single Reference:

```jsx
let user = { name: "John" };  // Object is reachable via 'user'

user = null;  // Reference lost → object becomes unreachable → GC removes it

```

### Multiple References:

```jsx
let user = { name: "John" };
let admin = user;  // Two references to same object

user = null;   // Object still reachable via 'admin'
admin = null;  // Now unreachable → GC can remove it

```

## Complex Example: Interlinked Objects

### Creating Interconnected Objects:

```jsx
function marry(man, woman) {
  woman.husband = man;    // Cross-references
  man.wife = woman;

  return {
    father: man,
    mother: woman
  };
}

let family = marry(
  { name: "John" },
  { name: "Ann" }
);

```

### Memory Structure:

```
family → { father: →, mother: → }
           ↓              ↓
    { name: "John" } ← → { name: "Ann" }
         ↑ wife      husband ↑

```

### Partial Cleanup:

```jsx
delete family.father;           // Remove one reference
delete family.mother.husband;   // Remove another reference

// John now has no INCOMING references (only outgoing)
// John becomes unreachable → GC removes it

```

**Key Rule**: Only **incoming references** matter for reachability, not outgoing ones.

## Unreachable Islands

### Complete Disconnection:

```jsx
family = null;  // Removes root reference

// Even though John ↔ Ann still reference each other,
// the entire "island" becomes unreachable from roots
// → Entire island gets garbage collected

```

### Visual Representation:

```
Before: [roots] → family → { John ↔ Ann }
After:  [roots]   family=null   { John ↔ Ann } (unreachable island)
                                      ↓
                                  [GC removes]

```

## Garbage Collection Algorithm: Mark-and-Sweep

### Basic Steps:

1. **Mark Phase**:
    - Start from roots and mark (remember) them
    - Visit and mark all references from roots
    - Continue marking referenced objects
    - Avoid revisiting same object twice
2. **Sweep Phase**:
    - Remove all unmarked objects
    - Free their memory

### Visual Process:

```jsx
// Step 1: Mark roots
[✓roots] → obj1 → obj2
           ↓
          obj3   obj4 (unreachable)

// Step 2: Mark referenced objects
[✓roots] → [✓obj1] → [✓obj2]
           ↓
          [✓obj3]   obj4 (unmarked)

// Step 3: Remove unmarked
[✓roots] → [✓obj1] → [✓obj2]
           ↓
          [✓obj3]   [REMOVED]

```

### Paint Bucket Analogy:

- Imagine spilling paint from roots
- Paint flows through all references
- Painted objects = reachable (kept)
- Unpainted objects = unreachable (removed)

## Performance Optimizations

### 1. Generational Collection:

```jsx
// Objects split into two categories:
let newObjects = [];  // Short lifespan - checked frequently
let oldObjects = [];  // Long survivors - checked less often

// Logic: Most objects die young, so focus on new ones

```

### 2. Incremental Collection:

```jsx
// Instead of: Mark ALL objects at once (causes delays)
// Do: Split into small chunks
for (let chunk of objectChunks) {
  markAndSweep(chunk);  // Many small GC cycles
  // Tiny delays instead of one big delay
}

```

### 3. Idle-Time Collection:

```jsx
// Run GC only when CPU is idle
if (cpu.isIdle()) {
  runGarbageCollection();
}
// Minimizes impact on code execution

```

## Practical Implications

### Memory Leaks Prevention:

```jsx
// ❌ Common leak: Forgot to clear reference
let globalCache = {};
function addToCache(id, data) {
  globalCache[id] = data;  // Never removed → memory leak
}

// ✅ Good practice: Clear when done
function clearCache(id) {
  delete globalCache[id];  // Make unreachable
}

```

### Event Listeners:

```jsx
// ❌ Potential leak
let element = document.getElementById('button');
element.addEventListener('click', handleClick);
// If element removed from DOM but listener not removed → leak

// ✅ Clean up
element.removeEventListener('click', handleClick);
element = null;  // Clear reference

```

### Circular References:

```jsx
// Modern GC handles circular references automatically
let obj1 = {};
let obj2 = {};
obj1.ref = obj2;
obj2.ref = obj1;  // Circular reference

obj1 = null;
obj2 = null;  // Both become unreachable → GC removes both

```

## Key Takeaways

### Automatic Management:

- ✅ GC runs automatically - no manual control needed
- ✅ Cannot force or prevent garbage collection
- ✅ Engine handles optimizations

### Reachability Rules:

- Objects stay in memory while reachable from roots
- Being referenced ≠ being reachable
- Entire islands of objects can become unreachable together
- Only incoming references matter for reachability

### Best Practices:

- Set references to `null` when done with objects
- Remove event listeners when elements are removed
- Don't worry about circular references (modern GC handles them)
- Avoid keeping unnecessary global references
- Let GC do its job - don't try to micro-manage memory

### Performance Understanding:

- Modern engines use sophisticated algorithms
- Multiple optimization strategies work together
- GC impact on performance is minimized through clever scheduling
- Understanding helps write more efficient code but don't over-optimize

### When to Care:

- Most of the time: Don't worry about GC
- Large applications: Be aware of memory patterns
- Performance critical: Understand object lifecycles
- Debugging: Know how references work