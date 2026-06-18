# 01 — DSA Survival Guide for the Interview
### Yuvraj Singh | Target: Software Developer | Calibrated to: Absolute Beginner

---

> **Brutal Honesty First:** You said you don't know DSA. That's your biggest liability in this interview. This guide won't teach you everything — that takes months. It will teach you enough to *not look completely lost*, explain your thinking, and recover gracefully when you get stuck. That's all you need.

---

## PART 1 — The Mental Model: How to Think About Any DSA Problem

Before you write a single line of code, you need this framework. Practice saying these words out loud:

### The 5-Step Interview Protocol (Memorize This)

```
1. REPEAT: "So what you want me to do is..."    ← Paraphrase the problem
2. CLARIFY: Ask 1-2 questions before coding     ← Shows you think before acting
3. EXAMPLE: "Let me trace through with an example..." ← Write a tiny test case
4. APPROACH: "My plan is..." + say complexity   ← Talk before typing
5. CODE: Write, narrate, don't go silent        ← Commentary = credit even if wrong
```

**Why this works:** Interviewers give partial credit for thinking. A wrong solution with correct reasoning often beats a correct solution that appeared from nowhere.

### What to Say When You're Stuck
```
"I'm thinking about this from a brute-force angle first..."
"Let me consider what data structure would make this faster..."
"If I could precompute X, then the lookup becomes O(1)..."
"I'm seeing a pattern here that looks like a sliding window..."
```

---

## PART 2 — The 6 Patterns That Cover 90% of Interview Problems

This interview is likely calibrated to your level (early university, backend focus). These patterns are what you'll face.

---

### PATTERN 1: Two Pointers

**When to use it:** Array is sorted OR you need pairs/subarrays with a condition.

**Mental Model:** Two runners on a track — one from each end, or one fast, one slow.

**Template:**
```javascript
function twoPointers(arr) {
  let left = 0;
  let right = arr.length - 1;

  while (left < right) {
    const sum = arr[left] + arr[right];
    
    if (sum === target) {
      return [left, right];       // Found answer
    } else if (sum < target) {
      left++;                     // Need bigger numbers → move left forward
    } else {
      right--;                    // Need smaller numbers → move right back
    }
  }
  return [];                      // No answer found
}
```

**Classic Problem: Two Sum II (sorted array)**
```javascript
// Given sorted array [2, 7, 11, 15], target = 9
// Expected output: [0, 1] (because 2 + 7 = 9)

function twoSum(numbers, target) {
  let left = 0;
  let right = numbers.length - 1;

  while (left < right) {
    let sum = numbers[left] + numbers[right];
    
    if (sum === target) return [left, right];
    if (sum < target)  left++;
    else               right--;
  }
  return [-1, -1];
}

// DRY RUN:
// [2, 7, 11, 15], target=9
// Step 1: left=0(2), right=3(15), sum=17 > 9 → right--
// Step 2: left=0(2), right=2(11), sum=13 > 9 → right--
// Step 3: left=0(2), right=1(7),  sum=9  = 9 → return [0,1] ✓
```

**Complexity:** Time O(n), Space O(1)

**Interview Discussion Points:**
- "I used two pointers because the array is sorted, which guarantees that moving left increases the sum and moving right decreases it."
- "This is better than brute force O(n²) because we never go backward."

---

### PATTERN 2: Sliding Window

**When to use it:** "Find the subarray/substring that satisfies condition X" — maximum, minimum, longest, etc.

**Mental Model:** A window of glass you slide across an array. You expand it to the right, shrink it from the left.

**Template:**
```javascript
function slidingWindow(arr) {
  let start = 0;
  let maxLength = 0;
  let windowState = 0;   // Track what's inside the window (sum, count, etc.)

  for (let end = 0; end < arr.length; end++) {
    // Add arr[end] to your window
    windowState += arr[end];

    // Shrink from left if window violates condition
    while (windowState > limit) {
      windowState -= arr[start];
      start++;
    }

    // Update answer
    maxLength = Math.max(maxLength, end - start + 1);
  }

  return maxLength;
}
```

**Classic Problem: Maximum sum subarray of size k**
```javascript
// Given [2, 1, 5, 1, 3, 2], k=3
// Answer: 9 (subarray [5, 1, 3])

function maxSumSubarray(arr, k) {
  let windowSum = 0;
  let maxSum = 0;
  let start = 0;

  for (let end = 0; end < arr.length; end++) {
    windowSum += arr[end];        // Expand window

    if (end >= k - 1) {           // Window is full
      maxSum = Math.max(maxSum, windowSum);
      windowSum -= arr[start];    // Slide: remove leftmost element
      start++;
    }
  }

  return maxSum;
}

// DRY RUN:
// [2,1,5,1,3,2], k=3
// end=0: win=[2], sum=2
// end=1: win=[2,1], sum=3
// end=2: win=[2,1,5], sum=8, maxSum=8, slide out arr[0]=2, start=1
// end=3: win=[1,5,1], sum=7, maxSum=8, slide out arr[1]=1, start=2
// end=4: win=[5,1,3], sum=9, maxSum=9, slide out arr[2]=5, start=3
// end=5: win=[1,3,2], sum=6, maxSum=9, done
// Answer: 9 ✓
```

**Complexity:** Time O(n), Space O(1)

---

### PATTERN 3: HashMap / Frequency Counter

**When to use it:** "Find duplicates", "count occurrences", "check if two things are equal", "find the complement".

**Mental Model:** A lookup table. Trade space for time.

**Template:**
```javascript
function frequencyCounter(arr) {
  const freq = {};  // or new Map()

  for (const item of arr) {
    freq[item] = (freq[item] || 0) + 1;
  }

  // Now use freq for lookups
  for (const item of arr) {
    if (freq[item] > 1) return true;  // Example: has duplicate
  }

  return false;
}
```

**Classic Problem: Two Sum (unsorted array)**
```javascript
// Given [2, 7, 11, 15], target = 9
// Answer: [0, 1]

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

// DRY RUN:
// nums=[2,7,11,15], target=9
// i=0: complement=7, seen={}, not found → seen={2:0}
// i=1: complement=2, seen={2:0}, FOUND at 0 → return [0,1] ✓
```

**Complexity:** Time O(n), Space O(n)

**Interview Script:**
- "I'm going to use a HashMap to store each number as I see it. For each number, I'll compute the complement and check if I've seen it before. This gives me O(n) time versus O(n²) for brute force."

---

### PATTERN 4: Stack (LIFO)

**When to use it:** "Balance brackets", "evaluate expressions", "undo history", anything where order matters and you process last-in first-out.

**Mental Model:** A stack of plates. You can only touch the top.

**Template:**
```javascript
function useStack(input) {
  const stack = [];

  for (const item of input) {
    if (shouldPush(item)) {
      stack.push(item);           // Add to top
    } else if (stack.length > 0 && matches(stack[stack.length-1], item)) {
      stack.pop();                // Remove from top
    } else {
      return false;               // Mismatch
    }
  }

  return stack.length === 0;      // Empty = all matched
}
```

**Classic Problem: Valid Parentheses**
```javascript
// Given "([]{})", return true
// Given "([)]", return false

function isValid(s) {
  const stack = [];
  const pairs = { ')': '(', ']': '[', '}': '{' };

  for (const char of s) {
    if ('([{'.includes(char)) {
      stack.push(char);                           // Opening bracket → push
    } else {
      if (stack.pop() !== pairs[char]) return false;  // Closing bracket → must match
    }
  }

  return stack.length === 0;                      // Stack empty = balanced
}

// DRY RUN:
// Input: "([{}])"
// '(' → stack=['(']
// '[' → stack=['(','[']
// '{' → stack=['(','[','{']
// '}' → pop '{', pairs['}']='{', match ✓, stack=['(','[']
// ']' → pop '[', pairs[']']='[', match ✓, stack=['(']
// ')' → pop '(', pairs[')']=']', match ✓, stack=[]
// return true ✓
```

**Complexity:** Time O(n), Space O(n)

---

### PATTERN 5: Binary Search

**When to use it:** Sorted array, need to find something. Anything that says "sorted" + "find" = consider binary search.

**Mental Model:** Phonebook lookup. Open in the middle. Too high? Left half. Too low? Right half.

```javascript
function binarySearch(arr, target) {
  let left = 0;
  let right = arr.length - 1;

  while (left <= right) {
    const mid = Math.floor((left + right) / 2);  // ALWAYS use Math.floor

    if (arr[mid] === target) {
      return mid;              // Found it
    } else if (arr[mid] < target) {
      left = mid + 1;          // Target is in right half
    } else {
      right = mid - 1;         // Target is in left half
    }
  }

  return -1;                   // Not found
}

// DRY RUN:
// arr=[1,3,5,7,9,11], target=7
// Step 1: left=0, right=5, mid=2, arr[2]=5 < 7 → left=3
// Step 2: left=3, right=5, mid=4, arr[4]=9 > 7 → right=3
// Step 3: left=3, right=3, mid=3, arr[3]=7 = 7 → return 3 ✓
```

**Complexity:** Time O(log n), Space O(1)

---

### PATTERN 6: Recursion + Tree Traversal

**When to use it:** Trees, nested structures, "depth of X", "path from X to Y".

**Mental Model:** A function that calls itself on smaller pieces.

```javascript
// A tree node looks like this:
class TreeNode {
  constructor(val) {
    this.val = val;
    this.left = null;
    this.right = null;
  }
}

// Three ways to traverse:
// In-Order (Left → Node → Right): gives sorted output for BST
// Pre-Order (Node → Left → Right): good for copying
// Post-Order (Left → Right → Node): good for deleting

function inOrder(node) {
  if (node === null) return;       // Base case: empty node
  inOrder(node.left);              // Go left
  console.log(node.val);           // Process current
  inOrder(node.right);             // Go right
}

// Classic: Height of a binary tree
function height(node) {
  if (node === null) return 0;                         // Base case
  const leftHeight  = height(node.left);               // Recurse left
  const rightHeight = height(node.right);              // Recurse right
  return 1 + Math.max(leftHeight, rightHeight);        // Height = 1 + tallest child
}
```

---

## PART 3 — Big-O Cheat Sheet (What You Must Memorize)

```
COMPLEXITY    | EXAMPLE              | MEANING
-----------   | ----------------     | -------
O(1)          | arr[5]               | Constant — same speed always
O(log n)      | Binary search        | Halves the problem each step
O(n)          | Single loop          | Linear — goes through once
O(n log n)    | Good sort (mergesort)| Sort + divide
O(n²)         | Nested loops         | Quadratic — usually too slow
O(2^n)        | Recursive subsets    | Exponential — very slow
```

**Say this in the interview:**
- "This has O(n) time complexity because we touch each element once."
- "This uses O(1) extra space because we only use a few variables, not an array."
- "The nested loop gives O(n²), but we can optimize to O(n) with a HashMap."

---

## PART 4 — The Brute Force Escape Hatch

If you're completely lost, **always** fall back to brute force and say:

> "I can see a brute force O(n²) solution — check every pair. Let me code that first and then optimize."

This is not failure. This is good engineering thinking. Write the nested loop version, get partial credit, then attempt to optimize.

```javascript
// Brute Force: Check every pair (Two Sum)
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
// Say: "This is O(n²) time, O(1) space. We can optimize with a HashMap."
```

---

## PART 5 — 30 Questions You MUST Practice Before the Interview

**Tier 1 (Highest Probability — do these first):**

1. Two Sum (HashMap)
2. Valid Parentheses (Stack)
3. Reverse a string / array (Two Pointers or loop)
4. Find the maximum in an array
5. FizzBuzz
6. Check if a string is a palindrome
7. Fibonacci (both iterative and recursive)
8. Binary search on sorted array
9. Find duplicate in array (HashMap)
10. Count character frequency in a string

**Tier 2 (Medium probability):**

11. Maximum subarray sum (Kadane's Algorithm)
12. Move zeros to end of array
13. Merge two sorted arrays
14. Reverse a linked list
15. Remove duplicates from sorted array
16. Check if two strings are anagrams
17. First non-repeating character
18. Longest common prefix
19. Sort array of 0s, 1s, 2s (Dutch flag)
20. Flatten a nested array

**Tier 3 (Lower probability — do if time allows):**

21. Validate a BST
22. Level order traversal of a tree (BFS)
23. Depth-first search on a graph
24. Minimum depth of binary tree
25. Climbing stairs (dynamic programming intro)
26. Subarray with given sum
27. Rotate an array by k positions
28. Find missing number in array [1..n]
29. Power of a number (fast exponentiation)
30. Longest substring without repeating characters

---

## PART 6 — Recovery Scripts (When You're Completely Stuck)

### Script 1: Buying Time
> "Let me think out loud for a moment... so if the input is [example], I need to produce [output]. The key constraint here is [X]..."

### Script 2: Asking for a Hint Gracefully
> "I have a general sense of the approach — I'm thinking about using [data structure] — but I want to make sure I'm going in the right direction. Does that seem reasonable?"

### Script 3: Admitting You Don't Know
> "I haven't encountered this specific problem before, but I think the underlying pattern is [X]. Let me try to apply that and see where it goes."

### Script 4: After Getting a Wrong Answer
> "Ah — I see the issue. My assumption about [X] was wrong. Let me reconsider..."

### Script 5: If the Problem is Hard
> "My first instinct is O(n²) brute force. Would it be okay if I code that first and then work on optimization?"

---

## PART 7 — Complexity Analysis Cheat Sheet for Common Patterns

```
PATTERN               | TIME    | SPACE  | WHEN TO USE
------------------    | ------  | -----  | ------------------
Two Pointers          | O(n)    | O(1)   | Sorted array, pair problems
Sliding Window        | O(n)    | O(1)   | Subarray/substring problems  
HashMap lookup        | O(n)    | O(n)   | Complement search, frequency
Binary Search         | O(logn) | O(1)   | Find in sorted collection
Recursion (tree)      | O(n)    | O(h)   | Tree traversal (h=height)
Sorting first         | O(nlogn)| O(1)   | When order matters
```

---

## PART 8 — Three Practice Problems with Full Solutions

### Problem 1: Reverse a String In-Place

```javascript
// Write a function that reverses a string
// Input: "hello"  → Output: "olleh"

function reverseString(s) {
  // Convert to array (strings are immutable)
  const arr = s.split('');
  let left = 0;
  let right = arr.length - 1;

  while (left < right) {
    // Swap characters
    [arr[left], arr[right]] = [arr[right], arr[left]];
    left++;
    right--;
  }

  return arr.join('');
}

// How to explain:
// "I use two pointers starting at both ends.
//  I swap characters and move both pointers inward.
//  Time: O(n), Space: O(n) for the array."
```

### Problem 2: Find Maximum Sum Subarray (Kadane's Algorithm)

```javascript
// Input: [-2, 1, -3, 4, -1, 2, 1, -5, 4]
// Output: 6  (subarray [4,-1,2,1])

function maxSubArray(nums) {
  let currentSum = nums[0];
  let maxSum = nums[0];

  for (let i = 1; i < nums.length; i++) {
    // Either extend current subarray OR start fresh from here
    currentSum = Math.max(nums[i], currentSum + nums[i]);
    maxSum = Math.max(maxSum, currentSum);
  }

  return maxSum;
}

// Mental model:
// At each position ask: "Is it better to start over here, 
// or keep adding to my existing subarray?"
// If currentSum < 0, it's dragging us down → start fresh
```

### Problem 3: Check if Brackets Are Balanced

```javascript
// Already shown above. Practice writing it from memory.
// The key insight: use a stack, push opening brackets,
// pop and verify when you see closing brackets.
```

---

> **Final DSA Reminder:** The interview is likely calibrated to your level. They know you're a second-year student. They're testing if you can *think* — not if you've memorized LeetCode hard problems. Apply the 5-Step Protocol, talk out loud, and use the recovery scripts. You'll do better than you think.
