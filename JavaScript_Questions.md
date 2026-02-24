# JavaScript & Node.js Interview Questions

[← Back to Main Appendix](README.md#appendix)

---

## Table of Contents

- [Closures](#javascript-closures)
- ['this' Keyword](#javascript-this-keyword)
- [Hoisting](#javascript-hoisting)
- [Function Declaration vs Expression](#function-declaration-vs-expression)
- [Promises & Microtasks](#promises--timeout)
- [Async/Await](#asyncawait)
- [Event Loop](#nodejs-event-loop)
- [Handling Long-Running Requests](#handling-long-running-requests)
- [Event Delegation](#event-delegation)
- [Debouncing vs Throttling](#debouncing-vs-throttling)
- [Array Methods (map, filter, reduce)](#array-methods)
- [Deep vs Shallow Copy](#deep-vs-shallow-copy)
- [== vs ===](#-vs-)
- [Null vs Undefined](#null-vs-undefined)
- [Spread Operator & Destructuring](#spread-operator--destructuring)
- [Arrow Functions Differences](#arrow-functions-differences)

---

## JavaScript Closures

**Q:** What is a closure?

**A:**

- Function that has access to outer scope variables
- Even after outer function has returned
- Creates private variables

**Example:**

```javascript
function createCounter() {
  let count = 0; // private variable

  return {
    increment() {
      count++;
      return count;
    },
    decrement() {
      count--;
      return count;
    },
    getCount() {
      return count;
    },
  };
}

const counter = createCounter();
console.log(counter.increment()); // 1
console.log(counter.increment()); // 2
console.log(counter.getCount()); // 2
// count is not directly accessible
```

**Use cases:**

- Data privacy/encapsulation
- Factory functions
- Module pattern
- Event handlers that need to remember state
- Callbacks with persistent data

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

## JavaScript 'this' Keyword

**Q:** How does the 'this' keyword work in JavaScript?

**A:**

**Context-dependent:**

- **Method call**: `obj.method()` → this = obj
- **Function call**: `func()` → this = undefined (strict) / window
- **Constructor**: `new Func()` → this = new object
- **Arrow functions**: inherit this from enclosing scope
- **Explicit binding**: call(), apply(), bind()

**Examples:**

```javascript
const obj = {
  name: "John",
  greet() {
    console.log(this.name); // 'John'
  },
};

obj.greet(); // 'John'
const greetFn = obj.greet;
greetFn(); // undefined (lost context)

// Fix with bind
const boundGreet = obj.greet.bind(obj);
boundGreet(); // 'John'

// Fix with arrow function
const obj2 = {
  name: "Jane",
  greet: () => {
    console.log(this.name); // Won't work - arrow functions don't have own 'this'
  },
};
```

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

## JavaScript Hoisting

**Q:** What is hoisting in JavaScript?

**A:**

**Definition:** JavaScript's behavior of moving declarations to the top of their scope during compilation phase, before code execution.

**What gets hoisted:**

1. **Function Declarations** - fully hoisted (declaration + definition)
2. **var declarations** - hoisted, initialized with `undefined`
3. **let/const declarations** - hoisted, but in "Temporal Dead Zone" (TDZ)
4. **class declarations** - hoisted, but in TDZ

**Function Hoisting:**

```javascript
// Works - function declaration is fully hoisted
sayHello();
function sayHello() {
  console.log("Hello!");
}

// Error - function expression is NOT hoisted
sayBye(); // TypeError: sayBye is not a function
const sayBye = function () {
  console.log("Bye!");
};
```

**var Hoisting:**

```javascript
console.log(x); // undefined (not ReferenceError)
var x = 5;
console.log(x); // 5

// Interpreted as:
// var x;           // declaration hoisted
// console.log(x);  // undefined
// x = 5;           // assignment stays
// console.log(x);  // 5
```

**let/const Hoisting & Temporal Dead Zone:**

```javascript
console.log(a); // ReferenceError: Cannot access 'a' before initialization
let a = 10;

console.log(b); // ReferenceError: Cannot access 'b' before initialization
const b = 20;

// let/const ARE hoisted but can't be accessed before declaration
// The period from scope start to declaration is the "TDZ"
```

**Best practices:**

- **Avoid `var`** - use `let`/`const` for block scoping
- **Declare at top of scope** - don't rely on hoisting
- **Use function expressions** or arrow functions for clarity
- **Initialize variables** when declaring them

**Common interview trap:**

```javascript
var funcs = [];
for (var i = 0; i < 3; i++) {
  funcs.push(function () {
    console.log(i);
  });
}
funcs[0](); // 3 (not 0!) - var is function-scoped
funcs[1](); // 3
funcs[2](); // 3

// Fix with let (block-scoped):
var funcs = [];
for (let i = 0; i < 3; i++) {
  funcs.push(function () {
    console.log(i);
  });
}
funcs[0](); // 0
funcs[1](); // 1
funcs[2](); // 2
```

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

## Function Declaration vs Expression

**Q:** What's the difference between function declarations and function expressions?

**A:**

**Function Declaration:**

```javascript
function greet(name) {
  return `Hello, ${name}`;
}
```

**Function Expression:**

```javascript
const greet = function (name) {
  return `Hello, ${name}`;
};

// Arrow function (also an expression)
const greet = (name) => `Hello, ${name}`;
```

**Key Differences:**

1. **Hoisting**
   - **Declarations** are hoisted (can call before definition)
   - **Expressions** are NOT hoisted (undefined or ReferenceError)

2. **Naming**
   - Declarations must have a name
   - Expressions can be anonymous or named

3. **Usage Context**
   - Declarations create a binding in current scope
   - Expressions can be used inline (callbacks, IIFEs)

**When to use each:**

**Function Declarations:**

- Top-level utility functions
- Functions that need to be called before definition
- Clear, readable module exports

**Function Expressions:**

- Callbacks and higher-order functions
- Conditional function creation
- Methods in objects
- IIFEs (Immediately Invoked Function Expressions)
- When you want block-scoped functions with `const`/`let`

**Modern best practices:**

- Use `const` with arrow functions for most cases (expressions)
- Use function declarations for top-level functions
- Avoid hoisting dependencies when possible

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

## Promises / Timeout

**Q:** What problem do promises solve?

**A:**

- Handle asynchronous code
- Avoid callback hell
- async/await is syntactic sugar over promises
- Errors handled via try/catch

**Promise States:**

- **Pending**: Initial state
- **Fulfilled**: Operation completed successfully
- **Rejected**: Operation failed

**Example:**

```javascript
// Callback hell (bad)
getData(function (a) {
  getMoreData(a, function (b) {
    getEvenMoreData(b, function (c) {
      console.log(c);
    });
  });
});

// With Promises (better)
getData()
  .then((a) => getMoreData(a))
  .then((b) => getEvenMoreData(b))
  .then((c) => console.log(c))
  .catch((error) => console.error(error));
```

**Follow-up:**  
**Q:** Which runs first: setTimeout or Promise.then?

**A:**

- Promise.then (microtask) runs before setTimeout (macrotask)
- Microtasks always execute before the next macrotask

```javascript
console.log("1");

setTimeout(() => console.log("2"), 0);

Promise.resolve().then(() => console.log("3"));

console.log("4");

// Output: 1, 4, 3, 2
```

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

## Async/Await

**Q:** What is async/await and how does it differ from Promises?

**A:**

**Definition:** Syntactic sugar over Promises that makes asynchronous code look synchronous.

**Key Differences:**

1. **Syntax**
   - Promises: `.then()` chains
   - Async/await: Sequential-looking code with `await`

2. **Error Handling**
   - Promises: `.catch()` or second argument to `.then()`
   - Async/await: `try/catch` blocks (more familiar)

3. **Readability**
   - Async/await is generally more readable for complex flows
   - Avoids callback nesting

**Examples:**

```javascript
// With Promises
fetchUser(id)
  .then((user) => fetchPosts(user.id))
  .then((posts) => fetchComments(posts[0].id))
  .then((comments) => console.log(comments))
  .catch((error) => console.error(error));

// With Async/Await
async function getData() {
  try {
    const user = await fetchUser(id);
    const posts = await fetchPosts(user.id);
    const comments = await fetchComments(posts[0].id);
    console.log(comments);
  } catch (error) {
    console.error(error);
  }
}
```

**Parallel Execution:**

```javascript
// Sequential (slow) - waits for each
const user = await fetchUser();
const posts = await fetchPosts(); // waits for user

// Parallel (fast) - runs simultaneously
const [user, posts] = await Promise.all([fetchUser(), fetchPosts()]);

// Or with Promise.allSettled for handling individual failures
const results = await Promise.allSettled([
  fetchUser(),
  fetchPosts(),
  fetchComments(),
]);
```

**Important Rules:**

1. **`await` only works inside `async` functions**
   - Top-level await available in modules (ES2022+)

2. **`async` functions always return a Promise**
   - Even if you return a non-Promise value

3. **Error propagation**
   - Unhandled errors in async functions become rejected promises

**Common Patterns:**

```javascript
// 1. Conditional awaiting
async function getUser(id, includeDetails = false) {
  const user = await fetchUser(id);
  if (includeDetails) {
    user.details = await fetchDetails(id);
  }
  return user;
}

// 2. Loop with await (sequential)
for (const id of userIds) {
  await processUser(id); // processes one at a time
}

// 3. Loop with parallel execution
await Promise.all(userIds.map((id) => processUser(id)));

// 4. Early return on error
async function validateAndSave(data) {
  if (!data.email) return { error: "Email required" };

  try {
    const result = await saveToDatabase(data);
    return { success: true, result };
  } catch (error) {
    return { error: error.message };
  }
}
```

**Red flags:**

- Using await in loops without understanding sequential vs parallel
- Not handling errors with try/catch
- Forgetting that async functions return Promises
- Not using Promise.all for independent operations

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

## Node.js Event Loop

**Q:** Explain the Node.js event loop in simple terms.

**A:**

**Core Concept:**

- Node.js runs on a single thread (main thread)
- Uses non-blocking I/O for concurrent operations
- Event loop coordinates execution of callbacks
- Async operations (file I/O, network) are handled by libuv (C++ library)

**Event Loop Phases (in order):**

1. **Timers** - `setTimeout()`, `setInterval()`
2. **Pending Callbacks** - I/O callbacks deferred from previous iteration
3. **Idle, Prepare** - Internal use
4. **Poll** - Retrieve new I/O events, execute I/O callbacks
5. **Check** - `setImmediate()` callbacks
6. **Close Callbacks** - `socket.on('close')`

**Microtasks vs Macrotasks:**

**Microtasks (higher priority):**

- `Promise.then()`, `Promise.catch()`, `Promise.finally()`
- `process.nextTick()` (highest priority)
- `queueMicrotask()`
- Execute after current operation, **before** moving to next phase

**Macrotasks (lower priority):**

- `setTimeout()`, `setInterval()`
- `setImmediate()`
- I/O operations
- One macrotask per phase iteration

**Execution Order Example:**

```javascript
console.log("1: Synchronous");

setTimeout(() => console.log("2: setTimeout"), 0);

Promise.resolve().then(() => console.log("3: Promise"));

process.nextTick(() => console.log("4: nextTick"));

console.log("5: Synchronous");

// Output:
// 1: Synchronous
// 5: Synchronous
// 4: nextTick (microtask - highest priority)
// 3: Promise (microtask)
// 2: setTimeout (macrotask)
```

**What blocks the event loop:**

- Synchronous operations (loops, JSON.parse of huge data)
- CPU-intensive calculations
- Blocking I/O (fs.readFileSync)
- Inefficient regex on large strings

**Performance Tips:**

1. Use async versions of file operations
2. Break CPU-intensive work into chunks
3. Use worker threads for CPU-bound tasks
4. Avoid deep synchronous recursion
5. Monitor event loop lag with metrics

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

## Handling Long-Running Requests

**Q:** How would you handle a request that takes a long time to process?

**A:**

**Strategies:**

1. **Identify the Bottleneck First**
   - Profile the request (measure execution time)
   - Check for multiple consecutive async requests (waterfall pattern)
   - Analyze database query performance
   - Monitor API response times

2. **Optimize Multiple Async Requests**
   - Run independent requests in parallel (Promise.all)
   - Avoid sequential await when possible
   - Batch multiple requests into single calls
   - Use GraphQL DataLoader to prevent N+1 queries

3. **Database Operation Improvements**
   - Identify slow queries (EXPLAIN/ANALYZE)
   - Add missing indexes
   - Optimize query structure
   - Fix N+1 query problems
   - Use connection pooling
   - Implement query result caching

4. **Caching**
   - Cache expensive computations (Redis, Memcached)
   - Use stale-while-revalidate pattern
   - Best for: frequently accessed, slow-changing data

**Key Considerations:**

- User experience (progress indicators, estimated time)
- Idempotency (can retry safely)
- Error handling and recovery
- Resource limits
- Monitoring and alerting

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

## Event Delegation

**Q:** What is event delegation and why is it useful?

**A:**

**Definition:** Instead of attaching event listeners to individual elements, attach one listener to a parent element and use event bubbling to handle events from child elements.

**How it works:**

- Events bubble up from target to parent elements
- Parent's listener checks `event.target` to determine which child triggered it
- Single listener handles events for multiple children

**Example:**

```javascript
// Without delegation (inefficient)
const buttons = document.querySelectorAll(".delete-btn");
buttons.forEach((btn) => {
  btn.addEventListener("click", handleDelete); // N listeners
});

// With delegation (efficient)
const container = document.querySelector(".button-container");
container.addEventListener("click", (e) => {
  if (e.target.matches(".delete-btn")) {
    // 1 listener
    handleDelete(e);
  }
});
```

**Benefits:**

1. **Performance** - Fewer event listeners = less memory
2. **Dynamic Content** - Works for elements added after page load
3. **Cleaner Code** - Centralized event handling

**Real-world use cases:**

- Todo lists - handle delete/edit for any item
- Tables - handle row clicks, button clicks
- Navigation menus - handle link clicks
- Comment sections - reply, like, delete buttons

**Implementation patterns:**

```javascript
// Check for specific element or ancestor
document.querySelector(".list").addEventListener("click", (e) => {
  // Method 1: exact match
  if (e.target.classList.contains("item")) {
    console.log("Item clicked");
  }

  // Method 2: check if click was inside .item (including children)
  const item = e.target.closest(".item");
  if (item) {
    console.log("Item or its child clicked");
  }
});
```

**Limitations:**

- Not all events bubble (focus, blur, scroll)
- Slight complexity in determining actual target
- Need to check event.target carefully

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

## Debouncing vs Throttling

**Q:** What's the difference between debouncing and throttling?

**A:**

**Both** limit how often a function executes, but differently:

**Debouncing:**

- Delays execution until **after** user stops triggering events
- Resets timer on each new event
- Only executes **once** after activity stops

**Throttling:**

- Executes at **regular intervals** during activity
- Guarantees function runs at most once per time period
- Executes **multiple times** while active

**Debounce Implementation:**

```javascript
function debounce(func, delay) {
  let timeoutId;
  return function (...args) {
    clearTimeout(timeoutId);
    timeoutId = setTimeout(() => func.apply(this, args), delay);
  };
}

// Usage
const searchInput = document.querySelector("#search");
const debouncedSearch = debounce(performSearch, 300);
searchInput.addEventListener("input", debouncedSearch);
```

**Throttle Implementation:**

```javascript
function throttle(func, limit) {
  let inThrottle;
  return function (...args) {
    if (!inThrottle) {
      func.apply(this, args);
      inThrottle = true;
      setTimeout(() => (inThrottle = false), limit);
    }
  };
}

// Usage
const throttledScroll = throttle(handleScroll, 100);
window.addEventListener("scroll", throttledScroll);
```

**When to use each:**

**Debouncing:**

- Search input (wait for user to finish typing)
- Form validation (validate after user stops)
- Window resize (recalculate layout after resize ends)
- Auto-save (save after user stops editing)

**Throttling:**

- Scroll events (infinite scroll, parallax effects)
- Mouse move tracking (drag and drop)
- Button clicks (prevent spam clicking)
- API rate limiting

**Visual comparison:**

```
User triggers events: | | | | |     | |          |
                      ↓ ↓ ↓ ↓ ↓     ↓ ↓          ↓
Debounced execution:                  ✓            ✓
                      (waits for pause)

Throttled execution:  ✓       ✓     ✓            ✓
                      (every N ms)
```

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

## Array Methods

**Q:** Explain map, filter, and reduce. When would you use each?

**A:**

**All three** are higher-order functions that operate on arrays without mutating the original.

**1. map() - Transform each element**

Returns **new array** with same length

```javascript
const numbers = [1, 2, 3, 4];
const doubled = numbers.map((n) => n * 2);
// [2, 4, 6, 8]

const users = [
  { id: 1, name: "John" },
  { id: 2, name: "Jane" },
];
const names = users.map((user) => user.name);
// ['John', 'Jane']
```

**2. filter() - Select elements**

Returns **new array** with filtered elements

```javascript
const numbers = [1, 2, 3, 4, 5, 6];
const evens = numbers.filter((n) => n % 2 === 0);
// [2, 4, 6]

const activeUsers = users.filter((user) => user.active);
```

**3. reduce() - Aggregate to single value**

Returns **single value** (any type)

```javascript
// Sum
const sum = numbers.reduce((acc, n) => acc + n, 0);

// Group by property
const grouped = users.reduce((acc, user) => {
  acc[user.role] = acc[user.role] || [];
  acc[user.role].push(user);
  return acc;
}, {});

// Flatten array
const nested = [[1, 2], [3, 4], [5]];
const flat = nested.reduce((acc, arr) => acc.concat(arr), []);
```

**Chaining methods:**

```javascript
const result = users
  .filter((user) => user.active)
  .map((user) => user.name.toUpperCase());
// ['JOHN', 'BOB']
```

**Other useful array methods:**

```javascript
// find() - first matching element
const user = users.find((u) => u.id === 2);

// findIndex() - index of first match
const index = users.findIndex((u) => u.id === 2);

// some() - true if any element passes test
const hasActive = users.some((u) => u.active);

// every() - true if all elements pass test
const allActive = users.every((u) => u.active);

// includes() - check if value exists
const hasJohn = names.includes("John");
```

**Common pitfalls:**

```javascript
// Wrong: forgetting to return in map
const doubled = numbers.map((n) => {
  n * 2;
}); // [undefined, undefined, ...]
const doubled = numbers.map((n) => n * 2); // correct

// Wrong: mutating objects in map
const updated = users.map((user) => {
  user.processed = true; // mutates original!
  return user;
});
const updated = users.map((user) => ({ ...user, processed: true })); // correct

// Wrong: using map when you want forEach
numbers.map((n) => console.log(n)); // creates unused array
numbers.forEach((n) => console.log(n)); // correct
```

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

## Deep vs Shallow Copy

**Q:** What's the difference between deep and shallow copy?

**A:**

**Shallow Copy:**

- Copies only top-level properties
- Nested objects/arrays are **referenced**, not copied

**Deep Copy:**

- Recursively copies all levels
- Nested objects/arrays are **duplicated**

**Examples:**

```javascript
const original = {
  name: "John",
  address: { city: "NYC", zip: "10001" },
};

// SHALLOW COPY methods:

// 1. Spread operator
const copy1 = { ...original };
copy1.name = "Jane"; // ✓ doesn't affect original
copy1.address.city = "LA"; // ✗ DOES affect original!

// 2. Object.assign()
const copy2 = Object.assign({}, original);

// 3. Array.slice() or [...arr]
const arr = [
  [1, 2],
  [3, 4],
];
const arrCopy = arr.slice();
arrCopy[0][0] = 99; // affects original!

// DEEP COPY methods:

// 1. JSON.parse/stringify (simple but limited)
const deepCopy1 = JSON.parse(JSON.stringify(original));
// Limitations: loses functions, undefined, Date objects, circular refs

// 2. structuredClone() (modern, recommended)
const deepCopy2 = structuredClone(original);
// Handles most types, circular references
// Not supported: functions, DOM nodes, symbols

// 3. Lodash _.cloneDeep()
const deepCopy3 = _.cloneDeep(original);

// 4. Custom recursive function
function deepClone(obj) {
  if (obj === null || typeof obj !== "object") return obj;
  if (obj instanceof Date) return new Date(obj);
  if (obj instanceof Array) return obj.map((item) => deepClone(item));

  const cloned = {};
  for (let key in obj) {
    if (obj.hasOwnProperty(key)) {
      cloned[key] = deepClone(obj[key]);
    }
  }
  return cloned;
}
```

**When to use each:**

**Shallow copy:**

- Working with flat objects
- Performance matters (faster)
- Immutable updates in React (one level)

**Deep copy:**

- Nested objects/arrays
- Need complete independence
- Cloning complex state
- Undo/redo functionality

**Common interview follow-up:**

```javascript
// "What does this output?"
const obj1 = { a: 1, b: { c: 2 } };
const obj2 = { ...obj1 };
obj2.b.c = 3;
console.log(obj1.b.c); // 3 (shallow copy!)
```

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

## == vs ===

**Q:** What's the difference between == and ===?

**A:**

**=== (Strict Equality)**

- Checks **type AND value**
- No type coercion
- Recommended for most cases

**== (Loose Equality)**

- Checks **value only**
- Performs type coercion
- Can lead to unexpected results

**Examples:**

```javascript
// Strict equality (===)
5 === 5          // true
5 === '5'        // false (different types)
0 === false      // false
null === undefined // false

// Loose equality (==)
5 == 5           // true
5 == '5'         // true (string coerced to number)
0 == false       // true (both coerced to 0)
null == undefined // true (special case)
'' == false      // true
[] == false      // true

// Tricky cases
'' == 0          // true
'0' == 0         // true
'0' == ''        // false (!)
[] == ![]        // true (!)
```

**When to use ==:**

```javascript
// Checking for null OR undefined
if (value == null) {
  // true if value is null OR undefined
  // more concise than: value === null || value === undefined
}

// That's about the only good use case!
```

**Best practices:**

- **Use === by default**
- Use == only for null/undefined check
- ESLint rule: `eqeqeq` enforces ===
- Similarly: use !== instead of !=

**Interview gotcha:**

```javascript
console.log([] == ![]); // true (WTF?)
// Explanation:
// 1. ![] is false (empty array is truthy)
// 2. [] == false
// 3. [] converts to '' (empty string)
// 4. false converts to 0
// 5. '' == 0 is true
```

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

## Null vs Undefined

**Q:** What's the difference between null and undefined?

**A:**

**undefined:**

- **Default value** when variable declared but not assigned
- **Absence** of value
- Automatically assigned by JavaScript
- Type: `undefined`

**null:**

- **Intentional** absence of value
- Must be explicitly assigned
- Represents "no value" or "empty"
- Type: `object` (JavaScript bug, kept for compatibility)

**Examples:**

```javascript
// undefined cases
let x;
console.log(x); // undefined

function foo(a) {
  console.log(a); // undefined if no argument passed
}

const obj = { name: "John" };
console.log(obj.age); // undefined

// null cases
let user = null; // explicitly "no user"

document.getElementById("missing"); // returns null if not found
```

**Type checking:**

```javascript
typeof undefined; // 'undefined'
typeof null; // 'object' (historical bug!)

undefined == null; // true (loose equality)
undefined === null; // false (different types)

// Checking for both
if (value == null) {
  // true for null OR undefined
  console.log("No value");
}
```

**Best practices:**

```javascript
// DON'T: explicitly assign undefined
let user = undefined; // bad

// DO: use null for intentional "no value"
let user = null; // good

// Modern approach: nullish coalescing
const name = userName ?? "Guest"; // uses 'Guest' if null or undefined
const name = userName || "Guest"; // uses 'Guest' if any falsy value
```

**Optional chaining:**

```javascript
// Old way
const city = user && user.address && user.address.city;

// New way (ES2020)
const city = user?.address?.city; // undefined if any part is null/undefined

// With nullish coalescing
const city = user?.address?.city ?? "Unknown";
```

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

## Spread Operator & Destructuring

**Q:** Explain the spread operator and destructuring. When would you use them?

**A:**

**Spread Operator (...)**

Expands iterables into individual elements

**Array spread:**

```javascript
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];

// Combine arrays
const combined = [...arr1, ...arr2]; // [1,2,3,4,5,6]

// Copy array
const copy = [...arr1];

// Function arguments
const numbers = [1, 2, 3];
Math.max(...numbers); // 3

// Convert string to array
const chars = [..."hello"]; // ['h','e','l','l','o']
```

**Object spread:**

```javascript
const user = { name: "John", age: 30 };
const location = { city: "NYC", country: "USA" };

// Merge objects
const profile = { ...user, ...location };
// {name: 'John', age: 30, city: 'NYC', country: 'USA'}

// Override properties
const updated = { ...user, age: 31 };
// {name: 'John', age: 31}
```

**Destructuring**

Extracts values from arrays/objects

**Array destructuring:**

```javascript
const arr = [1, 2, 3, 4, 5];

const [first, second] = arr; // first=1, second=2

const [first, , third] = arr; // skip elements

const [first, ...rest] = arr; // rest=[2,3,4,5]

// Swapping variables
let x = 1,
  y = 2;
[x, y] = [y, x]; // x=2, y=1
```

**Object destructuring:**

```javascript
const user = {
  name: "John",
  age: 30,
  address: { city: "NYC", zip: "10001" },
};

const { name, age } = user; // name='John', age=30

const { name: userName, age: userAge } = user; // rename

const { name, country = "USA" } = user; // default

const {
  address: { city, zip },
} = user; // nested

const { name, ...otherInfo } = user; // rest

// Function parameters
function greet({ name, age }) {
  console.log(`${name} is ${age}`);
}
```

**Real-world use cases:**

**React props:**

```javascript
function UserCard({ name, age, onEdit }) {
  return (
    <div>
      {name} - {age}
    </div>
  );
}

// Rest props
function Button({ label, ...restProps }) {
  return <button {...restProps}>{label}</button>;
}
```

**Common mistakes:**

```javascript
// Wrong: destructuring undefined
const { name } = undefined; // Error!
const { name } = user || {}; // Safe
const { name } = user ?? {}; // Also safe
```

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

## Arrow Functions Differences

**Q:** What are the key differences between arrow functions and regular functions?

**A:**

**Key Differences:**

**1. `this` binding:**

- **Regular functions**: `this` is dynamic (depends on how called)
- **Arrow functions**: `this` is lexical (inherits from enclosing scope)

```javascript
const obj = {
  name: "John",
  regularFunc: function () {
    console.log(this.name); // 'John' (this = obj)
  },
  arrowFunc: () => {
    console.log(this.name); // undefined (this = global/window)
  },
  nestedExample: function () {
    // Regular function in callback
    setTimeout(function () {
      console.log(this.name); // undefined (this = global)
    }, 100);

    // Arrow function in callback
    setTimeout(() => {
      console.log(this.name); // 'John' (inherits this)
    }, 100);
  },
};
```

**2. arguments object:**

- **Regular functions**: have `arguments` object
- **Arrow functions**: no `arguments` (use rest parameters)

```javascript
function regular() {
  console.log(arguments); // [1, 2, 3]
}
regular(1, 2, 3);

const arrow = (...args) => {
  console.log(args); // [1, 2, 3]
};
arrow(1, 2, 3);
```

**3. Constructor:**

- **Regular functions**: can be used with `new`
- **Arrow functions**: cannot be constructors

```javascript
function Person(name) {
  this.name = name;
}
const john = new Person("John"); // ✓ Works

const Person = (name) => {
  this.name = name;
};
const jane = new Person("Jane"); // ✗ TypeError!
```

**4. Syntax:**

```javascript
// Regular
function add(a, b) {
  return a + b;
}

// Arrow - concise
const add = (a, b) => a + b;

// Single parameter (no parens)
const double = (n) => n * 2;

// Object literal (wrap in parens)
const makeObj = (id) => ({ id: id, name: "Item" });
```

**When to use arrow functions:**

1. **Callbacks preserving `this`**

```javascript
class Counter {
  count = 0;
  start() {
    setInterval(() => {
      this.count++; // preserves this
    }, 1000);
  }
}
```

2. **Array methods**

```javascript
const doubled = numbers.map((n) => n * 2);
const evens = numbers.filter((n) => n % 2 === 0);
```

3. **React components**

```javascript
const Button = ({ label, onClick }) => (
  <button onClick={onClick}>{label}</button>
);
```

**When to use regular functions:**

1. **Methods needing dynamic `this`**

```javascript
const button = document.querySelector("button");
button.addEventListener("click", function () {
  this.classList.toggle("active"); // this = button
});
```

2. **Object methods**

```javascript
const obj = {
  name: "John",
  greet() {
    return `Hello, ${this.name}`;
  },
};
```

**Common mistakes:**

```javascript
// ✗ Arrow function as method
const obj = {
  name: "John",
  greet: () => {
    console.log(this.name); // undefined! (this ≠ obj)
  },
};

// ✓ Use regular function or shorthand
const obj = {
  name: "John",
  greet() {
    console.log(this.name); // 'John'
  },
};
```

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

[← Back to Main Appendix](README.md#appendix)
