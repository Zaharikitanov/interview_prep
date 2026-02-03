# Mid-Level Full-Stack Interview Questions (React / Node / NestJS / PostgreSQL)

---

## Appendix

### Quick Reference by Topic

**React**

- [Redux vs React Query](#redux-vs-react-query)
- [React Hooks - useMemo vs useCallback](#react-hooks)
- [useEffect Cleanup](#react-useeffect-cleanup)
- [Key Prop in Lists](#react-key-prop)
- [Context API vs Prop Drilling](#react-context-api)
- [Performance Optimization](#react-performance-optimization)

**Node.js & JavaScript**

- [Event Loop](#nodejs-event-loop)
- [Promises & Microtasks](#promises--timeout)
- [Closures](#javascript-closures)

**NestJS**

- [Decorator Execution Order](#nestjs-decorator-execution-order)
- [Guards & Pipes](#nestjs-guards--pipes--error-handling)
- [NestJS vs Next.js Backend](#nestjs-vs-nextjs-backend)

**Database (PostgreSQL)**

- [Indexes](#postgresql-indexes)
- [Transactions & ACID](#database-transactions)
- [N+1 Query Problem](#n1-query-problem)
- [SQL Injection Prevention](#sql-injection-prevention)
- [Normalization](#database-normalization)

**Authentication & Security**

- [Next.js Authentication](#nextjs-authentication)
- [OAuth2](#oauth2)
- [JWT vs Session Authentication](#jwt-vs-session-authentication)

**TypeScript**

- [Extending Interfaces](#typescript-extending-interfaces)

---

## NestJS Decorator Execution Order

**Q:** In what order are decorators executed in NestJS controllers?

**A:**  
For an incoming HTTP request, the execution order is:

1. **Middleware**
2. **Guards**
3. **Interceptors (before handler)**
4. **Pipes**
5. **Controller method (handler)**
6. **Interceptors (after handler)**
7. **Exception Filters**
8. **Response sent to client**

**One-line summary:**  
Middleware → Guards → Interceptors → Pipes → Controller → Interceptors → Exception Filters

**Notes:**

- Guards run **before** pipes
- Pipes run **right before** the controller method
- Interceptors wrap the execution (before & after)
- Exception filters catch errors thrown anywhere below them

[↑ Back to Appendix](#appendix)

---

## 2. Redux vs React Query

**Q:** When would you use Redux and when React Query?

**A:**

- Redux → client state
  - UI state
  - auth state
  - forms, filters
- React Query → server state
  - data fetching
  - caching
  - background refetch
  - retries
- Using both together is common

**Follow-ups:**

- Where would you store logged-in user info?
- Would you store API data in Redux? Why / why not?

**Red flags:**

- “React Query replaces Redux”
- Using Redux for everything

---

## 3. Node.js Event Loop

**Q:** Explain the Node.js event loop in simple terms.

**A:**

- Node.js is single-threaded
- Uses non-blocking I/O
- Async operations are delegated to the OS
- Event loop processes queues when the call stack is free

**Bonus:**

- Microtasks → Promises
- Macrotasks → setTimeout

**Red flags:**

- “Node.js is multi-threaded”
- Cannot explain it in simple words

---

## Promises / Timeout

**Q:** What problem do promises solve?

**A:**

- Handle asynchronous code
- Avoid callback hell
- async/await is syntactic sugar over promises
- Errors handled via try/catch

**Follow-up:**  
**Q:** Which runs first: setTimeout or Promise.then?

**A:**

- Promise.then (microtask)

[↑ Back to Appendix](#appendix)

---

## Next.js vs NestJS + Authentication

### Next.js Authentication

**Q:** Did you implement authentication in Next.js? How?

**A:**

- NextAuth / Auth.js
- OAuth2 providers (Google, GitHub, etc.)
- JWT-based or session-based authentication

[↑ Back to Appendix](#appendix)

---

### OAuth2

**Q:** What is OAuth2?

**A:**

- Authorization framework
- Uses access tokens
- Involves an authorization server
- OAuth2 ≠ authentication

**Red flags:**

- "OAuth is login"

[↑ Back to Appendix](#appendix)

---

### NestJS Guards / Pipes / Error handling

**Q:** What are Guards and Pipes in NestJS?

**A:**

- Guards
  - Authorization / authentication
  - Executed before controller
  - JWT validation, roles
- Pipes
  - Validation
  - Transformation
  - DTO validation

**Q:** How does error handling work in NestJS?

**A:**

- HttpException
- Global exception filters
- Centralized error handling

[↑ Back to Appendix](#appendix)

---

### NestJS vs Next.js backend

**Q:** Why choose NestJS over Next.js API routes?

**A:**

- Structured architecture
- Built-in dependency injection
- Better scalability
- More suitable for large backends

[↑ Back to Appendix](#appendix)

---

## TypeScript

### TypeScript Extending Interfaces

**Q:** How do you extend interfaces in TypeScript?

**A:**

```ts
interface User {
  id: number;
}

interface Admin extends User {
  role: string;
}
```

[↑ Back to Appendix](#appendix)

---

## React Hooks

**Q:** What's the difference between useMemo and useCallback?

**A:**

- `useMemo` → memoizes a **value**
- `useCallback` → memoizes a **function**
- Both prevent unnecessary re-computations
- Use when passing props to child components

**Example:**

```tsx
const expensiveValue = useMemo(() => computeValue(data), [data]);
const handleClick = useCallback(() => doSomething(id), [id]);
```

**Red flags:**

- Using them everywhere unnecessarily
- Not understanding dependencies array

[↑ Back to Appendix](#appendix)

---

## React useEffect Cleanup

**Q:** Why do we need cleanup functions in useEffect?

**A:**

- Prevent memory leaks
- Cancel subscriptions, timers, event listeners
- Runs when component unmounts or before re-running effect

**Example:**

```tsx
useEffect(() => {
  const timer = setTimeout(() => {}, 1000);
  return () => clearTimeout(timer); // cleanup
}, []);
```

**Red flags:**

- Not cleaning up event listeners
- Not canceling API requests

[↑ Back to Appendix](#appendix)

---

## PostgreSQL Indexes

**Q:** What are database indexes and when should you use them?

**A:**

- Speed up queries by creating a lookup structure
- Trade-off: faster reads, slower writes
- Use on columns in WHERE, JOIN, ORDER BY clauses

**Types:**

- B-tree (default)
- Hash
- GIN (for JSONB, arrays)

**Example:**

```sql
CREATE INDEX idx_user_email ON users(email);
```

**Red flags:**

- Not understanding the write trade-off
- Indexing everything

[↑ Back to Appendix](#appendix)

---

## Database Transactions

**Q:** What are database transactions and why are they important?

**A:**

- Group multiple operations into one atomic unit
- ACID properties: Atomicity, Consistency, Isolation, Durability
- All succeed or all fail (rollback)

**Example in Node.js:**

```js
await db.transaction(async (trx) => {
  await trx("accounts").where({ id: 1 }).decrement("balance", 100);
  await trx("accounts").where({ id: 2 }).increment("balance", 100);
});
```

**Red flags:**

- Not using transactions for related operations
- Can't explain ACID

[↑ Back to Appendix](#appendix)

---

## N+1 Query Problem

**Q:** What is the N+1 query problem?

**A:**

- Making N additional queries in a loop
- Common in ORMs without proper eager loading
- Solution: Use JOIN or eager loading

**Bad example:**

```js
const users = await User.findAll(); // 1 query
for (const user of users) {
  const posts = await user.getPosts(); // N queries
}
```

**Good example:**

```js
const users = await User.findAll({
  include: [Post], // 1 query with JOIN
});
```

[↑ Back to Appendix](#appendix)

---

## JWT vs Session Authentication

**Q:** What's the difference between JWT and session-based auth?

**A:**

**JWT (stateless):**

- Token stored on client
- No server-side storage
- Scales horizontally easily
- Can't revoke without blacklist

**Session (stateful):**

- Session ID stored in cookie
- Session data on server (Redis, DB)
- Easy to revoke
- Requires sticky sessions or shared storage

**Red flags:**

- "JWT is always better"
- Storing sensitive data in JWT

[↑ Back to Appendix](#appendix)

---

## SQL Injection Prevention

**Q:** How do you prevent SQL injection?

**A:**

- **Never** concatenate user input into SQL
- Use parameterized queries / prepared statements
- Use ORM query builders

**Bad:**

```js
const query = `SELECT * FROM users WHERE id = ${userId}`;
```

**Good:**

```js
const query = "SELECT * FROM users WHERE id = $1";
await db.query(query, [userId]);
```

**Red flags:**

- Not knowing what SQL injection is
- String concatenation in queries

[↑ Back to Appendix](#appendix)

---

## React Key Prop

**Q:** Why does React need the key prop in lists?

**A:**

- Helps React identify which items changed
- Optimizes re-rendering
- Should be stable and unique
- **Don't use array index** if list can reorder

**Example:**

```tsx
{
  users.map((user) => <UserCard key={user.id} user={user} />);
}
```

**Red flags:**

- Using index as key always
- Not understanding reconciliation

[↑ Back to Appendix](#appendix)

---

## JavaScript Closures

**Q:** What is a closure?

**A:**

- Function that has access to outer scope variables
- Even after outer function has returned
- Creates private variables

**Example:**

```js
function createCounter() {
  let count = 0;
  return {
    increment: () => ++count,
    getCount: () => count,
  };
}
```

**Red flags:**

- Can't explain in simple terms
- Not understanding scope chain

[↑ Back to Appendix](#appendix)

---

## React Context API

**Q:** When would you use Context API vs prop drilling?

**A:**

**Context API:**

- Avoid prop drilling through many levels
- Share global state (theme, user, language)
- Don't overuse for local state

**Trade-offs:**

- All consumers re-render when context changes
- Consider splitting contexts

**Red flags:**

- Using Context for everything
- "Context replaces Redux"

[↑ Back to Appendix](#appendix)

---

## Database Normalization

**Q:** What is database normalization?

**A:**

- Process of organizing data to reduce redundancy
- Split data into related tables
- Avoids update anomalies

**Forms:**

- 1NF: Atomic values
- 2NF: No partial dependencies
- 3NF: No transitive dependencies

**Trade-off:** More JOINs, but less redundancy

**Red flags:**

- Over-normalizing (too many JOINs)
- Not understanding when to denormalize

[↑ Back to Appendix](#appendix)

---

## React Performance Optimization

**Q:** How would you optimize a slow React component?

**A:**

1. Use React DevTools Profiler
2. Memoize with `React.memo`
3. Use `useMemo` / `useCallback` for expensive computations
4. Virtualize long lists (react-window)
5. Code splitting / lazy loading
6. Avoid inline function definitions in JSX

**Red flags:**

- Premature optimization
- Not measuring before optimizing

[↑ Back to Appendix](#appendix)
