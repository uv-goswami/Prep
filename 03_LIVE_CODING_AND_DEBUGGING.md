# 03 — Live Coding, Debugging & Communication Guide
### How to Survive the In-Browser Editor and Debug Round

---

> **Your Biggest Risk:** The live coding round. You said you can't write "Hello World in a browser editor without AI." This guide will give you enough practice code and communication scripts to get through it. The key insight: **interviewers don't expect perfection — they expect thinking.**

---

## PART 1 — The In-Browser Coding Setup

### What You'll See

The interview uses an in-browser code editor. Likely one of:
- **CodeSignal** — most common for startup interviews
- **HackerRank** — common for mid-size companies  
- **CoderPad** — common for senior/staff-level
- **Replit / StackBlitz** — full project environments

### Before You Type: Your Opening Ritual (Do This Every Time)

```
1. Read the problem twice without touching the keyboard
2. Say out loud: "So the function takes X and returns Y, correct?"
3. Write a comment block FIRST:
   // Input: array of numbers
   // Output: single number (the maximum)
   // Approach: single pass, track max
   // Complexity: O(n) time, O(1) space
4. NOW write the code
```

This takes 60 seconds and makes you look 3 levels more senior than you are.

---

## PART 2 — The JavaScript/TypeScript You MUST Know Cold

These are the operations you'll need in any coding challenge. Practice typing these from memory — no copy-paste.

### Strings
```javascript
// Length
str.length

// Access character
str[0]       // or str.charAt(0)

// Split into array
str.split('')          // by character: "hello" → ['h','e','l','l','o']
str.split(' ')         // by space: "hello world" → ['hello', 'world']

// Join array back to string
arr.join('')           // ['h','e','l'] → "hel"
arr.join(', ')         // ['a','b','c'] → "a, b, c"

// Reverse a string
str.split('').reverse().join('')

// Includes
str.includes('hello')  // boolean

// Starts/ends with
str.startsWith('hel')
str.endsWith('lo')

// Uppercase / lowercase
str.toUpperCase()
str.toLowerCase()

// Trim whitespace
str.trim()

// Get substring
str.substring(0, 3)    // first 3 chars
str.slice(-3)          // last 3 chars
```

### Arrays
```javascript
// Create
const arr = [1, 2, 3];
const arr2 = new Array(5).fill(0);  // [0,0,0,0,0]

// Add/remove
arr.push(4)        // add to end → [1,2,3,4]
arr.pop()          // remove from end → returns 3
arr.unshift(0)     // add to front → [0,1,2,3]
arr.shift()        // remove from front → returns 0

// Access
arr[0]             // first element
arr[arr.length-1]  // last element

// Iterate (prefer these over for-loop when no index needed)
arr.forEach(x => console.log(x))
const doubled = arr.map(x => x * 2)
const evens = arr.filter(x => x % 2 === 0)
const sum = arr.reduce((acc, x) => acc + x, 0)

// Check existence
arr.includes(2)             // boolean
arr.indexOf(2)              // index or -1
arr.findIndex(x => x > 3)  // first index where condition true

// Sort (WARNING: default sorts as strings!)
arr.sort((a, b) => a - b)   // ascending numbers
arr.sort((a, b) => b - a)   // descending numbers

// Slice (non-destructive)
arr.slice(1, 3)             // [arr[1], arr[2]] — does not include index 3

// Splice (destructive — removes and/or inserts)
arr.splice(1, 2)            // remove 2 elements starting at index 1

// Spread (copy)
const copy = [...arr]
const merged = [...arr1, ...arr2]

// Destructuring
const [first, second, ...rest] = arr
```

### Objects / Maps
```javascript
// Object
const obj = { key: 'value' };
obj.key                         // access
obj['key']                      // access with variable key
Object.keys(obj)                // array of keys
Object.values(obj)              // array of values
Object.entries(obj)             // array of [key, value] pairs

// Frequency counter pattern
const freq = {};
for (const char of str) {
  freq[char] = (freq[char] || 0) + 1;
}

// Map (preferred for interview hashmaps)
const map = new Map();
map.set('key', 'value');
map.get('key');                 // 'value'
map.has('key');                 // true
map.delete('key');
map.size;                       // number of entries
for (const [key, val] of map) { /* iterate */ }

// Set (for unique values)
const set = new Set([1, 2, 2, 3]);  // {1, 2, 3}
set.add(4);
set.has(2);                    // true
set.delete(2);
set.size;
const arr = [...set];           // convert back to array
```

### Loops
```javascript
// Classic for-loop
for (let i = 0; i < arr.length; i++) {
  console.log(arr[i]);
}

// For-of (elements)
for (const item of arr) {
  console.log(item);
}

// For-in (keys of object)
for (const key in obj) {
  console.log(key, obj[key]);
}

// While
let i = 0;
while (i < arr.length) {
  i++;
}

// Break and continue
for (const x of arr) {
  if (x === 5) break;       // exit loop
  if (x === 3) continue;    // skip this iteration
}
```

### Math & Numbers
```javascript
Math.max(1, 2, 3)            // 3
Math.min(1, 2, 3)            // 1
Math.max(...arr)             // max of array
Math.abs(-5)                 // 5
Math.floor(3.7)              // 3  ← use this for binary search mid
Math.ceil(3.2)               // 4
Math.round(3.5)              // 4
Math.sqrt(16)                // 4
x % 2 === 0                  // even check
Number.isInteger(x)          // integer check
parseInt('42')               // string to int
parseFloat('3.14')           // string to float
Number('42')                 // alternative
Infinity                     // for initializing min tracking
-Infinity                    // for initializing max tracking
```

---

## PART 3 — 10 Must-Practice Implementations

Write each of these in full from scratch. Time yourself. Target: 5–8 minutes each.

### 1. FizzBuzz (Absolute Must-Know)
```javascript
function fizzBuzz(n) {
  for (let i = 1; i <= n; i++) {
    if (i % 15 === 0) console.log('FizzBuzz');  // Check 15 FIRST
    else if (i % 3 === 0) console.log('Fizz');
    else if (i % 5 === 0) console.log('Buzz');
    else console.log(i);
  }
}
// Common mistake: checking 3 and 5 before 15 — 15 would print "Fizz" instead of "FizzBuzz"
```

### 2. Palindrome Check
```javascript
function isPalindrome(s) {
  const clean = s.toLowerCase().replace(/[^a-z0-9]/g, '');  // remove non-alphanumeric
  let left = 0;
  let right = clean.length - 1;
  
  while (left < right) {
    if (clean[left] !== clean[right]) return false;
    left++;
    right--;
  }
  return true;
}
// Test: isPalindrome("A man, a plan, a canal: Panama") → true
```

### 3. Fibonacci (Both Iterative and Recursive)
```javascript
// Iterative (preferred — no stack overflow)
function fibIterative(n) {
  if (n <= 1) return n;
  let prev = 0, curr = 1;
  for (let i = 2; i <= n; i++) {
    let next = prev + curr;
    prev = curr;
    curr = next;
  }
  return curr;
}

// Recursive (know this but explain the stack depth problem)
function fibRecursive(n) {
  if (n <= 1) return n;
  return fibRecursive(n - 1) + fibRecursive(n - 2);
}
// ⚠️ Recursive is O(2^n) — exponential — say "this is inefficient for large n"
```

### 4. Find Maximum in Array
```javascript
function findMax(arr) {
  if (arr.length === 0) return null;  // handle edge case
  
  let max = arr[0];  // start with first element, not 0 (negative numbers!)
  
  for (let i = 1; i < arr.length; i++) {
    if (arr[i] > max) max = arr[i];
  }
  
  return max;
}
// ⚠️ Common mistake: initializing max = 0. Fails for all-negative arrays.
```

### 5. Reverse an Array
```javascript
function reverseArray(arr) {
  let left = 0;
  let right = arr.length - 1;
  
  while (left < right) {
    [arr[left], arr[right]] = [arr[right], arr[left]];  // destructuring swap
    left++;
    right--;
  }
  
  return arr;
}
// Also works: return [...arr].reverse() — but know the two-pointer version
```

### 6. Count Character Frequency
```javascript
function charFrequency(s) {
  const freq = {};
  
  for (const char of s) {
    freq[char] = (freq[char] || 0) + 1;
  }
  
  return freq;
}
// Usage: charFrequency("hello") → { h:1, e:1, l:2, o:1 }
```

### 7. Check Anagrams
```javascript
function areAnagrams(s1, s2) {
  if (s1.length !== s2.length) return false;
  
  const freq = {};
  
  for (const char of s1) {
    freq[char] = (freq[char] || 0) + 1;
  }
  
  for (const char of s2) {
    if (!freq[char]) return false;
    freq[char]--;
  }
  
  return true;
}
// Test: areAnagrams("listen", "silent") → true
```

### 8. Remove Duplicates from Array
```javascript
// Using Set (cleanest)
function removeDuplicates(arr) {
  return [...new Set(arr)];
}

// Using filter (if asked to implement manually)
function removeDuplicatesManual(arr) {
  const seen = new Set();
  return arr.filter(item => {
    if (seen.has(item)) return false;
    seen.add(item);
    return true;
  });
}
```

### 9. Find Missing Number in [1..n]
```javascript
// Expected sum formula: n*(n+1)/2
function findMissing(arr) {
  const n = arr.length + 1;  // one number is missing, so array has n-1 elements
  const expectedSum = (n * (n + 1)) / 2;
  const actualSum = arr.reduce((acc, x) => acc + x, 0);
  return expectedSum - actualSum;
}
// Test: findMissing([1,2,4,5,6]) → 3
// Say: "This is O(n) time and O(1) space using the arithmetic sum formula."
```

### 10. Two Sum
```javascript
function twoSum(nums, target) {
  const seen = new Map();  // value → index
  
  for (let i = 0; i < nums.length; i++) {
    const complement = target - nums[i];
    
    if (seen.has(complement)) {
      return [seen.get(complement), i];
    }
    
    seen.set(nums[i], i);
  }
  
  return [];
}
// Test: twoSum([2,7,11,15], 9) → [0,1]
```

---

## PART 4 — The Debugging Round Survival Guide

### What the Debugging Round Looks Like

"Clone this repo and fix this issue." You'll be given a GitHub URL, asked to screen-share, and expected to:
1. Understand the repo structure quickly
2. Reproduce the bug
3. Trace it to its root cause
4. Fix it
5. Explain what you did

### The Systematic Debugging Method (STOP, READ, TRACE, FIX)

```
S — STOP. Read the error message completely. The answer is usually in it.
R — READ. Understand what the code is supposed to do.
T — TRACE. Follow the execution path from input to error.
I — ISOLATE. Find the smallest code change that reproduces the bug.
X — FIX. Make the minimal change. Don't refactor while debugging.
```

### Step 1: Understand the Repo (First 2 Minutes)
```bash
# These are the first commands you run on any unfamiliar repo
ls                     # what files are here
cat package.json       # what runtime, what scripts, what dependencies
cat README.md          # how to run this
```

### Step 2: Read the Error Message

When something breaks, the terminal usually tells you exactly what:
```
TypeError: Cannot read properties of undefined (reading 'id')
  at getMe (src/controllers/user.ts:5:42)
```

This means: on line 5 of user.ts, column 42, something is undefined when you tried to read .id from it. Go to that line.

### Step 3: Add Temporary Debug Logs
```javascript
// When you don't understand what a variable contains, LOG IT
console.log('DEBUG req.user:', req.user);
console.log('DEBUG userId:', userId);
console.log('DEBUG type:', typeof userId);

// CRITICAL: Remove all console.log('DEBUG...') before submitting
```

### Step 4: Common Bug Patterns and Fixes

**Bug Type 1: Undefined/null access**
```javascript
// BUG: crashes if user is null
const id = req.user.id;

// FIX: optional chaining + guard
const id = req.user?.id;
if (!id) return res.status(401).json({ error: 'Unauthorized' });
```

**Bug Type 2: Async/await missing**
```javascript
// BUG: user will be a Promise, not the actual user
const user = prismaClient.user.findUnique({ where: { id } });
console.log(user.email);  // crashes

// FIX: add await
const user = await prismaClient.user.findUnique({ where: { id } });
```

**Bug Type 3: Wrong comparison**
```javascript
// BUG: string '3' !== number 3
if (user.id === 3) { ... }

// FIX: use triple equals and correct types
if (user.id === '3') { ... }
// or
if (Number(user.id) === 3) { ... }
```

**Bug Type 4: Array mutation**
```javascript
// BUG: sort() mutates the original array
const sorted = arr.sort((a, b) => a - b);
// arr is now also sorted (same reference)

// FIX: copy first
const sorted = [...arr].sort((a, b) => a - b);
```

**Bug Type 5: Off-by-one errors**
```javascript
// BUG: misses last element
for (let i = 0; i < arr.length - 1; i++) { ... }
//                              ^^^^ wrong

// FIX: use strict less-than (no -1)
for (let i = 0; i < arr.length; i++) { ... }
```

**Bug Type 6: Missing return statement**
```javascript
// BUG: function returns undefined instead of the value
function add(a, b) {
  const result = a + b;
  // forgot return!
}

// FIX:
function add(a, b) {
  return a + b;
}
```

**Bug Type 7: Scope issue (var vs let)**
```javascript
// BUG: var is function-scoped, not block-scoped
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100);  // prints 3, 3, 3
}

// FIX: use let
for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100);  // prints 0, 1, 2
}
```

### Step 5: What to Say While Debugging

Say these out loud — this is what gets you credit even when you haven't found the bug yet:

```
"Let me start by reading the error message... it says [X]..."
"The error is in [file] at line [N]. Let me go there."
"I'm going to add a console.log here to see what this variable actually is."
"OK, so req.user is undefined. That means the authMiddleware didn't attach it. Let me check the middleware..."
"I think the bug is [X] because [reason]. Let me verify by [test]."
"Found it — the issue is [X]. The fix is [Y] because [reason]."
```

---

## PART 5 — React Knowledge (For the Frontend Editor)

You might be asked to build a small React component. Here's what you must know:

### The Simplest Possible React Component
```jsx
import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);  // state: initial value 0

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>
    </div>
  );
}

export default Counter;
```

### Fetching Data from an API
```jsx
import { useState, useEffect } from "react";

function UserList() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    fetch('/api/users')
      .then(res => res.json())
      .then(data => {
        setUsers(data);
        setLoading(false);
      })
      .catch(err => {
        setError(err.message);
        setLoading(false);
      });
  }, []);  // empty array = run once on mount

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;

  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.name}</li>  // key is required for lists
      ))}
    </ul>
  );
}
```

### Form Handling
```jsx
function LoginForm() {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');

  const handleSubmit = async (e) => {
    e.preventDefault();  // prevent page reload
    
    const response = await fetch('/api/auth/login', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ email, password })
    });
    
    const data = await response.json();
    console.log(data);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input 
        type="email" 
        value={email}
        onChange={e => setEmail(e.target.value)}
        placeholder="Email"
      />
      <input 
        type="password"
        value={password}
        onChange={e => setPassword(e.target.value)}
        placeholder="Password"
      />
      <button type="submit">Login</button>
    </form>
  );
}
```

---

## PART 6 — Communication Scripts for the Live Coding Round

### When You Start
> "Let me read this twice before I start coding... OK, so the function takes [X] and needs to return [Y]. Can I confirm: if the input is [example], the output should be [expected output]?"

### When You Know the Approach
> "My plan is to use [approach]. The reason I'm choosing this over [alternative] is [reason]. This gives me [complexity]."

### When You're Writing Code
> "I'm creating a HashMap here to store [what]... Now I'm iterating through the array... At each step I'm checking if [condition]..."

### When You Hit a Bug
> "Hmm, that's not the output I expected. Let me trace through with the example input manually... Ah, I see — my loop is exiting one step early because of the off-by-one condition here. Let me fix the boundary."

### When You're Stuck (Honest Recovery)
> "I'm not immediately seeing the optimal approach. Let me start with the brute force version and then optimize. Brute force would be O(n²) by checking every pair — let me code that..."

### When You Finish
> "Let me trace through the example to verify: input [X], step 1 [Y], step 2 [Z], output [result]. That matches. Let me also think about edge cases — what if the array is empty? What if all elements are the same?"

---

## PART 7 — 30 More Likely Interview Questions

These extend the 50+ in the project guide. Use these for mock practice.

**Q36:** What is the difference between `==` and `===` in JavaScript?
> `==` coerces types (1 == '1' is true). `===` checks value AND type (1 === '1' is false). Always use `===` in production code.

**Q37:** What is a closure?
> A function that retains access to variables from its outer scope even after the outer function has finished. Example: `function makeCounter() { let count = 0; return () => ++count; }` — the returned function closes over `count`.

**Q38:** What is the event loop in Node.js?
> Node.js is single-threaded. The event loop allows it to handle async operations without blocking. I/O operations (database queries, file reads) are offloaded to the OS. When they complete, a callback is queued in the event loop. The loop processes these callbacks when the call stack is empty. This is why `await` doesn't block — it yields control back to the event loop.

**Q39:** What is middleware in Express?
> A function with signature (req, res, next) that sits between the request and the route handler. It can modify req/res, terminate the request, or call next() to pass control forward. Middleware runs in the order it's registered. In VIPASA: authMiddleware → roleMiddleware → validateData → controller.

**Q40:** What is the difference between PUT and PATCH?
> PUT replaces the entire resource — you send the complete new state. PATCH applies a partial update — you send only the fields you want to change. VIPASA uses PATCH for status updates because only one field changes.

**Q41:** What is REST?
> Representational State Transfer. A set of constraints for designing web APIs: stateless communication, resources identified by URLs, standard HTTP verbs (GET/POST/PUT/PATCH/DELETE), uniform interface. My VIPASA API follows REST — /api/staff/applications is a resource, POST creates one, GET lists them.

**Q42:** What is the difference between process.env and hardcoding values?
> Environment variables allow the same codebase to behave differently in dev/staging/production without code changes. Hardcoding a database URL means changing code to deploy. With process.env, ops/DevOps manages these separately and they never end up in version control.

**Q43:** What is Docker and have you used it?
> Docker packages an application with its dependencies into an isolated container that runs the same everywhere. I've listed Docker as a tool on my resume — I've used it to run a local PostgreSQL container during development instead of installing Postgres directly.

**Q44:** What is a Promise in JavaScript?
> An object representing a computation that may complete in the future. It has three states: pending, fulfilled, rejected. `.then()` handles fulfillment, `.catch()` handles rejection. `async/await` is syntactic sugar that makes Promise chains look like synchronous code.

**Q45:** What is the difference between null and undefined?
> `undefined` means a variable was declared but never assigned. `null` is an explicit absence of value — a developer intentionally set it to nothing. `typeof undefined === 'undefined'`, `typeof null === 'object'` (a famous JS quirk).

**Q46:** What HTTP status codes should you know?
> 200 OK, 201 Created, 400 Bad Request (validation failed), 401 Unauthorized (not logged in), 403 Forbidden (logged in but no permission), 404 Not Found, 409 Conflict (duplicate email), 500 Internal Server Error.

**Q47:** What is TypeScript and why use it over JavaScript?
> TypeScript adds a static type system on top of JavaScript. The compiler catches type errors before runtime. It's especially valuable in larger codebases where you can't hold everything in memory — types serve as documentation and the IDE gives better autocomplete.

**Q48:** What is the difference between a library and a framework?
> A library (like Lodash, bcrypt) provides functions you call. You're in control. A framework (like NestJS, Next.js) calls your code — you fill in the gaps it defines. "Don't call us, we'll call you" — the Hollywood Principle. Express is somewhere in between — very minimal, mostly library-like.

**Q49:** What is rate limiting and why do you need it?
> Rate limiting caps how many requests a client can make in a time window. Without it, a single client can spam your API — consuming resources or brute-forcing authentication. Express-rate-limit is a middleware that tracks requests by IP and returns 429 Too Many Requests when the limit is exceeded.

**Q50:** What would you monitor in production?
> At minimum: error rate (5xx responses), latency (p50/p95/p99), request volume, database connection pool usage, CPU/memory. In Node.js specifically, event loop lag is important — high lag means the server is blocking. Tools: Prometheus + Grafana, Datadog, or Sentry for error tracking.

---

> **Final Reminder Before the Interview:** The human doing the interview was once where you are. They know what it's like to not know something. What they can't forgive is dishonesty or panic. Know your own code, be honest about what you don't know, and use the communication scripts to keep talking even when you're uncertain.
