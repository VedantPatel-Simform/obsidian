# Strings

### What is a String?

A string is a sequence of characters used to represent text. It is one of the primitive data types in JavaScript.

### String as Primitive Data Type

Strings in JavaScript are primitive values and immutable.

### String Wrapper Object vs Primitive String

When you call a method on a primitive string, JavaScript wraps it in a temporary `String` object to access methods.

### Automatic Boxing and Unboxing

Primitive strings are temporarily converted into `String` objects when accessing methods.

### Is String Immutable?

Yes. Modifying a string creates a new one. You cannot change a character at a specific index.

### Immutability Concept and Implications

Each string operation like concatenation or slice creates a new string, which can impact memory.

### String Concatenation Creates New Strings

```jsx
let a = "hello";
let b = a + " world"; // New string created

```

### Memory Implications

Repeated operations on large strings may increase memory usage due to immutability.

### Can We Change Char by Index?

No. Index assignment does nothing. In strict mode, it may throw.

```jsx
"use strict";
let s = "hello";
s[0] = "H"; // No effect

```

### Bracket Notation Behavior

Similar to `charAt()`, but immutable.

### Strict Mode vs Non-Strict Mode

In strict mode, writing to an index of a string throws a `TypeError`.

### charAt() vs Bracket Notation

Both return the character at a given index. `charAt()` is more compatible.

## JavaScript String Methods - Quick Reference

### Character Access

### `charAt()` - Get character at index

```jsx
const str = "Hello";
console.log(str.charAt(0)); // "H"
console.log(str.charAt(10)); // "" (empty string if out of bounds)

```

### Searching

### `indexOf()` - Find first occurrence

```jsx
const str = "Hello World";
console.log(str.indexOf("World")); // 6
console.log(str.indexOf("xyz")); // -1 (not found)

```

### `includes()` - Check if string contains substring

```jsx
const str = "Hello World";
console.log(str.includes("World")); // true
console.log(str.includes("xyz")); // false

```

### `startsWith()` - Check if string starts with substring

```jsx
const str = "Hello World";
console.log(str.startsWith("Hello")); // true
console.log(str.startsWith("World")); // false

```

### `endsWith()` - Check if string ends with substring

```jsx
const str = "Hello World";
console.log(str.endsWith("World")); // true
console.log(str.endsWith("Hello")); // false

```

### Slicing

### `slice()` - Extract part of string

```jsx
const str = "Hello World";
console.log(str.slice(0, 5)); // "Hello"
console.log(str.slice(6)); // "World"
console.log(str.slice(-5)); // "World"

```

### `substring()` - Extract substring

```jsx
const str = "Hello World";
console.log(str.substring(0, 5)); // "Hello"
console.log(str.substring(6)); // "World"

```

### Conversion

### `split()` - Split string into array

```jsx
const str = "apple,banana,cherry";
console.log(str.split(",")); // ["apple", "banana", "cherry"]
console.log("Hello".split("")); // ["H", "e", "l", "l", "o"]

```

### Replacing

### `replace()` - Replace first occurrence

```jsx
const str = "Hello World Hello";
console.log(str.replace("Hello", "Hi")); // "Hi World Hello"

```

### `replaceAll()` - Replace all occurrences

```jsx
const str = "Hello World Hello";
console.log(str.replaceAll("Hello", "Hi")); // "Hi World Hi"

```

### Case Conversion

### `toLowerCase()` - Convert to lowercase

```jsx
const str = "Hello World";
console.log(str.toLowerCase()); // "hello world"

```

### `toUpperCase()` - Convert to uppercase

```jsx
const str = "Hello World";
console.log(str.toUpperCase()); // "HELLO WORLD"

```

### Trimming

### `trim()` - Remove whitespace from both ends

```jsx
const str = "  Hello World  ";
console.log(str.trim()); // "Hello World"

```

### Padding

### `padStart()` - Pad string at start

```jsx
const str = "5";
console.log(str.padStart(3, "0")); // "005"

```

### `padEnd()` - Pad string at end

```jsx
const str = "5";
console.log(str.padEnd(3, "0")); // "500"

```

### Quick Reference Summary

**Access**: `charAt()`

**Search**: `indexOf()`, `includes()`, `startsWith()`, `endsWith()`

**Slice**: `slice()`, `substring()`

**Convert**: `split()`

**Replace**: `replace()`, `replaceAll()`

**Case**: `toLowerCase()`, `toUpperCase()`

**Trim**: `trim()`

**Pad**: `padStart()`, `padEnd()`

### Template Literals and Tagged Templates

Use backticks (```) for multiline and interpolated strings. Tagged templates allow custom processing.

### String Escape Sequences

Examples: `\\\\`, `,` , `'`, `"`

### Unicode Handling in Strings

Unicode code points may need special handling (`codePointAt`, `fromCodePoint`).

# Arrays

### What is an Array?

A special object used to store ordered collections.

### Arrays as Objects

Internally, arrays are objects with indexed properties.

### Array-like Objects vs Real Arrays

Array-like: have `length` and indexed values (e.g., `arguments`). Real arrays have array methods.

### Sparse Arrays and Holes

Arrays can have missing indices. Not all methods handle them the same.

### Array Length Property

Length reflects highest index + 1, but may not represent actual number of elements.

### Dynamic Nature

Arrays grow or shrink dynamically as elements are added or removed.

### Array Methods

### Mutating Methods (Modify Original Array)

### `pop()` - Remove last element

```jsx
const arr = [1, 2, 3, 4];
const removed = arr.pop();
console.log(arr);     // [1, 2, 3]
console.log(removed); // 4

```

### `push()` - Add elements to end

```jsx
const arr = [1, 2, 3];
const newLength = arr.push(4, 5);
console.log(arr);       // [1, 2, 3, 4, 5]
console.log(newLength); // 5

```

### `shift()` - Remove first element

```jsx
const arr = [1, 2, 3, 4];
const removed = arr.shift();
console.log(arr);     // [2, 3, 4]
console.log(removed); // 1

```

### `unshift()` - Add elements to beginning

```jsx
const arr = [3, 4, 5];
const newLength = arr.unshift(1, 2);
console.log(arr);       // [1, 2, 3, 4, 5]
console.log(newLength); // 5

```

### `splice()` - Add/remove elements at any position

```jsx
const arr = [1, 2, 3, 4, 5];
const removed = arr.splice(2, 2, 'a', 'b');
console.log(arr);     // [1, 2, 'a', 'b', 5]
console.log(removed); // [3, 4]

```

### `sort()` - Sort elements

```jsx
const arr = [3, 1, 4, 1, 5];
arr.sort();
console.log(arr); // [1, 1, 3, 4, 5]

const names = ['Charlie', 'Alice', 'Bob'];
names.sort();
console.log(names); // ['Alice', 'Bob', 'Charlie']

```

### `reverse()` - Reverse array order

```jsx
const arr = [1, 2, 3, 4, 5];
arr.reverse();
console.log(arr); // [5, 4, 3, 2, 1]

```

### `fill()` - Fill with static value

```jsx
const arr = [1, 2, 3, 4, 5];
arr.fill(0, 2, 4);
console.log(arr); // [1, 2, 0, 0, 5]

```

## Non-Mutating Methods (Return New Array/Value)

### `slice()` - Extract portion of array

```jsx
const arr = [1, 2, 3, 4, 5];
const sliced = arr.slice(1, 4);
console.log(sliced); // [2, 3, 4]
console.log(arr);    // [1, 2, 3, 4, 5] (unchanged)

```

### `concat()` - Merge arrays

```jsx
const arr1 = [1, 2];
const arr2 = [3, 4];
const merged = arr1.concat(arr2, [5, 6]);
console.log(merged); // [1, 2, 3, 4, 5, 6]

```

### `join()` - Convert to string

```jsx
const arr = ['Hello', 'World', '!'];
const joined = arr.join(' ');
console.log(joined); // "Hello World !"

```

### `indexOf()` - Find first index of element

```jsx
const arr = [1, 2, 3, 2, 4];
const index = arr.indexOf(2);
console.log(index); // 1

```

### `includes()` - Check if element exists

```jsx
const arr = [1, 2, 3, 4, 5];
const hasThree = arr.includes(3);
console.log(hasThree); // true

```

### Iteration Methods

### `map()` - Transform each element

```jsx
const arr = [1, 2, 3, 4];
const doubled = arr.map(x => x * 2);
console.log(doubled); // [2, 4, 6, 8]

```

### `filter()` - Filter elements by condition

```jsx
const arr = [1, 2, 3, 4, 5, 6];
const evens = arr.filter(x => x % 2 === 0);
console.log(evens); // [2, 4, 6]

```

### `reduce()` - Reduce to single value

```jsx
const arr = [1, 2, 3, 4, 5];
const sum = arr.reduce((acc, curr) => acc + curr, 0);
console.log(sum); // 15

```

### `forEach()` - Execute function for each element

```jsx
const arr = [1, 2, 3];
arr.forEach((item, index) => {
  console.log(`Index ${index}: ${item}`);
});
// Index 0: 1
// Index 1: 2
// Index 2: 3

```

### `find()` - Find first matching element

```jsx
const arr = [1, 2, 3, 4, 5];
const found = arr.find(x => x > 3);
console.log(found); // 4

```

### `some()` - Test if any element passes condition

```jsx
const arr = [1, 2, 3, 4, 5];
const hasEven = arr.some(x => x % 2 === 0);
console.log(hasEven); // true

```

### `every()` - Test if all elements pass condition

```jsx
const arr = [2, 4, 6, 8];
const allEven = arr.every(x => x % 2 === 0);
console.log(allEven); // true

```

### `flat()` - Flatten nested arrays

```jsx
const arr = [1, [2, 3], [4, [5, 6]]];
const flattened = arr.flat(2);
console.log(flattened); // [1, 2, 3, 4, 5, 6]

```

### ES2022+ Methods

### `at()` - Access element by index (supports negative)

```jsx
const arr = [1, 2, 3, 4, 5];
console.log(arr.at(-1)); // 5 (last element)
console.log(arr.at(-2)); // 4 (second to last)

```

### Static Array Methods

### `Array.from()` - Create array from iterable

```jsx
const str = "hello";
const chars = Array.from(str);
console.log(chars); // ['h', 'e', 'l', 'l', 'o']

const doubled = Array.from([1, 2, 3], x => x * 2);
console.log(doubled); // [2, 4, 6]

const arr = Array.from([...new Array(10)].map((val, i) => i))]
console.log(arr) // [0,1,2....9]
```

### `Array.of()` - Create array from arguments

```jsx
const arr = Array.of(1, 2, 3, 4, 5);
console.log(arr); // [1, 2, 3, 4, 5]

```

### `Array.isArray()` - Check if value is array

```jsx
console.log(Array.isArray([1, 2, 3])); // true
console.log(Array.isArray("hello"));   // false

```

### Quick Reference Summary

**Mutating**: `pop`, `push`, `shift`, `unshift`, `splice`, `sort`, `reverse`, `fill`

**Non-Mutating**: `slice`, `concat`, `join`, `indexOf`, `includes`

**Iteration**: `map`, `filter`, `reduce`, `forEach`, `find`, `some`, `every`, `flat`

**Modern**: `at`, `with`

**Static**: `Array.from`, `Array.of`, `Array.isArray`

### Array.prototype and Method Chaining

Chaining array methods is common in functional-style JS.

### Array Destructuring and Spread

```jsx
const [a, b, ...rest] = [1, 2, 3, 4];

```

### Iterators and for...of

Use `for...of` to iterate over arrays.

### Symbol.iterator

Arrays have `Array.prototype[Symbol.iterator]`, enabling iteration.

### Holes and How Methods Handle Them

Sparse arrays may be skipped in some methods (e.g., `forEach` skips holes).

### Performance Considerations

- Mutating vs non-mutating methods have different performance profiles.
- Pre-allocating arrays can improve performance.

### Method Return Values

- Some return the original array (e.g., `sort`, `reverse`)
- Others return new arrays (e.g., `map`, `filter`, `slice`)