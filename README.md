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
- [useReducer vs useState](#react-usereducer-vs-usestate)
- [Error Boundaries](#react-error-boundaries)
- [Controlled vs Uncontrolled Components](#controlled-vs-uncontrolled-components)
- [Virtual DOM](#virtual-dom)

**Node.js & JavaScript**

- [Event Loop](#nodejs-event-loop)
- [Promises & Microtasks](#promises--timeout)
- [Closures](#javascript-closures)
- [Event Loop Phases](#javascript-event-loop-phases)
- [Prototypes](#javascript-prototypes)
- ['this' Keyword](#javascript-this-keyword)
- [Streams](#nodejs-streams)

**NestJS**

- [Decorator Execution Order](#nestjs-decorator-execution-order)
- [Guards & Pipes](#nestjs-guards--pipes--error-handling)
- [NestJS vs Next.js Backend](#nestjs-vs-nextjs-backend)
- [Modules and Providers](#nestjs-modules-and-providers)
- [Dependency Injection](#nestjs-dependency-injection)

**Database (PostgreSQL)**

- [Indexes](#postgresql-indexes)
- [Transactions & ACID](#database-transactions)
- [N+1 Query Problem](#n1-query-problem)
- [SQL Injection Prevention](#sql-injection-prevention)
- [Normalization](#database-normalization)
- [Connection Pooling](#database-connection-pooling)
- [Migrations](#database-migrations)
- [Composite Indexes](#composite-indexes)

**Authentication & Security**

- [Next.js Authentication](#nextjs-authentication)
- [OAuth2](#oauth2)
- [JWT vs Session Authentication](#jwt-vs-session-authentication)
- [Refresh Tokens](#refresh-tokens)
- [Password Hashing](#password-hashing)

**TypeScript**

- [Extending Interfaces](#typescript-extending-interfaces)
- [Generics](#typescript-generics)
- [Union and Intersection Types](#typescript-union-and-intersection-types)
- [Type Guards](#typescript-type-guards)

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

## Redux vs React Query

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

## Node.js Event Loop

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

---

## React useReducer vs useState

**Q:** When would you use useReducer instead of useState?

**A:**

**useReducer:**

- Complex state logic with multiple sub-values
- Next state depends on previous state
- State transitions need to be predictable
- Better for testing (pure reducer functions)

**useState:**

- Simple state (primitives, single values)
- Independent state updates
- Less boilerplate

**Example:**

```tsx
const [state, dispatch] = useReducer(reducer, initialState);

// vs

const [count, setCount] = useState(0);
```

**Red flags:**

- Using useReducer for simple boolean toggles
- Not understanding reducer pattern

[↑ Back to Appendix](#appendix)

---

## React Error Boundaries

**Q:** What are Error Boundaries and how do they work?

**A:**

- Catch JavaScript errors in component tree
- Display fallback UI instead of crashing
- Only work in class components (for now)
- Don't catch errors in event handlers or async code

**Example:**

```tsx
class ErrorBoundary extends React.Component {
  componentDidCatch(error, errorInfo) {
    // Log error
  }

  render() {
    if (this.state.hasError) {
      return <h1>Something went wrong.</h1>;
    }
    return this.props.children;
  }
}
```

**Red flags:**

- Expecting them to catch all errors
- Not having any error boundaries in production

[↑ Back to Appendix](#appendix)

---

## Controlled vs Uncontrolled Components

**Q:** What's the difference between controlled and uncontrolled components?

**A:**

**Controlled:**

- React state is the "single source of truth"
- Value controlled via props and onChange
- More control, validation, conditional logic

**Uncontrolled:**

- DOM is the source of truth
- Access via refs
- Less code, closer to traditional HTML

**Example:**

```tsx
// Controlled
<input value={value} onChange={e => setValue(e.target.value)} />

// Uncontrolled
<input ref={inputRef} defaultValue="initial" />
```

**Red flags:**

- Mixing controlled/uncontrolled patterns
- Not understanding when to use each

[↑ Back to Appendix](#appendix)

---

## Virtual DOM

**Q:** What is the Virtual DOM and why does React use it?

**A:**

- Lightweight JavaScript representation of actual DOM
- React compares virtual DOM snapshots (diffing)
- Batches updates to real DOM for efficiency
- Minimizes expensive DOM operations

**Process:**

1. State changes → new Virtual DOM created
2. Diff with previous Virtual DOM
3. Calculate minimal changes needed
4. Update only changed parts in real DOM

**Red flags:**

- "Virtual DOM is always faster" (not always true)
- Not understanding reconciliation

[↑ Back to Appendix](#appendix)

---

## JavaScript Event Loop Phases

**Q:** What are the different phases of the Node.js event loop?

**A:**

**Phases (in order):**

1. **Timers** - setTimeout, setInterval callbacks
2. **Pending callbacks** - I/O callbacks deferred
3. **Idle, prepare** - internal use
4. **Poll** - retrieve new I/O events
5. **Check** - setImmediate callbacks
6. **Close callbacks** - socket.on('close')

**Plus:**

- **process.nextTick()** - executes before any phase
- **Promises (microtasks)** - execute after current operation

**Red flags:**

- Not understanding execution order
- Confusing setTimeout(fn, 0) with setImmediate

[↑ Back to Appendix](#appendix)

---

## JavaScript Prototypes

**Q:** Explain prototypal inheritance in JavaScript.

**A:**

- Every object has a prototype (another object)
- Objects inherit properties/methods from prototype
- Prototype chain ends at null
- Classes are syntactic sugar over prototypes

**Example:**

```js
function Person(name) {
  this.name = name;
}

Person.prototype.greet = function () {
  return `Hello, ${this.name}`;
};

const john = new Person("John");
john.greet(); // "Hello, John"
```

**Red flags:**

- Confusing with classical inheritance
- Modifying built-in prototypes

[↑ Back to Appendix](#appendix)

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

**Example:**

```js
const obj = {
  value: 42,
  regular: function () {
    return this.value;
  },
  arrow: () => this.value,
};

obj.regular(); // 42
obj.arrow(); // undefined (this from outer scope)
```

**Red flags:**

- Not understanding arrow functions don't bind this
- Losing context when passing methods as callbacks

[↑ Back to Appendix](#appendix)

---

## Node.js Streams

**Q:** What are streams in Node.js and when would you use them?

**A:**

- Process data piece by piece (chunks)
- Don't load entire data into memory
- Four types: Readable, Writable, Duplex, Transform

**Use cases:**

- Large file processing
- HTTP requests/responses
- Database queries with large results
- Real-time data processing

**Example:**

```js
const fs = require("fs");
const readStream = fs.createReadStream("large-file.txt");
const writeStream = fs.createWriteStream("output.txt");

readStream.pipe(writeStream);
```

**Red flags:**

- Loading entire files into memory with fs.readFile
- Not handling backpressure

[↑ Back to Appendix](#appendix)

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

**Example:**

```ts
@Module({
  imports: [DatabaseModule],
  providers: [UserService],
  controllers: [UserController],
  exports: [UserService],
})
export class UserModule {}
```

**Red flags:**

- Not understanding dependency injection
- Circular dependencies between modules

[↑ Back to Appendix](#appendix)

---

## NestJS Dependency Injection

**Q:** How does dependency injection work in NestJS?

**A:**

- Constructor-based injection
- NestJS IoC container manages instances
- Default scope is Singleton
- Promotes loose coupling and testability

**Example:**

```ts
@Injectable()
export class UserService {
  constructor(
    private readonly userRepository: UserRepository,
    private readonly emailService: EmailService,
  ) {}
}
```

**Scopes:**

- **Singleton** (default) - one instance
- **Request** - new instance per request
- **Transient** - new instance each time

**Red flags:**

- Not understanding injection scopes
- Creating instances with 'new' instead of injection

[↑ Back to Appendix](#appendix)

---

## Database Connection Pooling

**Q:** What is connection pooling and why is it important?

**A:**

- Reuse database connections instead of creating new ones
- Pool maintains a set of open connections
- Reduces connection overhead and latency
- Limits concurrent connections to database

**Configuration:**

```js
const pool = new Pool({
  max: 20, // max connections
  min: 5, // min connections
  idle: 10000, // idle timeout
  acquire: 30000, // acquire timeout
});
```

**Red flags:**

- Creating new connection for every query
- Not closing connections
- Pool size too large or too small

[↑ Back to Appendix](#appendix)

---

## Database Migrations

**Q:** What are database migrations and why use them?

**A:**

- Version control for database schema
- Track and apply schema changes
- Rollback capabilities
- Consistency across environments

**Example structure:**

```sql
-- Up migration
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  email VARCHAR(255) UNIQUE NOT NULL
);

-- Down migration
DROP TABLE users;
```

**Tools:** Knex, TypeORM, Prisma Migrate, Flyway

**Red flags:**

- Manual schema changes in production
- Not testing migrations on staging
- Missing rollback migrations

[↑ Back to Appendix](#appendix)

---

## Composite Indexes

**Q:** What are composite indexes and when should you use them?

**A:**

- Index on multiple columns
- Order of columns matters
- Useful for queries filtering/sorting on multiple columns
- Leftmost prefix rule applies

**Example:**

```sql
CREATE INDEX idx_user_location
  ON users(country, city, created_at);

-- Works well:
WHERE country = 'US' AND city = 'NYC'

-- Won't use index:
WHERE city = 'NYC' (missing leftmost column)
```

**Red flags:**

- Wrong column order
- Too many columns (diminishing returns)
- Not understanding leftmost prefix rule

[↑ Back to Appendix](#appendix)

---

## TypeScript Generics

**Q:** What are generics in TypeScript and why use them?

**A:**

- Create reusable components that work with multiple types
- Type safety without losing type information
- Commonly used in arrays, promises, functions

**Example:**

```ts
function identity<T>(arg: T): T {
  return arg;
}

interface Repository<T> {
  find(id: number): Promise<T>;
  save(entity: T): Promise<T>;
}

class UserRepository implements Repository<User> {
  // Type-safe implementation
}
```

**Red flags:**

- Using 'any' when generics would work
- Overly complex generic constraints

[↑ Back to Appendix](#appendix)

---

## TypeScript Union and Intersection Types

**Q:** What's the difference between union and intersection types?

**A:**

**Union (|):** Value can be one of several types

```ts
type Status = "pending" | "approved" | "rejected";
type ID = string | number;
```

**Intersection (&):** Combines multiple types

```ts
type Employee = Person & { employeeId: number };
type AdminUser = User & Admin & { permissions: string[] };
```

**Use cases:**

- Union: flexible inputs, discriminated unions
- Intersection: compose types, extend interfaces

**Red flags:**

- Confusing union with intersection
- Creating impossible intersections

[↑ Back to Appendix](#appendix)

---

## TypeScript Type Guards

**Q:** What are type guards and how do you use them?

**A:**

- Runtime checks that narrow types
- Help TypeScript understand type at runtime
- typeof, instanceof, custom type predicates

**Examples:**

```ts
// typeof guard
if (typeof value === "string") {
  value.toUpperCase();
}

// Custom type guard
function isUser(obj: any): obj is User {
  return "id" in obj && "email" in obj;
}

if (isUser(data)) {
  console.log(data.email); // TypeScript knows it's User
}
```

**Red flags:**

- Using 'any' and type assertions instead
- Not narrowing union types properly

[↑ Back to Appendix](#appendix)

---

## Refresh Tokens

**Q:** What are refresh tokens and why use them?

**A:**

- Long-lived tokens to obtain new access tokens
- Access tokens are short-lived (15 min)
- Refresh tokens stored securely (httpOnly cookie)
- Reduces re-authentication frequency

**Flow:**

1. Login → receive access token + refresh token
2. Access token expires → use refresh token
3. Get new access token + new refresh token
4. Logout → invalidate refresh token

**Red flags:**

- Storing refresh tokens in localStorage
- Not rotating refresh tokens
- Same expiry for access and refresh tokens

[↑ Back to Appendix](#appendix)

---

## Password Hashing

**Q:** How should you hash passwords? What algorithm?

**A:**

**Use:** bcrypt or Argon2 (modern choice)

**Never:** MD5, SHA-1, plain SHA-256

**Why:**

- Designed to be slow (resist brute force)
- Built-in salt generation
- Adjustable work factor

**Example (bcrypt):**

```js
const bcrypt = require("bcrypt");

// Hash
const hash = await bcrypt.hash(password, 10); // 10 rounds

// Verify
const isValid = await bcrypt.compare(password, hash);
```

**Red flags:**

- Using fast hash functions (MD5, SHA)
- Not salting passwords
- Storing passwords reversibly (encryption)

[↑ Back to Appendix](#appendix)
