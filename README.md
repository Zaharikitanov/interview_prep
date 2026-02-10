# Mid-Level Full-Stack Interview Questions (React / Node / NestJS / PostgreSQL)

---

## Appendix

### Quick Reference by Topic

**React**

- [Virtual DOM](#virtual-dom)
- [React Hooks - useMemo vs useCallback](#react-hooks)
- [useEffect Cleanup](#react-useeffect-cleanup)
- [Custom Hooks](#custom-hooks)
- [Redux vs React Query](#redux-vs-react-query)
- [Performance Optimization](#react-performance-optimization)

**Node.js & JavaScript**

- [Closures](#javascript-closures)
- ['this' Keyword](#javascript-this-keyword)
- [Promises & Microtasks](#promises--timeout)
- [Event Loop](#nodejs-event-loop)
- [Handling Long-Running Requests](#handling-long-running-requests)

**NestJS**

- [Modules and Providers](#nestjs-modules-and-providers)
- [Dependency Injection](#nestjs-dependency-injection)
- [Guards & Pipes](#nestjs-guards--pipes--error-handling)
- [Decorator Execution Order](#nestjs-decorator-execution-order)
- [Interceptors](#nestjs-interceptors)
- [Custom Decorators](#nestjs-custom-decorators)

**Database (PostgreSQL)**

- [SQL Injection Prevention](#sql-injection-prevention)
- [Indexes](#postgresql-indexes)
- [Normalization](#database-normalization)
- [Many-to-Many Relationships](#many-to-many-relationships)
- [Migrations](#database-migrations)
- [N+1 Query Problem](#n1-query-problem)
- [Composite Indexes](#composite-indexes)
- [Table Partitioning](#table-partitioning)
- [CAP Theorem](#cap-theorem)

**TypeScript**

- [Extending Interfaces](#typescript-extending-interfaces)
- [Union and Intersection Types](#typescript-union-and-intersection-types)
- [Generics](#typescript-generics)

**OOP & Design Principles**

- [Four Pillars of OOP](#four-pillars-of-oop)
- [SOLID Principles](#solid-principles)
- [Abstract Class vs Interface](#abstract-class-vs-interface)
- [Practical OOP & Design Situations](#practical-oop--design-situations)

**Authentication & Security**

- [JWT vs Session Authentication](#jwt-vs-session-authentication)
- [OAuth2](#oauth2)
- [Next.js Authentication](#nextjs-authentication)

**Behavioral & Soft Skills**

- [Learning from Mistakes](#learning-from-mistakes)
- [Receiving Critical Feedback](#receiving-critical-feedback)
- [Disagreement with Team Member](#disagreement-with-team-member)
- [Technical Decision Making](#technical-decision-making)
- [Mentoring Others](#mentoring-others)

---

## NestJS Decorator Execution Order

**Q:** In what order are decorators executed in NestJS controllers?

**A:**  
For an incoming HTTP request, the execution order is:

1. **Middleware** - logging, CORS, body parsing
2. **Guards** - authentication, role-based authorization
3. **Interceptors (before handler)** - transform request, add timing
4. **Pipes** - validate/transform DTOs, parse params
5. **Controller method (handler)** - business logic execution
6. **Interceptors (after handler)** - transform response, add metadata
7. **Exception Filters** - catch errors, format error responses
8. **Response sent to client** - final HTTP response

**One-line summary:**  
Middleware â†’ Guards â†’ Interceptors â†’ Pipes â†’ Controller â†’ Interceptors â†’ Exception Filters

**Notes:**

- Guards run **before** pipes
- Pipes run **right before** the controller method
- Interceptors wrap the execution (before & after)
- Exception filters catch errors thrown anywhere below them

[â†‘ Back to Appendix](#appendix)

---

## NestJS Interceptors

**Q:** What are Interceptors in NestJS and when would you use them?

**A:**

**Definition:** Classes with @Injectable() that implement NestInterceptor interface. Execute logic before and after route handler execution.

**Execution:**

- Run before the handler (transform request)
- Run after the handler (transform response)
- Wrap handler execution using RxJS operators

**Common use cases:**

1. **Response Transformation**
   - Wrap responses in standard format
   - Add metadata (timestamps, request IDs)
   - Transform data structure

2. **Logging & Monitoring**
   - Log request/response data
   - Track execution time
   - Performance monitoring

3. **Caching**
   - Cache responses
   - Check cache before handler execution
   - Return cached data when available

4. **Error Handling**
   - Catch and transform exceptions
   - Add context to errors
   - Retry logic

5. **Timeout Management**
   - Set request timeouts
   - Cancel long-running requests

**Key methods:**

- `intercept(context, next)` - main method
- Uses RxJS operators: map, tap, catchError, timeout

**Scope:**

- Global (app.useGlobalInterceptors)
- Controller level (@UseInterceptors)
- Route level (@UseInterceptors on method)

**Difference from Middleware:**

- Interceptors have access to ExecutionContext
- Can access route handler and class
- Execute after Guards and Pipes
- Can transform responses (middleware cannot)

**Red flags:**

- Using interceptors for authentication (use Guards)
- Heavy business logic in interceptors
- Not understanding execution order
- Blocking operations without proper async handling

[â†‘ Back to Appendix](#appendix)

---

## NestJS Custom Decorators

**Q:** How do you create and use custom decorators in NestJS?

**A:**

**Types of custom decorators:**

1. **Parameter Decorators**
   - Extract data from request
   - Access user, headers, query params
   - Combine with pipes for transformation/validation

2. **Method Decorators**
   - Add metadata to routes
   - Combine multiple decorators
   - Create authorization decorators

3. **Class Decorators**
   - Apply metadata to entire controller
   - Set common configuration

**Common use cases:**

1. **User Extraction**
   - @CurrentUser() - get authenticated user
   - @UserId() - extract user ID
   - Clean controller methods

2. **Authorization**
   - @Roles('admin', 'user') - role-based access
   - @Public() - skip authentication
   - @Permissions() - permission checks

3. **Request Data**
   - @IpAddress() - get client IP
   - @UserAgent() - get user agent
   - @Headers() - specific headers

4. **Validation & Transformation**
   - Combine with pipes
   - Parse and validate custom data

**Benefits:**

- Cleaner, more readable controllers
- Reusable logic across routes
- Type safety with TypeScript
- Separation of concerns
- Easier testing

**Composition:**

- Use applyDecorators() to combine multiple decorators
- Create complex decorators from simple ones
- Maintain consistency across application

**Red flags:**

- Overusing decorators for simple logic
- Not understanding decorator execution context
- Creating decorators with side effects
- Poor naming conventions
- Not leveraging built-in decorators when available

---

## Redux vs React Query

**Q:** When would you use Redux and when React Query?

**A:**

- Redux â†’ client state
  - UI state
  - auth state
  - forms, filters
- React Query â†’ server state
  - data fetching
  - caching
  - background refetch
  - retries
- Using both together is common

**Follow-ups:**

- Where would you store logged-in user info?
- Would you store API data in Redux? Why / why not?

**Red flags:**

- â€œReact Query replaces Reduxâ€
- Using Redux for everything

---

## Node.js Event Loop

**Q:** Explain the Node.js event loop in simple terms.

**A:**

**Core Concept:**

- Node.js runs on a single thread (main thread)
- Uses non-blocking I/O for concurrent operations
- Event loop coordinates execution of callbacks
- Async operations (file I/O, network) are handled by libuv (C++ library)
- When async operations complete, their callbacks are queued
- Event loop processes these queues when call stack is empty

**How it works:**

1. Execute synchronous code (call stack)
2. When call stack is empty, check queues
3. Execute callbacks from queues in specific order
4. Repeat (loop)

**Event Loop Phases (in order):**

1. **Timers Phase**
   - Executes callbacks from `setTimeout()` and `setInterval()`
   - Only runs callbacks whose timer has expired

2. **Pending Callbacks Phase**
   - Executes I/O callbacks deferred from previous iteration
   - System operations (TCP errors, etc.)

3. **Idle, Prepare Phase**
   - Internal use only

4. **Poll Phase** (most important)
   - Retrieves new I/O events
   - Executes I/O callbacks (except timers, setImmediate, close callbacks)
   - Will block here if no other work pending

5. **Check Phase**
   - Executes `setImmediate()` callbacks
   - Runs after poll phase completes

6. **Close Callbacks Phase**
   - Executes close event callbacks (`socket.on('close')`)

**Microtasks vs Macrotasks:**

**Microtasks (higher priority):**

- `Promise.then()`, `Promise.catch()`, `Promise.finally()`
- `process.nextTick()` (highest priority, runs before other microtasks)
- `queueMicrotask()`
- Execute after current operation, **before** moving to next phase
- Processed completely between each phase

**Macrotasks (lower priority):**

- `setTimeout()`, `setInterval()` (timers phase)
- `setImmediate()` (check phase)
- I/O operations (poll phase)
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

**setImmediate vs setTimeout(0):**

```javascript
setTimeout(() => console.log("setTimeout"), 0);
setImmediate(() => console.log("setImmediate"));

// Order is non-deterministic (depends on system performance)
// Inside I/O callback, setImmediate ALWAYS runs first:

fs.readFile("file.txt", () => {
  setTimeout(() => console.log("setTimeout"), 0);
  setImmediate(() => console.log("setImmediate"));
  // Output: setImmediate, setTimeout (guaranteed order)
});
```

**Common Patterns:**

**1. process.nextTick() for immediate execution:**

```javascript
// Runs before any I/O or timers
process.nextTick(() => {
  console.log("Runs first");
});
```

**2. setImmediate() for deferring work:**

```javascript
// Useful for breaking up CPU-intensive work
function processItems(items) {
  items.forEach((item, i) => {
    // Process item
    if (i % 100 === 0) {
      setImmediate(() => {}); // Give event loop a chance to process I/O
    }
  });
}
```

**3. Avoiding blocking:**

```javascript
// BAD - blocks event loop
const data = fs.readFileSync("large-file.txt");

// GOOD - non-blocking
fs.readFile("large-file.txt", (err, data) => {
  // Callback runs when ready
});
```

**Key Principles:**

- **Single-threaded** â†’ Only one call stack
- **Non-blocking** â†’ Async operations don't block the thread
- **Concurrent** â†’ Multiple operations in progress (not parallel)
- **Libuv thread pool** â†’ Handles file I/O, DNS, crypto (default 4 threads)

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

[↑ Back to Appendix](#appendix)

---

## Handling Long-Running Requests

**Q:** How would you handle a request that takes a long time to process?

**A:**

**Strategies:**

1. **Identify the Bottleneck First**
   - Profile the request (measure execution time)
   - Check for multiple consecutive async requests (waterfall pattern)
   - Analyze database query performance (slow query logs)
   - Monitor API response times

2. **Optimize Multiple Async Requests**
   - Run independent requests in parallel (Promise.all)
   - Avoid sequential await when possible
   - Batch multiple requests into single calls
   - Use GraphQL DataLoader to prevent N+1 queries
   - Example: fetch user + posts + comments concurrently, not sequentially

3. **Database Operation Improvements**
   - Identify slow queries (EXPLAIN/ANALYZE)
   - Add missing indexes on filtered/joined columns
   - Optimize query structure (avoid SELECT \*, use specific columns)
   - Fix N+1 query problems (eager loading, JOINs)
   - Use connection pooling
   - Consider read replicas for heavy read operations
   - Implement query result caching
   - Use pagination instead of loading all records

4. **Caching**
   - Cache expensive computations (Redis, Memcached)
   - Use stale-while-revalidate pattern
   - Reduce redundant processing
   - Best for: frequently accessed, slow-changing data

**Key Considerations:**

- User experience (progress indicators, estimated time)
- Idempotency (can retry safely)
- Error handling and recovery
- Resource limits (prevent resource exhaustion)
- Monitoring and alerting

**Red flags:**

- Blocking the event loop with synchronous operations
- No timeout handling
- Not considering user experience
- Ignoring memory/resource limits
- No retry or error recovery strategy

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

[â†‘ Back to Appendix](#appendix)

---

## Next.js vs NestJS + Authentication

### Next.js Authentication

**Q:** Did you implement authentication in Next.js? How?

**A:**

- NextAuth / Auth.js
- OAuth2 providers (Google, GitHub, etc.)
- JWT-based or session-based authentication

[â†‘ Back to Appendix](#appendix)

---

### OAuth2

**Q:** What is OAuth2?

**A:**

- Authorization framework
- Uses access tokens
- Involves an authorization server
- OAuth2 â‰  authentication

**Red flags:**

- "OAuth is login"

[â†‘ Back to Appendix](#appendix)

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

[â†‘ Back to Appendix](#appendix)

---

## TypeScript

### TypeScript Extending Interfaces

**Q:** How do you extend interfaces in TypeScript?

**A:**

Use the `extends` keyword to inherit properties from a parent interface.

[â†‘ Back to Appendix](#appendix)

---

## React Hooks

**Q:** What's the difference between useMemo and useCallback?

**A:**

- `useMemo` â†’ memoizes a **value**
- `useCallback` â†’ memoizes a **function**
- Both prevent unnecessary re-computations
- Use when passing props to child components

**Red flags:**

- Using them everywhere unnecessarily
- Not understanding dependencies array

[â†‘ Back to Appendix](#appendix)

---

## React useEffect Cleanup

**Q:** Why do we need cleanup functions in useEffect?

**A:**

- Prevent memory leaks
- Cancel subscriptions, timers, event listeners
- Runs when component unmounts or before re-running effect

**Red flags:**

- Not cleaning up event listeners
- Not canceling API requests

[â†‘ Back to Appendix](#appendix)

---

## Custom Hooks

**Q:** What are custom hooks and when would you create one?

**A:**

**Definition:** Functions that start with "use" and can call other hooks. Extract reusable stateful logic.

**When to create:**

- Reusing stateful logic across multiple components
- Sharing complex logic without prop drilling
- Abstracting component logic for better readability
- Encapsulating side effects and state management

**Common use cases:**

1. **Data Fetching**
   - useApi, useFetch, useQuery
   - Handle loading, error, and data states
   - Automatic cleanup and cancellation

2. **Form Handling**
   - useForm, useInput, useValidation
   - Manage form state, validation, submission
   - Reusable across multiple forms

3. **Browser APIs**
   - useLocalStorage, useSessionStorage
   - useWindowSize, useMediaQuery
   - useGeolocation, useOnlineStatus

4. **Event Listeners**
   - useEventListener, useClickOutside
   - useKeyPress, useDebounce
   - Automatic cleanup on unmount

5. **Authentication/Authorization**
   - useAuth, usePermissions
   - Centralize auth logic
   - Share user state across components

**Rules:**

- Must start with "use" prefix
- Can call other hooks inside
- Follow all hooks rules (top level, React functions only)
- Return values/functions that components need

**Benefits:**

- DRY (Don't Repeat Yourself)
- Easier testing (test hooks independently)
- Better code organization
- Share logic without render props or HOCs

**Red flags:**

- Creating hooks for non-reusable logic
- Not following hooks naming convention
- Overcomplicating simple logic
- Breaking hooks rules inside custom hooks
- Creating hooks that are too tightly coupled to specific components

[â†‘ Back to Appendix](#appendix)

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

**Red flags:**

- Not understanding the write trade-off
- Indexing everything

[â†‘ Back to Appendix](#appendix)

---

## N+1 Query Problem

**Q:** What is the N+1 query problem?

**A:**

- Making N additional queries in a loop
- Common in ORMs without proper eager loading
- Solution: Use JOIN or eager loading

[â†‘ Back to Appendix](#appendix)

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

[â†‘ Back to Appendix](#appendix)

---

## SQL Injection Prevention

**Q:** How do you prevent SQL injection?

**A:**

- **Never** concatenate user input into SQL
- Use parameterized queries / prepared statements
- Use ORM query builders

**Red flags:**

- Not knowing what SQL injection is
- String concatenation in queries

[â†‘ Back to Appendix](#appendix)

---

## JavaScript Closures

**Q:** What is a closure?

**A:**

- Function that has access to outer scope variables
- Even after outer function has returned
- Creates private variables

**Red flags:**

- Can't explain in simple terms
- Not understanding scope chain

[â†‘ Back to Appendix](#appendix)

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

[â†‘ Back to Appendix](#appendix)

---

## Many-to-Many Relationships

**Q:** How do you resolve many-to-many relationships in a database? Is it a good practice?

**A:**

**Solution:** Use a bridge table (junction table) with foreign keys to both tables.

**Yes, it's good practice:**

- Maintains data integrity
- Avoids data duplication
- Follows normalization principles
- Enables efficient querying

**Additional data in bridge tables:**

- Can store relationship-specific data (enrollment date, grades, status)
- Composite primary key prevents duplicates

**Red flags:**

- Storing comma-separated IDs instead of proper relationships
- Not using foreign key constraints
- Duplicating data across tables

[â†‘ Back to Appendix](#appendix)

---

## Table Partitioning

**Q:** What is table partitioning and when should you use it?

**A:**

**Definition:** Splitting a large table into smaller, more manageable pieces (partitions) while appearing as a single table.

**When to use:**

- Very large tables (millions/billions of rows)
- Time-series data (logs, events, metrics)
- Queries typically filter by partition key
- Need to archive/delete old data efficiently
- Improve query performance on specific ranges

**Types:**

- **Range partitioning** â†’ by date ranges, numeric ranges
- **List partitioning** â†’ by specific values (country, status)
- **Hash partitioning** â†’ distribute evenly across partitions

**Benefits:**

- **Query performance** â†’ scan only relevant partitions (partition pruning)
- **Maintenance** â†’ easier to archive/drop old partitions
- **Bulk operations** â†’ faster on specific partitions
- **Index size** â†’ smaller indexes per partition

**Trade-offs:**

- Added complexity
- Need to manage partition creation
- Cross-partition queries can be slower
- More planning required

**Red flags:**

- Partitioning small tables unnecessarily
- Wrong partition key choice
- Not automating partition creation
- Ignoring maintenance overhead

[â†‘ Back to Appendix](#appendix)

---

## CAP Theorem

**Q:** What is the CAP theorem?

**A:**

**Definition:** In a distributed system, you can only guarantee 2 out of 3 properties:

1. **Consistency (C)** - All nodes see the same data at the same time
2. **Availability (A)** - Every request gets a response (success or failure)
3. **Partition Tolerance (P)** - System continues despite network partitions

**Key insight:** Network partitions will happen, so you must choose between Consistency or Availability.

**Trade-offs:**

**CP (Consistency + Partition Tolerance):**

- Sacrifice availability during partitions
- Wait for all nodes to sync before responding
- Examples: MongoDB (in certain configs), HBase, Redis Cluster
- Use when: Data accuracy is critical (financial transactions)

**AP (Availability + Partition Tolerance):**

- Sacrifice consistency during partitions
- Always respond, even with stale data
- Examples: Cassandra, DynamoDB, CouchDB
- Use when: Uptime is critical, eventual consistency acceptable

**CA (Consistency + Availability):**

- Not realistic in distributed systems
- Only possible without network partitions
- Single-node databases (traditional RDBMS)

**Eventual Consistency:**

- Common in AP systems
- Data will become consistent over time
- Acceptable delay between updates propagating

**Real-world examples:**

- **Banking** â†’ CP (can't show wrong balance)
- **Social media feeds** â†’ AP (okay to show slightly old posts)
- **Shopping cart** â†’ AP (better to let user add items than show error)
- **Inventory systems** â†’ CP (prevent overselling)

**Red flags:**

- "You can have all three"
- Not understanding network partitions are inevitable
- Choosing wrong trade-off for use case
- Not knowing eventual consistency

[â†‘ Back to Appendix](#appendix)

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

[â†‘ Back to Appendix](#appendix)

---

## Virtual DOM

**Q:** What is the Virtual DOM and why does React use it?

**A:**

- Lightweight JavaScript representation of actual DOM
- React compares virtual DOM snapshots (diffing)
- Batches updates to real DOM for efficiency
- Minimizes expensive DOM operations

**Process:**

1. State changes â†’ new Virtual DOM created
2. Diff with previous Virtual DOM
3. Calculate minimal changes needed
4. Update only changed parts in real DOM

**Red flags:**

- "Virtual DOM is always faster" (not always true)
- Not understanding reconciliation

[â†‘ Back to Appendix](#appendix)

---

## JavaScript 'this' Keyword

**Q:** How does the 'this' keyword work in JavaScript?

**A:**

**Context-dependent:**

- **Method call**: `obj.method()` â†’ this = obj
- **Function call**: `func()` â†’ this = undefined (strict) / window
- **Constructor**: `new Func()` â†’ this = new object
- **Arrow functions**: inherit this from enclosing scope
- **Explicit binding**: call(), apply(), bind()

**Red flags:**

- Not understanding arrow functions don't bind this
- Losing context when passing methods as callbacks

[â†‘ Back to Appendix](#appendix)

---

## NestJS Modules and Providers

**Q:** What are Modules and Providers in NestJS?

**A:**

**Modules:**

- Organize application structure
- Encapsulate related components
- @Module() decorator with imports/exports

**Providers:**

- Injectable dependencies (services, repositories)
- @Injectable() decorator
- Managed by NestJS dependency injection

**Red flags:**

- Not understanding dependency injection
- Circular dependencies between modules

[â†‘ Back to Appendix](#appendix)

---

## NestJS Dependency Injection

**Q:** How does dependency injection work in NestJS?

**A:**

- Constructor-based injection
- NestJS IoC container manages instances
- Default scope is Singleton
- Promotes loose coupling and testability

**Scopes:**

- **Singleton** (default) - one instance
- **Request** - new instance per request
- **Transient** - new instance each time

**Red flags:**

- Not understanding injection scopes
- Creating instances with 'new' instead of injection

[â†‘ Back to Appendix](#appendix)

---

## Database Migrations

**Q:** What are database migrations and why use them?

**A:**

- Version control for database schema
- Track and apply schema changes
- Rollback capabilities
- Consistency across environments

**Tools:** Knex, TypeORM, Prisma Migrate, Flyway

**Red flags:**

- Manual schema changes in production
- Not testing migrations on staging
- Missing rollback migrations

[â†‘ Back to Appendix](#appendix)

---

## Composite Indexes

**Q:** What are composite indexes and when should you use them?

**A:**

- Index on multiple columns
- Order of columns matters
- Useful for queries filtering/sorting on multiple columns
- Leftmost prefix rule applies

**Red flags:**

- Wrong column order
- Too many columns (diminishing returns)
- Not understanding leftmost prefix rule

[â†‘ Back to Appendix](#appendix)

---

## TypeScript Generics

**Q:** What are generics in TypeScript and why use them?

**A:**

- Create reusable components that work with multiple types
- Type safety without losing type information
- Commonly used in arrays, promises, functions

**Red flags:**

- Using 'any' when generics would work
- Overly complex generic constraints

[â†‘ Back to Appendix](#appendix)

---

## TypeScript Union and Intersection Types

**Q:** What's the difference between union and intersection types?

**A:**

**Union (|):** Value can be one of several types

**Intersection (&):** Combines multiple types

**Use cases:**

- Union: flexible inputs, discriminated unions
- Intersection: compose types, extend interfaces

**Red flags:**

- Confusing union with intersection
- Creating impossible intersections

[â†‘ Back to Appendix](#appendix)

---

## Behavioral & Soft Skills

### Disagreement with Team Member

**Q:** Tell me about a time you had a disagreement with a team member or manager. How was it resolved?

**Framework:** Use STAR (Situation, Task, Action, Result)

**Key Points to Cover:**

- Describe the disagreement objectively
- Focus on the technical/logical reasoning, not emotions
- Show you listened to the other perspective
- Explain how you reached a resolution
- Highlight positive outcome or learning

**Red flags:**

- Blaming others or being defensive
- Not acknowledging other viewpoints
- Unresolved conflict or bad outcome
- Personal attacks or emotional responses

[â†‘ Back to Appendix](#appendix)

---

### Learning from Mistakes

**Q:** Tell me about a time you made a mistake. What did you learn?

**Key Points to Cover:**

- Own the mistake honestly
- Explain the context (not as an excuse)
- Describe immediate actions to fix it
- What processes you changed to prevent it
- Show growth and maturity

**What interviewers want to see:**

- Accountability (not blaming others/tools)
- Problem-solving under pressure
- Process improvement mindset
- Emotional maturity

**Red flags:**

- No real mistake (humblebrag)
- Blaming others
- No learning or change in behavior
- Repeating same type of mistakes

[â†‘ Back to Appendix](#appendix)

---

### Receiving Critical Feedback

**Q:** Tell me about a time you received critical feedback. How did you respond?

**Key Points to Cover:**

- Show you're receptive to feedback
- Didn't take it personally
- Asked clarifying questions
- Made concrete changes
- Followed up to show improvement

**Good approaches:**

- Thank the person for feedback
- Ask questions to understand better
- Take time to process before responding
- Create action plan for improvement
- Follow up to show progress

**Red flags:**

- Getting defensive or argumentative
- Ignoring the feedback
- No concrete examples or changes
- Making excuses

[â†‘ Back to Appendix](#appendix)

---

### Technical Decision Making

**Q:** Describe a time you had to make a difficult technical decision. How did you approach it?

**Key Points to Cover:**

- Research and data gathering process
- Stakeholders consulted
- Trade-offs considered
- How you communicated the decision
- Outcome and reflection

**What to emphasize:**

- Systematic approach
- Data-driven reasoning
- Considering team/business context
- Documentation of decisions
- Willingness to revisit decisions

**Red flags:**

- Following trends without analysis
- Not considering team capabilities
- Ignoring business constraints
- Unable to explain reasoning

[â†‘ Back to Appendix](#appendix)

---

### Mentoring Others

**Q:** Tell me about a time you helped a junior developer or colleague learn something new.

**Key Points to Cover:**

- How you identified the learning need
- Your teaching approach
- How you ensured understanding
- Their progress and success
- Your own learning from teaching

**Good practices:**

- Patient and encouraging
- Breaking down complex topics
- Checking for understanding (not just nodding)
- Giving them space to struggle/learn
- Celebrating their progress

**Red flags:**

- Impatient or condescending
- Just giving answers without explaining
- Not checking for understanding
- Taking over instead of guiding

[â†‘ Back to Appendix](#appendix)

---

## Four Pillars of OOP

**Q:** What are the four pillars of object-oriented programming?

**A:**

1. **Encapsulation**
   - Bundle data + behavior, hide internal state
   - Access through public methods; enforce invariants

2. **Abstraction**
   - Expose only essential details
   - Hide complexity behind a simple interface

3. **Inheritance**
   - Reuse behavior by extending a base class
   - Models an â€œis-aâ€ relationship

4. **Polymorphism**
   - Same interface, different implementations
   - Subtypes can be used interchangeably

**Common follow-ups:**

- When to prefer composition over inheritance
- Examples of polymorphism in TypeScript/JavaScript

**Red flags:**

- â€œInheritance is always betterâ€
- Confusing abstraction with encapsulation

[â†‘ Back to Appendix](#appendix)

---

## SOLID Principles

**Q:** What does SOLID stand for?

**A:**

1. **S â€” Single Responsibility Principle**
   - A class/module should have one reason to change

2. **O â€” Open/Closed Principle**
   - Open for extension, closed for modification

3. **L â€” Liskov Substitution Principle**
   - Subtypes must be substitutable for their base types

4. **I â€” Interface Segregation Principle**
   - Prefer small, specific interfaces over â€œfatâ€ ones

5. **D â€” Dependency Inversion Principle**
   - Depend on abstractions, not concrete implementations

**Example cues:**

- Use interfaces for external services (DIP)
- Split large services into focused ones (SRP)

**Red flags:**

- â€œSOLID means use inheritance everywhereâ€
- Changing base types breaks derived behavior (LSP violation)

[â†‘ Back to Appendix](#appendix)

---

## Abstract Class vs Interface

**Q:** When would you use an abstract class vs an interface?

**A:**

**Abstract class**

- Can provide shared implementation and state
- Allows non-abstract methods
- Enforces a common base with partial behavior

**Interface**

- Defines a contract only
- Multiple interfaces can be implemented
- Better for decoupling and testing

**Rule of thumb:**

- Use an **interface** for capabilities/roles
- Use an **abstract class** when you need shared base behavior

**Common follow-ups:**

- Can a class implement multiple interfaces? (Yes)
- Can a class extend multiple classes? (No)

**Red flags:**

- Using abstract classes just to â€œshare typesâ€
- Overusing inheritance for code reuse

[â†‘ Back to Appendix](#appendix)

---

## Practical OOP & Design Situations

**Q:** What real-world situations show up where OOP/design principles matter?

**A:**

1. **Adding a new payment provider**
   - Use an interface for `PaymentGateway`
   - Add a new implementation without changing existing flows

2. **Introducing a new notification channel (email/SMS/push)**
   - Use polymorphism to avoid `if/else` chains
   - Extend with new channel implementations

3. **Refactoring a â€œGodâ€ service**
   - Split by responsibility (SRP)
   - Improve cohesion and testability

4. **Swapping a data source (REST â†’ GraphQL)**
   - Depend on abstractions (DIP)
   - Keep callers unchanged

5. **Shared logic across similar entities**
   - Use composition (shared helpers) before inheritance
   - Avoid deep class hierarchies

6. **Feature flagging behavior**
   - Prefer strategy pattern (polymorphism)
   - Replace conditionals with interchangeable strategies

7. **Large interface with unused methods**
   - Split into smaller interfaces (ISP)
   - Reduce forced implementations

8. **Breaking API changes from base class**
   - Validate substitutability (LSP)
   - Avoid narrowing method contracts

**Red flags:**

- Changes requiring edits across many unrelated classes
- Fragile inheritance trees
- Widespread conditional logic on type

[â†‘ Back to Appendix](#appendix)
