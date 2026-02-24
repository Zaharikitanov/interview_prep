# Mid-Level Full-Stack Interview Questions (React / Node / NestJS / PostgreSQL)

A comprehensive collection of interview questions and answers for mid-level full-stack developers, organized by topic for easy navigation.

---

## 📚 Topic Files

This guide is organized into separate files for better maintainability and navigation:

### Frontend

- **[React Questions](React_Questions.md)** - Virtual DOM, Hooks, Performance, Redux vs React Query
- **[JavaScript & Node.js Questions](JavaScript_Questions.md)** - Closures, Async/Await, Event Loop, ES6+ Features

### Backend

- **[NestJS Questions](NestJS_Questions.md)** - DI, Guards & Pipes, Decorators, Interceptors
- **[Database Questions](Database_Questions.md)** - SQL, PostgreSQL, Indexes, Normalization, Performance

### Fundamentals

- **[OOP & TypeScript Questions](OOP_TypeScript_Questions.md)** - SOLID Principles, Design Patterns, TypeScript Features
- **[Behavioral Questions](Behavioral_Questions.md)** - Soft Skills, Team Collaboration, Problem Solving

---

## Appendix

### Quick Reference by Topic

**React** → [React_Questions.md](React_Questions.md)

- [Virtual DOM](React_Questions.md#virtual-dom)
- [React Hooks - useMemo vs useCallback](React_Questions.md#react-hooks---usememo-vs-usecallback)
- [useEffect Cleanup](React_Questions.md#useeffect-cleanup)
- [Custom Hooks](React_Questions.md#custom-hooks)
- [Redux vs React Query](React_Questions.md#redux-vs-react-query)
- [Performance Optimization](React_Questions.md#performance-optimization)

**Node.js & JavaScript** → [JavaScript_Questions.md](JavaScript_Questions.md)

- [Closures](JavaScript_Questions.md#javascript-closures)
- ['this' Keyword](JavaScript_Questions.md#javascript-this-keyword)
- [Hoisting](JavaScript_Questions.md#javascript-hoisting)
- [Function Declaration vs Expression](JavaScript_Questions.md#function-declaration-vs-expression)
- [Promises & Microtasks](JavaScript_Questions.md#promises--timeout)
- [Async/Await](JavaScript_Questions.md#asyncawait)
- [Event Loop](JavaScript_Questions.md#nodejs-event-loop)
- [Handling Long-Running Requests](JavaScript_Questions.md#handling-long-running-requests)
- [Event Delegation](JavaScript_Questions.md#event-delegation)
- [Debouncing vs Throttling](JavaScript_Questions.md#debouncing-vs-throttling)
- [Array Methods (map, filter, reduce)](JavaScript_Questions.md#array-methods)
- [Deep vs Shallow Copy](JavaScript_Questions.md#deep-vs-shallow-copy)
- [== vs ===](JavaScript_Questions.md#-vs-)
- [Null vs Undefined](JavaScript_Questions.md#null-vs-undefined)
- [Spread Operator & Destructuring](JavaScript_Questions.md#spread-operator--destructuring)
- [Arrow Functions Differences](JavaScript_Questions.md#arrow-functions-differences)

**NestJS** → [NestJS_Questions.md](NestJS_Questions.md)

- [Modules and Providers](NestJS_Questions.md#modules-and-providers)
- [Dependency Injection](NestJS_Questions.md#dependency-injection)
- [Guards & Pipes](NestJS_Questions.md#guards--pipes)
- [Decorator Execution Order](NestJS_Questions.md#decorator-execution-order)
- [Interceptors](NestJS_Questions.md#interceptors)
- [Custom Decorators](NestJS_Questions.md#custom-decorators)

**Database (PostgreSQL)** → [Database_Questions.md](Database_Questions.md)

- [SQL Injection Prevention](Database_Questions.md#sql-injection-prevention)
- [Indexes](Database_Questions.md#indexes)
- [Normalization](Database_Questions.md#normalization)
- [Many-to-Many Relationships](Database_Questions.md#many-to-many-relationships)
- [Migrations](Database_Questions.md#migrations)
- [N+1 Query Problem](Database_Questions.md#n1-query-problem)
- [Composite Indexes](Database_Questions.md#composite-indexes)
- [Table Partitioning](Database_Questions.md#table-partitioning)
- [CAP Theorem](Database_Questions.md#cap-theorem)

**OOP & TypeScript** → [OOP_TypeScript_Questions.md](OOP_TypeScript_Questions.md)

- [Four Pillars of OOP](OOP_TypeScript_Questions.md#four-pillars-of-oop)
- [SOLID Principles](OOP_TypeScript_Questions.md#solid-principles)
- [Abstract Class vs Interface](OOP_TypeScript_Questions.md#abstract-class-vs-interface)
- [Practical OOP & Design Situations](OOP_TypeScript_Questions.md#practical-oop--design-situations)
- [Extending Interfaces](OOP_TypeScript_Questions.md#extending-interfaces)
- [Union and Intersection Types](OOP_TypeScript_Questions.md#union-and-intersection-types)
- [Generics](OOP_TypeScript_Questions.md#generics)

**Authentication & Security**

- [JWT vs Session Authentication](#jwt-vs-session-authentication)
- [OAuth2](#oauth2)
- [Next.js Authentication](#nextjs-authentication)

**Behavioral & Soft Skills** → [Behavioral_Questions.md](Behavioral_Questions.md)

- [Learning from Mistakes](Behavioral_Questions.md#learning-from-mistakes)
- [Receiving Critical Feedback](Behavioral_Questions.md#receiving-critical-feedback)
- [Disagreement with Team Member](Behavioral_Questions.md#disagreement-with-team-member)
- [Technical Decision Making](Behavioral_Questions.md#technical-decision-making)
- [Mentoring Others](Behavioral_Questions.md#mentoring-others)

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

[↑ Back to Appendix](#appendix)

---

## OAuth2

**Q:** What is OAuth2?

**A:**

- Authorization framework
- Uses access tokens
- Involves an authorization server
- OAuth2 ≠ authentication

[↑ Back to Appendix](#appendix)

---

## Next.js Authentication

**Q:** Did you implement authentication in Next.js? How?

**A:**

- NextAuth / Auth.js
- OAuth2 providers (Google, GitHub, etc.)
- JWT-based or session-based authentication

[↑ Back to Appendix](#appendix)
