# Database Interview Questions (PostgreSQL)

[← Back to Main Appendix](README.md#appendix)

---

## Table of Contents

- [SQL Injection Prevention](#sql-injection-prevention)
- [Indexes](#indexes)
- [Normalization](#normalization)
- [Many-to-Many Relationships](#many-to-many-relationships)
- [Migrations](#migrations)
- [N+1 Query Problem](#n1-query-problem)
- [Composite Indexes](#composite-indexes)
- [Table Partitioning](#table-partitioning)
- [CAP Theorem](#cap-theorem)

---

## SQL Injection Prevention

**Q:** How do you prevent SQL injection?

**A:**

**Definition:** SQL injection is a vulnerability where attackers can insert malicious SQL code through user input to manipulate or access the database.

**Prevention Methods:**

**1. Parameterized Queries / Prepared Statements (BEST):**

```javascript
// BAD - Vulnerable to SQL injection
const userId = req.params.id;
const query = `SELECT * FROM users WHERE id = ${userId}`;
// Attack: /users/1; DROP TABLE users;--

// GOOD - Parameterized query
const query = "SELECT * FROM users WHERE id = $1";
const result = await client.query(query, [userId]);
```

**2. Use ORM Query Builders:**

```javascript
// TypeORM
const user = await userRepository.findOne({ where: { id: userId } });

// Prisma
const user = await prisma.user.findUnique({ where: { id: userId } });

// Sequelize
const users = await User.findAll({ where: { email: userEmail } });
```

**3. Input Validation:**

```javascript
// Validate input before using in queries
if (!/^\d+$/.test(userId)) {
  throw new BadRequestException("Invalid user ID");
}
```

**4. Escape User Input (if absolutely necessary):**

```javascript
// Last resort - use library functions
const escapedName = pg.escapeLiteral(userName);
```

**What NOT to do:**

```javascript
// ❌ String concatenation
const query = `SELECT * FROM users WHERE name = '${userName}'`;

// ❌ String interpolation
const query = `SELECT * FROM users WHERE name = '${userName}'`;

// ❌ Using user input directly
db.query(`DELETE FROM users WHERE id = ${req.body.id}`);
```

**Why parameterized queries work:**

- User input treated as data, not SQL code
- Database separates SQL structure from values
- Special characters automatically escaped
- Cannot alter query structure

**Example attack scenarios:**

```javascript
// Attack 1: Always true condition
// Input: ' OR '1'='1
const query = `SELECT * FROM users WHERE name = '${userName}'`;
// Result: SELECT * FROM users WHERE name = '' OR '1'='1'
// Returns all users!

// Attack 2: Dropping tables
// Input: '; DROP TABLE users;--
const query = `SELECT * FROM users WHERE id = ${userId}`;
// Result: SELECT * FROM users WHERE id = 1; DROP TABLE users;--
// Deletes entire table!
```

**Red flags:**

- Building queries with string concatenation
- Not using parameterized queries
- Trusting user input without validation
- Rolling your own escaping functions

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

## Indexes

**Q:** What are database indexes and when should you use them?

**A:**

**Definition:** Data structures that improve the speed of data retrieval operations on database tables.

**How they work:**

- Like a book index - quickly find data without scanning entire table
- Store sorted references to table rows
- Trade-off: **Faster reads, slower writes**

**When to use indexes:**

1. **WHERE clauses** - Columns frequently filtered
2. **JOIN conditions** - Foreign keys
3. **ORDER BY** - Sorting columns
4. **GROUP BY** - Grouping columns
5. **Unique constraints** - Prevent duplicates

**Example:**

```sql
-- Without index (slow - full table scan)
SELECT * FROM users WHERE email = 'john@example.com';
-- Scans ALL rows

-- Create index
CREATE INDEX idx_users_email ON users(email);

-- With index (fast - index lookup)
SELECT * FROM users WHERE email = 'john@example.com';
-- Directly finds rows matching email
```

**Types of Indexes:**

**1. B-tree (default, most common):**

```sql
CREATE INDEX idx_name ON users(name);
```

- Good for equality and range queries
- Supports `<`, `<=`, `=`, `>=`, `>`, `BETWEEN`, `IN`
- Maintains sorted order

**2. Hash:**

```sql
CREATE INDEX idx_email ON users USING HASH(email);
```

- Only equality comparisons (`=`)
- Faster than B-tree for exact matches
- Cannot do range queries

**3. GIN (Generalized Inverted Index):**

```sql
CREATE INDEX idx_tags ON posts USING GIN(tags);
CREATE INDEX idx_data ON documents USING GIN(data jsonb_path_ops);
```

- For JSONB, arrays, full-text search
- Multiple values per row
- Larger, slower writes, but fast searches

**4. GiST (Generalized Search Tree):**

```sql
CREATE INDEX idx_location ON places USING GIST(location);
```

- Geometric data, full-text search
- Range types, IP addresses

**When NOT to use indexes:**

- Small tables (overhead not worth it)
- Columns with low cardinality (few distinct values)
- Frequently updated columns (write overhead)
- Columns rarely used in queries

**Trade-offs:**

**Benefits:**

- Dramatically faster SELECT queries
- Faster JOINs
- Enforce uniqueness

**Costs:**

- Slower INSERT, UPDATE, DELETE
- Additional disk space
- Maintenance overhead

**Example - Performance Impact:**

```sql
-- Table with 1 million users
-- Without index:
SELECT * FROM users WHERE email = 'john@example.com';
-- Query time: ~500ms (full table scan)

-- With index:
CREATE INDEX idx_users_email ON users(email);
SELECT * FROM users WHERE email = 'john@example.com';
-- Query time: ~2ms (index lookup)
```

**Best Practices:**

1. **Index foreign keys** - Almost always beneficial
2. **Monitor slow queries** - EXPLAIN ANALYZE
3. **Don't over-index** - Each index has cost
4. **Consider composite indexes** - Multiple columns
5. **Unique indexes** - Enforce constraints + performance

**Checking if index is used:**

```sql
EXPLAIN ANALYZE SELECT * FROM users WHERE email = 'john@example.com';

-- Look for:
-- Index Scan using idx_users_email (GOOD)
-- Seq Scan on users (BAD - not using index)
```

**Red flags:**

- No indexes on foreign keys
- Indexing every column
- Not analyzing query performance
- Using `SELECT *` instead of specific columns

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

## Normalization

**Q:** What is database normalization?

**A:**

**Definition:** Process of organizing data to reduce redundancy and improve data integrity.

**Goals:**

- Eliminate data redundancy (duplicate data)
- Ensure data dependencies make sense
- Avoid update/insert/delete anomalies

**Normal Forms:**

**1NF (First Normal Form) - Atomic Values:**

**Rules:**

- Each column contains atomic (indivisible) values
- Each row is unique
- No repeating groups

```sql
-- ❌ NOT in 1NF (multiple values in one cell)
CREATE TABLE users (
  id INT,
  name VARCHAR,
  phones VARCHAR -- '555-1234, 555-5678' (multiple phones)
);

-- ✓ In 1NF (atomic values)
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR
);

CREATE TABLE user_phones (
  user_id INT REFERENCES users(id),
  phone VARCHAR
);
```

**2NF (Second Normal Form) - No Partial Dependencies:**

**Rules:**

- Must be in 1NF
- No partial dependencies (non-key columns depend on entire primary key)
- Relevant only for composite keys

```sql
-- ❌ NOT in 2NF (student_name depends only on student_id, not full key)
CREATE TABLE enrollments (
  student_id INT,
  course_id INT,
  student_name VARCHAR, -- Depends only on student_id
  grade VARCHAR,
  PRIMARY KEY (student_id, course_id)
);

-- ✓ In 2NF (split into separate tables)
CREATE TABLE students (
  id INT PRIMARY KEY,
  name VARCHAR
);

CREATE TABLE enrollments (
  student_id INT REFERENCES students(id),
  course_id INT REFERENCES courses(id),
  grade VARCHAR,
  PRIMARY KEY (student_id, course_id)
);
```

**3NF (Third Normal Form) - No Transitive Dependencies:**

**Rules:**

- Must be in 2NF
- No transitive dependencies (non-key columns depend only on primary key, not other non-key columns)

```sql
-- ❌ NOT in 3NF (city and state depend on zip_code, not directly on user_id)
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR,
  zip_code VARCHAR,
  city VARCHAR,     -- Depends on zip_code
  state VARCHAR     -- Depends on zip_code
);

-- ✓ In 3NF (split dependencies)
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR,
  zip_code VARCHAR REFERENCES zip_codes(code)
);

CREATE TABLE zip_codes (
  code VARCHAR PRIMARY KEY,
  city VARCHAR,
  state VARCHAR
);
```

**Benefits:**

- **Reduce redundancy** - No duplicate data
- **Data integrity** - Update in one place
- **Avoid anomalies**:
  - **Update anomaly** - Inconsistent data after update
  - **Insert anomaly** - Can't insert without other data
  - **Delete anomaly** - Lose related data when deleting

**Trade-offs:**

**Pros:**

- Less storage (no duplicates)
- Easier updates
- Better data integrity

**Cons:**

- More JOINs (can be slower)
- More complex queries
- More tables to manage

**Example - Update Anomaly:**

```sql
-- Denormalized (not good)
CREATE TABLE orders (
  id INT,
  customer_name VARCHAR,
  customer_email VARCHAR,
  product_name VARCHAR
);

-- If customer email changes, must update ALL their orders
-- Risk of inconsistent data if you miss some rows

-- Normalized (better)
CREATE TABLE customers (
  id INT PRIMARY KEY,
  name VARCHAR,
  email VARCHAR
);

CREATE TABLE orders (
  id INT PRIMARY KEY,
  customer_id INT REFERENCES customers(id),
  product_name VARCHAR
);

-- Customer email in ONE place, update once
```

**When to denormalize:**

- Read-heavy workloads (analyze > update)
- Performance critical queries
- Reporting/analytics tables
- Calculated/aggregated values
- After measuring actual performance issues

**Red flags:**

- Not understanding the trade-offs
- Premature denormalization
- Duplicate data without reason
- No foreign key constraints

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

## Many-to-Many Relationships

**Q:** How do you resolve many-to-many relationships in a database? Is it a good practice?

**A:**

**Problem:** Two entities can relate to each other multiple times.

**Examples:**

- Students ↔ Courses (students take many courses, courses have many students)
- Users ↔ Roles (users have many roles, roles belong to many users)
- Posts ↔ Tags (posts have many tags, tags apply to many posts)

**Solution: Bridge Table (Junction Table)**

**Structure:**

- Table with foreign keys to both related tables
- Often includes additional relationship data
- Composite primary key or separate ID

**Basic Example:**

```sql
-- Students
CREATE TABLE students (
  id SERIAL PRIMARY KEY,
  name VARCHAR NOT NULL
);

-- Courses
CREATE TABLE courses (
  id SERIAL PRIMARY KEY,
  name VARCHAR NOT NULL
);

-- Bridge table: student_courses
CREATE TABLE student_courses (
  student_id INT REFERENCES students(id) ON DELETE CASCADE,
  course_id INT REFERENCES courses(id) ON DELETE CASCADE,
  PRIMARY KEY (student_id, course_id)
);

-- Query: Get all courses for a student
SELECT c.*
FROM courses c
JOIN student_courses sc ON c.id = sc.course_id
WHERE sc.student_id = 1;

-- Query: Get all students in a course
SELECT s.*
FROM students s
JOIN student_courses sc ON s.id = sc.student_id
WHERE sc.course_id = 101;
```

**With Additional Data:**

```sql
-- Bridge table with relationship-specific data
CREATE TABLE student_courses (
  id SERIAL PRIMARY KEY,
  student_id INT REFERENCES students(id) ON DELETE CASCADE,
  course_id INT REFERENCES courses(id) ON DELETE CASCADE,
  enrolled_at TIMESTAMP DEFAULT NOW(),
  grade VARCHAR,
  status VARCHAR, -- 'active', 'completed', 'dropped'
  UNIQUE(student_id, course_id) -- Prevent duplicate enrollments
);
```

**More Complex Example - Posts & Tags:**

```sql
CREATE TABLE posts (
  id SERIAL PRIMARY KEY,
  title VARCHAR NOT NULL,
  content TEXT,
  created_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE tags (
  id SERIAL PRIMARY KEY,
  name VARCHAR UNIQUE NOT NULL
);

CREATE TABLE post_tags (
  post_id INT REFERENCES posts(id) ON DELETE CASCADE,
  tag_id INT REFERENCES tags(id) ON DELETE CASCADE,
  created_at TIMESTAMP DEFAULT NOW(),
  PRIMARY KEY (post_id, tag_id)
);

-- Query: Get posts with a specific tag
SELECT p.*
FROM posts p
JOIN post_tags pt ON p.id = pt.post_id
JOIN tags t ON pt.tag_id = t.id
WHERE t.name = 'javascript';

-- Query: Get all tags for a post
SELECT t.*
FROM tags t
JOIN post_tags pt ON t.id = pt.tag_id
WHERE pt.post_id = 42;

-- Query: Count posts per tag
SELECT t.name, COUNT(pt.post_id) as post_count
FROM tags t
LEFT JOIN post_tags pt ON t.id = pt.tag_id
GROUP BY t.id, t.name
ORDER BY post_count DESC;
```

**Is it good practice?**

**YES!** For many reasons:

**1. Maintains Data Integrity:**

- Enforces relationships with foreign keys
- Prevents orphaned data
- Ensures referential integrity

**2. Avoids Data Duplication:**

- No repeated data
- Follows normalization principles
- Single source of truth

**3. Enables Efficient Querying:**

- Index on foreign keys for fast lookups
- Easy to add, remove relationships
- Scalable

**4. Flexibility:**

- Can store relationship metadata (when, status, etc.)
- Easy to modify relationships
- Supports complex queries

**Alternatives (and why they're bad):**

**❌ Storing as comma-separated values:**

```sql
-- DON'T DO THIS!
CREATE TABLE posts (
  id INT,
  title VARCHAR,
  tags VARCHAR -- 'javascript,react,node' (BAD!)
);

-- Problems:
-- - Hard to query specific tags
-- - No referential integrity
-- - Difficult to update
-- - Can't enforce tag validity
```

**❌ JSON arrays (sometimes acceptable):**

```sql
CREATE TABLE posts (
  id INT,
  title VARCHAR,
  tags JSONB -- ['javascript', 'react'] (Not ideal)
);

-- Use only when:
-- - Tags are truly document properties
-- - Don't need to query by tags frequently
-- - Tags don't need their own attributes
```

**ORM Examples:**

**TypeORM:**

```typescript
@Entity()
class Student {
  @ManyToMany(() => Course, (course) => course.students)
  @JoinTable() // Creates bridge table
  courses: Course[];
}

@Entity()
class Course {
  @ManyToMany(() => Student, (student) => student.courses)
  students: Student[];
}
```

**Prisma:**

```prisma
model Student {
  id       Int       @id @default(autoincrement())
  courses  Course[]  // Implicit many-to-many
}

model Course {
  id       Int       @id @default(autoincrement())
  students Student[]
}
```

**Red flags:**

- Not using bridge tables for many-to-many
- Storing arrays/CSVs instead of proper relationships
- Missing indexes on foreign keys
- Not understanding CASCADE behavior
- Forgetting UNIQUE constraint to prevent duplicates

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

## Migrations

**Q:** What are database migrations and why use them?

**A:**

**Definition:** Version control for your database schema. Track and apply incremental changes to database structure.

**Why use migrations:**

**1. Version Control:**

- Track schema changes over time
- See history of database evolution
- Link schema changes to application code

**2. Consistency Across Environments:**

- Same schema in dev, staging, production
- Reproducible database state
- No manual SQL scripts

**3. Rollback Capabilities:**

- Undo changes if something breaks
- Safe deployments
- Test schema changes

**4. Team Collaboration:**

- Multiple developers can work on schema
- Merge conflicts visible
- Clear change history

**5. Deployment Automation:**

- Run migrations as part of CI/CD
- Automated database updates
- No manual intervention

**How migrations work:**

```
Initial State (v1)
     ↓ [Migration 1: Create users table]
State v2
     ↓ [Migration 2: Add email column]
State v3
     ↓ [Migration 3: Create posts table]
Current State (v4)
```

**Example Migration:**

```typescript
// TypeORM Migration
export class CreateUsersTable1234567890 implements MigrationInterface {
  public async up(queryRunner: QueryRunner): Promise<void> {
    await queryRunner.createTable(
      new Table({
        name: "users",
        columns: [
          {
            name: "id",
            type: "int",
            isPrimary: true,
            isGenerated: true,
            generationStrategy: "increment",
          },
          {
            name: "email",
            type: "varchar",
            isUnique: true,
          },
          {
            name: "created_at",
            type: "timestamp",
            default: "CURRENT_TIMESTAMP",
          },
        ],
      }),
    );
  }

  public async down(queryRunner: QueryRunner): Promise<void> {
    await queryRunner.dropTable("users");
  }
}
```

**SQL Migration (Knex):**

```javascript
// Migration up
exports.up = function (knex) {
  return knex.schema.createTable("users", (table) => {
    table.increments("id").primary();
    table.string("email").unique().notNullable();
    table.timestamps(true, true);
  });
};

// Migration down (rollback)
exports.down = function (knex) {
  return knex.schema.dropTable("users");
};
```

**Prisma Migration:**

```prisma
// schema.prisma
model User {
  id         Int      @id @default(autoincrement())
  email      String   @unique
  posts      Post[]
  createdAt  DateTime @default(now())
}

// Run: npx prisma migrate dev --name add_user_model
// Auto-generates SQL migration files
```

**Common Migration Operations:**

**Create table:**

```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  email VARCHAR UNIQUE NOT NULL
);
```

**Add column:**

```sql
ALTER TABLE users ADD COLUMN age INT;
```

**Remove column:**

```sql
ALTER TABLE users DROP COLUMN age;
```

**Rename column:**

```sql
ALTER TABLE users RENAME COLUMN name TO full_name;
```

**Add index:**

```sql
CREATE INDEX idx_users_email ON users(email);
```

**Add foreign key:**

```sql
ALTER TABLE posts
ADD CONSTRAINT fk_user
FOREIGN KEY (user_id) REFERENCES users(id);
```

**Migration Best Practices:**

**1. Never modify existing migrations:**

```
❌ Edit migration file after it's run
✓ Create new migration to fix issues
```

**2. Test migrations:**

```
✓ Run on dev/staging first
✓ Test rollback (down migration)
✓ Verify data integrity
```

**3. Keep migrations small:**

```
✓ One logical change per migration
❌ Huge migrations with many changes
```

**4. Handle data carefully:**

```typescript
// Good - migrate data before dropping column
public async up(queryRunner: QueryRunner): Promise<void> {
  // 1. Add new column
  await queryRunner.query(`ALTER TABLE users ADD COLUMN full_name VARCHAR`);

  // 2. Migrate data
  await queryRunner.query(`UPDATE users SET full_name = name`);

  // 3. Drop old column
  await queryRunner.query(`ALTER TABLE users DROP COLUMN name`);
}
```

**5. Use transactions:**

```typescript
// Wrap in transaction - all or nothing
public async up(queryRunner: QueryRunner): Promise<void> {
  await queryRunner.startTransaction();
  try {
    await queryRunner.query(/* ... */);
    await queryRunner.query(/* ... */);
    await queryRunner.commitTransaction();
  } catch (err) {
    await queryRunner.rollbackTransaction();
    throw err;
  }
}
```

**Migration Tools:**

- **TypeORM** - ORM with migrations
- **Prisma Migrate** - Schema-based migrations
- **Knex** - SQL query builder with migrations
- **Flyway** - Database-agnostic migrations
- **Liquibase** - Enterprise migration tool
- **Sequelize** - ORM with migrations

**Workflow:**

```bash
# 1. Create migration
npm run migration:create -- AddEmailToUsers

# 2. Edit migration file (up/down methods)

# 3. Run migration
npm run migration:run

# 4. Rollback if needed
npm run migration:revert
```

**Red flags:**

- Manual schema changes without migrations
- Editing existing migrations
- No rollback strategy
- Not testing migrations before production
- Ignoring data migration needs

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

## N+1 Query Problem

**Q:** What is the N+1 query problem?

**A:**

**Definition:** Performance anti-pattern where you execute 1 query to fetch N records, then N additional queries to fetch related data for each record.

**Example Problem:**

```javascript
// Fetch all posts (1 query)
const posts = await Post.find(); // Returns 10 posts

// For EACH post, fetch author (10 queries)
for (const post of posts) {
  post.author = await User.findById(post.userId); // N queries!
}

// Total: 1 + 10 = 11 queries
```

**Why it's bad:**

- **Performance** - Multiple round trips to database
- **Latency** - Each query has overhead
- **Scalability** - Gets worse as N grows
- **Resources** - Wastes database connections

**Solutions:**

**1. Eager Loading (JOIN):**

```javascript
// TypeORM
const posts = await postRepository.find({
  relations: ['author'] // Single query with JOIN
});

// Sequelize
const posts = await Post.findAll({
  include: [User] // JOIN automatically
});

// SQL equivalent
SELECT posts.*, users.*
FROM posts
JOIN users ON posts.user_id = users.id;

// Result: 1 query instead of 11!
```

**2. Batch Loading (DataLoader):**

```javascript
// GraphQL DataLoader pattern
const userLoader = new DataLoader(async (userIds) => {
  const users = await User.findByIds(userIds);
  return userIds.map((id) => users.find((u) => u.id === id));
});

// Batches requests
const posts = await Post.find();
const postsWithAuthors = await Promise.all(
  posts.map(async (post) => ({
    ...post,
    author: await userLoader.load(post.userId), // Batched!
  })),
);

// Queries:
// 1. SELECT * FROM posts
// 2. SELECT * FROM users WHERE id IN (1, 2, 3, ...) -- Single batched query
```

**3. IN Query:**

```javascript
// Manual batching
const posts = await Post.find();
const userIds = posts.map(post => post.userId);

// Single query for all users
const users = await User.find({ id: { $in: userIds } });

// Map users to posts
const userMap = new Map(users.map(u => [u.id, u]));
posts.forEach(post => {
  post.author = userMap.get(post.userId);
});

// SQL equivalent
SELECT * FROM users WHERE id IN (1, 2, 3, 4, 5);
```

**4. Subquery:**

```sql
-- Single query with subquery
SELECT
  posts.*,
  (SELECT name FROM users WHERE id = posts.user_id) as author_name
FROM posts;
```

**Real-world Example:**

```typescript
// ❌ N+1 Problem (Bad)
async getPosts() {
  const posts = await this.postRepository.find(); // 1 query

  for (const post of posts) {
    post.author = await this.userRepository.findOne(post.userId); // N queries
    post.comments = await this.commentRepository.find({ postId: post.id }); // N more queries
  }

  return posts;
  // Total: 1 + N + N queries (201 if 100 posts!)
}

// ✓ Optimized (Good)
async getPosts() {
  const posts = await this.postRepository.find({
    relations: ['author', 'comments'] // Eager load
  });

  return posts;
  // Total: 1 query with JOINs
}
```

**TypeORM Relations:**

```typescript
@Entity()
class Post {
  @ManyToOne(() => User, (user) => user.posts)
  author: User;

  @OneToMany(() => Comment, (comment) => comment.post)
  comments: Comment[];
}

// Eager loading
const posts = await postRepository.find({
  relations: ["author", "comments", "comments.user"],
});
```

**Prisma Include:**

```typescript
const posts = await prisma.post.findMany({
  include: {
    author: true,
    comments: {
      include: {
        user: true,
      },
    },
  },
});
```

**Detection:**

```javascript
// Enable query logging
// TypeORM
{
  logging: true; // See all queries
}

// Sequelize
{
  logging: console.log;
}

// Look for repeated similar queries:
// SELECT * FROM users WHERE id = 1
// SELECT * FROM users WHERE id = 2
// SELECT * FROM users WHERE id = 3
// ^ This is N+1!
```

**When to use each solution:**

- **JOIN/Eager Loading** - When you always need related data
- **DataLoader** - GraphQL, complex nested queries
- **IN Query** - Simple cases, full control over batching
- **Lazy Loading** - When related data rarely needed

**Red flags:**

- Queries in loops
- Not using eager loading when needed
- Loading too much data (opposite problem)
- Not monitoring query count

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

## Composite Indexes

**Q:** What are composite indexes and when should you use them?

**A:**

**Definition:** Index on multiple columns (instead of just one).

**Syntax:**

```sql
CREATE INDEX idx_name ON table(column1, column2, column3);
```

**When to use:**

1. **Queries filter on multiple columns together**
2. **ORDER BY multiple columns**
3. **Covering index** (index contains all queried columns)

**Example:**

```sql
-- Create composite index
CREATE INDEX idx_users_city_age ON users(city, age);

-- This query benefits from the index
SELECT * FROM users WHERE city = 'NYC' AND age > 25;

-- SQL: Uses idx_users_city_age for fast lookup
```

**Leftmost Prefix Rule (CRITICAL!):**

**The index can be used for queries that match the leftmost columns in order.**

```sql
CREATE INDEX idx_users_city_age_name ON users(city, age, name);

-- ✓ Uses index (matches leftmost prefix)
WHERE city = 'NYC'
WHERE city = 'NYC' AND age = 25
WHERE city = 'NYC' AND age = 25 AND name = 'John'

-- ✓ Partially uses index
WHERE city = 'NYC' AND name = 'John' -- Uses city part only

-- ❌ Does NOT use index (skips leftmost column)
WHERE age = 25
WHERE name = 'John'
WHERE age = 25 AND name = 'John'
```

**Column Order Matters!**

```sql
-- If you have index (city, age)
CREATE INDEX idx_city_age ON users(city, age);

-- Good queries:
SELECT * FROM users WHERE city = 'NYC' AND age = 25; -- Uses both
SELECT * FROM users WHERE city = 'NYC'; -- Uses city

-- Bad queries (won't use this index efficiently):
SELECT * FROM users WHERE age = 25; -- Can't use index!
```

**Covering Index:**

Index contains all columns needed by query - no table access required.

```sql
CREATE INDEX idx_users_email_name ON users(email, name);

-- Query only needs columns in index
SELECT name FROM users WHERE email = 'john@example.com';
-- Database can answer from index alone (Index Only Scan)
-- Faster than accessing table!
```

**Real-world Examples:**

**1. Search with filters:**

```sql
-- E-commerce product search
CREATE INDEX idx_products_category_price ON products(category, price);

SELECT * FROM products
WHERE category = 'electronics' AND price BETWEEN 100 AND 500;
-- Fast lookup using composite index
```

**2. Range queries:**

```sql
-- Events by date and location
CREATE INDEX idx_events_date_city ON events(date, city);

SELECT * FROM events
WHERE date >= '2024-01-01' AND city = 'NYC';
```

**3. Sorting:**

```sql
CREATE INDEX idx_users_lastname_firstname ON users(last_name, first_name);

SELECT * FROM users
ORDER BY last_name, first_name;
-- Index already sorted, no sort operation needed!
```

**Multiple Indexes vs Composite:**

```sql
-- Option 1: Two separate indexes
CREATE INDEX idx_city ON users(city);
CREATE INDEX idx_age ON users(age);

-- Option 2: One composite index
CREATE INDEX idx_city_age ON users(city, age);

-- Query: WHERE city = 'NYC' AND age = 25
-- Option 1: Database picks one index, filters rest
-- Option 2: Uses both columns efficiently (better!)
```

**Trade-offs:**

**Benefits:**

- Faster queries on multiple columns
- Can replace multiple single-column indexes
- More efficient than separate indexes

**Costs:**

- Larger index size
- Slower writes (INSERT, UPDATE, DELETE)
- Order matters (less flexible)

**How many composite indexes?**

```sql
-- You can have multiple composite indexes
CREATE INDEX idx_city_age ON users(city, age);
CREATE INDEX idx_country_city ON users(country, city);
CREATE INDEX idx_email_status ON users(email, status);

-- But don't go overboard! Each index has cost.
```

**Best Practices:**

1. **Analyze query patterns** - What columns are filtered together?
2. **Put most selective column first** - Usually
3. **Consider query frequency** - Index common queries
4. **Monitor performance** - Use EXPLAIN ANALYZE
5. **Don't over-index** - Every index slows writes

**Check if index is used:**

```sql
EXPLAIN ANALYZE
SELECT * FROM users WHERE city = 'NYC' AND age = 25;

-- Look for:
-- "Index Scan using idx_city_age" (GOOD)
-- "Seq Scan on users" (BAD - not using index)
```

**Red flags:**

- Ignoring leftmost prefix rule
- Wrong column order
- Too many indexes on one table
- Not analyzing actual query patterns

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

## Table Partitioning

**Q:** What is table partitioning and when should you use it?

**A:**

**Definition:** Splitting a large table into smaller, more manageable pieces (partitions) while appearing as a single logical table.

**When to use:**

- **Very large tables** (millions/billions of rows)
- **Time-series data** (logs, events, metrics, transactions)
- **Queries typically filter by partition key** (date, region, etc.)
- **Need to archive/delete old data efficiently**
- **Improve query performance on specific ranges**

**Types of Partitioning:**

**1. Range Partitioning (most common):**

Partition by value ranges (dates, numbers)

```sql
-- Partition by date range
CREATE TABLE events (
  id SERIAL,
  event_name VARCHAR,
  event_time TIMESTAMP,
  data JSONB
) PARTITION BY RANGE (event_time);

-- Create partitions for each month
CREATE TABLE events_2024_01 PARTITION OF events
  FOR VALUES FROM ('2024-01-01') TO ('2024-02-01');

CREATE TABLE events_2024_02 PARTITION OF events
  FOR VALUES FROM ('2024-02-01') TO ('2024-03-01');

CREATE TABLE events_2024_03 PARTITION OF events
  FOR VALUES FROM ('2024-03-01') TO ('2024-04-01');

-- Query specific month (only scans relevant partition)
SELECT * FROM events
WHERE event_time >= '2024-02-01' AND event_time < '2024-03-01';
-- Scans only events_2024_02 partition!
```

**2. List Partitioning:**

Partition by specific values

```sql
-- Partition by region
CREATE TABLE sales (
  id SERIAL,
  region VARCHAR,
  amount DECIMAL,
  sale_date DATE
) PARTITION BY LIST (region);

CREATE TABLE sales_north PARTITION OF sales
  FOR VALUES IN ('north', 'northeast', 'northwest');

CREATE TABLE sales_south PARTITION OF sales
  FOR VALUES IN ('south', 'southeast', 'southwest');

-- Query scans only relevant partition
SELECT * FROM sales WHERE region = 'north';
-- Scans only sales_north partition
```

**3. Hash Partitioning:**

Distribute evenly across partitions

```sql
-- Partition by hash (for load distribution)
CREATE TABLE users (
  id SERIAL,
  username VARCHAR,
  created_at TIMESTAMP
) PARTITION BY HASH (id);

CREATE TABLE users_0 PARTITION OF users
  FOR VALUES WITH (MODULUS 4, REMAINDER 0);

CREATE TABLE users_1 PARTITION OF users
  FOR VALUES WITH (MODULUS 4, REMAINDER 1);

CREATE TABLE users_2 PARTITION OF users
  FOR VALUES WITH (MODULUS 4, REMAINDER 2);

CREATE TABLE users_3 PARTITION OF users
  FOR VALUES WITH (MODULUS 4, REMAINDER 3);

-- IDs distributed evenly across 4 partitions
```

**Benefits:**

**1. Query Performance (Partition Pruning):**

```sql
-- Without partitioning: scans entire table (10M rows)
SELECT * FROM logs WHERE log_date = '2024-01-15';

-- With partitioning: scans only relevant partition (300K rows)
-- Much faster!
```

**2. Easier Maintenance:**

```sql
-- Drop old partition (fast! - no table lock)
DROP TABLE logs_2023_01;

-- vs deleting rows (slow, locks table)
DELETE FROM logs WHERE log_date >= '2023-01-01' AND log_date < '2023-02-01';
```

**3. Smaller Indexes:**

- Each partition has its own smaller index
- Faster index scans
- Less index maintenance overhead

**4. Bulk Operations:**

```sql
-- Attach prepared partition with data (fast)
CREATE TABLE logs_2024_12 (LIKE logs INCLUDING ALL);
-- Load data into logs_2024_12
ALTER TABLE logs ATTACH PARTITION logs_2024_12
  FOR VALUES FROM ('2024-12-01') TO ('2025-01-01');
```

**Real-world Example:**

```sql
-- Application logs table (grows rapidly)
CREATE TABLE application_logs (
  id BIGSERIAL,
  log_level VARCHAR,
  message TEXT,
  created_at TIMESTAMP DEFAULT NOW()
) PARTITION BY RANGE (created_at);

-- Monthly partitions
CREATE TABLE logs_2024_01 PARTITION OF application_logs
  FOR VALUES FROM ('2024-01-01') TO ('2024-02-01');

-- Indexes per partition (automatically created)
CREATE INDEX idx_logs_2024_01_level ON logs_2024_01(log_level);

-- Query recent logs (fast - single partition)
SELECT * FROM application_logs
WHERE created_at >= '2024-01-01' AND created_at < '2024-02-01'
  AND log_level = 'ERROR';
-- Scans only logs_2024_01

-- Archive old partitions
DROP TABLE logs_2023_06; -- Instant deletion!
```

**Trade-offs:**

**Pros:**

- Much faster queries on large datasets
- Efficient archival/deletion
- Better maintenance windows
- Smaller index sizes
- Parallel query execution possible

**Cons:**

- Added complexity
- Must manage partition creation
- Cross-partition queries can be slower
- More careful planning required
- Overhead of partition management

**Managing Partitions:**

**Automated creation (smart approach):**

```typescript
// Script to auto-create future partitions
async function createMonthlyPartition(year: number, month: number) {
  const startDate = new Date(year, month - 1, 1);
  const endDate = new Date(year, month, 1);

  const tableName = `events_${year}_${month.toString().padStart(2, "0")}`;

  await db.query(`
    CREATE TABLE IF NOT EXISTS ${tableName} PARTITION OF events
    FOR VALUES FROM ('${startDate.toISOString()}') TO ('${endDate.toISOString()}')
  `);
}

// Run monthly to create next month's partition
```

**Best Practices:**

1. **Choose partition key wisely** - Most queried column (usually date)
2. **Partition size** - 10-100GB per partition (sweet spot)
3. **Pre-create partitions** - Don't create on demand
4. **Monitor partition usage** - Some may be hot, others cold
5. **Automate management** - Scripts for creation/archival

**When NOT to partition:**

- Tables under 100GB
- No clear partition key
- Queries don't filter on partition key
- Premature optimization

**Red flags:**

- Partitioning small tables
- Wrong partition key
- Not creating partitions in advance (inserts fail!)
- Cross-partition queries without planning
- Manual partition management

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

## CAP Theorem

**Q:** What is the CAP theorem?

**A:**

**Definition:** In a distributed database system, you can only guarantee **2 out of 3** properties:

1. **Consistency (C)** - All nodes see the same data at the same time
2. **Availability (A)** - Every request gets a response (success or failure)
3. **Partition Tolerance (P)** - System continues despite network partitions

**Key Insight:** Network partitions **will** happen in distributed systems, so you must choose between **Consistency** or **Availability** during a partition.

**Visual Representation:**

```
        Consistency (C)
             △
            / \
           /   \
          /     \
         /  CA   \
        /   not   \
       / realistic \
      /   in dist  \
     /    systems   \
    /_______________\
Partition      Availability
Tolerance (P)       (A)

Reality: Choose CP or AP
```

**CP (Consistency + Partition Tolerance):**

Sacrifice availability during partitions - wait for all nodes to sync before responding.

**Characteristics:**

- Strong consistency guaranteed
- May refuse requests during partition
- System appears "down" during network issues
- All nodes have same data or no response

**Examples:**

- **MongoDB** (with majority read/write concern)
- **HBase**
- **Redis Cluster** (in certain configs)
- **Traditional RDBMS** (PostgreSQL, MySQL in cluster)

**Use when:**

- Data accuracy is critical
- Incorrect data causes serious problems
- Better to fail than show wrong data
- Financial transactions, inventory systems

**Example scenario:**

```
Bank transfer: $100 from Account A → Account B

Network partition occurs
CP System: Waits for all nodes to agree
- Either all nodes see transfer OR
- Transaction fails, retry later
- Never shows inconsistent balances
Result: Service may be unavailable, but data always correct
```

**AP (Availability + Partition Tolerance):**

Sacrifice consistency during partitions - always respond, even with potentially stale data.

**Characteristics:**

- Always available
- May return stale data during partition
- Eventually consistent
- System continues working during network issues

**Examples:**

- **Cassandra**
- **DynamoDB**
- **CouchDB**
- **Riak**

**Use when:**

- Uptime is critical
- Eventual consistency acceptable
- Stale data tolerable short-term
- Social media, caches, recommendations

**Example scenario:**

```
Social media "like" counter

Network partition occurs
AP System: Both partitions accept likes
- Node A: 100 likes
- Node B: 102 likes (partition)
- Users see different counts temporarily
Result: Service available, data syncs later (eventual consistency)
```

**CA (Consistency + Availability):**

**Not realistic in true distributed systems** because network partitions will happen.

**Only possible in:**

- Single-node databases
- Systems without network partitions (same datacenter, perfect network)
- Traditional monolithic databases

**Eventual Consistency:**

Common in AP systems - data **will** become consistent over time, just not immediately.

**Example:**

```
Twitter follower count:
- You: see 1,000 followers
- Friend: sees 999 followers (stale cache)
- After minutes/seconds: both see 1,000
Result: Acceptable for this use case
```

**Real-world Decision Making:**

**Banking (CP):**

```
Account balance: $1,000
Withdraw: $500
Withdraw: $600 (from another ATM)

CP: Second withdraw fails - ensures no overdraft
AP: Both allowed? Account goes negative! (BAD)
Decision: Choose CP
```

**Shopping Cart (AP):**

```
Add item to cart
Network partition
Item appears/disappears temporarily

AP: Let user continue shopping (merge conflicts later)
CP: Block user until network recovers (bad UX)
Decision: Choose AP
```

**Social Media Feed (AP):**

```
Post to Instagram
Some users see it immediately
Others see it 30 seconds later

AP: Everyone can post/view (eventual consistency OK)
CP: Block posting until all servers sync (terrible UX)
Decision: Choose AP
```

**Inventory System (CP or AP depending):**

```
Last item in stock
Two people try to buy simultaneously

Limited stock: CP (prevent overselling)
Digital goods: AP (copies unlimited)
Decision: Depends on product type
```

**Modern Hybrid Approaches:**

Many systems offer **tunable consistency**:

**Cassandra:**

```
// Strong consistency (CP-like)
SELECT * FROM users WHERE id = 1 CONSISTENCY ALL;

// High availability (AP-like)
SELECT * FROM users WHERE id = 1 CONSISTENCY ONE;

// Balance
SELECT * FROM users WHERE id = 1 CONSISTENCY QUORUM;
```

**MongoDB:**

```typescript
// Strong consistency
await collection.findOne({ _id: 1 }, { readConcern: "majority" });

// Faster but potentially stale
await collection.findOne({ _id: 1 }, { readConcern: "local" });
```

**Summary Table:**

| System             | Type  | Use Case                  |
| ------------------ | ----- | ------------------------- |
| PostgreSQL Cluster | CP    | Financial transactions    |
| MongoDB            | CP    | Critical data accuracy    |
| Cassandra          | AP    | High availability needed  |
| DynamoDB           | AP    | Global scale, eventual OK |
| Redis              | CP/AP | Depending on config       |

**Red flags:**

- Not understanding the trade-offs
- Choosing CP when AP makes more sense (or vice versa)
- Thinking you can have all three properties
- Not considering eventual consistency implications
- Using AP system where strong consistency required

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

[← Back to Main Appendix](README.md#appendix)
