# OOP & TypeScript Interview Questions

[← Back to Main Appendix](README.md#appendix)

---

## Table of Contents

**Object-Oriented Programming:**

- [Four Pillars of OOP](#four-pillars-of-oop)
- [SOLID Principles](#solid-principles)
- [Abstract Class vs Interface](#abstract-class-vs-interface)
- [Practical OOP & Design Situations](#practical-oop--design-situations)

**TypeScript:**

- [Extending Interfaces](#extending-interfaces)
- [Union and Intersection Types](#union-and-intersection-types)
- [Generics](#generics)

---

## Four Pillars of OOP

**Q:** What are the four pillars of object-oriented programming?

**A:**

**1. Encapsulation**

**Definition:** Bundle data (properties) and behavior (methods) together, hide internal state

**Purpose:**

- Control access to data
- Enforce invariants
- Prevent invalid states
- Change internal implementation without affecting external code

**Example:**

```typescript
class BankAccount {
  private balance: number; // Private - cannot access directly

  constructor(initialBalance: number) {
    if (initialBalance < 0) {
      throw new Error("Balance cannot be negative");
    }
    this.balance = initialBalance;
  }

  // Public method to access balance (controlled)
  getBalance(): number {
    return this.balance;
  }

  // Public method to modify balance (with validation)
  deposit(amount: number): void {
    if (amount <= 0) {
      throw new Error("Deposit amount must be positive");
    }
    this.balance += amount;
  }

  withdraw(amount: number): void {
    if (amount > this.balance) {
      throw new Error("Insufficient funds");
    }
    this.balance -= amount;
  }
}

// Usage
const account = new BankAccount(1000);
// account.balance = -500; // ❌ Error: balance is private
account.deposit(500); // ✓ Controlled access
console.log(account.getBalance()); // 1500
```

**Benefits:**

- Data protection
- Validation enforcement
- Flexibility to change internals
- Clear public API

---

**2. Abstraction**

**Definition:** Expose only essential details, hide complexity behind a simple interface

**Purpose:**

- Reduce complexity
- Show only relevant information
- Hide implementation details
- Easier to use and understand

**Example:**

```typescript
// Abstract interface - users don't need to know HOW it sends
interface EmailService {
  sendEmail(to: string, subject: string, body: string): Promise<void>;
}

// Implementation details hidden
class SendGridEmailService implements EmailService {
  async sendEmail(to: string, subject: string, body: string): Promise<void> {
    // Complex SendGrid API calls hidden
    // API keys, authentication, retry logic, etc.
    // Users don't need to know these details
  }
}

class MailgunEmailService implements EmailService {
  async sendEmail(to: string, subject: string, body: string): Promise<void> {
    // Different implementation, same interface
  }
}

// Usage - simple interface, complex implementation hidden
const emailService: EmailService = new SendGridEmailService();
await emailService.sendEmail("user@example.com", "Hello", "Welcome!");
// User doesn't know/care if it's SendGrid or Mailgun
```

**Benefits:**

- Simpler to use
- Hide complexity
- Focus on what, not how
- Easy to swap implementations

---

**3. Inheritance**

**Definition:** Reuse behavior by extending a base class, models "is-a" relationship

**Purpose:**

- Code reuse
- Hierarchical relationships
- Extend existing functionality
- Polymorphism support

**Example:**

```typescript
// Base class
class Animal {
  constructor(public name: string) {}

  makeSound(): void {
    console.log("Some generic sound");
  }

  move(): void {
    console.log(`${this.name} is moving`);
  }
}

// Dog "is-a" Animal (inheritance)
class Dog extends Animal {
  // Override method
  makeSound(): void {
    console.log("Woof! Woof!");
  }

  // Add new method
  fetch(): void {
    console.log(`${this.name} is fetching the ball`);
  }
}

// Cat "is-a" Animal
class Cat extends Animal {
  makeSound(): void {
    console.log("Meow!");
  }

  scratch(): void {
    console.log(`${this.name} is scratching`);
  }
}

// Usage
const dog = new Dog("Buddy");
dog.makeSound(); // "Woof! Woof!" (overridden)
dog.move(); // "Buddy is moving" (inherited)
dog.fetch(); // "Buddy is fetching the ball" (new)

const cat = new Cat("Whiskers");
cat.makeSound(); // "Meow!" (overridden)
cat.move(); // "Whiskers is moving" (inherited)
```

**When to prefer composition over inheritance:**

- "Has-a" relationship instead of "is-a"
- Need to mix multiple behaviors
- Avoid deep inheritance hierarchies
- More flexibility needed

```typescript
// Composition example (often better than inheritance)
class Engine {
  start() {
    console.log("Engine started");
  }
}

class GPS {
  navigate(destination: string) {
    console.log(`Navigating to ${destination}`);
  }
}

// Car "has-a" Engine and "has-a" GPS
class Car {
  private engine = new Engine();
  private gps = new GPS();

  start() {
    this.engine.start();
  }
  navigate(destination: string) {
    this.gps.navigate(destination);
  }
}
```

---

**4. Polymorphism**

**Definition:** Same interface, different implementations - subtypes can be used interchangeably

**Purpose:**

- Write flexible code
- Treat different objects uniformly
- Extend without modifying existing code
- Dynamic behavior based on type

**Example:**

```typescript
interface PaymentMethod {
  processPayment(amount: number): void;
}

class CreditCard implements PaymentMethod {
  processPayment(amount: number): void {
    console.log(`Processing $${amount} via Credit Card`);
    // Credit card specific logic
  }
}

class PayPal implements PaymentMethod {
  processPayment(amount: number): void {
    console.log(`Processing $${amount} via PayPal`);
    // PayPal specific logic
  }
}

class Cryptocurrency implements PaymentMethod {
  processPayment(amount: number): void {
    console.log(`Processing $${amount} via Cryptocurrency`);
    // Crypto specific logic
  }
}

// Polymorphism in action
class PaymentProcessor {
  // Accepts ANY PaymentMethod implementation
  processPayment(method: PaymentMethod, amount: number): void {
    method.processPayment(amount);
    // Same code works for all payment methods!
  }
}

// Usage
const processor = new PaymentProcessor();
processor.processPayment(new CreditCard(), 100); // "Processing $100 via Credit Card"
processor.processPayment(new PayPal(), 50); // "Processing $50 via PayPal"
processor.processPayment(new Cryptocurrency(), 200); // "Processing $200 via Cryptocurrency"

// Add new payment method? No changes to PaymentProcessor!
```

**Benefits:**

- Flexible, extensible code
- Same interface, different behavior
- Easy to add new implementations
- Code to interface, not implementation

---

**Common Interview Follow-ups:**

**Q:** When to prefer composition over inheritance?

**A:**

- **Composition (has-a):** More flexible, mix multiple behaviors
- **Inheritance (is-a):** True hierarchical relationship

```typescript
// Inheritance (is-a): Employee IS-A Person
class Person {
  constructor(public name: string) {}
}
class Employee extends Person {
  constructor(
    name: string,
    public employeeId: string,
  ) {
    super(name);
  }
}

// Composition (has-a): Car HAS-A Engine
class Car {
  constructor(private engine: Engine) {}
}
```

**Q:** Give an example of polymorphism in TypeScript/JavaScript

**A:** See PaymentMethod example above, or:

```typescript
// Different shapes, same interface
interface Shape {
  calculateArea(): number;
}

const shapes: Shape[] = [
  new Circle(5),
  new Rectangle(4, 6),
  new Triangle(3, 4),
];

shapes.forEach((shape) => {
  console.log(shape.calculateArea()); // Polymorphism!
  // Same method, different implementations
});
```

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

## SOLID Principles

**Q:** What does SOLID stand for?

**A:**

**S – Single Responsibility Principle**

**Definition:** A class/module should have **one reason to change** - one responsibility.

**Bad Example:**

```typescript
// ❌ Multiple responsibilities
class User {
  constructor(
    public name: string,
    public email: string,
  ) {}

  // Responsibility 1: User data
  validateEmail(): boolean {
    return /\S+@\S+\.\S+/.test(this.email);
  }

  // Responsibility 2: Database operations
  save(): void {
    // Save to database
  }

  // Responsibility 3: Email operations
  sendWelcomeEmail(): void {
    // Send email
  }

  // Responsibility 4: Logging
  logActivity(): void {
    // Log user actions
  }
}
// Changes to email service? Must modify User class
// Changes to database? Must modify User class
// Too many reasons to change!
```

**Good Example:**

```typescript
// ✓ Each class has ONE responsibility
class User {
  constructor(
    public name: string,
    public email: string,
  ) {}

  validateEmail(): boolean {
    return /\S+@\S+\.\S+/.test(this.email);
  }
}

class UserRepository {
  save(user: User): void {
    // Database operations
  }
}

class EmailService {
  sendWelcomeEmail(user: User): void {
    // Email operations
  }
}

class Logger {
  logActivity(message: string): void {
    // Logging
  }
}
```

---

**O – Open/Closed Principle**

**Definition:** **Open for extension, closed for modification** - add new functionality without changing existing code.

**Bad Example:**

```typescript
// ❌ Must modify class to add new discount type
class DiscountCalculator {
  calculate(type: string, price: number): number {
    if (type === "student") {
      return price * 0.9;
    } else if (type === "senior") {
      return price * 0.8;
    } else if (type === "employee") {
      return price * 0.75;
    }
    return price;
    // Adding new discount? Modify this class!
  }
}
```

**Good Example:**

```typescript
// ✓ Extend without modifying
interface DiscountStrategy {
  calculate(price: number): number;
}

class StudentDiscount implements DiscountStrategy {
  calculate(price: number): number {
    return price * 0.9;
  }
}

class SeniorDiscount implements DiscountStrategy {
  calculate(price: number): number {
    return price * 0.8;
  }
}

class EmployeeDiscount implements DiscountStrategy {
  calculate(price: number): number {
    return price * 0.75;
  }
}

// Add new military discount? Just create new class!
class MilitaryDiscount implements DiscountStrategy {
  calculate(price: number): number {
    return price * 0.85;
  }
}

// No modification to existing code
class DiscountCalculator {
  calculate(strategy: DiscountStrategy, price: number): number {
    return strategy.calculate(price);
  }
}
```

---

**L – Liskov Substitution Principle**

**Definition:** **Subtypes must be substitutable for their base types** - derived classes must be usable through base class interface without knowing the difference.

**Bad Example:**

```typescript
// ❌ Violates LSP
class Bird {
  fly(): void {
    console.log("Flying");
  }
}

class Penguin extends Bird {
  fly(): void {
    throw new Error("Penguins cannot fly!");
    // Breaks expectations of Bird class
  }
}

function makeBirdFly(bird: Bird) {
  bird.fly(); // Assumes all birds can fly!
}

makeBirdFly(new Penguin()); // ❌ Throws error!
```

**Good Example:**

```typescript
// ✓ Follows LSP
abstract class Bird {
  abstract move(): void;
}

class FlyingBird extends Bird {
  move(): void {
    console.log("Flying");
  }
}

class Penguin extends Bird {
  move(): void {
    console.log("Swimming");
  }
}

function makeBirdMove(bird: Bird) {
  bird.move(); // Works for all subtypes
}

makeBirdMove(new FlyingBird()); // ✓ Flying
makeBirdMove(new Penguin()); // ✓ Swimming
```

---

**I – Interface Segregation Principle**

**Definition:** **Prefer small, specific interfaces over "fat" ones** - clients shouldn't be forced to depend on methods they don't use.

**Bad Example:**

```typescript
// ❌ Fat interface - forces unnecessary implementation
interface Worker {
  work(): void;
  eat(): void;
  sleep(): void;
}

class HumanWorker implements Worker {
  work(): void {
    console.log("Working");
  }
  eat(): void {
    console.log("Eating");
  }
  sleep(): void {
    console.log("Sleeping");
  }
}

class RobotWorker implements Worker {
  work(): void {
    console.log("Working");
  }
  eat(): void {
    /* Robots don't eat! */
  } // ❌ Forced to implement
  sleep(): void {
    /* Robots don't sleep! */
  } // ❌ Forced to implement
}
```

**Good Example:**

```typescript
// ✓ Segregated interfaces
interface Workable {
  work(): void;
}

interface Eatable {
  eat(): void;
}

interface Sleepable {
  sleep(): void;
}

class HumanWorker implements Workable, Eatable, Sleepable {
  work(): void {
    console.log("Working");
  }
  eat(): void {
    console.log("Eating");
  }
  sleep(): void {
    console.log("Sleeping");
  }
}

class RobotWorker implements Workable {
  work(): void {
    console.log("Working");
  }
  // No need to implement eat/sleep!
}
```

---

**D – Dependency Inversion Principle**

**Definition:** **Depend on abstractions, not concrete implementations** - high-level modules shouldn't depend on low-level modules, both should depend on abstractions.

**Bad Example:**

```typescript
// ❌ Depends on concrete implementation
class MySQLDatabase {
  save(data: string): void {
    console.log("Saving to MySQL");
  }
}

class UserService {
  private database = new MySQLDatabase(); // ❌ Tight coupling

  saveUser(user: string): void {
    this.database.save(user);
    // Changing to PostgreSQL? Must modify this class!
  }
}
```

**Good Example:**

```typescript
// ✓ Depend on abstraction
interface Database {
  save(data: string): void;
}

class MySQLDatabase implements Database {
  save(data: string): void {
    console.log("Saving to MySQL");
  }
}

class PostgreSQLDatabase implements Database {
  save(data: string): void {
    console.log("Saving to PostgreSQL");
  }
}

class UserService {
  // Depends on abstraction, not concrete class
  constructor(private database: Database) {}

  saveUser(user: string): void {
    this.database.save(user);
    // Can use ANY database implementation!
  }
}

// Usage
const mySQLService = new UserService(new MySQLDatabase());
const postgresService = new UserService(new PostgreSQLDatabase());
// Easy to swap!
```

**Example use case:** Use interfaces for external services (DIP)

```typescript
interface PaymentGateway {
  charge(amount: number): Promise<void>;
}

class StripeGateway implements PaymentGateway {
  async charge(amount: number): Promise<void> {
    // Stripe-specific code
  }
}

class PayPalGateway implements PaymentGateway {
  async charge(amount: number): Promise<void> {
    // PayPal-specific code
  }
}

class CheckoutService {
  constructor(private paymentGateway: PaymentGateway) {}

  async processCheckout(amount: number): Promise<void> {
    await this.paymentGateway.charge(amount);
    // Doesn't care if it's Stripe or PayPal!
  }
}
```

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

## Abstract Class vs Interface

**Q:** When would you use an abstract class vs an interface?

**A:**

**Abstract Class:**

**Definition:** Class that cannot be instantiated, can provide shared implementation and state.

**Characteristics:**

- Can have implementation (concrete methods)
- Can have state (properties)
- Can have constructors
- Single inheritance only (extend one class)

**When to use:**

- Need shared implementation
- Common state across subclasses
- Want to provide base functionality

**Example:**

```typescript
abstract class Animal {
  // State (properties)
  constructor(
    protected name: string,
    protected age: number,
  ) {}

  // Concrete method (shared implementation)
  getInfo(): string {
    return `${this.name} is ${this.age} years old`;
  }

  // Abstract method (must be implemented by subclasses)
  abstract makeSound(): void;
  abstract move(): void;
}

class Dog extends Animal {
  // Must implement abstract methods
  makeSound(): void {
    console.log("Woof!");
  }

  move(): void {
    console.log(`${this.name} is running`);
  }

  // Can use inherited method
  displayInfo(): void {
    console.log(this.getInfo()); // From base class
  }
}

class Cat extends Animal {
  makeSound(): void {
    console.log("Meow!");
  }

  move(): void {
    console.log(`${this.name} is walking`);
  }
}

// const animal = new Animal('Generic', 5); // ❌ Error: Cannot instantiate abstract class
const dog = new Dog("Buddy", 3); // ✓
dog.displayInfo(); // "Buddy is 3 years old"
dog.makeSound(); // "Woof!"
```

---

**Interface:**

**Definition:** Contract that defines structure only, no implementation.

**Characteristics:**

- No implementation (just signatures)
- No state (no properties with values)
- No constructors
- Multiple inheritance (implement many interfaces)

**When to use:**

- Define a contract only
- Need multiple inheritance
- No shared implementation needed
- Decouple classes

**Example:**

```typescript
interface Flyable {
  fly(): void;
  getAltitude(): number;
}

interface Swimmable {
  swim(): void;
  dive(depth: number): void;
}

// Can implement multiple interfaces
class Duck implements Flyable, Swimmable {
  fly(): void {
    console.log("Duck is flying");
  }

  getAltitude(): number {
    return 100;
  }

  swim(): void {
    console.log("Duck is swimming");
  }

  dive(depth: number): void {
    console.log(`Duck diving to ${depth}m`);
  }
}

class Airplane implements Flyable {
  fly(): void {
    console.log("Airplane is flying");
  }

  getAltitude(): number {
    return 10000;
  }
  // Doesn't need Swimmable
}
```

---

**Comparison:**

| Feature            | Abstract Class                       | Interface                 |
| ------------------ | ------------------------------------ | ------------------------- |
| **Implementation** | Can have methods with implementation | No implementation         |
| **State**          | Can have properties                  | No properties with values |
| **Constructor**    | Can have constructor                 | No constructor            |
| **Inheritance**    | Single (extend one)                  | Multiple (implement many) |
| **Use case**       | Shared base behavior                 | Define contract           |

---

**Rule of Thumb:**

**Use Abstract Class when:**

- Need **shared base behavior** or state
- Have common implementation for multiple subclasses
- Need constructor logic
- "is-a" relationship with shared functionality

**Use Interface when:**

- Define **capabilities/roles** (can fly, can swim)
- No shared implementation needed
- Need multiple inheritance
- Better for decoupling and testing

---

**Common Follow-ups:**

**Q:** Can a class implement multiple interfaces?

**A:** **Yes!**

```typescript
class Superhero implements Flyable, Swimmable, Fightable {
  // Implement all methods from all interfaces
}
```

**Q:** Can a class extend multiple classes?

**A:** **No!** Only single inheritance for classes.

```typescript
class Child extends Parent1, Parent2 {} // ❌ Error!
class Child extends Parent {} // ✓ Single inheritance only
```

**Q:** Can an interface extend multiple interfaces?

**A:** **Yes!**

```typescript
interface Superhero extends Flyable, Swimmable, Fightable {
  // Combines all three interfaces
}
```

**Q:** Can abstract classes implement interfaces?

**A:** **Yes!**

```typescript
abstract class Vehicle implements Drivable {
  // Can implement some methods, leave others abstract
  abstract start(): void;

  stop(): void {
    console.log("Vehicle stopped");
  }
}
```

---

**Real-world Example:**

```typescript
// Interface for contract
interface PaymentProcessor {
  processPayment(amount: number): Promise<void>;
}

// Abstract class for shared functionality
abstract class BasePaymentProcessor implements PaymentProcessor {
  // Shared state
  protected transactionId: string;

  // Shared implementation
  protected generateTransactionId(): string {
    return `TXN-${Date.now()}-${Math.random()}`;
  }

  protected logTransaction(amount: number): void {
    console.log(`Processing $${amount}, ID: ${this.transactionId}`);
  }

  // Force subclasses to implement
  abstract processPayment(amount: number): Promise<void>;
}

// Concrete implementations
class StripeProcessor extends BasePaymentProcessor {
  async processPayment(amount: number): Promise<void> {
    this.transactionId = this.generateTransactionId(); // Use shared method
    this.logTransaction(amount); // Use shared method
    // Stripe-specific logic
    console.log("Charged via Stripe");
  }
}

class PayPalProcessor extends BasePaymentProcessor {
  async processPayment(amount: number): Promise<void> {
    this.transactionId = this.generateTransactionId(); // Use shared method
    this.logTransaction(amount); // Use shared method
    // PayPal-specific logic
    console.log("Charged via PayPal");
  }
}
```

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

## Practical OOP & Design Situations

**Q:** What real-world situations show up where OOP/design principles matter?

**A:**

**1. Adding a New Payment Provider**

**Problem:** Need to add Stripe, PayPal, Square without changing existing code.

**Solution:** Interface (abstraction) + DIP

```typescript
// Interface defines contract
interface PaymentGateway {
  charge(amount: number): Promise<void>;
  refund(transactionId: string): Promise<void>;
}

// Implementations
class StripeGateway implements PaymentGateway {
  async charge(amount: number): Promise<void> {
    // Stripe-specific code
  }
  async refund(transactionId: string): Promise<void> {
    // Stripe refund
  }
}

class PayPalGateway implements PaymentGateway {
  async charge(amount: number): Promise<void> {
    // PayPal-specific code
  }
  async refund(transactionId: string): Promise<void> {
    // PayPal refund
  }
}

// Service depends on abstraction
class PaymentService {
  constructor(private gateway: PaymentGateway) {}

  async processPayment(amount: number): Promise<void> {
    await this.gateway.charge(amount);
    // Add new gateway? No changes here!
  }
}
```

**Principles used:** DIP, Open/Closed, Polymorphism

---

**2. Notification Channels (Email/SMS/Push)**

**Problem:** Avoid `if/else` chains, easy to add new channels.

**Solution:** Polymorphism + Strategy Pattern

```typescript
// ❌ Bad - if/else chain
function sendNotification(type: string, message: string) {
  if (type === "email") {
    // Send email
  } else if (type === "sms") {
    // Send SMS
  } else if (type === "push") {
    // Send push
  }
  // Adding new channel? Modify this function!
}

// ✓ Good - polymorphism
interface NotificationChannel {
  send(message: string): Promise<void>;
}

class EmailChannel implements NotificationChannel {
  async send(message: string): Promise<void> {
    console.log(`Email: ${message}`);
  }
}

class SMSChannel implements NotificationChannel {
  async send(message: string): Promise<void> {
    console.log(`SMS: ${message}`);
  }
}

class PushChannel implements NotificationChannel {
  async send(message: string): Promise<void> {
    console.log(`Push: ${message}`);
  }
}

class NotificationService {
  constructor(private channels: NotificationChannel[]) {}

  async notify(message: string): Promise<void> {
    await Promise.all(this.channels.map((channel) => channel.send(message)));
  }
}

// Usage
const service = new NotificationService([
  new EmailChannel(),
  new SMSChannel(),
  new PushChannel(),
]);

await service.notify("Hello!");
// Add Telegram channel? Just create new class!
```

**Principles used:** Open/Closed, Polymorphism, ISP

---

**3. Refactoring a "God" Service**

**Problem:** Massive service doing everything - hard to test, maintain.

**Solution:** SRP - Split by responsibility

```typescript
// ❌ Bad - God service
class UserService {
  createUser() {}
  updateUser() {}
  deleteUser() {}
  sendWelcomeEmail() {}
  logActivity() {}
  generateReport() {}
  validateEmail() {}
  hashPassword() {}
  // 50+ more methods...
}

// ✓ Good - split by responsibility
class UserService {
  constructor(
    private userRepository: UserRepository,
    private emailService: EmailService,
    private logger: Logger,
  ) {}

  async createUser(data: CreateUserDto): Promise<User> {
    const user = await this.userRepository.create(data);
    await this.emailService.sendWelcome(user.email);
    this.logger.log(`User created: ${user.id}`);
    return user;
  }
}

class UserRepository {
  create(data: CreateUserDto): Promise<User> {}
  update(id: string, data: UpdateUserDto): Promise<User> {}
  delete(id: string): Promise<void> {}
}

class EmailService {
  sendWelcome(email: string): Promise<void> {}
  sendPasswordReset(email: string): Promise<void> {}
}

class Logger {
  log(message: string): void {}
  error(message: string, error: Error): void {}
}

class UserValidator {
  validateEmail(email: string): boolean {}
}

class PasswordHasher {
  hash(password: string): string {}
  compare(password: string, hash: string): boolean {}
}
```

**Principles used:** SRP, DIP

---

**4. Swapping Data Source (REST → GraphQL)**

**Problem:** Change from REST API to GraphQL without breaking everything.

**Solution:** DIP - Depend on abstractions

```typescript
// Interface (abstraction)
interface UserDataSource {
  getUser(id: string): Promise<User>;
  getUsers(): Promise<User[]>;
}

// REST implementation
class RESTUserDataSource implements UserDataSource {
  async getUser(id: string): Promise<User> {
    const response = await fetch(`/api/users/${id}`);
    return response.json();
  }

  async getUsers(): Promise<User[]> {
    const response = await fetch("/api/users");
    return response.json();
  }
}

// GraphQL implementation
class GraphQLUserDataSource implements UserDataSource {
  async getUser(id: string): Promise<User> {
    const query = `query { user(id: "${id}") { id name email } }`;
    // GraphQL fetch logic
    return result.data.user;
  }

  async getUsers(): Promise<User[]> {
    const query = `query { users { id name email } }`;
    // GraphQL fetch logic
    return result.data.users;
  }
}

// Service doesn't care about implementation
class UserService {
  constructor(private dataSource: UserDataSource) {}

  async getUserProfile(id: string): Promise<User> {
    return this.dataSource.getUser(id);
    // Works with REST or GraphQL!
  }
}
```

**Principles used:** DIP, Open/Closed

---

**5. Shared Logic Across Similar Entities**

**Problem:** User, Admin, Guest have similar authentication logic.

**Solution:** Use composition before inheritance

```typescript
// ❌ Bad - deep inheritance
class Entity {}
class Person extends Entity {}
class User extends Person {}
class Admin extends User {}
// Deep hierarchy, hard to change

// ✓ Good - composition
class AuthenticationService {
  authenticate(credentials: Credentials): Promise<boolean> {}
}

class PermissionService {
  hasPermission(userId: string, permission: string): boolean {}
}

class User {
  constructor(
    private auth: AuthenticationService,
    private permissions: PermissionService,
  ) {}

  async login(credentials: Credentials): Promise<boolean> {
    return this.auth.authenticate(credentials);
  }

  canAccess(resource: string): boolean {
    return this.permissions.hasPermission(this.id, resource);
  }
}

class Admin {
  constructor(
    private auth: AuthenticationService,
    private permissions: PermissionService,
  ) {}

  // Reuses same services
}
```

**Principles used:** Composition over inheritance, SRP

---

**6. Feature Flagging Behavior**

**Problem:** Different behavior based on feature flags, avoid if/else.

**Solution:** Strategy pattern (polymorphism)

```typescript
// ❌ Bad - conditional logic everywhere
function processPayment(amount: number) {
  if (featureFlags.newPaymentFlow) {
    // New logic
  } else {
    // Old logic
  }
}

// ✓ Good - strategy pattern
interface PaymentStrategy {
  process(amount: number): Promise<void>;
}

class LegacyPaymentStrategy implements PaymentStrategy {
  async process(amount: number): Promise<void> {
    // Old logic
  }
}

class NewPaymentStrategy implements PaymentStrategy {
  async process(amount: number): Promise<void> {
    // New logic
  }
}

class PaymentService {
  private strategy: PaymentStrategy;

  constructor(featureFlags: FeatureFlags) {
    this.strategy = featureFlags.newPaymentFlow
      ? new NewPaymentStrategy()
      : new LegacyPaymentStrategy();
  }

  async process(amount: number): Promise<void> {
    return this.strategy.process(amount);
    // No conditionals in business logic!
  }
}
```

**Principles used:** Open/Closed, Polymorphism

---

**7. Large Interface with Unused Methods**

**Problem:** Interface forces implementation of unnecessary methods.

**Solution:** ISP - Split into smaller interfaces

```typescript
// ❌ Bad - fat interface
interface Document {
  open(): void;
  save(): void;
  print(): void;
  fax(): void;
  scan(): void;
  email(): void;
}

class PDFDocument implements Document {
  open() {}
  save() {}
  print() {}
  fax() {} // Don't need this!
  scan() {} // Or this!
  email() {}
}

// ✓ Good - segregated interfaces
interface Openable {
  open(): void;
}

interface Saveable {
  save(): void;
}

interface Printable {
  print(): void;
}

interface Emailable {
  email(): void;
}

class PDFDocument implements Openable, Saveable, Printable, Emailable {
  open() {}
  save() {}
  print() {}
  email() {}
  // Only implement what's needed!
}
```

**Principles used:** ISP

---

**8. Breaking API Changes from Base Class**

**Problem:** Changing base class breaks all subclasses (LSP violation).

**Solution:** Ensure substitutability

```typescript
// ❌ Bad - violates LSP
class Rectangle {
  constructor(
    protected width: number,
    protected height: number,
  ) {}

  setWidth(width: number) {
    this.width = width;
  }

  setHeight(height: number) {
    this.height = height;
  }

  getArea(): number {
    return this.width * this.height;
  }
}

class Square extends Rectangle {
  setWidth(width: number) {
    this.width = width;
    this.height = width; // Breaks expectations!
  }

  setHeight(height: number) {
    this.width = height;
    this.height = height; // Breaks expectations!
  }
}

function testRectangle(rect: Rectangle) {
  rect.setWidth(5);
  rect.setHeight(4);
  console.log(rect.getArea()); // Expects 20
}

testRectangle(new Square(3)); // Returns 16! (LSP violated)

// ✓ Good - separate interfaces
interface Shape {
  getArea(): number;
}

class Rectangle implements Shape {
  constructor(
    private width: number,
    private height: number,
  ) {}
  getArea(): number {
    return this.width * this.height;
  }
}

class Square implements Shape {
  constructor(private size: number) {}
  getArea(): number {
    return this.size * this.size;
  }
}
```

**Principles used:** LSP

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

## Extending Interfaces

**Q:** How do you extend interfaces in TypeScript?

**A:**

Use the `extends` keyword to inherit properties from parent interfaces.

**Basic Extension:**

```typescript
interface Person {
  name: string;
  age: number;
}

interface Employee extends Person {
  employeeId: string;
  department: string;
}

const employee: Employee = {
  name: "John",
  age: 30,
  employeeId: "E123",
  department: "Engineering",
};
```

**Multiple Interface Extension:**

```typescript
interface Nameable {
  name: string;
}

interface Ageable {
  age: number;
}

interface Emailable {
  email: string;
}

// Extend multiple interfaces
interface User extends Nameable, Ageable, Emailable {
  id: string;
}

const user: User = {
  id: "1",
  name: "John",
  age: 30,
  email: "john@example.com",
};
```

**Overriding Properties:**

```typescript
interface Animal {
  name: string;
  age: number | string; // Can be number or string
}

interface Dog extends Animal {
  age: number; // Narrow the type
  breed: string;
}
```

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

## Union and Intersection Types

**Q:** What's the difference between union and intersection types?

**A:**

**Union (|) - OR**

Value can be **one of several types**.

```typescript
// Can be string OR number
type ID = string | number;

const id1: ID = "123"; // ✓
const id2: ID = 123; // ✓
const id3: ID = true; // ❌ Error

// Union of object types
type Dog = {
  bark(): void;
};

type Cat = {
  meow(): void;
};

type Pet = Dog | Cat;

const myPet: Pet = { bark: () => console.log("Woof!") }; // ✓ Dog
const yourPet: Pet = { meow: () => console.log("Meow!") }; // ✓ Cat
```

**Intersection (&) - AND**

Value must have **all properties** from all types.

```typescript
// Must have properties from BOTH types
type Person = {
  name: string;
  age: number;
};

type Employee = {
  employeeId: string;
  department: string;
};

type EmployeePerson = Person & Employee;

const employee: EmployeePerson = {
  name: "John",
  age: 30,
  employeeId: "E123",
  department: "Engineering",
  // Must have ALL properties
};
```

**Use Cases:**

**Union - Flexible inputs:**

```typescript
function formatId(id: string | number): string {
  return `ID: ${id}`;
}

formatId(123); // ✓
formatId("ABC"); // ✓
```

**Intersection - Compose types:**

```typescript
type Timestamped = {
  createdAt: Date;
  updatedAt: Date;
};

type User = {
  id: string;
  name: string;
};

// Combine multiple types
type TimestampedUser = User & Timestamped;

const user: TimestampedUser = {
  id: "1",
  name: "John",
  createdAt: new Date(),
  updatedAt: new Date(),
};
```

**Discriminated Unions:**

```typescript
type Success = {
  status: "success";
  data: any;
};

type Error = {
  status: "error";
  error: string;
};

type Result = Success | Error;

function handleResult(result: Result) {
  if (result.status === "success") {
    console.log(result.data); // TypeScript knows it's Success
  } else {
    console.log(result.error); // TypeScript knows it's Error
  }
}
```

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

## Generics

**Q:** What are generics in TypeScript and why use them?

**A:**

**Definition:** Create reusable components that work with **multiple types** while maintaining type safety.

**Basic Generic Function:**

```typescript
// Without generics - loses type information
function identity(arg: any): any {
  return arg;
}

const result = identity("hello"); // result is 'any'

// With generics - preserves type
function identityGeneric<T>(arg: T): T {
  return arg;
}

const result1 = identityGeneric<string>("hello"); // result1 is 'string'
const result2 = identityGeneric<number>(123); // result2 is 'number'
const result3 = identityGeneric(true); // Type inferred as 'boolean'
```

**Generic Array Function:**

```typescript
function getFirstElement<T>(arr: T[]): T | undefined {
  return arr[0];
}

const firstNum = getFirstElement([1, 2, 3]); // number | undefined
const firstStr = getFirstElement(["a", "b"]); // string | undefined
```

**Generic Interface:**

```typescript
interface Box<T> {
  value: T;
}

const numberBox: Box<number> = { value: 123 };
const stringBox: Box<string> = { value: "hello" };
```

**Generic Class:**

```typescript
class DataHolder<T> {
  private data: T;

  constructor(data: T) {
    this.data = data;
  }

  getData(): T {
    return this.data;
  }

  setData(data: T): void {
    this.data = data;
  }
}

const numberHolder = new DataHolder<number>(123);
numberHolder.setData(456); // ✓
// numberHolder.setData('hello'); // ❌ Error: string not assignable to number

const stringHolder = new DataHolder<string>("hello");
```

**Commonly Used Generics:**

**Arrays:**

```typescript
const numbers: Array<number> = [1, 2, 3];
// Same as: const numbers: number[] = [1, 2, 3];
```

**Promises:**

```typescript
async function fetchUser(): Promise<User> {
  const response = await fetch("/api/user");
  return response.json(); // Returns Promise<User>
}

const user: User = await fetchUser();
```

**React State:**

```typescript
const [count, setCount] = useState<number>(0);
const [user, setUser] = useState<User | null>(null);
```

**Why Use Generics:**

- **Type safety without losing type information**
- **Reusable across multiple types**
- **Catch errors at compile time**
- **Better IDE autocomplete**

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

[← Back to Main Appendix](README.md#appendix)
