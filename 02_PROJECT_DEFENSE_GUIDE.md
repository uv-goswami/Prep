# 02 — Project Deep-Dive & Interview Q&A
### Based on Your Actual Resume + VIPASA Codebase

---

> **Read This First:** This guide is built entirely from YOUR code and YOUR resume. Every question in here has been pulled from what you actually built. Do not memorize generic answers — understand your own code, then use these as a reference. If you say something in the interview that you can't back up with specifics from your code, an experienced interviewer will catch it immediately.

---

## SECTION 1: The VIPASA ERP System — Architecture Deep Dive

### What You Actually Built (From Your Code)

Based on reading your codebase, here is the honest picture of what VIPASA is:

```
CLIENT (HTTP requests)
       ↓
   Express.js Server (src/server.ts) — port 3000
       ↓
   Route Layer (src/routes/)
   ├── /api/auth    → register, login, /me
   ├── /api/health  → health check
   ├── /api/client  → auth-protected, Client role only
   │   ├── /profile → update my profile
   │   └── /applications → list & get my apps
   └── /api/staff   → auth-protected, Admin+Staff roles
       ├── /clients → CRUD on clients
       ├── /applications → create, update status
       ├── /services → create, list
       └── /document → file upload
       ↓
   Middleware Pipeline
   ├── authMiddleware.ts  → validates JWT, attaches user to req
   ├── roleMiddleware.ts  → requireRole(['Admin','Staff'])
   └── validationMiddleware.ts → Zod schema validation
       ↓
   Controller Layer (src/controllers/)
   ├── auth.ts      → register + login logic
   ├── user.ts      → getMe, update profile, staff client mgmt
   ├── application.ts → create, status update, paginated lists
   ├── document.ts  → file upload handler
   └── service.ts   → service CRUD
       ↓
   Prisma ORM (src/lib/prisma.ts)
       ↓
   PostgreSQL via pg Pool + @prisma/adapter-pg
```

**Database Entities:**
- `User` (id, email, phone, passwordHash, role: Admin/Staff/Client, isActive)
- `ClientProfile` (linked to User 1:1 — demographics, address, documents)
- `StaffProfile` (linked to User 1:1)
- `Application` (clientId, serviceId, staffId, status enum, priority, dueDate)
- `Document` (applicationId, clientId, docUrl, docType, name)
- `Service` (name, basePrice, requiredDocs, estimatedDays)

---

## SECTION 2: Architecture Questions (50+ with Answers)

### GROUP A: High-Level Architecture

**Q1: Walk me through the architecture of your ERP system.**

> **Strong Answer:** "The system follows a layered REST API architecture. HTTP requests come into Express.js and hit a routing layer that dispatches to controllers. Before any controller runs, a middleware pipeline executes: the auth middleware validates the JWT token, the role middleware enforces RBAC, and the validation middleware uses Zod to sanitize the request body. Controllers contain the business logic and interact with PostgreSQL through Prisma ORM. File uploads go through Multer which handles parsing and storage to disk or object storage."

**Q2: Why Express.js instead of NestJS or Fastify?**

> **Strong Answer:** "Express is the most widely-used Node.js framework with extensive ecosystem support. For this project, I prioritized simplicity and familiarity — Express gives you full control with minimal magic. NestJS would add opinionated structure that might be over-engineering for a solo project. I did evaluate Fastify for its performance benchmarks — it's faster than Express — but Express has better Prisma adapter support at the time and more familiar middleware patterns."

> ⚠️ **Red Flag:** If you say "I just used Express because it's popular" — an interviewer will push back. Add the trade-off reasoning.

**Q3: Why TypeScript?**

> **Strong Answer:** "TypeScript gives compile-time type safety which is critical for a codebase with many layers — schema, controller, Prisma models. Without it, you silently pass the wrong shape to a function and only discover the bug at runtime. With TypeScript, Prisma auto-generates types for every model so the compiler catches mismatches. It also makes refactoring safer and improves IDE autocomplete, which matters a lot when you're navigating 20+ files."

**Q4: Why PostgreSQL over MongoDB?**

> **Strong Answer:** "The domain is relational by nature. Users have exactly one profile. Applications belong to exactly one client and one service. Documents belong to applications. These relationships map naturally to relational tables with foreign key constraints. MongoDB would require manual relationship management in application code, which adds bugs and complexity. PostgreSQL also gives you ACID transactions, which matters for financial/document workflows where partial writes are dangerous."

**Q5: What is the data flow for a staff member creating an application?**

> **Strong Answer:** "The staff member sends a POST to /api/staff/applications with a JSON body. The authMiddleware verifies the Bearer token and attaches the decoded JWT payload (id, role) to req.user. The roleMiddleware checks that the role is 'Admin' or 'Staff'. The validateData middleware runs the createApplicationSchema Zod schema against req.body — it validates that name is at least 3 chars, clientId and serviceId are valid UUIDs, and optional fields like priority match their enum. If validation fails, a 400 is returned immediately. If valid, the createApplication controller runs, generates an applicationNo, and inserts a record into the database using Prisma."

---

### GROUP B: Authentication & Security

**Q6: Explain your authentication system.**

> **Strong Answer:** "I use JWT-based stateless authentication. On login, the server verifies the user's password hash with bcrypt, then signs a JWT containing the user's id and role with a secret key. The token is returned to the client. On every protected request, the client sends this token in the Authorization header as 'Bearer {token}'. The authMiddleware decodes and verifies it — if the signature is valid, it attaches the payload to the request as req.user. This means the server doesn't store session state, which makes horizontal scaling easier."

**Q7: What are the security risks in your current JWT implementation?**

> **Honest Strong Answer:** "There are a few things I'd improve in production. First, my current token expiry is set to '1d' — that's quite long. In production I'd use short-lived access tokens (15 minutes) with refresh tokens. Second, I don't have token revocation — if a token is stolen, it's valid until expiry. A solution is a token blacklist or using an intrinsic ID per token checked against a Redis store. Third, the JWT secret is loaded from an environment variable but I have no validation that the secret is actually set — if process.env.JWT_SECRET is undefined, jwt.sign still works but with 'undefined' as the secret, which is a critical vulnerability."

> ⚠️ **Critical Issue in Your Code (Lines in loginClient):** Your JWT expiry is `'1d'` not the "short TTL" mentioned in your resume. Be honest about this in the interview — say "I've identified this as something to improve."

**Q8: Why bcrypt? What does the second argument (10) mean?**

> **Strong Answer:** "bcrypt is a one-way hashing function designed specifically for passwords. It's intentionally slow — unlike SHA256 which can do billions of hashes per second, bcrypt rate-limits brute force attacks. The second argument (10) is the cost factor or 'rounds' — bcrypt runs 2^10 = 1024 iterations of its internal algorithm. Higher is more secure but slower. 10 is the standard recommendation that balances security (preventing GPU-based attacks) with latency (around 100ms per hash)."

**Q9: What is RBAC and how did you implement it?**

> **Strong Answer:** "RBAC stands for Role-Based Access Control. Instead of assigning permissions to individual users, you assign them to roles, and users get roles. I implemented this with a requireRole middleware. When you protect a route, you call requireRole(['Admin', 'Staff']). The middleware reads the user.role from the decoded JWT and checks if it's in the allowed array. If not, it returns 403 Forbidden. Roles are stored in the JWT payload at login time, so the server can make authorization decisions without a database query on every request."

**Q10: What is Zod and why did you use it?**

> **Strong Answer:** "Zod is a TypeScript-first schema validation library. I used it to validate incoming request bodies and query parameters before they reach controllers. The reason is defense-in-depth: even though PostgreSQL has column type constraints, I wanted to catch malformed data at the API boundary before it touches the database. For example, if a staff member sends a createApplication request with an invalid UUID as clientId, Zod catches it and returns a 400 with a descriptive error — without this, Prisma would throw a database error with a cryptic message. Zod also gives me TypeScript types for free by inferring them from the schema."

---

### GROUP C: Database Design

**Q11: Explain your database schema.**

> **Strong Answer:** "I have six main tables. User stores core identity — email, phone, hashed password, and role. I split profile data into ClientProfile and StaffProfile tables linked by userId as a foreign key — this is a one-to-one relationship. Service is a catalog of services the consultancy offers. Application is the central entity — it references a Client, a Service, and a Staff member, with status tracked as an enum (Draft, PendingDocuments, UnderReview, Approved, Rejected, Completed). Document stores uploaded files with a reference to an Application or Client."

**Q12: Why did you separate User, ClientProfile, and StaffProfile?**

> **Strong Answer:** "This is called table-per-hierarchy or joined-table inheritance. The User table stores fields common to all users — email, password, role. Client-specific data like gender, address, and KYC documents goes into ClientProfile. This separation means when I query a user for authentication, I'm not loading unnecessary client profile data. It also makes it easier to add new role types in the future — I'd add a new profile table without touching the User table. The downside is that getting a full client record requires a JOIN, but that's acceptable."

**Q13: What is Prisma and why use it over writing raw SQL?**

> **Strong Answer:** "Prisma is a type-safe ORM for Node.js and TypeScript. It generates TypeScript types from your schema, so when I call prismaClient.application.findMany(), TypeScript knows exactly what shape the result is. Compared to raw SQL, this eliminates an entire category of bugs — typos in column names, wrong types, missing fields. It also provides query builders that prevent SQL injection by default. The trade-off is that complex queries — like highly optimized aggregations — are sometimes easier to write in raw SQL, and Prisma occasionally generates suboptimal queries. For those cases, I can use prisma.$queryRaw()."

**Q14: What is database indexing and did you use it?**

> **Honest Answer:** "Indexing creates a separate data structure (usually a B-tree) that the database uses to look up rows without scanning the entire table. Without an index on a WHERE clause column, a query scans all rows — O(n). With an index, it's O(log n). In my schema, I declared FK constraints which Prisma and PostgreSQL automatically index. I should have also explicitly added indexes on application.status and user.email since those are frequently queried. This is something I'd improve — I mentioned indexing in my resume but the schema doesn't have explicit non-FK indexes yet. I'm aware of the gap."

> ⚠️ **Critical Resume Gap:** Your resume says "FK constraints and indexing" but the schema only has FK-implied indexes. If pushed, admit this honestly — it's better than getting caught.

**Q15: What is pagination and how did you implement it?**

> **Strong Answer:** "Pagination breaks large result sets into pages to avoid sending thousands of rows to the client at once. I implemented offset-based pagination. The client sends page and limit as query parameters — validated by my paginationQuerySchema. In the controller, I calculate skip as (page-1) * limit. I then run two parallel queries using Promise.all — findMany with skip/take for the current page, and count for the total. I return both the data and pagination metadata (total, totalPages, current page) so the frontend can render page controls."

---

### GROUP D: File Uploads

**Q16: Walk me through how file uploads work in your system.**

> **Strong Answer:** "File uploads use Multer, a Node.js middleware for multipart/form-data. I configured a diskStorage engine that saves files to an 'uploads/' directory. The filename is a timestamp plus a random number to prevent collisions. A fileFilter function checks the MIME type — I only allow 'application/pdf' and 'image/*'. There's also a 5MB file size limit. When a POST request comes in, Multer processes the file, then control passes to my uploadDocument controller which reads the file URL from req.file and creates a Document record in the database."

**Q17: What are the security risks with your file upload implementation?**

> **Honest Answer:** "A few I'm aware of. First, MIME type can be spoofed — I check req.file.mimetype but this comes from the Content-Type header which the client controls. In production I'd also check the file's magic bytes using a library like file-type. Second, I'm storing files on local disk, which doesn't work for horizontal scaling — you'd need shared storage like AWS S3. My code actually has the logic for this: the storageProvider.ts supports a location property which S3-multer uses. Third, the upload directory is accessible if someone knows the path — in production these should not be publicly accessible without authentication."

---

### GROUP E: Scalability & Trade-offs

**Q18: What are the scalability limitations of this system?**

> **Strong Answer:** "Several. First, stateless JWT is good for scaling — no session state means you can add more server instances. But local file storage won't work across multiple instances — I'd migrate to S3 or similar. Second, I'm using a single PostgreSQL instance. For high read load, I'd add read replicas. For write-heavy workloads, connection pooling (which I've partially addressed with pg.Pool) becomes critical. Third, there's no caching layer — frequently read data like service catalogs could be cached in Redis. Fourth, no rate limiting on the API — a bad actor could spam the registration endpoint."

**Q19: What would you add if you had more time?**

> **Strong Answer:** "In priority order: refresh token mechanism to fix the long JWT TTL issue, rate limiting on auth endpoints with express-rate-limit, S3 integration for file storage (the code already has a placeholder), proper error logging with something like Winston or Pino instead of console.error, and integration tests for the authentication flow. I'd also add database migrations properly — right now I depend entirely on Prisma's push command which isn't safe for production schema changes."

**Q20: Why use Promise.all for the paginated queries?**

> **Strong Answer:** "Promise.all executes promises in parallel rather than sequentially. If I await the findMany and then await the count, they run one after the other — total time is T_findMany + T_count. With Promise.all they execute simultaneously — total time is max(T_findMany, T_count). Since both hit the database and are independent, parallel execution is strictly better. This optimization matters under load when database round-trips have latency."

---

### GROUP F: Your Resume Claims (Be Ready to Defend Each One)

**Q21: Your resume says you implemented "JWT Authentication with Short TTL." But in your code, the expiry is 1 day. Can you explain?**

> **Honest Answer:** "You're right to call that out. My initial design intent was short TTL with refresh tokens, and the refresh token infrastructure is partially implemented. But in the current build, the TTL is 1 day for development convenience. In production I'd change this to 15 minutes for the access token and implement the refresh token endpoint. I overstated this on my resume — the architecture supports it, but the implementation isn't complete."

**Q22: You mention "OpenAPI/Swagger documentation." Can you show it or describe it?**

> **Honest Answer (if you built it):** Describe the endpoint structure, parameters, and response schemas you documented.
> **Honest Answer (if you didn't fully build it):** "I set up the OpenAPI spec structure and documented the core auth and application endpoints. It's not complete — I'd want to finish documenting all error responses and add request examples before calling it production-ready."

**Q23: You mention "Object Storage Services" for PDFs. Which one did you use?**

> **Honest Answer:** "In the current build, files go to local disk. The storageProvider.ts has a fileFilter and naming convention designed to be compatible with multer-s3. The switch to S3 would require adding the multer-s3 package and configuring AWS credentials. I designed the interface to support this transition — the document controller reads from req.file.location (S3 URL) or req.file.path (local path) with a fallback."

---

### GROUP G: The AI-Powered Job Tracker

**Q24: Explain the AI Job Tracker architecture.**

> **Strong Answer:** "The frontend is React with Vite. The backend is Fastify — I chose Fastify for this project because it has excellent JSON schema support and is significantly faster than Express. State management is in Upstash Redis — I used it for user sessions, caching job listings fetched from the JSearch API (so I don't hit rate limits on every search), and storing application status. The AI feature works by taking a PDF resume, using pdf-parser to extract the text, and sending both the resume text and a job description to the Google Gemini API with a prompt asking it to score the match and identify missing skills."

**Q25: Why Redis for sessions instead of database sessions?**

> **Strong Answer:** "Redis is an in-memory key-value store — reads and writes are orders of magnitude faster than PostgreSQL. Sessions are read on every authenticated request, so they need to be fast. Redis also supports TTL natively — setting a session key to expire in 24 hours is a single command. Using the database for sessions would add a DB query to every request and pollute the main database with transient session data."

---

### GROUP H: Open Source Contribution

**Q26: Tell me about your NeutralinoJS contribution.**

> **Strong Answer:** "Neutralino is a lightweight desktop app framework. I found a bug on Linux where the framework throws a Gdk-CRITICAL assertion error — specifically 'assertion gdk_screen_get_primary_monitor failed' — when it tries to center the application window on startup in environments without a primary monitor, like certain display managers or headless setups. The issue was that the code unconditionally called a Gdk function that assumes a primary monitor exists. My fix added a null check before that call. I submitted the PR, it was reviewed by maintainers, and merged into main."

**Q27: What did you learn from contributing to open source?**

> **Strong Answer:** "How to read unfamiliar code quickly. The NeutralinoJS codebase is C++ which I had limited experience with, but I was able to trace the crash to its root cause by reading the Gdk documentation and following the call stack. Also, the discipline of writing a clean PR — specific commit message, clear description, minimal change. Merged PRs prefer surgical fixes over large refactors."

---

### GROUP I: Hackathon Projects

**Q28: Tell me about what you built at Singularity Vibeathon 2026.**

> **Strong Answer:** "I built a fully offline AI assistant using a local LLM. The key challenge was finding a model small enough to run without a GPU on commodity hardware while still producing useful outputs. I used [model name if you remember — llama.cpp / Ollama / etc.] and exposed it through a simple interface. The offline requirement meant I couldn't use any cloud APIs — everything had to run locally. This taught me about model quantization and the trade-off between model size, speed, and output quality."

**Q29: Explain "Adversa" — the testing tool from HackArena.**

> **Strong Answer:** "Adversa is a tool that generates logically correct but deliberately malformed test data. The concept is chaos engineering for data validation. For example, you give Adversa a user registration schema, and it generates inputs that pass obvious validation — valid email format, correct length — but have edge cases that many validators miss: emails with unusual Unicode characters, strings at exactly the boundary of a length limit, null bytes in strings, numbers at integer overflow. The goal is to expose gaps in error handling before they hit production."

---

### GROUP J: System Design (Expect at Least One)

**Q30: How would you design the notification system for VIPASA?**

> **Strong Answer:** "I'd add a notifications table and implement event-driven notifications. When application status changes, the controller emits an event. A notification service subscribes to that event and creates a notification record per user who should be informed. For real-time delivery, I'd use WebSockets (socket.io) or Server-Sent Events for a simpler implementation. For email notifications, I'd use a queue (Bull with Redis) so that if the email provider is slow, it doesn't block the status update response. Push notifications would need a separate service like Firebase Cloud Messaging."

**Q31: How would you add multi-tenancy to VIPASA?**

> **Strong Answer:** "Currently VIPASA is a single-tenant system — one consultancy, one database. For multi-tenancy, there are three approaches: separate databases per tenant (best isolation, expensive), shared database with a tenantId column on every table (most common, less isolation), or schema-per-tenant in PostgreSQL. I'd go with the tenantId column approach. Every query would be wrapped to filter by the current user's tenantId. This would require adding a tenantId to the JWT payload and updating every Prisma query to include where: { tenantId }. Prisma middleware could intercept all queries and add this automatically."

---

## SECTION 3: Behavioral Questions (With Answers Tied to Your Experience)

**Q32: Tell me about yourself.**

> **Script:** "I'm Yuvraj, a second-year CS student at University of Delhi. I got into software by building tools I wanted to exist — starting with small scripts, moving to full-stack projects. My main project right now is VIPASA, an ERP system for consultancies, where I designed the database schema, built the REST API from scratch, and implemented authentication and document management. I've also contributed to open source — got a PR merged into NeutralinoJS — and I'm interested in backend systems, specifically API design and data modeling."

**Q33: Describe a technical challenge you faced and how you solved it.**

> **Script (use your Prisma typing challenge):** "When I first set up Prisma with the pg adapter, I ran into an issue where the TypeScript types for nested relations weren't being inferred correctly, which caused compile errors when I tried to use include with select. The root issue was that @prisma/adapter-pg requires a specific import order — dotenv/config needs to load before anything else, and the client needs to be instantiated exactly once as a singleton. I spent a few hours debugging by reading the Prisma source and eventually resolved it by restructuring the lib/prisma.ts file to use a module-level singleton."

**Q34: What's something you built that you're proud of?**

> **Script:** "The pagination implementation in VIPASA. It sounds simple but getting it right required thinking about multiple things: input validation with Zod to prevent negative pages or giant limits, parallel database queries with Promise.all, and returning complete pagination metadata so the client doesn't need a second request. I also had to handle the edge case where a filtered query returns 0 results — the total still comes back as 0 and totalPages as 0, rather than throwing an error."

**Q35: What's something you'd do differently if you rebuilt your project?**

> **Script:** "I'd add an integration test suite from day one. Right now I tested everything manually with Postman, which is slow and doesn't catch regressions. Every time I change a controller, I have to re-test all the endpoints by hand. With Jest and Supertest I could have automated endpoint tests that run in seconds. I'd also set up proper migration files in Prisma from the beginning instead of using prisma db push — that approach doesn't scale to a team or a production database with real data."

---

## SECTION 4: Top 20 Questions Most Likely to Expose Weaknesses

These are the questions where you're most at risk. Study these answers hardest.

1. **"Can you explain the difference between authentication and authorization?"**
   - Auth = proving who you are (login). Authorization = checking what you're allowed to do (RBAC). JWT handles auth; requireRole handles authorization.

2. **"What happens if your JWT_SECRET is not set in production?"**
   - jwt.sign would sign with `undefined` as the secret. Any token signed this way would be accepted by any server also missing the secret. This is a critical vulnerability. The fix is to validate env vars at startup and throw if they're missing.

3. **"How does bcrypt protect against rainbow table attacks?"**
   - bcrypt automatically generates and stores a random salt with every hash. Two identical passwords produce different hashes. Rainbow tables can't precompute hashes for every possible salt, so they're useless against bcrypt.

4. **"What is SQL injection and how does Prisma prevent it?"**
   - SQL injection is when user input is embedded directly in a SQL query, allowing an attacker to run arbitrary SQL. Prisma uses parameterized queries — it never interpolates user input into SQL strings. Input is always passed as a separate parameter bound at the database level.

5. **"What is the N+1 query problem?"**
   - When you fetch a list of N records and then run a query for each one (N additional queries = N+1 total). Prisma's include solves this with a JOIN — it fetches related data in one or two queries instead of N.

6. **"What does `async/await` do under the hood?"**
   - async/await is syntactic sugar over Promises and the event loop. async marks a function that returns a Promise. await pauses execution until the Promise resolves, but doesn't block the event loop — other callbacks can run while waiting. Under the hood it uses generator functions.

7. **"What is CORS and why do you need it?"**
   - Cross-Origin Resource Sharing. Browsers block JavaScript from making requests to a different domain than the page origin. CORS headers tell the browser which origins are allowed. Your server needs the `cors` middleware (which you have installed) to set these headers — without it, your frontend can't call your API from a different domain.

8. **"Your getApplications function is incomplete — there's a comment saying it needs to be removed. What is that about?"**
   - Be honest: "That's a known TODO in my code. That function was an early placeholder for staff application listing. I was refactoring it out in favor of more specific endpoints. In production code I'd remove it rather than leave commented stubs."

9. **"What would happen if two staff members created an application for the same client at the same time?"**
   - With the current code, two identical applications would be created — there's no uniqueness constraint preventing it. The code even has a comment: 'Here We will build the business logic to prevent double clicks.' The fix would be a unique constraint on (clientId, serviceId, status='Draft') or using a database-level lock.

10. **"Why do you use `(req as any).user` instead of extending the Request type in TypeScript?"**
    - Be honest: "That's a TypeScript shortcut I took. The proper way is to extend Express's Request interface in a types.d.ts file: `declare global { namespace Express { interface Request { user?: JWTPayload } } }`. Using `as any` works but loses type safety. It's something I'd fix in a production codebase."

11-20: Prepared in section 4 of the Mock Interview document (guide 03).

---

## SECTION 5: Questions to Ask the Interviewer

These show strategic thinking. Ask 2-3 at the end:

1. "What does the tech stack look like where I'd be working, and how does it compare to what I've built?"
2. "What does the first 30 days look like for someone in this role?"
3. "What's the biggest technical challenge the team is working through right now?"
4. "How do you approach code reviews — what do reviewers typically focus on?"
5. "What does growth look like for someone starting in this position?"

---

> **Remember:** The goal of project questions is to see if you actually built it or just copy-pasted it. An interviewer will ask follow-up questions that get progressively more specific. If you know your own code, you'll pass. Read through your actual codebase once more before the interview.
