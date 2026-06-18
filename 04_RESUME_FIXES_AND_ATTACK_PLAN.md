# 04 — Immediate Action: Resume, GitHub, LinkedIn Fixes + Mock Interview Plan
### What to Fix RIGHT NOW Before the Interview

---

> **Premise:** This guide is brutally honest. Some of these are things you need to fix before the interview. Others are things to be aware of so you don't get caught out. Every item has been derived from either your resume text or your actual code.

---

## SECTION 1: Resume Fixes (Do Before Interview)

### 🔴 CRITICAL Issues (Fix Immediately)

**Issue 1: "JWT Authentication (Short TTL)" — This is Inaccurate**

Your current JWT expiry in `loginClient` is `'1d'` (1 day). That is not short TTL.

**Fix Option A (Best):** Actually implement short TTL + refresh tokens before the interview:
- Change `expiresIn: '1d'` to `expiresIn: '15m'`
- Add a `/api/auth/refresh` endpoint that takes a refresh token and issues a new access token
- Store refresh tokens in the database with an expiry

**Fix Option B (Minimum):** Change resume language to:
> "JWT Authentication with configurable TTL, designed for short-lived tokens with refresh token support (refresh endpoint in progress)"

**Fix Option C (Honest Retreat):** Change to:
> "JWT Authentication, stateless session management with bcrypt password hashing"

**Issue 2: "FK constraints and indexing" — Indexing is Incomplete**

Your schema has FK-implied indexes but no explicit performance indexes on:
- `application.status` (queried in every staff listing)
- `user.email` (queried on every login)
- `user.phone` (queried on every phone-based login)

**Fix:** Add these to your Prisma schema before the interview:
```prisma
model User {
  // ... existing fields
  @@index([email])
  @@index([phone])
}

model Application {
  // ... existing fields
  @@index([status])
  @@index([clientId, status])
}
```
Then run `npx prisma db push` to apply.

**Issue 3: The `getApplications` Incomplete Function**

In `src/controllers/application.ts`, lines ~150-155:
```typescript
export const getApplications = async (req:Request, res:Response) => {
    const user = (req as any).user as {...};
    const rawPage = Number(req.query.page)
    // EMPTY — no implementation, no return
}
```

This function is exported, imported in `staff/application.ts`, and connected to a live route `/api/staff/applications GET`. If an interviewer tests your API, this will crash or return nothing.

**Fix:** Either implement it or remove it and use a different function. Minimum fix:
```typescript
export const getApplications = async (req:Request, res:Response) => {
    return res.status(501).json({ error: "Not implemented" });
}
```

---

### 🟡 IMPORTANT Issues (Fix If You Have Time)

**Issue 4: Missing Middleware Security Headers**

Your `server.ts` doesn't use `helmet` — but helmet is in your `package.json`. This is a quick win:
```typescript
// Current server.ts:
import express from "express";
import rootRouter from "./routes/index.js";
const app = express();
app.use(express.json())

// Fix — add helmet and cors:
import express from "express";
import helmet from "helmet";
import cors from "cors";
import rootRouter from "./routes/index.js";
const app = express();
app.use(helmet());            // Sets security headers
app.use(cors());              // Handles CORS
app.use(express.json())
```

Both are already installed. Zero-cost fix.

**Issue 5: Resume Describes Multer as "Object Storage Services" — This is Misleading**

Your resume says: "Used Multer to securely parse and store the incoming files(Pdfs) in Object Storage Services"

The truth: Files go to local disk (`uploads/` directory), not object storage like S3 or GCS.

**Fix in resume:** Change to:
> "Used Multer to securely parse and store uploaded PDF/image files with strict MIME-type validation, naming sanitization, and 5MB file-size limits; architecture supports swap to cloud object storage (S3)"

---

### 🟢 Nice to Have (Lower Priority)

**Issue 6: Resume Doesn't Mention TypeScript Explicitly in Skills**

You have TypeScript in your languages list but your ERP project description focuses on tech, not outcomes. Add a quantifiable outcome if possible:

> "Reduced runtime errors significantly through strict TypeScript typing with Prisma-generated types and Zod schema validation at API boundaries"

**Issue 7: GitHub Links Are Missing on Resume for VIPASA**

The resume shows "GitHub" as a link for the AI Job Tracker but the ERP system doesn't have a visible GitHub link noted. If the VIPASA repo is private, that's fine — but if it's public, add the link. If it's private, add "(Private Repository)" to avoid interviewers wondering.

**Issue 8: No Metrics Anywhere**

Strong resumes have numbers. Consider adding:
- "Implemented pagination reducing response payload size by ~80% for listing endpoints"
- "API handles [N] endpoint types with JWT + RBAC protection"

Even rough estimates signal you think about impact, not just implementation.

---

## SECTION 2: GitHub Fixes

### Before the Interview: Audit Your Public Repositories

**VIPASA ERP:**
- [ ] Ensure README.md exists with: project description, how to run locally, API overview, tech stack
- [ ] Check that no `.env` file is committed (run `git log --all --full-history -- .env`)
- [ ] Ensure `.gitignore` includes: `node_modules/`, `.env`, `uploads/`, `dist/`
- [ ] Check `package.json` — the version should reflect an active project, not 0.0.0

**Recommended README Structure:**
```markdown
# VIPASA — ERP System for Consultancies

REST API backend for managing consultancy clients, applications, services, and documents.

## Tech Stack
- Node.js + Express + TypeScript
- PostgreSQL + Prisma ORM
- JWT Authentication + RBAC
- Zod validation + Multer file uploads

## Getting Started
1. Clone: `git clone [url]`
2. Install: `npm install`
3. Setup env: `cp .env.example .env` and fill DATABASE_URL, JWT_SECRET
4. Database: `npx prisma db push`
5. Seed: `npx ts-node prisma/seed.ts`
6. Run: `npm run dev`

## API Overview
- POST /api/auth/register — Register new client
- POST /api/auth/login — Login and get JWT
- GET  /api/client/applications — List my applications (Client)
- POST /api/staff/applications — Create application (Staff/Admin)
- GET  /api/staff/clients — List all clients (Staff/Admin)
[... etc]
```

**AI Job Tracker:**
- [ ] Ensure the live demo link works (your resume says "Live Demo")
- [ ] README should explain what the AI matching actually does
- [ ] If Gemini API key is in the code, rotate it immediately and move to env vars

**NeutralinoJS:**
- [ ] Add a line to your GitHub bio mentioning the merged PR (shows open source involvement)

---

## SECTION 3: LinkedIn Fixes

- [ ] Headline: Change from default to something like: "Backend Developer | Node.js, TypeScript, PostgreSQL | Open Source Contributor"
- [ ] About section: Write 3 sentences. What you build, what you're interested in, what you're looking for.
- [ ] Projects: Add VIPASA and the AI Job Tracker with descriptions
- [ ] Featured: Pin the NeutralinoJS PR or a project demo
- [ ] Skills: Add TypeScript, Node.js, REST APIs, PostgreSQL, Prisma, JWT, Docker to skills and request endorsements
- [ ] Ensure the GitHub link in your profile is correct

---

## SECTION 4: What to Say About Missing Evidence

**If asked about something you haven't built yet:**

❌ Wrong: "Oh yeah, I definitely built that."
❌ Wrong: (silence / subject change)
✅ Right: "I haven't implemented that fully yet — what I have is [X], and the next step would be [Y]. I thought about this problem and my approach would be [Z]."

**Specific scenarios:**

"Where are the Swagger docs?"
> "I set up the basic OpenAPI spec structure and documented the core auth endpoints. The full documentation isn't complete — I'd want to document all error responses and add request/response examples. But the API contract itself is well-defined in the Zod schemas."

"Can you show me the refresh token implementation?"
> "The refresh token logic isn't in the current build — I have access tokens working with a 1-day TTL. The design is clear though: on login, issue both an access token and a refresh token (stored hashed in the database with an expiry). The /auth/refresh endpoint would validate the refresh token and issue a new short-lived access token."

"I see `getApplications` is not implemented?"
> "Good catch — that's a stub function that I flagged as needing to be removed. It's connected to a route but doesn't return anything useful. The actual implementation for staff-facing application listing is `getStaffClientApplications`. I should have either completed or removed the stub."

---

## SECTION 5: The Day-Before Checklist

### Mental Prep
- [ ] Read through all 4 guides once
- [ ] Practice the 5-Step Protocol out loud for one DSA problem
- [ ] Read your controller code once (application.ts, auth.ts, authMiddleware.ts)
- [ ] Memorize the architecture flow: Request → Route → Middleware → Controller → Prisma → DB

### Technical Prep
- [ ] Test that your project actually runs locally: `npm run dev`
- [ ] Have Postman ready with your most important endpoints
- [ ] Close all social media tabs on your browser
- [ ] Test your microphone, camera, and screen share

### Materials to Have Open
- [ ] Your resume (PDF)
- [ ] GitHub open to VIPASA repo
- [ ] A blank text editor for scratch work

---

## SECTION 6: Mock Interview Attack Plan — Top 30 Most Dangerous Questions

These are the 30 questions most likely to catch you off guard. Practice answering each one out loud.

| # | Question | Your Weakness | Script Approach |
|---|---------|---------------|-----------------|
| 1 | Walk me through your architecture | Overcomplicating | Use the flow diagram from Guide 02 |
| 2 | Why Express not NestJS/Fastify? | No reason prepared | Simplicity + ecosystem + Prisma support |
| 3 | Short TTL JWT — show me in code | TTL is actually 1d | Honest: "this needs to be updated" |
| 4 | What indexes did you add? | Only FK indexes | Honest gap + explain what you'd add |
| 5 | The getApplications function is empty | Direct code evidence | "Known stub, should be removed" |
| 6 | What is O(n) time complexity? | DSA knowledge gap | From guide 01: "touches each element once" |
| 7 | Write a function that [simple task] | Blank-screen fear | 5-step protocol + narrate |
| 8 | How does bcrypt work? | Might not know internals | From guide 02: rounds, slow hash |
| 9 | What's a race condition? | Might not know term | Two operations reading/writing same resource simultaneously, leading to unpredictable results |
| 10 | What is ACID? | DB theory gap | Atomicity (all or nothing), Consistency (valid state), Isolation (concurrent transactions don't corrupt), Durability (committed = persisted) |
| 11 | Why use Prisma vs raw SQL? | Just repeating marketing | Type safety + generated types + injection prevention |
| 12 | What is CORS? | Might stumble | Browser security policy + your cors package |
| 13 | How would you test this API? | No tests in codebase | Jest + Supertest, describe endpoint behavior, test edge cases |
| 14 | What is the difference between 401 and 403? | Might confuse them | 401 = not logged in; 403 = logged in, no permission |
| 15 | How would you add logging? | No logging currently | Winston/Pino, structured JSON logs, request ID per request |
| 16 | What is a deadlock? | DB theory gap | Two transactions each holding a lock the other needs, stuck forever |
| 17 | Explain async/await to a junior | Communication test | Syntactic sugar for Promises; await yields to event loop without blocking |
| 18 | What would break if you deployed 2 instances? | Local file storage | Multer saves to disk — multiple servers have different disks. Fix: S3 |
| 19 | Why no rate limiting on auth endpoints? | Gap in security | Known gap — express-rate-limit would be the fix |
| 20 | Have you written tests? | No tests currently | Manual with Postman; next step is Jest+Supertest integration tests |
| 21 | What is a foreign key constraint? | DB basics | Enforces referential integrity — you can't insert a row that references a non-existent parent |
| 22 | What is the N+1 problem? | Might not know term | From guide 02 — Prisma's include prevents it |
| 23 | What does `helmet` do? | It's in your package.json but not used | Sets HTTP security headers (CSP, XSS protection, etc.) — actually it's installed, I should add it to server.ts |
| 24 | How does Promise.all differ from sequential awaits? | Concurrency concept | Parallel vs sequential — guide 02, Q20 |
| 25 | What is a connection pool? | Used it but might not explain | pg.Pool manages multiple DB connections — instead of one connection that blocks, pool has N ready |
| 26 | Reverse a linked list | DSA challenge | Iterative: prev/curr/next pointer dance |
| 27 | What is the event loop? | Node internals | Single thread, callbacks queued, event loop processes when call stack is empty |
| 28 | What is XSS? How do you prevent it? | Security basics | Cross-site scripting — injecting JavaScript via user input. Prevention: sanitize input, don't use innerHTML, use CSP headers |
| 29 | What is SQL injection? | Security basics | Injecting SQL through user input. Prevention: parameterized queries (Prisma does this) |
| 30 | Where do you see yourself in 2 years? | Behavioral trap | "Working on systems at larger scale, having led technical decisions on a team feature, and continuing to contribute to open source while applying what I've learned to increasingly complex engineering problems" |

---

## SECTION 7: The Opening 5 Minutes

The first 5 minutes set the tone. Practice this exact script:

**Interviewer:** "Tell me about yourself."

**Your Script:**
> "I'm Yuvraj, a second-year CS student at Delhi University. I got into backend development by building things I wanted to exist. My main project right now is VIPASA — an ERP system for consultancies. I built the entire backend from scratch: REST API with Express and TypeScript, PostgreSQL through Prisma, JWT authentication with role-based access, file uploads with Multer, and Zod for request validation. Before that I built an AI-powered job tracker that uses Gemini to match resumes against job listings. I also got a pull request merged into NeutralinoJS, which is an open-source desktop framework. I'm most interested in backend systems — API design, data modeling, and making things reliable. I'm looking to grow under engineers who care about code quality."

**Then immediately ask:**
> "Before we dive in — is there a particular part of my background you'd like to start with, or would you prefer to go in order?"

This takes control and shows confidence.

---

> **Final Truth:** You have real projects with real code. You've built things that many computer science students haven't touched. The gap is not experience — it's preparation and communication. These four guides give you the words for your work. The rest is practice.
>
> **One week before the interview:** Re-read all 4 guides.
> **Three days before:** Practice explaining your architecture out loud.
> **Day before:** Run through the top 30 questions in section 6 with a friend or out loud alone.
> **Day of:** Trust your preparation. You know more than you think.
