# Interview Prep — Part 2
## Working Project (Live Coding) + Debug Challenge

> This covers the two hands-on rounds: building a small feature live in an in-browser editor, and debugging an existing issue in a cloned repo while sharing your screen and reasoning out loud. These rounds test something different from the conversational rounds — they test whether your understanding actually translates into working hands, under mild pressure, while being watched. This is exactly where "framework user who copy-pastes with AI" habits get exposed if you haven't prepared specifically for working without that crutch.

---

# PART ONE: THE WORKING PROJECT ROUND

## What This Round Actually Is

The brief says: "Build a small feature in an in-browser editor — frontend runs live, backend gets graded." This tells you something specific: you'll likely be given a small, scoped task — not "build a full app," but something like "add a search filter to this list," "build a form that validates and submits," "add an endpoint that does X," or "fix this component so it does Y." The frontend portion will show you live visual feedback. The backend portion will be evaluated by reading your code and possibly running tests against it.

This is fundamentally different from a take-home project where you have unlimited time and can lean on AI tools or extensive searching. You will be watched, likely time-boxed, and expected to talk through your reasoning as you go. The skill being tested is not "can you produce correct code" — it's "can you think in code, in real time, in front of another person."

---

## The Reality You Need to Accept Before This Round

You've described yourself honestly as a framework user with a habit of copy-pasting with AI. In a live round with screen share, that habit cannot be the strategy — there's no AI tool to lean on, and even if there were, an interviewer watching you alt-tab to ChatGPT and paste in a solution is an instant, unambiguous red flag that ends the interview's credibility regardless of whether the code works.

This is not said to alarm you. It's said because the single highest-leverage thing you can do before this interview is **practice writing small pieces of code without AI assistance, narrating your thinking out loud as you go**, specifically so the live round doesn't feel like the first time you've done this.

---

## How to Behave During Live Coding — The Process That Matters More Than Speed

Interviewers running this kind of round are almost always evaluating your *process* as much as your final output. Here is the process to follow, every time, regardless of what the task is:

**Step 1 — Restate the problem out loud before writing anything.**

"So I need to build a search input that filters this list of items by name, and it should update as the user types." Say this even if it feels obvious. It confirms you understood correctly, gives the interviewer a chance to correct a misunderstanding before you've written code, and buys you ten seconds to organize your thinking.

**Step 2 — Ask one clarifying question if there's real ambiguity, but don't stall.**

If the task says "filter by name" and you're unsure whether it should be case-sensitive, or whether it should match anywhere in the string versus only the start, ask. This is not a sign of weakness — assuming the wrong requirement and building the wrong thing wastes more time than a ten-second question.

**Step 3 — State your approach in plain English before coding.**

"I'll keep the search term in state with useState, filter the list on every render based on that term using a case-insensitive includes check, and render the filtered list." Say this before typing. It forces you to actually have a plan, and it lets the interviewer follow your reasoning instead of silently watching you type and wondering what you're doing.

**Step 4 — Write the simplest version first. Get something working end to end before improving it.**

Do not try to write the polished, edge-case-handled, fully validated version on your first pass. Get the core behavior working — even with rough edges — then improve it. This matters because a working-but-imperfect solution with time left to refine is a much stronger outcome than an unfinished "perfect" solution when time runs out.

**Step 5 — Narrate while you type, but don't narrate every keystroke.**

Good narration sounds like: "I'm going to add state for the search term... now I'll write the filter logic... I want to make sure this doesn't break if the list is empty, so let me handle that case." Bad narration is either total silence (interviewer has no idea what you're thinking) or narrating literally every character typed (exhausting and adds no signal).

**Step 6 — Test your own code before declaring it done.**

If it's a frontend task with live preview, actually interact with it — type in the search box, check empty states, check what happens with no matches. If it's a backend task, mentally walk through what happens with valid input, then with missing or malformed input. Saying "let me check this works" and then actually checking is a strong, senior-feeling habit that most junior candidates skip.

**Step 7 — If you get stuck, say so out loud and reason through it rather than going silent.**

"I'm not sure if this is the cleanest way to do this — let me think for a second" is a completely normal, good thing to say. Silence while visibly stuck is what reads badly. Talking through your stuck-ness, even if you don't immediately resolve it, shows the interviewer your actual thought process, which is the entire point of the round.

---

## React Patterns You Need to Be Able to Write From Memory

Given the brief mentions "frontend runs live," this is very likely React (it's the overwhelmingly standard choice for this kind of platform, and matches your existing exposure). Below are the patterns you need to be able to produce without looking anything up, because they cover the large majority of "build a small feature" tasks.

### Controlled Input With State

```jsx
function SearchableList({ items }) {
  const [searchTerm, setSearchTerm] = useState('');

  // Filter on every render based on current searchTerm
  // For small lists this is fine to compute directly during render
  const filteredItems = items.filter(item =>
    item.name.toLowerCase().includes(searchTerm.toLowerCase())
  );

  return (
    <div>
      <input
        type="text"
        value={searchTerm}
        onChange={(e) => setSearchTerm(e.target.value)}
        placeholder="Search..."
      />
      
      {filteredItems.length === 0 ? (
        <p>No results found</p>
      ) : (
        <ul>
          {filteredItems.map(item => (
            <li key={item.id}>{item.name}</li>
          ))}
        </ul>
      )}
    </div>
  );
}
```

Notice the things this example deliberately includes that a rushed version often skips: a `key` prop on the mapped list (you know from Pillar 7 why this matters), an empty state (no results found), and lowercase comparison so the search isn't case-sensitive unless specified otherwise. If you can produce this pattern fluidly, you can adapt it to most "filter/search a list" tasks.

### A Form With Basic Validation and Submission

```jsx
function CreateUserForm({ onSubmit }) {
  const [formData, setFormData] = useState({ name: '', email: '' });
  const [errors, setErrors] = useState({});
  const [isSubmitting, setIsSubmitting] = useState(false);

  function handleChange(e) {
    const { name, value } = e.target;
    setFormData(prev => ({ ...prev, [name]: value }));
  }

  function validate() {
    const newErrors = {};
    if (!formData.name.trim()) {
      newErrors.name = 'Name is required';
    }
    if (!formData.email.includes('@')) {
      newErrors.email = 'Valid email is required';
    }
    return newErrors;
  }

  async function handleSubmit(e) {
    e.preventDefault(); // Stop the browser's default full-page-reload form submit

    const validationErrors = validate();
    if (Object.keys(validationErrors).length > 0) {
      setErrors(validationErrors);
      return;
    }

    setErrors({});
    setIsSubmitting(true);

    try {
      await onSubmit(formData);
      setFormData({ name: '', email: '' }); // Reset on success
    } catch (err) {
      setErrors({ submit: 'Something went wrong. Please try again.' });
    } finally {
      setIsSubmitting(false);
    }
  }

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <input
          name="name"
          value={formData.name}
          onChange={handleChange}
          placeholder="Name"
        />
        {errors.name && <span className="error">{errors.name}</span>}
      </div>

      <div>
        <input
          name="email"
          value={formData.email}
          onChange={handleChange}
          placeholder="Email"
        />
        {errors.email && <span className="error">{errors.email}</span>}
      </div>

      {errors.submit && <p className="error">{errors.submit}</p>}

      <button type="submit" disabled={isSubmitting}>
        {isSubmitting ? 'Submitting...' : 'Submit'}
      </button>
    </form>
  );
}
```

Things deliberately present here that show maturity: a single `formData` object updated generically via the input's `name` attribute (rather than separate state for every field — scales better as fields grow), `e.preventDefault()` (a very common thing to forget, and forgetting it causes a visible, embarrassing full page reload during a live demo), a disabled submit button while submitting (prevents double-submission), and a try/catch/finally around the async submission so the loading state always resolves even on failure.

### Fetching Data on Mount

```jsx
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    let cancelled = false; // Guard against state updates after unmount

    async function fetchUser() {
      setLoading(true);
      setError(null);
      try {
        const res = await fetch(`/api/users/${userId}`);
        if (!res.ok) throw new Error('Failed to fetch user');
        const data = await res.json();
        if (!cancelled) setUser(data);
      } catch (err) {
        if (!cancelled) setError(err.message);
      } finally {
        if (!cancelled) setLoading(false);
      }
    }

    fetchUser();

    return () => { cancelled = true; }; // Cleanup
  }, [userId]);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;
  if (!user) return null;

  return <div>{user.name}</div>;
}
```

If the task is simpler and doesn't require this level of polish, it's fine to skip the cancellation guard — but knowing it exists and being able to mention "in a more production-ready version I'd guard against state updates after unmount" if asked shows depth without over-engineering the live task itself.

---

## Backend (Express) Patterns You Need Fluent

Since the brief says backend gets graded by reading code (not live visual feedback), correctness, structure, and clarity matter more than speed of typing here.

### A Simple, Correctly-Structured Endpoint

```javascript
// Given: build an endpoint that creates a new task

const express = require('express');
const router = express.Router();

router.post('/tasks', async (req, res) => {
  const { title, dueDate } = req.body;

  // Validate input before doing anything else
  if (!title || typeof title !== 'string' || title.trim().length === 0) {
    return res.status(400).json({ error: 'Title is required' });
  }

  try {
    const task = await prisma.task.create({
      data: {
        title: title.trim(),
        dueDate: dueDate ? new Date(dueDate) : null,
        completed: false,
      },
    });

    res.status(201).json({ data: task });
  } catch (err) {
    console.error('Failed to create task:', err);
    res.status(500).json({ error: 'Failed to create task' });
  }
});

module.exports = router;
```

What this demonstrates without you having to say it out loud: correct status codes (201 for creation, 400 for bad input, 500 for server error — covered in Pillar 2), input validation before touching the database, trimming string input, error handling that doesn't leak internal error details to the client, and a consistent response shape (`{ data: ... }` or `{ error: ... }`).

### Handling a "Not Found" Case Correctly

```javascript
router.get('/tasks/:id', async (req, res) => {
  try {
    const task = await prisma.task.findUnique({
      where: { id: req.params.id },
    });

    if (!task) {
      return res.status(404).json({ error: 'Task not found' });
    }

    res.json({ data: task });
  } catch (err) {
    console.error('Failed to fetch task:', err);
    res.status(500).json({ error: 'Failed to fetch task' });
  }
});
```

A very common mistake under live pressure is forgetting the `!task` check and letting `res.json({ data: null })` go out with a 200 status when the resource doesn't exist. If you remember one thing from this section, remember to always check for the not-found case explicitly on any single-resource GET, PATCH, or DELETE.

### A Pattern for "Update Only What's Provided" (PATCH)

```javascript
router.patch('/tasks/:id', async (req, res) => {
  const { title, completed, dueDate } = req.body;

  // Build an update object with only the fields that were actually provided
  const updateData = {};
  if (title !== undefined) updateData.title = title.trim();
  if (completed !== undefined) updateData.completed = completed;
  if (dueDate !== undefined) updateData.dueDate = new Date(dueDate);

  try {
    const task = await prisma.task.update({
      where: { id: req.params.id },
      data: updateData,
    });
    res.json({ data: task });
  } catch (err) {
    if (err.code === 'P2025') {
      // Prisma's "record to update not found" error
      return res.status(404).json({ error: 'Task not found' });
    }
    console.error('Failed to update task:', err);
    res.status(500).json({ error: 'Failed to update task' });
  }
});
```

This is a genuinely good pattern to know cold because PATCH semantics (covered in Pillar 9) are exactly this — only touch the fields that were sent, leave everything else as-is.

---

## If You Get Stuck Mid-Task — A Real Strategy, Not Panic

Getting stuck during a live round is normal and expected — interviewers know this and are not looking for flawless fluency. What separates a good outcome from a bad one is *how* you handle being stuck.

**If you don't know the exact syntax for something**: Say what you're trying to do in plain English, and write pseudocode first. "I want to filter this array down to items where the status equals 'active' — let me write that out." Then translate it. This is a completely normal, professional way to work and interviewers respond well to it.

**If you've written something and it's not working**: Don't silently delete and rewrite from scratch repeatedly. Say "let me check what's actually happening here" and use `console.log` to inspect values at each step. This is real debugging, and demonstrating it live is exactly what the next round (debugging) is also testing — so building this habit now serves both rounds.

**If you genuinely don't know how to do part of the task**: It is significantly better to say "I'm not sure of the exact approach for this part — here's what I'd try first" and attempt something, than to freeze. A visible, reasoned attempt that doesn't fully work is a much stronger signal than silence.

---

# PART TWO: THE DEBUG CHALLENGE

## What This Round Actually Is

The brief says: "Clone a repo and debug an existing issue... share your screen and reason aloud." This is, in many ways, the most realistic round of the entire interview — it's the closest to what actual day-to-day engineering work looks like, and it's testing a skill that's almost completely independent of how much you've memorized: **systematic debugging method**.

This is genuinely good news for you. Unlike DSA trivia or framework-specific syntax recall, debugging method is learnable as a *process*, and once you have the process, it transfers to any codebase, any language, any bug — including ones you've never seen before.

---

## The Systematic Debugging Process — Memorize This Sequence

Most candidates fail debugging rounds not because they lack technical knowledge, but because they debug randomly — changing things, hoping it fixes the issue, without a method. Here is the method to follow, every single time, regardless of what the bug is.

### Step 1: Reproduce the Bug First. Do Not Skip This.

Before reading a single line of code, actually run the application and trigger the bug yourself. Confirm you can see the broken behavior with your own eyes. This sounds obvious, but under interview pressure, candidates often jump straight to reading code based on the bug description alone — and end up fixing something that isn't actually the problem, or missing context about exactly how the bug manifests.

Say out loud: "Let me first reproduce this myself so I understand exactly what's happening." Then do it.

### Step 2: Read the Error Message or Symptom Completely, Don't Skim

If there's a stack trace, read it from top to bottom. The top of a JavaScript stack trace tells you exactly where the error was thrown — the file, the line number. This connects directly to what you learned in Pillar 3 about the call stack: a stack trace is literally a snapshot of the call stack at the moment of the error, read from innermost call (top) to outermost (bottom).

If there's no error message — just wrong behavior — write down precisely what you expected versus what actually happened. "I expected the list to show 5 items, it's showing 3" is a concrete, checkable statement you can use to verify your fix later.

### Step 3: Form a Hypothesis Before Changing Any Code

Say out loud what you think might be causing this, based on the symptom. "Given that the count is off by exactly 2, I suspect this might be an off-by-one error in a loop, or a filter condition that's excluding items it shouldn't." You don't need to be right — you need to demonstrate that you're reasoning toward a cause rather than guessing randomly.

### Step 4: Narrow Down the Location Systematically

Use one of these concrete techniques, narrated out loud:

**Binary search through the code path**: If a request goes through five functions before producing the wrong output, don't read all five carefully right away. Add a `console.log` (or use the debugger) at the midpoint of that chain — function 3. Check if the data is already wrong by that point. If yes, the bug is in functions 1-3. If the data is still correct at that point, the bug is in functions 4-5. Repeat this halving. This is the same logarithmic-search thinking from the DSA section, applied to debugging instead of data structures — a connection worth making out loud if it fits naturally, because it shows the kind of cross-cutting reasoning interviewers value.

**Trace the data, not the code structure**: Pick the specific piece of data that's wrong (a specific variable, a specific field in a response) and follow it backward through the code — where was it set, where was it transformed, where did it come from. This is often more effective than reading functions top to bottom in execution order, because it keeps you focused on the thing that's actually broken instead of getting lost in unrelated code.

### Step 5: Verify Your Hypothesis With Evidence Before Fixing

Once you have a `console.log` or breakpoint showing you the actual value at the suspected location, confirm it matches your hypothesis. "Okay, I can see here that `items` is an array of 5, but after this `.filter()` call it's down to 3 — so the bug is in this filter condition, not somewhere downstream." Only now do you look closely at that specific line.

### Step 6: Understand Why, Not Just What, Before Fixing

Once you've found the specific broken line, take a moment to articulate *why* it's wrong, not just that it is wrong. "This filter is checking `item.status === 'active'` but the data actually has `status: 'Active'` with a capital A, so the comparison is failing silently." This matters because a fix made without understanding the root cause often only patches the symptom you happened to find, leaving the actual underlying issue (in this example, inconsistent casing somewhere upstream) to resurface elsewhere.

### Step 7: Fix It, Then Re-Verify Using the Same Reproduction Steps From Step 1

Don't just assume your fix worked because the code now "looks right." Go back to the exact reproduction steps you used in Step 1 and confirm the bug is actually gone. This closes the loop and is a habit that separates careful engineers from ones who introduce new bugs while "fixing" old ones.

### Step 8: Consider If the Same Bug Exists Elsewhere

If you found a casing mismatch bug in one filter, briefly check — out loud — whether the same pattern might exist in similar code nearby. You don't need to fix everything, but saying "this same pattern might exist in the other filters in this file, worth checking" shows the kind of systems thinking that goes beyond just patching the one reported instance.

---

## Common Bug Categories You're Likely to Encounter (And How to Recognize Them Fast)

Given that this is a general software developer interview rather than a frontend or backend-only role, the debugging task could be either. Here are categories you should be able to recognize quickly, mapped to what you already know from this course.

### Async/Timing Bugs

**Symptom**: Data shows up as `undefined`, or a value that should be set isn't there yet, or things happen in the wrong order.

**What to suspect immediately**: A missing `await`, a `.then()` that wasn't chained correctly, or state being read before an async operation completed. This connects directly to Pillar 4 (Event Loop) — if you see a function that looks synchronous but is calling an async operation without awaiting it, that's almost certainly your bug.

```javascript
// Buggy version — missing await
function getUser(id) {
  const user = fetchUserFromDB(id); // This returns a Promise, not the actual user!
  return user.name; // Trying to read .name on a Promise object — undefined or error
}

// Fixed version
async function getUser(id) {
  const user = await fetchUserFromDB(id);
  return user.name;
}
```

### Off-by-One / Index Errors

**Symptom**: A list is missing the first or last item, or includes one extra item it shouldn't, or a loop crashes on the last iteration.

**What to suspect immediately**: `<` vs `<=` in a loop condition, or an array index calculated incorrectly (especially around `length - 1` vs `length`).

### State Mutation Bugs (React-Specific)

**Symptom**: You call a state setter, but the UI doesn't update, or updates to the wrong thing.

**What to suspect immediately**: Direct mutation of state instead of creating a new object/array. This connects to Pillar 7 — React determines whether to re-render partly by comparing object references; if you mutate an array in place rather than creating a new array, React may not detect the change.

```javascript
// Buggy — mutates the existing array, React may not notice the change
function addItem(newItem) {
  items.push(newItem);       // Mutates in place
  setItems(items);            // Same reference as before — React might skip re-render
}

// Fixed — creates a new array
function addItem(newItem) {
  setItems([...items, newItem]); // New array reference — React detects the change
}
```

### Stale Closure Bugs (React-Specific)

**Symptom**: A function inside `useEffect` or an event handler is using an old, outdated value of a state variable even though the state has since changed.

**What to suspect immediately**: A missing dependency in a `useEffect` dependency array, causing the effect to "close over" the value from when it was first created. This is directly covered in Pillar 3 (Closures) and Pillar 7 (useEffect) — the closure captured the variable's value at creation time, and without the dependency listed, the effect never re-runs to capture the new value.

### Null/Undefined Access Errors

**Symptom**: "Cannot read property 'x' of undefined" or similar.

**What to suspect immediately**: Data that's expected to exist doesn't yet — often because it's still loading (async data not yet arrived) or because an API returned an unexpected shape (a field that's sometimes missing). Check whether there's a loading state being handled, or whether optional chaining (`?.`) is needed around a field that might not always be present.

### Database/Query Bugs

**Symptom**: A query returns no results when it should return some, or returns the wrong records, or a relation is missing.

**What to suspect immediately** (connecting to Pillar 8): A WHERE clause condition that's too strict or has a typo, a missing JOIN/include causing related data not to load, or — as you've directly experienced in VIPASA — a casing mismatch between how a column is referenced in code versus how it actually exists in the database.

---

## How to Talk While You Debug — Sample Narration

Here is what good, continuous narration sounds like during this round, strung together as an example. You don't need to memorize this exact wording, but internalize the *shape* of it:

"Okay, let me first run this and see the bug myself... I can see the list is showing duplicate entries. Let me check the network tab to see what the API is actually returning... okay, the API response itself looks correct, only one entry per item, so the bug is probably in how the frontend is rendering this, not in the data itself. Let me look at the component that renders this list... I see it's using `.map()` here — let me check the key prop... the key is using the array index instead of the item's actual id, and if the list re-orders, that could cause rendering issues, but that wouldn't explain duplicates specifically. Let me add a console.log right before this map to see what's actually in the array at render time... interesting, the array itself has duplicate entries before we even render it. So the bug isn't in rendering, it's upstream — let me check where this array is being built..."

Notice what this narration demonstrates: continuous hypothesis formation and revision, willingness to be wrong and correct course based on evidence ("that wouldn't explain duplicates specifically"), and methodical narrowing down from frontend toward the actual source. This is exactly the process from the eight-step method above, just happening in real, slightly messy, spoken form — which is exactly what it should sound like.

---

## What NOT to Do in This Round

Do not silently stare at the code for long stretches without speaking — even if you're genuinely thinking, narrate the thinking ("I'm trying to trace where this value comes from").

Do not immediately start changing code speculatively before you've reproduced and understood the bug — this looks like guessing, not debugging, even if you occasionally get lucky.

Do not claim to understand something you don't — if you encounter unfamiliar code (a library you haven't used, a pattern you don't recognize), say so honestly: "I haven't worked with this specific library before, let me check what this function does" and then actually look at its usage in context or its documentation. This is normal, expected behavior in real engineering work, and pretending otherwise is far more damaging than admitting unfamiliarity.

Do not give up on a hypothesis too early or hold onto a wrong one too long — both are visible. If your evidence contradicts your hypothesis, say so out loud and pivot: "Okay, that's not it, the data's actually fine at this point, so I was wrong about that — let me look further downstream."

---

## A Final Note Connecting Both Rounds

Both the live coding round and the debug round are fundamentally testing the same underlying thing: **can you reason in real time, out loud, in front of someone, using a structured process rather than guessing or freezing.** This is exactly the skill that's hardest to fake and easiest to build through deliberate practice beforehand.

The single best preparation you can do in the time before this interview is to take any small piece of code — from VIPASA, from a tutorial, from this course's examples — and practice narrating your reasoning about it out loud, alone, in a room, as if someone were watching. It will feel strange at first. By the third or fourth time, it starts to feel natural. That gap between strange and natural is exactly what you want to close before walking into this interview, not during it.

---

*You have now covered all five rounds of this interview format. Return to Part 1 for the DSA discussion and project deep-dive material.*
