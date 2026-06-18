# DSA Interview Preparation Guide
### Tailored for Yuvraj Singh — Software Developer Interview at HireDude.ai
> **Your honest starting point:** DSA knowledge is weak. No competitive programming background. Goal is not to become a competitive programmer — it is to survive the DSA round of *this specific interview*, which is concept-level discussion + one in-browser coding problem. This guide is built around that goal.

---

## Table of Contents

1. [How To Think Like an Interviewer Thinks](#section-1)
2. [Big-O Complexity — The Universal Language](#section-2)
3. [Arrays — The Foundation of Everything](#section-3)
4. [Strings — Arrays in Disguise](#section-4)
5. [Hash Maps — Your Best Friend](#section-5)
6. [Two Pointers — The Pattern You'll Use Most](#section-6)
7. [Sliding Window — Fixed and Variable](#section-7)
8. [Linked Lists — Pointer Manipulation](#section-8)
9. [Stacks and Queues](#section-9)
10. [Recursion and the Call Stack](#section-10)
11. [Binary Search — Beyond Simple Lookup](#section-11)
12. [Sorting — What You Must Know Without Googling](#section-12)
13. [Trees — The Most Common Advanced Topic](#section-13)
14. [Graphs — Conceptual Understanding for Discussion](#section-14)
15. [Dynamic Programming — Recognition, Not Mastery](#section-15)
16. [Problem-Solving Framework for the Interview](#section-16)
17. [Communication Scripts — What to Say While Coding](#section-17)
18. [Practice Problems with Full Walkthroughs](#section-18)

---

## Section 1: How To Think Like an Interviewer Thinks {#section-1}

### What a HireDude.ai senior engineer is actually testing

The interview description says "calibrated to your level" and "talk through approach, not code." This is significant. They are NOT looking for you to perfectly implement a red-black tree. They are checking three things:

1. **Can you reason about problems systematically?** — You don't panic. You break the problem down.
2. **Do you know the vocabulary?** — When they say "what's the time complexity here?", you don't go blank.
3. **Can you write working code in an editor?** — Not elegant code. Working code.

The biggest rejection signal is silence. An interviewer watching you stare at the screen for 60 seconds with nothing coming out of your mouth is a hard fail. Even wrong thinking that you verbalize is better than correct thinking that's invisible.

### The Four Failure Modes You Must Avoid

**Failure Mode 1: Jumping to code before understanding the problem.**
Fix: Repeat the problem back in your own words first. Always.

**Failure Mode 2: Silence when stuck.**
Fix: Have a script. "I'm trying to figure out what data structure fits here. I know I need fast lookup, so I'm thinking a hash map..." Even thinking aloud counts as performance.

**Failure Mode 3: Writing code and then explaining it.**
Fix: Explain *while* you write. "I'm creating a variable called `seen` to track what we've already visited..." Narrate every line.

**Failure Mode 4: Giving up when the first approach is wrong.**
Fix: Have a recovery script. "That approach has O(n²) time. Let me think if there's a better structure here..." Then pivot. They are watching how you recover, not just whether you succeed.

---

## Section 2: Big-O Complexity — The Universal Language {#section-2}

### Why You Cannot Skip This

Every question about your VIPASA project will eventually touch complexity. "Is your pagination efficient?" is a Big-O question. "What happens when you have 100,000 clients?" is a Big-O question. You must be fluent here.

### What Big-O Actually Means

Big-O is a way of describing **how the time or memory your code needs grows as the input gets bigger.** It deliberately ignores constants and small details — it answers only the question: "If I give this function 10x more data, does it take 10x longer? 100x longer? The same time?"

You do not measure actual milliseconds. You count how many operations the algorithm performs *relative to input size n*.

### The Complexity Classes — From Fastest to Slowest

**O(1) — Constant Time**
No matter how big the input, the operation takes the same time.

```javascript
// Accessing an array element by index
const arr = [10, 20, 30, 40, 50];
const x = arr[3]; // Always one operation, regardless of array size
// O(1)

// Looking up a key in a JavaScript object / Map
const map = { "yuvraj": 25 };
const age = map["yuvraj"]; // O(1)
```

Real-world analogy: Opening a book to a specific page number. Page 250 takes the same time as page 3.

**O(log n) — Logarithmic Time**
Each step eliminates *half* the remaining possibilities. Extremely efficient for large inputs.

```javascript
// Binary search in a sorted array
// Input: [1, 3, 5, 7, 9, 11, 13], target: 7
// Step 1: middle is 7. Found! → 1 operation for n=7
// Input: 1,000,000 elements → only ~20 operations
```

Real-world analogy: Finding a word in a dictionary. You open to the middle, decide if your word is before or after, and eliminate half. You never read every page.

**O(n) — Linear Time**
You touch each element once. Double the input, double the time.

```javascript
// Finding the maximum value in an unsorted array
function findMax(arr) {
    let max = arr[0];
    for (let i = 1; i < arr.length; i++) { // Runs n times
        if (arr[i] > max) max = arr[i];
    }
    return max;
}
// n=10 → 10 iterations, n=1000 → 1000 iterations
```

Real-world analogy: Searching for a specific person in an unsorted list by checking each name one by one.

**O(n log n) — Linearithmic Time**
Efficient sorting algorithms live here. Better than O(n²), worse than O(n).

```javascript
// JavaScript's built-in Array.sort() uses this
[5, 2, 8, 1, 9].sort((a, b) => a - b);
// Under the hood: TimSort (merge sort + insertion sort hybrid)
// n=1,000 → ~10,000 operations
// n=1,000,000 → ~20,000,000 operations (compare to n²: 1,000,000,000,000)
```

**O(n²) — Quadratic Time**
Nested loops over the same input. Acceptable for small inputs, disastrous for large ones.

```javascript
// Bubble sort — classic O(n²)
function bubbleSort(arr) {
    for (let i = 0; i < arr.length; i++) {       // n times
        for (let j = 0; j < arr.length - 1; j++) { // n times
            if (arr[j] > arr[j+1]) {
                [arr[j], arr[j+1]] = [arr[j+1], arr[j]];
            }
        }
    }
    return arr;
}
// n=100 → 10,000 operations
// n=10,000 → 100,000,000 operations — suddenly very slow
```

**O(2ⁿ) — Exponential Time**
Typically brute-force recursive solutions. Unusable for n > 30.

**O(n!) — Factorial Time**
Generating all permutations. Completely impractical beyond n=10.

### The Cheat Sheet You Must Memorize

```
O(1)       < O(log n)    < O(n)    < O(n log n) < O(n²)    < O(2ⁿ)
Constant     Log            Linear    n-log-n       Quadratic   Exponential
HashMap      Binary Search  Loop      Merge Sort    Nested Loop  Recursion (bad)
```

### Space Complexity — Same Idea, Different Axis

Space complexity measures **how much extra memory** your algorithm uses, again relative to input size.

```javascript
// O(1) space — only a few variables, regardless of input size
function sum(arr) {
    let total = 0;        // one variable
    for (const x of arr) total += x;
    return total;
}

// O(n) space — you create a new structure proportional to input
function doubled(arr) {
    const result = [];    // grows with input
    for (const x of arr) result.push(x * 2);
    return result;
}

// O(n) space — HashMap that stores each element
function twoSum(nums, target) {
    const seen = new Map(); // could grow to size n
    // ...
}
```

### How to Talk About Complexity in an Interview

**Template answer:**
> "The outer loop runs n times. Inside, I'm doing a hash map lookup which is O(1). So overall time complexity is O(n), and space is O(n) because I store at most n elements in the map."

Never just say a number. Always **justify it** with one sentence about what causes it.

### Practice Questions on Complexity

**Q1:** What is the time complexity of this function?
```javascript
function mystery(arr) {
    let count = 0;
    for (let i = 0; i < arr.length; i++) {
        for (let j = i + 1; j < arr.length; j++) {
            if (arr[i] + arr[j] === 10) count++;
        }
    }
    return count;
}
```
**Answer:** O(n²). Two nested loops where j starts at i+1 — roughly n²/2 iterations, which simplifies to O(n²). Space is O(1) because no extra data structure is created.

**Q2:** What is the complexity of `Array.prototype.includes()` in JavaScript?
**Answer:** O(n) in the worst case. JavaScript arrays are not sorted, so it must check each element. This is why hash maps are preferred for frequent lookup.

**Q3:** In your VIPASA project, you use `prismaClient.user.count()`. A senior engineer asks: "What's the DB complexity of this?" What do you say?
**Answer:** "The count query in PostgreSQL without a WHERE clause is O(n) in theory — it scans all rows. But Prisma uses PostgreSQL's built-in sequential scan or index scan. With the right index on the `role` column, a filtered count like `count({ where: { role: 'Client' } })` becomes much faster — O(log n + k) where k is the matching row count, because PostgreSQL uses the index to skip non-Client rows."

---

## Section 3: Arrays — The Foundation of Everything {#section-3}

### Mental Model First

An array is a **contiguous block of memory** where each element sits right next to the previous one. This physical layout is why:
- **Reading by index is O(1)** — you calculate the memory address directly: `base_address + index * element_size`
- **Appending to the end is O(1) amortized** — JavaScript arrays are dynamic; when they run out of space, they allocate double the capacity (amortized O(1))
- **Inserting or deleting in the middle is O(n)** — everything after the insertion point must shift

### JavaScript Array Internals You Must Know

JavaScript arrays are NOT like C arrays. They are more like hash maps with integer keys, with optimizations for sequential access. The V8 engine uses different internal representations depending on usage pattern — but for interviews, treat them as dynamic arrays.

```javascript
// Key operations and their complexities

const arr = [1, 2, 3, 4, 5];

arr[2]              // O(1) — index access
arr.push(6)         // O(1) amortized — append to end
arr.pop()           // O(1) — remove from end
arr.unshift(0)      // O(n) — insert at beginning, shifts everything
arr.shift()         // O(n) — remove from beginning, shifts everything
arr.splice(2, 1)    // O(n) — insert/remove in middle
arr.indexOf(3)      // O(n) — linear search
arr.includes(3)     // O(n) — linear search
arr.slice(1, 3)     // O(k) where k is slice length — creates new array
arr.concat([7, 8])  // O(n+m) — creates new array
arr.sort()          // O(n log n)
arr.reverse()       // O(n)
arr.map/filter/reduce // O(n)
```

### Pattern 1: Two-Sum (Hash Map Approach)

This is THE most common array interview problem. You MUST be able to code this without thinking.

**Problem:** Given an array `nums` and a target sum, return the indices of two numbers that add up to the target.

```
Input: nums = [2, 7, 11, 15], target = 9
Output: [0, 1]  (because nums[0] + nums[1] = 2 + 7 = 9)
```

**Brute Force (O(n²)) — what NOT to do in an interview:**
```javascript
function twoSumBrute(nums, target) {
    for (let i = 0; i < nums.length; i++) {
        for (let j = i + 1; j < nums.length; j++) {
            if (nums[i] + nums[j] === target) {
                return [i, j];
            }
        }
    }
    return [];
}
```

**Optimal (O(n)) — what TO do:**
```javascript
function twoSum(nums, target) {
    // Map stores: value → index
    const seen = new Map();
    
    for (let i = 0; i < nums.length; i++) {
        const complement = target - nums[i];
        // "Do I already have the number that pairs with nums[i]?"
        if (seen.has(complement)) {
            return [seen.get(complement), i];
        }
        // Record this number and its index for future iterations
        seen.set(nums[i], i);
    }
    
    return []; // No solution found
}
```

**Dry Run — Walk Through This Manually:**
```
nums = [2, 7, 11, 15], target = 9

i=0: nums[i]=2, complement=9-2=7. seen={} → 7 not in seen. Add: seen={2:0}
i=1: nums[i]=7, complement=9-7=2. seen={2:0} → 2 IS in seen! Return [seen.get(2), 1] = [0, 1]
```

**How to explain this in an interview:**
> "My first thought is a nested loop, which works but is O(n²). I can do better with a hash map. For each element, I ask: does the number that would complete my target already exist in the map? If yes, I found my pair. If no, I store the current number. This reduces it to O(n) time and O(n) space."

### Pattern 2: Finding Duplicates

```javascript
// Problem: Does this array have any duplicate values?

// O(n²) approach - naive
function hasDuplicateNaive(nums) {
    for (let i = 0; i < nums.length; i++) {
        for (let j = i + 1; j < nums.length; j++) {
            if (nums[i] === nums[j]) return true;
        }
    }
    return false;
}

// O(n) approach - with Set
function hasDuplicate(nums) {
    const seen = new Set();
    for (const num of nums) {
        if (seen.has(num)) return true; // Already saw this number
        seen.add(num);
    }
    return false;
}

// O(n log n) approach - sort first, then check neighbors
function hasDuplicateSort(nums) {
    nums.sort((a, b) => a - b);        // Sort: duplicates become adjacent
    for (let i = 0; i < nums.length - 1; i++) {
        if (nums[i] === nums[i + 1]) return true; // Adjacent duplicates
    }
    return false;
}
```

**Interview talking point:** "There's a space vs. time tradeoff here. The hash Set approach is O(n) time and O(n) space. The sort approach is O(n log n) time and O(1) extra space if we sort in place. The right choice depends on whether memory or speed is more constrained."

### Pattern 3: Prefix Sums

A prefix sum is a running total that lets you answer "what is the sum of elements from index i to index j?" in O(1) after O(n) setup.

```javascript
// Problem: Given array [1, 2, 3, 4, 5], answer multiple range sum queries fast.

function buildPrefixSum(arr) {
    const prefix = new Array(arr.length + 1).fill(0);
    for (let i = 0; i < arr.length; i++) {
        prefix[i + 1] = prefix[i] + arr[i];
    }
    return prefix;
    // prefix = [0, 1, 3, 6, 10, 15]
    //           ^  ^  ^   ^   ^   ^
    //           0  1  1+2 +3  +4  +5
}

// Sum of elements from index 1 to 3 (inclusive) = arr[1]+arr[2]+arr[3] = 2+3+4 = 9
// Without prefix sum: loop from 1 to 3 = O(k) per query
// With prefix sum: prefix[4] - prefix[1] = 10 - 1 = 9 → O(1) per query

function rangeSum(prefix, left, right) {
    return prefix[right + 1] - prefix[left];
}
```

**Why you care (VIPASA connection):** "In my ERP project, if I wanted to display a dashboard showing 'total applications this month' broken down by day, I could precompute a prefix sum over daily counts so that any date-range query is O(1) instead of scanning all applications every time."

### Common Array Interview Problems with Solutions

**Problem: Find maximum subarray sum (Kadane's Algorithm)**

```javascript
// Input: [-2, 1, -3, 4, -1, 2, 1, -5, 4]
// Output: 6  (subarray [4, -1, 2, 1] has sum 6)

function maxSubarraySum(nums) {
    let maxSum = nums[0];      // Best sum found so far (overall)
    let currentSum = nums[0];  // Best sum ending at current position
    
    for (let i = 1; i < nums.length; i++) {
        // Decision: should I extend the current subarray, or start fresh?
        // If currentSum is negative, starting fresh is better
        currentSum = Math.max(nums[i], currentSum + nums[i]);
        maxSum = Math.max(maxSum, currentSum);
    }
    
    return maxSum;
}

/*
Dry Run: [-2, 1, -3, 4, -1, 2, 1, -5, 4]
i=0: currentSum=-2, maxSum=-2
i=1: currentSum=max(1, -2+1)=max(1,-1)=1, maxSum=max(-2,1)=1
i=2: currentSum=max(-3, 1-3)=max(-3,-2)=-2, maxSum=1
i=3: currentSum=max(4, -2+4)=max(4,2)=4, maxSum=4
i=4: currentSum=max(-1, 4-1)=max(-1,3)=3, maxSum=4
i=5: currentSum=max(2, 3+2)=max(2,5)=5, maxSum=5
i=6: currentSum=max(1, 5+1)=max(1,6)=6, maxSum=6
i=7: currentSum=max(-5, 6-5)=max(-5,1)=1, maxSum=6
i=8: currentSum=max(4, 1+4)=max(4,5)=5, maxSum=6

Answer: 6 ✓
*/
```

**Time:** O(n), **Space:** O(1)

---

## Section 4: Strings — Arrays in Disguise {#section-4}

### The Key Insight

In JavaScript, strings are **immutable sequences of characters**. Every string method that looks like it "modifies" the string actually creates a new one. This has performance implications.

```javascript
// String immutability
let s = "hello";
s[0] = "H";  // Silently fails — strings are immutable in JS
console.log(s); // Still "hello"

// Correct way to "modify"
s = "H" + s.slice(1); // Creates a NEW string "Hello"
```

### Critical String Operations and Complexities

```javascript
const s = "hello world";

s.length          // O(1) — stored as a property
s[0]              // O(1) — character access
s.charAt(0)       // O(1)
s.indexOf("o")    // O(n) — linear search
s.includes("ell") // O(n)
s.slice(2, 5)     // O(k) — k = length of slice
s.split("")       // O(n) — creates array of characters
s.toUpperCase()   // O(n) — creates new string
s.trim()          // O(n)
s + " more"       // O(n+m) — string concatenation creates new string

// WARNING: String concatenation in a loop is O(n²)!
// BAD:
let result = "";
for (let i = 0; i < n; i++) {
    result += chars[i];  // Creates new string EVERY iteration
}
// Total work: 1 + 2 + 3 + ... + n = O(n²)

// GOOD: Use an array and join at the end
const parts = [];
for (let i = 0; i < n; i++) {
    parts.push(chars[i]);
}
const result = parts.join(""); // O(n) — one allocation
```

### Pattern: Anagram Check

```javascript
// Problem: Are "listen" and "silent" anagrams?
// (same characters, different order)

// Approach 1: Sort both and compare — O(n log n)
function isAnagramSort(s, t) {
    if (s.length !== t.length) return false;
    return s.split("").sort().join("") === t.split("").sort().join("");
}

// Approach 2: Character frequency count — O(n)
function isAnagram(s, t) {
    if (s.length !== t.length) return false;
    
    const count = new Map();
    
    // Count characters in s
    for (const char of s) {
        count.set(char, (count.get(char) || 0) + 1);
    }
    
    // Subtract characters in t
    for (const char of t) {
        if (!count.has(char)) return false;
        count.set(char, count.get(char) - 1);
        if (count.get(char) < 0) return false;
    }
    
    return true;
}
```

**Dry Run:**
```
s = "listen", t = "silent"

After counting s: {l:1, i:1, s:1, t:1, e:1, n:1}
Processing t:
  's' → count={l:1, i:1, s:0, t:1, e:1, n:1}
  'i' → count={l:1, i:0, s:0, t:1, e:1, n:1}
  'l' → count={l:0, i:0, s:0, t:1, e:1, n:1}
  'e' → count={l:0, i:0, s:0, t:1, e:0, n:1}
  'n' → count={l:0, i:0, s:0, t:1, e:0, n:0}
  't' → count={l:0, i:0, s:0, t:0, e:0, n:0}
All went to 0 → true ✓
```

### Pattern: Valid Palindrome

```javascript
// Problem: Is "racecar" a palindrome? Is "A man a plan a canal Panama"?

// Simple version (clean string)
function isPalindrome(s) {
    let left = 0, right = s.length - 1;
    while (left < right) {
        if (s[left] !== s[right]) return false;
        left++;
        right--;
    }
    return true;
}

// Real interview version (with spaces and punctuation)
function isPalindromeReal(s) {
    // Normalize: lowercase, remove non-alphanumeric
    s = s.toLowerCase().replace(/[^a-z0-9]/g, "");
    let left = 0, right = s.length - 1;
    while (left < right) {
        if (s[left] !== s[right]) return false;
        left++;
        right--;
    }
    return true;
}

// isPalindromeReal("A man a plan a canal Panama") → true
```

---

## Section 5: Hash Maps — Your Best Friend {#section-5}

### Why Hash Maps Are Powerful

A hash map (JavaScript `Map` or plain object `{}`) gives you O(1) average for:
- Insert a key-value pair
- Look up a value by key
- Delete a key-value pair
- Check if a key exists

This turns many O(n²) problems into O(n) problems. The "trick" in probably 40% of interview problems is: "use a hash map to remember something you've seen before."

### JavaScript Map vs Object — Know the Difference

```javascript
// Object {} — keys must be strings or symbols
const obj = {};
obj["name"] = "Yuvraj";  // key is string
obj[42] = "value";       // 42 gets converted to string "42"

// Map — keys can be ANYTHING (objects, numbers, etc.)
const map = new Map();
map.set("name", "Yuvraj");
map.set(42, "value");
map.set({id: 1}, "object key");  // Object as key!

// Map operations
map.has("name")          // → true
map.get("name")          // → "Yuvraj"
map.delete("name")       // removes key
map.size                 // → count of entries
map.keys()               // iterator over keys
map.values()             // iterator over values
map.entries()            // iterator over [key, value] pairs

// Iterating
for (const [key, value] of map) {
    console.log(key, value);
}
```

**When to use Map vs Object:**
- Use `Map` when keys are not strings, when you need `.size`, or when insertion order matters
- Use `{}` for simple string-key lookups (slightly faster for simple cases)

### Pattern: Frequency Counter

```javascript
// Problem: Find the most frequent element in an array

function mostFrequent(arr) {
    const freq = new Map();
    
    // Build frequency map
    for (const item of arr) {
        freq.set(item, (freq.get(item) || 0) + 1);
    }
    
    // Find maximum
    let maxCount = 0;
    let maxItem = null;
    for (const [item, count] of freq) {
        if (count > maxCount) {
            maxCount = count;
            maxItem = item;
        }
    }
    
    return maxItem;
}

// mostFrequent([1, 2, 2, 3, 2, 1]) → 2
```

### Pattern: Group Anagrams

```javascript
// Problem: Group words that are anagrams of each other
// Input: ["eat","tea","tan","ate","nat","bat"]
// Output: [["bat"],["nat","tan"],["ate","eat","tea"]]

function groupAnagrams(strs) {
    const map = new Map();
    
    for (const str of strs) {
        // Sort the word → all anagrams produce the same sorted key
        const key = str.split("").sort().join("");
        
        if (!map.has(key)) {
            map.set(key, []);
        }
        map.get(key).push(str);
    }
    
    return Array.from(map.values());
}

/*
"eat" → sorted: "aet" → group "aet": ["eat"]
"tea" → sorted: "aet" → group "aet": ["eat", "tea"]
"tan" → sorted: "ant" → group "ant": ["tan"]
"ate" → sorted: "aet" → group "aet": ["eat", "tea", "ate"]
"nat" → sorted: "ant" → group "ant": ["tan", "nat"]
"bat" → sorted: "abt" → group "abt": ["bat"]

Result: [["eat","tea","ate"], ["tan","nat"], ["bat"]]
*/
```

---

## Section 6: Two Pointers — The Pattern You'll Use Most {#section-6}

### The Core Idea

Two pointers is a technique where you maintain two index variables that move through an array simultaneously. It converts many O(n²) problems (nested loops) into O(n) solutions.

**When to recognize it:** You're searching for a pair (or triplet) of elements that satisfies some condition, and the array is sorted (or can be sorted).

### Pattern 1: Opposite Ends

```javascript
// Problem: Given a sorted array, find two numbers that sum to target

function twoSumSorted(nums, target) {
    let left = 0;
    let right = nums.length - 1;
    
    while (left < right) {
        const sum = nums[left] + nums[right];
        
        if (sum === target) {
            return [left, right];
        } else if (sum < target) {
            // Sum too small → move left pointer right to increase sum
            left++;
        } else {
            // Sum too big → move right pointer left to decrease sum
            right--;
        }
    }
    
    return []; // No solution
}

/*
nums = [1, 2, 3, 4, 6], target = 6

left=0(1), right=4(6): sum=7 > 6 → right--
left=0(1), right=3(4): sum=5 < 6 → left++
left=1(2), right=3(4): sum=6 == 6 → return [1, 3] ✓
*/
```

### Pattern 2: Fast and Slow Pointers

```javascript
// Problem: Remove duplicates from SORTED array in-place
// Must do it in O(1) extra space

function removeDuplicates(nums) {
    if (nums.length === 0) return 0;
    
    let slow = 0; // Points to the last position of the "unique" section
    
    for (let fast = 1; fast < nums.length; fast++) {
        // fast scans ahead looking for new unique values
        if (nums[fast] !== nums[slow]) {
            slow++;
            nums[slow] = nums[fast]; // Copy the unique value to the slow position
        }
    }
    
    return slow + 1; // Length of the unique section
}

/*
nums = [1, 1, 2, 3, 3, 4]
slow=0, fast=1: nums[1]=1 == nums[0]=1 → skip
slow=0, fast=2: nums[2]=2 != nums[0]=1 → slow=1, nums[1]=2 → [1,2,2,3,3,4]
slow=1, fast=3: nums[3]=3 != nums[1]=2 → slow=2, nums[2]=3 → [1,2,3,3,3,4]
slow=2, fast=4: nums[4]=3 == nums[2]=3 → skip
slow=2, fast=5: nums[5]=4 != nums[2]=3 → slow=3, nums[3]=4 → [1,2,3,4,3,4]

Return slow+1 = 4 (first 4 elements are [1,2,3,4])
*/
```

### Pattern 3: Container With Most Water

```javascript
// Problem: Given heights [1,8,6,2,5,4,8,3,7], find two bars that trap maximum water
// Water = min(height[left], height[right]) * (right - left)

function maxWater(height) {
    let left = 0, right = height.length - 1;
    let maxWater = 0;
    
    while (left < right) {
        const water = Math.min(height[left], height[right]) * (right - left);
        maxWater = Math.max(maxWater, water);
        
        // Move the SHORTER wall — the taller one might find a better partner
        if (height[left] < height[right]) {
            left++;
        } else {
            right--;
        }
    }
    
    return maxWater;
}
```

---

## Section 7: Sliding Window — Fixed and Variable {#section-7}

### Mental Model

A sliding window is a subarray (or substring) that "slides" through the main array. Instead of re-computing from scratch each step, you add the new element coming in and remove the element going out.

**When to recognize it:** "Find the maximum/minimum/longest/shortest subarray/substring that satisfies condition X."

### Fixed-Size Window

```javascript
// Problem: Find maximum sum of any subarray of size k

function maxSumSubarray(nums, k) {
    let windowSum = 0;
    
    // Build the first window
    for (let i = 0; i < k; i++) {
        windowSum += nums[i];
    }
    
    let maxSum = windowSum;
    
    // Slide the window: add right element, remove left element
    for (let i = k; i < nums.length; i++) {
        windowSum += nums[i];         // Add incoming element
        windowSum -= nums[i - k];     // Remove outgoing element
        maxSum = Math.max(maxSum, windowSum);
    }
    
    return maxSum;
}

/*
nums = [2, 1, 5, 1, 3, 2], k = 3

Initial window [2,1,5]: sum=8, maxSum=8
Slide: add 1, remove 2 → [1,5,1]: sum=7, maxSum=8
Slide: add 3, remove 1 → [5,1,3]: sum=9, maxSum=9
Slide: add 2, remove 5 → [1,3,2]: sum=6, maxSum=9

Answer: 9 ✓
*/
```

### Variable-Size Window

```javascript
// Problem: Find the length of the longest substring without repeating characters
// "abcabcbb" → 3 ("abc")

function lengthOfLongestSubstring(s) {
    const seen = new Set(); // Characters in the current window
    let left = 0;
    let maxLen = 0;
    
    for (let right = 0; right < s.length; right++) {
        // Shrink window from left while we have a duplicate
        while (seen.has(s[right])) {
            seen.delete(s[left]);
            left++;
        }
        
        seen.add(s[right]);
        maxLen = Math.max(maxLen, right - left + 1);
    }
    
    return maxLen;
}

/*
s = "abcabcbb"

right=0('a'): seen={}, add 'a' → seen={'a'}, window="a", len=1
right=1('b'): 'b' not in seen, add → seen={'a','b'}, window="ab", len=2
right=2('c'): 'c' not in seen, add → seen={'a','b','c'}, window="abc", len=3
right=3('a'): 'a' in seen! remove s[left]='a', left=1 → seen={'b','c'}, add 'a' → seen={'b','c','a'}, window="bca", len=3
right=4('b'): 'b' in seen! remove s[left]='b', left=2 → seen={'c','a'}, add 'b' → seen={'c','a','b'}, window="cab", len=3
... (len stays at 3)

Answer: 3 ✓
*/
```

---

## Section 8: Linked Lists — Pointer Manipulation {#section-8}

### What a Linked List Is

A linked list is a chain of **nodes**. Each node has a value and a pointer to the next node. Unlike an array, elements are NOT stored contiguously in memory — they're scattered, connected only by pointers.

```
Array:   [10][20][30][40]   — contiguous memory
LinkedList: [10|→] [20|→] [30|→] [40|null] — scattered, connected by pointers
```

### Why This Matters (Trade-offs vs Array)

```
Operation          Array        Linked List
─────────────────────────────────────────────
Access by index    O(1)         O(n)   ← LL loses here
Insert at head     O(n)         O(1)   ← LL wins here
Insert at tail     O(1)         O(n) without tail pointer
Delete in middle   O(n)         O(1) if you have the node
```

### Building a Linked List in JavaScript

```javascript
// Node class
class ListNode {
    constructor(val, next = null) {
        this.val = val;
        this.next = next;
    }
}

// Building: 1 → 2 → 3 → 4 → 5 → null
const head = new ListNode(1,
    new ListNode(2,
        new ListNode(3,
            new ListNode(4,
                new ListNode(5)))));

// Traversal
function printList(head) {
    let current = head;
    const values = [];
    while (current !== null) {
        values.push(current.val);
        current = current.next;
    }
    console.log(values.join(" → "));
}
// Output: 1 → 2 → 3 → 4 → 5
```

### Pattern: Reverse a Linked List

This is THE most fundamental linked list operation. You must be able to code this without hesitation.

```javascript
function reverseList(head) {
    let prev = null;
    let current = head;
    
    while (current !== null) {
        const nextTemp = current.next; // Save next before overwriting
        current.next = prev;           // Reverse the pointer
        prev = current;                // Move prev forward
        current = nextTemp;            // Move current forward
    }
    
    return prev; // prev is now the new head
}

/*
Before: 1 → 2 → 3 → 4 → 5 → null

Step 1: prev=null, cur=1
  nextTemp=2, cur.next=null, prev=1, cur=2
  State: null ← 1  2 → 3 → 4 → 5

Step 2: prev=1, cur=2
  nextTemp=3, cur.next=1, prev=2, cur=3
  State: null ← 1 ← 2  3 → 4 → 5

Step 3: prev=2, cur=3
  nextTemp=4, cur.next=2, prev=3, cur=4
  State: null ← 1 ← 2 ← 3  4 → 5

Step 4: prev=3, cur=4 → nextTemp=5, cur.next=3, prev=4, cur=5
Step 5: prev=4, cur=5 → nextTemp=null, cur.next=4, prev=5, cur=null

Loop ends. Return prev=5
After: 5 → 4 → 3 → 2 → 1 → null ✓
*/
```

### Pattern: Detect a Cycle (Floyd's Algorithm)

```javascript
// Fast pointer moves 2 steps, slow moves 1 step
// If there's a cycle, fast will lap slow and they'll meet

function hasCycle(head) {
    let slow = head;
    let fast = head;
    
    while (fast !== null && fast.next !== null) {
        slow = slow.next;       // Move 1 step
        fast = fast.next.next;  // Move 2 steps
        
        if (slow === fast) return true; // They met → cycle exists
    }
    
    return false; // fast reached end → no cycle
}
```

**Intuition:** Imagine two runners on a circular track. The faster runner will always eventually lap the slower one. If the track is a straight line with no loop, the faster runner just runs off the end.

---

## Section 9: Stacks and Queues {#section-9}

### Stack — Last In, First Out (LIFO)

Think of a stack of plates. You add to the top, you take from the top.

```javascript
// JavaScript array as stack (very common in interviews)
const stack = [];

stack.push(1);   // push: O(1) — add to top
stack.push(2);
stack.push(3);
// stack = [1, 2, 3]

stack.pop();     // pop: O(1) — remove from top → returns 3
stack[stack.length - 1]; // peek: O(1) — view top without removing → 2
```

**Classic stack problems:**
- Balanced parentheses checking
- Implementing browser back-button history
- Evaluating arithmetic expressions
- Depth-first search (DFS) in graphs/trees

### Pattern: Valid Parentheses

```javascript
// Problem: Is "(()[]{})(" valid? → No. Is "()[]{}" valid? → Yes.

function isValid(s) {
    const stack = [];
    const pairs = { ')': '(', ']': '[', '}': '{' };
    
    for (const char of s) {
        if (char === '(' || char === '[' || char === '{') {
            // Opening bracket → push to stack
            stack.push(char);
        } else {
            // Closing bracket → must match top of stack
            if (stack.length === 0 || stack[stack.length - 1] !== pairs[char]) {
                return false;
            }
            stack.pop();
        }
    }
    
    return stack.length === 0; // Stack must be empty at end
}

/*
s = "({[]})"
'(' → stack=['(']
'{' → stack=['(', '{']
'[' → stack=['(', '{', '[']
']' → top='[', pairs[']']='[' → match! pop → stack=['(', '{']
'}' → top='{', pairs['}']='{}' → match! pop → stack=['(']
')' → top='(', pairs[')']='' → match! pop → stack=[]
Return stack.length===0 → true ✓
*/
```

### Queue — First In, First Out (FIFO)

Think of a queue at a checkout counter. First person in is first person out.

```javascript
// JavaScript array as queue (note: shift() is O(n) — inefficient for large queues)
const queue = [];
queue.push(1);     // enqueue: O(1)
queue.push(2);
queue.push(3);
queue.shift();     // dequeue: O(n) — removes from front (shifts all elements!)

// For interview purposes, shift() is acceptable. In production you'd use a proper deque.
```

**When to use a queue:** BFS (breadth-first search), processing events in order, level-order tree traversal.

---

## Section 10: Recursion and the Call Stack {#section-10}

### The Mental Model

Recursion is a function that calls itself. Every recursive solution has:
1. **Base case** — the condition where the function stops calling itself
2. **Recursive case** — where the function calls itself with a smaller problem

The key insight: **each recursive call waits for its sub-call to return before it can finish.** This creates a stack of pending calls — literally the call stack in your browser/Node.js.

### Understanding the Call Stack

```javascript
function factorial(n) {
    if (n <= 1) return 1;        // Base case
    return n * factorial(n - 1); // Recursive case
}

factorial(4)
```

**What happens in memory:**
```
Call Stack at deepest point:

factorial(4) ← waiting for factorial(3) to return
  factorial(3) ← waiting for factorial(2) to return
    factorial(2) ← waiting for factorial(1) to return
      factorial(1) ← BASE CASE → returns 1

Then it unwinds:
factorial(2) receives 1 → returns 2*1 = 2
factorial(3) receives 2 → returns 3*2 = 6
factorial(4) receives 6 → returns 4*6 = 24
```

### Writing Recursive Functions — The Template

```javascript
function solve(problem) {
    // 1. Base case(s): What's the simplest version of this problem?
    if (isBaseCase(problem)) return baseAnswer;
    
    // 2. Reduce: Make the problem smaller
    const smallerProblem = reduce(problem);
    
    // 3. Recurse: Solve the smaller problem
    const smallerAnswer = solve(smallerProblem);
    
    // 4. Combine: Build the answer to the original problem
    return combine(smallerAnswer, problem);
}
```

### Fibonacci — The Classic

```javascript
// Naive recursive — O(2ⁿ) TIME, O(n) SPACE
function fibNaive(n) {
    if (n <= 1) return n; // Base cases: fib(0)=0, fib(1)=1
    return fibNaive(n-1) + fibNaive(n-2);
}

// Problem: fibNaive(40) computes fib(38) TWICE, fib(37) FOUR TIMES, etc.
// Exponential explosion.

// Memoized — O(n) TIME, O(n) SPACE
function fib(n, memo = {}) {
    if (n <= 1) return n;
    if (memo[n] !== undefined) return memo[n]; // Cache hit!
    memo[n] = fib(n-1, memo) + fib(n-2, memo);
    return memo[n];
}

// Bottom-up (iterative) — O(n) TIME, O(1) SPACE
function fibIterative(n) {
    if (n <= 1) return n;
    let prev2 = 0, prev1 = 1;
    for (let i = 2; i <= n; i++) {
        const curr = prev1 + prev2;
        prev2 = prev1;
        prev1 = curr;
    }
    return prev1;
}
```

### Binary Search Tree (BST) Traversals via Recursion

```javascript
class TreeNode {
    constructor(val, left = null, right = null) {
        this.val = val;
        this.left = left;
        this.right = right;
    }
}

//       4
//      / \
//     2   6
//    / \ / \
//   1  3 5  7

// In-order: LEFT → ROOT → RIGHT → gives sorted order for BST
function inorder(node) {
    if (node === null) return []; // Base case
    return [...inorder(node.left), node.val, ...inorder(node.right)];
    // Returns [1, 2, 3, 4, 5, 6, 7]
}

// Pre-order: ROOT → LEFT → RIGHT
function preorder(node) {
    if (node === null) return [];
    return [node.val, ...preorder(node.left), ...preorder(node.right)];
    // Returns [4, 2, 1, 3, 6, 5, 7]
}

// Post-order: LEFT → RIGHT → ROOT
function postorder(node) {
    if (node === null) return [];
    return [...postorder(node.left), ...postorder(node.right), node.val];
    // Returns [1, 3, 2, 5, 7, 6, 4]
}
```

---

## Section 11: Binary Search — Beyond Simple Lookup {#section-11}

### The Fundamental Idea

Binary search works ONLY on **sorted** data. By checking the middle element, you eliminate half the search space in one step.

```javascript
// Standard binary search
function binarySearch(nums, target) {
    let left = 0;
    let right = nums.length - 1;
    
    while (left <= right) {
        // CRITICAL: Use this formula to avoid integer overflow
        // Don't write: (left + right) / 2
        const mid = left + Math.floor((right - left) / 2);
        
        if (nums[mid] === target) {
            return mid; // Found it!
        } else if (nums[mid] < target) {
            left = mid + 1;  // Target is in right half
        } else {
            right = mid - 1; // Target is in left half
        }
    }
    
    return -1; // Not found
}

/*
nums = [1, 3, 5, 7, 9, 11, 13], target = 7

left=0, right=6, mid=3: nums[3]=7 === 7 → return 3 ✓

nums = [1, 3, 5, 7, 9, 11, 13], target = 6

left=0, right=6, mid=3: nums[3]=7 > 6 → right=2
left=0, right=2, mid=1: nums[1]=3 < 6 → left=2
left=2, right=2, mid=2: nums[2]=5 < 6 → left=3
left=3 > right=2 → loop ends → return -1 ✓
*/
```

### Advanced: Finding First/Last Position

```javascript
// Problem: In [1,2,2,2,3,4], find first and last occurrence of 2

function searchRange(nums, target) {
    return [findFirst(nums, target), findLast(nums, target)];
}

function findFirst(nums, target) {
    let left = 0, right = nums.length - 1;
    let result = -1;
    
    while (left <= right) {
        const mid = left + Math.floor((right - left) / 2);
        if (nums[mid] === target) {
            result = mid;     // Record this position
            right = mid - 1;  // But keep searching LEFT for earlier occurrence
        } else if (nums[mid] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    
    return result;
}

function findLast(nums, target) {
    let left = 0, right = nums.length - 1;
    let result = -1;
    
    while (left <= right) {
        const mid = left + Math.floor((right - left) / 2);
        if (nums[mid] === target) {
            result = mid;    // Record this position
            left = mid + 1;  // But keep searching RIGHT for later occurrence
        } else if (nums[mid] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    
    return result;
}
```

---

## Section 12: Sorting — What You Must Know Without Googling {#section-12}

### The Algorithm Everyone Knows But Almost Nobody Implements Correctly

**Bubble Sort — O(n²) — Only for Small Arrays / Teaching**

```javascript
function bubbleSort(arr) {
    const n = arr.length;
    for (let i = 0; i < n - 1; i++) {
        let swapped = false;
        for (let j = 0; j < n - i - 1; j++) {
            if (arr[j] > arr[j + 1]) {
                [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]]; // Swap
                swapped = true;
            }
        }
        if (!swapped) break; // Already sorted — optimization
    }
    return arr;
}

/*
[5, 3, 1, 4, 2] → after pass 1 → [3, 1, 4, 2, 5] (5 "bubbled" to end)
                → after pass 2 → [1, 3, 2, 4, 5]
                → after pass 3 → [1, 2, 3, 4, 5]
*/
```

**Merge Sort — O(n log n) — Divide and Conquer**

```javascript
function mergeSort(arr) {
    if (arr.length <= 1) return arr; // Base case
    
    const mid = Math.floor(arr.length / 2);
    const left = mergeSort(arr.slice(0, mid));   // Sort left half
    const right = mergeSort(arr.slice(mid));      // Sort right half
    
    return merge(left, right); // Merge sorted halves
}

function merge(left, right) {
    const result = [];
    let i = 0, j = 0;
    
    // While both arrays have elements, pick the smaller one
    while (i < left.length && j < right.length) {
        if (left[i] <= right[j]) {
            result.push(left[i++]);
        } else {
            result.push(right[j++]);
        }
    }
    
    // Append remaining elements
    while (i < left.length) result.push(left[i++]);
    while (j < right.length) result.push(right[j++]);
    
    return result;
}

/*
mergeSort([5, 3, 1, 4, 2])
  mergeSort([5, 3]) + mergeSort([1, 4, 2])
    mergeSort([5]) + mergeSort([3])
      → [5] + [3] → merge([5],[3]) → [3,5]
    mergeSort([1]) + mergeSort([4,2])
      mergeSort([4]) + mergeSort([2])
        → [4] + [2] → merge([4],[2]) → [2,4]
      → merge([1],[2,4]) → [1,2,4]
    → merge([3,5],[1,2,4]) → [1,2,3,4,5] ✓
*/
```

**What to Know for Interviews:**

| Algorithm    | Time (avg)  | Time (worst) | Space  | Stable? | Notes                        |
|-------------|-------------|--------------|--------|---------|------------------------------|
| Bubble Sort | O(n²)       | O(n²)        | O(1)   | Yes     | Only for teaching             |
| Selection   | O(n²)       | O(n²)        | O(1)   | No      | Always n² comparisons         |
| Insertion   | O(n)–O(n²)  | O(n²)        | O(1)   | Yes     | Good for nearly-sorted data   |
| Merge Sort  | O(n log n)  | O(n log n)   | O(n)   | Yes     | Predictable, good for linked lists |
| Quick Sort  | O(n log n)  | O(n²)        | O(log n)| No     | Fastest in practice (avg)     |
| Heap Sort   | O(n log n)  | O(n log n)   | O(1)   | No      | In-place, slower in practice  |

**What JavaScript's `.sort()` uses:**  TimSort — a hybrid of merge sort and insertion sort. O(n log n) worst case.

---

## Section 13: Trees — The Most Common Advanced Topic {#section-13}

### Tree Vocabulary (You Must Know These Words)

```
        1         ← Root (no parent)
       / \
      2   3       ← Children of 1; Siblings of each other
     / \
    4   5         ← Leaf nodes (no children)

Depth of node 4: 2 (distance from root)
Height of tree: 2 (longest path from root to leaf)
```

- **Root:** The top node with no parent
- **Leaf:** A node with no children
- **Height:** Length of longest path from root to any leaf
- **Depth:** Distance from root to a given node
- **Balanced tree:** Height is O(log n) — operations are efficient
- **Unbalanced tree:** Height can be O(n) — degenerate case (linked list)

### Binary Search Tree (BST) Property

For every node:
- ALL values in left subtree are **less than** node's value
- ALL values in right subtree are **greater than** node's value

```
      8
     / \
    3   10
   / \    \
  1   6    14
     / \   /
    4   7 13

✓ 3's left subtree: {1} — all < 3 ✓
✓ 3's right subtree: {6, 4, 7} — all > 3 ✓
✓ 10's right subtree: {14, 13} — all > 10 ✓
```

### Tree Traversals — Must Know All Three

```javascript
// Given tree:
//       4
//      / \
//     2   6
//    / \ / \
//   1  3 5  7

function inorder(node, result = []) {
    if (!node) return result;
    inorder(node.left, result);   // Go all the way left first
    result.push(node.val);        // Visit root
    inorder(node.right, result);  // Then right
    return result;
}
// [1, 2, 3, 4, 5, 6, 7] — SORTED ORDER for BST

function preorder(node, result = []) {
    if (!node) return result;
    result.push(node.val);        // Visit root FIRST
    preorder(node.left, result);
    preorder(node.right, result);
    return result;
}
// [4, 2, 1, 3, 6, 5, 7] — Root first, useful for copying tree

function postorder(node, result = []) {
    if (!node) return result;
    postorder(node.left, result);
    postorder(node.right, result);
    result.push(node.val);        // Visit root LAST
    return result;
}
// [1, 3, 2, 5, 7, 6, 4] — Children before parent, useful for deletion

// Level-order (BFS) — uses a QUEUE, not recursion
function levelOrder(root) {
    if (!root) return [];
    const result = [];
    const queue = [root];
    
    while (queue.length > 0) {
        const levelSize = queue.length;
        const level = [];
        
        for (let i = 0; i < levelSize; i++) {
            const node = queue.shift();
            level.push(node.val);
            if (node.left) queue.push(node.left);
            if (node.right) queue.push(node.right);
        }
        result.push(level);
    }
    
    return result;
}
// [[4], [2, 6], [1, 3, 5, 7]]
```

### Tree Height / Maximum Depth

```javascript
function maxDepth(root) {
    if (!root) return 0; // Base case: empty tree has depth 0
    
    const leftDepth = maxDepth(root.left);
    const rightDepth = maxDepth(root.right);
    
    return 1 + Math.max(leftDepth, rightDepth);
}

/*
maxDepth(4)
  maxDepth(2)
    maxDepth(1) → max(0,0)+1 = 1
    maxDepth(3) → max(0,0)+1 = 1
    → max(1,1)+1 = 2
  maxDepth(6)
    maxDepth(5) → 1
    maxDepth(7) → 1
    → max(1,1)+1 = 2
  → max(2,2)+1 = 3

Answer: 3 (path: 4→2→1 or 4→2→3 or 4→6→5 or 4→6→7) ✓
*/
```

---

## Section 14: Graphs — Conceptual Understanding for Discussion {#section-14}

### What a Graph Is

A graph is a collection of **nodes (vertices)** connected by **edges**. Unlike trees, graphs can have cycles, disconnected components, and can be directed (one-way edges) or undirected.

```
Undirected:    Directed:
A - B          A → B
|   |          ↑   ↓
C - D          D ← C
```

**Real-world graphs:** Social networks (people = nodes, friendships = edges), maps (cities = nodes, roads = edges), the internet (web pages = nodes, links = edges).

### Graph Representations

```javascript
// Adjacency List — most common for interviews
// Space: O(V + E) where V = vertices, E = edges
const graph = {
    A: ['B', 'C'],
    B: ['A', 'D'],
    C: ['A', 'D'],
    D: ['B', 'C']
};

// Adjacency Matrix — good for dense graphs
// Space: O(V²)
const matrix = [
    // A  B  C  D
    [0, 1, 1, 0],  // A
    [1, 0, 0, 1],  // B
    [1, 0, 0, 1],  // C
    [0, 1, 1, 0],  // D
];
```

### BFS — Level by Level (Queue)

```javascript
function bfs(graph, start) {
    const visited = new Set();
    const queue = [start];
    visited.add(start);
    const order = [];
    
    while (queue.length > 0) {
        const node = queue.shift();
        order.push(node);
        
        for (const neighbor of graph[node]) {
            if (!visited.has(neighbor)) {
                visited.add(neighbor);
                queue.push(neighbor);
            }
        }
    }
    
    return order;
}

// bfs(graph, 'A') → ['A', 'B', 'C', 'D']
```

### DFS — Go Deep First (Stack or Recursion)

```javascript
function dfs(graph, start, visited = new Set()) {
    visited.add(start);
    const order = [start];
    
    for (const neighbor of graph[start]) {
        if (!visited.has(neighbor)) {
            order.push(...dfs(graph, neighbor, visited));
        }
    }
    
    return order;
}

// dfs(graph, 'A') → ['A', 'B', 'D', 'C']
```

---

## Section 15: Dynamic Programming — Recognition, Not Mastery {#section-15}

### What DP Actually Is

Dynamic Programming is **recursion + memoization**. You break a problem into overlapping subproblems and store solutions so you don't recompute them.

**Two signs a problem might be DP:**
1. "Find the maximum/minimum" of something
2. "How many ways can you..." do something

**You are NOT expected to master DP in this interview.** But you must be able to:
- Recognize when a recursive solution has redundant computation
- Know the word "memoization" and what it means
- Explain the Fibonacci optimization (you already learned this)

### The Coin Change Problem (Classic DP — Know Conceptually)

```javascript
// Problem: Given coins [1, 5, 10, 25] and amount 36,
// what's the minimum number of coins needed?

// Greedy (works for standard coin systems): 25 + 10 + 1 = 3 coins
// But greedy FAILS for coins like [1, 3, 4] and amount 6:
// Greedy: 4 + 1 + 1 = 3 coins
// Optimal: 3 + 3 = 2 coins

// DP approach: O(amount * coins) time
function coinChange(coins, amount) {
    // dp[i] = minimum coins needed to make amount i
    const dp = new Array(amount + 1).fill(Infinity);
    dp[0] = 0; // 0 coins needed to make amount 0
    
    for (let i = 1; i <= amount; i++) {
        for (const coin of coins) {
            if (coin <= i && dp[i - coin] + 1 < dp[i]) {
                dp[i] = dp[i - coin] + 1;
            }
        }
    }
    
    return dp[amount] === Infinity ? -1 : dp[amount];
}
```

**How to talk about DP in an interview:**
> "My initial recursive solution works but recomputes the same subproblems repeatedly — for fib(5) I compute fib(3) twice. I can optimize this with memoization, storing each result as I compute it. That takes it from O(2ⁿ) to O(n). This technique of storing subproblem results is called dynamic programming."

---

## Section 16: Problem-Solving Framework {#section-16}

### The 5-Step Method — Do This Every Time

**Step 1: Understand (2 minutes)**
- Repeat the problem in your own words: "So you want me to..."
- Ask about edge cases: "What if the input is empty? What if there are duplicates?"
- Confirm the output format: "Should I return an array, a single number, a boolean?"

**Step 2: Examples (1 minute)**
- Work through the given examples by hand
- Create your own simple example: "Let me trace through [1, 2, 3] with target 3..."

**Step 3: Brute Force First (2 minutes)**
- State the naive solution even if it's O(n²)
- "The obvious approach is to try every pair. That's O(n²), but let me think if we can do better."
- Never code the brute force if you can see a better approach — but DO verbalize it

**Step 4: Optimize (3 minutes)**
- Think about what structure could speed things up
- "I need fast lookup → hash map"
- "The array is sorted → binary search or two pointers"
- "I need to track a window → sliding window"

**Step 5: Code + Narrate (remaining time)**
- Write the code AND narrate simultaneously
- After coding: "Let me trace through the example to verify..."
- After verifying: "Time complexity is O(n) because the loop runs once. Space is O(n) for the map."

### Pattern Recognition Cheat Sheet

| Trigger Words | Pattern to Use |
|---|---|
| "sorted array, find pair" | Two pointers |
| "maximum sum subarray of size k" | Sliding window (fixed) |
| "longest substring without..." | Sliding window (variable) |
| "fastest lookup, check if seen" | Hash map / Set |
| "find in sorted array" | Binary search |
| "all possible combinations/subsets" | Recursion / backtracking |
| "minimum/maximum path in tree" | DFS recursion |
| "level-by-level tree traversal" | BFS with queue |
| "detect cycle" | Fast/slow pointers |
| "overlapping subproblems" | DP with memoization |

---

## Section 17: Communication Scripts {#section-17}

### What to Say When You First See the Problem

> "Okay, let me make sure I understand this. You're giving me [restate problem]. I need to return [restate output]. Is that right?"

### What to Say While Thinking

> "I'm thinking about what data structure fits here. If I need fast lookup, a hash map gives me O(1). If the array is sorted, two pointers might work..."

> "My first thought is a brute force with two nested loops, which is O(n²). I think I can do better by [explain your key insight]."

### What to Say While Coding

> "I'm declaring a Map here to store elements I've seen, keyed by value, value is the index..."

> "This loop runs once through the array, so the total time is O(n)..."

> "I'm checking the base case first — if the input is empty, return early..."

### What to Say When Stuck

> "I'm stuck for a second. Let me think out loud — I know I need to [goal]. My constraint is [constraint]. The structure that addresses that is..."

> "Let me try a smaller example. If the array is just [1, 2]... that means... okay, that tells me..."

> "I'm not 100% sure about this edge case. I'm going to put a comment here and come back to it after the main logic works."

### What to Say When You Find a Bug

> "Ah, I see the issue — I'm using `<` but it should be `<=` because [reason]. Let me fix that."

> "This doesn't handle the case where [edge case]. Let me add that check."

### What to Say at the End

> "Let me trace through the original example to verify... [trace]. Looks correct. Time complexity is O(n) because [reason]. Space is O(n) for the [data structure]."

---

## Section 18: Practice Problems with Full Walkthroughs {#section-18}

### Problems Most Likely in This Interview (Ranked by Probability)

---

**Problem 1: Two Sum ⭐ (90% chance)**
Already covered in Section 3. Must code from memory.

---

**Problem 2: Valid Parentheses ⭐ (70% chance)**
Already covered in Section 9. Must code from memory.

---

**Problem 3: Reverse a String**

```javascript
// "hello" → "olleh"

// Approach 1: built-ins (show this fast, then offer O(1) space version)
function reverseStr1(s) {
    return s.split("").reverse().join("");
}

// Approach 2: two pointers, in-place on array
function reverseStr2(s) {
    const chars = s.split("");
    let left = 0, right = chars.length - 1;
    while (left < right) {
        [chars[left], chars[right]] = [chars[right], chars[left]];
        left++;
        right--;
    }
    return chars.join("");
}
// Time: O(n), Space: O(n) for the char array
```

---

**Problem 4: FizzBuzz (Basic Warmup)**

```javascript
// Print 1-100. Replace multiples of 3 with "Fizz", 5 with "Buzz", 15 with "FizzBuzz"

function fizzBuzz(n) {
    for (let i = 1; i <= n; i++) {
        if (i % 15 === 0) console.log("FizzBuzz"); // Check 15 BEFORE 3 and 5
        else if (i % 3 === 0) console.log("Fizz");
        else if (i % 5 === 0) console.log("Buzz");
        else console.log(i);
    }
}

// COMMON MISTAKE: checking 3 and 5 before 15 — then FizzBuzz never triggers
```

---

**Problem 5: Find Duplicate in Array**

```javascript
// Given [1, 3, 4, 2, 2], find the duplicate number (appears twice)

// Using Set — O(n) time, O(n) space
function findDuplicate(nums) {
    const seen = new Set();
    for (const num of nums) {
        if (seen.has(num)) return num;
        seen.add(num);
    }
    return -1;
}

// Floyd's cycle detection — O(n) time, O(1) space (advanced)
// The array [1,3,4,2,2] can be treated as a linked list:
// index 0 → nums[0]=1 → nums[1]=3 → nums[3]=2 → nums[2]=4 → nums[4]=2 → cycle!
function findDuplicateOptimal(nums) {
    let slow = nums[0];
    let fast = nums[nums[0]];
    
    while (slow !== fast) {
        slow = nums[slow];
        fast = nums[nums[fast]];
    }
    
    slow = 0;
    while (slow !== fast) {
        slow = nums[slow];
        fast = nums[fast];
    }
    
    return slow;
}
```

---

**Problem 6: Maximum Consecutive Ones**

```javascript
// Input: [1,1,0,1,1,1]  Output: 3

function findMaxConsecutiveOnes(nums) {
    let maxCount = 0;
    let currentCount = 0;
    
    for (const num of nums) {
        if (num === 1) {
            currentCount++;
            maxCount = Math.max(maxCount, currentCount);
        } else {
            currentCount = 0; // Reset on encountering 0
        }
    }
    
    return maxCount;
}
// Time: O(n), Space: O(1)
```

---

**Problem 7: Rotate Array**

```javascript
// Rotate [1,2,3,4,5,6,7] by k=3 → [5,6,7,1,2,3,4]

// Approach 1: Extra array — O(n) time, O(n) space
function rotateSimple(nums, k) {
    k = k % nums.length; // Handle k > length
    const rotated = [...nums.slice(nums.length - k), ...nums.slice(0, nums.length - k)];
    for (let i = 0; i < nums.length; i++) nums[i] = rotated[i];
}

// Approach 2: Reverse trick — O(n) time, O(1) space
// 1. Reverse whole array: [7,6,5,4,3,2,1]
// 2. Reverse first k elements: [5,6,7,4,3,2,1]
// 3. Reverse remaining: [5,6,7,1,2,3,4] ✓

function rotate(nums, k) {
    k = k % nums.length;
    reverse(nums, 0, nums.length - 1);
    reverse(nums, 0, k - 1);
    reverse(nums, k, nums.length - 1);
}

function reverse(nums, start, end) {
    while (start < end) {
        [nums[start], nums[end]] = [nums[end], nums[start]];
        start++;
        end--;
    }
}
```

---

**Problem 8: Check if Binary Tree is Balanced**

```javascript
function isBalanced(root) {
    function height(node) {
        if (!node) return 0;
        
        const leftH = height(node.left);
        if (leftH === -1) return -1; // Propagate "unbalanced" signal
        
        const rightH = height(node.right);
        if (rightH === -1) return -1;
        
        if (Math.abs(leftH - rightH) > 1) return -1; // This node is unbalanced
        
        return 1 + Math.max(leftH, rightH);
    }
    
    return height(root) !== -1;
}
```

---

### 20 More Problems to Practice (In Order of Difficulty)

Practice these in the browser at [leetcode.com](https://leetcode.com). For each, time yourself — aim for a working solution in under 20 minutes.

**Easy (Must Solve):**
1. Contains Duplicate (LeetCode 217)
2. Best Time to Buy and Sell Stock (LeetCode 121)
3. Merge Two Sorted Lists (LeetCode 21)
4. Maximum Depth of Binary Tree (LeetCode 104)
5. Climbing Stairs (LeetCode 70) — intro to DP
6. Reverse Linked List (LeetCode 206)
7. Palindrome Number (LeetCode 9)
8. Single Number (LeetCode 136)
9. Move Zeroes (LeetCode 283)
10. Intersection of Two Arrays II (LeetCode 350)

**Medium (Should Attempt):**
11. Longest Substring Without Repeating (LeetCode 3)
12. 3Sum (LeetCode 15)
13. Container With Most Water (LeetCode 11)
14. Group Anagrams (LeetCode 49)
15. Jump Game (LeetCode 55)
16. Unique Paths (LeetCode 62)
17. Find Minimum in Rotated Sorted Array (LeetCode 153)
18. Kth Largest Element (LeetCode 215)
19. Word Search (LeetCode 79)
20. Validate BST (LeetCode 98)

---

## Final Checklist Before the Interview

### Day Before
- [ ] Re-read the Two Sum solution until you can write it without looking
- [ ] Re-read Valid Parentheses until you can write it without looking
- [ ] Practice saying your complexity answers out loud (not just thinking them)
- [ ] Practice the "stuck" script 3 times
- [ ] Know the difference between Stack and Queue cold

### Day Of (Morning)
- [ ] Trace through FizzBuzz, Two Sum, Valid Parentheses one more time
- [ ] Review your Big-O cheat sheet

### During DSA Round
- [ ] Repeat problem before starting
- [ ] State brute force before optimizing
- [ ] Narrate every line you write
- [ ] Never go silent for more than 10 seconds — verbalize your thoughts
- [ ] State complexity at the end unprompted

### The Single Most Important Thing

The interviewer can teach you the correct algorithm. They cannot teach you to communicate. If you silently stare and then produce perfect code, you fail. If you talk through your confused thinking and produce slightly buggy code, you might pass. **Talk. Always. Even when lost.**

---

*End of DSA.md — Next: PROJECT_DEFENSE.md covers your VIPASA architecture, 50+ expected questions, and every answer you need.*
