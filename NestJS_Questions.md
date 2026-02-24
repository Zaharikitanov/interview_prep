# NestJS Interview Questions

[← Back to Main Appendix](README.md#appendix)

---

## Table of Contents

- [Modules and Providers](#modules-and-providers)
- [Dependency Injection](#dependency-injection)
- [Guards & Pipes](#guards--pipes)
- [Decorator Execution Order](#decorator-execution-order)
- [Interceptors](#interceptors)
- [Custom Decorators](#custom-decorators)

---

## Modules and Providers

**Q:** What are Modules and Providers in NestJS?

**A:**

**Modules:**

**Definition:** Organizational units that group related components (controllers, providers, etc.) together.

**Key characteristics:**

- Use `@Module()` decorator
- Define what the module imports, exports, and provides
- Every NestJS app has at least one root module
- Help organize application structure and manage dependencies

**Module decorator properties:**

```typescript
@Module({
  imports: [OtherModule], // Modules this module depends on
  controllers: [UserController], // Controllers in this module
  providers: [UserService], // Providers (services) in this module
  exports: [UserService], // Make available to other modules
})
export class UserModule {}
```

**Types of modules:**

1. **Feature Modules** - Organize code by feature (UserModule, AuthModule)
2. **Shared Modules** - Provide common functionality (DatabaseModule, ConfigModule)
3. **Global Modules** - Available everywhere with `@Global()` decorator
4. **Dynamic Modules** - Configured at runtime (`.forRoot()`, `.forFeature()`)

**Example - Feature Module:**

```typescript
@Module({
  imports: [DatabaseModule],
  controllers: [UserController],
  providers: [UserService, UserRepository],
  exports: [UserService], // Other modules can use UserService
})
export class UserModule {}
```

**Providers:**

**Definition:** Classes that can be injected as dependencies. Usually services, repositories, factories, helpers.

**Key characteristics:**

- Use `@Injectable()` decorator
- Managed by NestJS IoC (Inversion of Control) container
- Can be injected into controllers and other providers
- Default scope is Singleton (one instance shared)

**Example - Service Provider:**

```typescript
@Injectable()
export class UserService {
  constructor(
    private userRepository: UserRepository,
    private emailService: EmailService,
  ) {}

  async createUser(data: CreateUserDto) {
    const user = await this.userRepository.create(data);
    await this.emailService.sendWelcome(user.email);
    return user;
  }
}
```

**Provider types:**

```typescript
// 1. Class provider (most common)
{
  provide: UserService,
  useClass: UserService
}

// 2. Value provider
{
  provide: 'CONFIG',
  useValue: { apiKey: '123' }
}

// 3. Factory provider
{
  provide: 'DATABASE_CONNECTION',
  useFactory: async (config: ConfigService) => {
    return await createConnection(config.get('DB_URL'));
  },
  inject: [ConfigService]
}

// 4. Existing provider (alias)
{
  provide: 'AliasService',
  useExisting: UserService
}
```

**Scope options:**

```typescript
@Injectable({ scope: Scope.DEFAULT })     // Singleton (shared across app)
@Injectable({ scope: Scope.REQUEST })     // New instance per request
@Injectable({ scope: Scope.TRANSIENT })   // New instance every time injected
```

**Benefits:**

- Clear separation of concerns
- Dependency management
- Reusability across the application
- Easy testing (can mock providers)
- Encapsulation of related functionality

**Red flags:**

- Circular dependencies between modules
- Not exporting providers that other modules need
- Overusing global modules
- Putting business logic in controllers instead of services

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

## Dependency Injection

**Q:** How does dependency injection work in NestJS?

**A:**

**Definition:** Design pattern where dependencies are provided to a class rather than the class creating them itself. NestJS has a built-in IoC (Inversion of Control) container that manages dependencies.

**How it works:**

1. **Mark class as injectable** with `@Injectable()`
2. **Register provider** in module
3. **Inject via constructor** where needed
4. **NestJS resolves** and provides the instance

**Example:**

```typescript
// 1. Mark service as injectable
@Injectable()
export class UserService {
  findAll() {
    return ["user1", "user2"];
  }
}

// 2. Register in module
@Module({
  providers: [UserService], // NestJS will manage this
  controllers: [UserController],
})
export class UserModule {}

// 3. Inject via constructor
@Controller("users")
export class UserController {
  constructor(private userService: UserService) {}

  @Get()
  getUsers() {
    return this.userService.findAll();
  }
}
```

**Benefits:**

1. **Loose coupling** - Classes don't create their dependencies
2. **Testability** - Easy to mock dependencies in tests
3. **Reusability** - Same service instance can be shared
4. **Maintainability** - Change implementations without changing consumers

**Constructor-based injection (recommended):**

```typescript
@Injectable()
export class UserService {
  constructor(
    private userRepository: UserRepository,
    private emailService: EmailService,
    @Inject("CONFIG") private config: Config,
  ) {}
}
```

**Property-based injection (not recommended):**

```typescript
@Injectable()
export class UserService {
  @Inject(UserRepository)
  private userRepository: UserRepository;
}
```

**Scopes:**

**1. DEFAULT (Singleton) - Most common:**

```typescript
@Injectable() // Same instance shared across entire app
export class UserService {}
```

- One instance created and reused
- Best for stateless services
- Most performant
- Default behavior

**2. REQUEST - New instance per HTTP request:**

```typescript
@Injectable({ scope: Scope.REQUEST })
export class RequestContextService {
  constructor(@Inject(REQUEST) private request: Request) {}
}
```

- New instance for each request
- Useful for request-specific data
- Higher memory/CPU usage
- Can access request object

**3. TRANSIENT - New instance every time:**

```typescript
@Injectable({ scope: Scope.TRANSIENT })
export class TransientService {}
```

- New instance each time injected
- Rarely used
- Useful for per-operation state

**Custom providers:**

```typescript
// Token-based injection
const CONFIG = 'CONFIG';

@Module({
  providers: [
    {
      provide: CONFIG,
      useValue: { apiKey: 'abc123' }
    }
  ]
})
export class AppModule {}

// Usage
constructor(@Inject(CONFIG) private config: any) {}
```

**Factory providers with async:**

```typescript
{
  provide: 'DATABASE_CONNECTION',
  useFactory: async (config: ConfigService): Promise<Connection> => {
    const connection = await createConnection({
      host: config.get('DB_HOST'),
      port: config.get('DB_PORT')
    });
    return connection;
  },
  inject: [ConfigService] // Dependencies for factory
}
```

**Optional dependencies:**

```typescript
@Injectable()
export class UserService {
  constructor(
    @Optional() private logger?: LoggerService,
    @Inject("CACHE") private cache?: CacheService,
  ) {}
}
```

**Circular dependency resolution:**

```typescript
// Use forwardRef() to break circular dependencies
@Injectable()
export class UserService {
  constructor(
    @Inject(forwardRef(() => AuthService))
    private authService: AuthService,
  ) {}
}
```

**Testing with DI:**

```typescript
describe("UserService", () => {
  let service: UserService;
  let mockRepository: jest.Mocked<UserRepository>;

  beforeEach(async () => {
    mockRepository = {
      find: jest.fn(),
      create: jest.fn(),
    } as any;

    const module = await Test.createTestingModule({
      providers: [
        UserService,
        {
          provide: UserRepository,
          useValue: mockRepository, // Mock dependency
        },
      ],
    }).compile();

    service = module.get<UserService>(UserService);
  });

  it("should create user", async () => {
    mockRepository.create.mockResolvedValue({ id: 1, name: "John" });
    const result = await service.createUser({ name: "John" });
    expect(result).toEqual({ id: 1, name: "John" });
  });
});
```

**Red flags:**

- Circular dependencies (use forwardRef as last resort)
- Using property injection instead of constructor injection
- Not understanding scope implications
- Not registering providers in modules
- Injecting request-scoped providers into singleton providers

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

## Guards & Pipes

**Q:** What are Guards and Pipes in NestJS?

**A:**

**Guards:**

**Purpose:** Authorization and Authentication.

**When they run:** Before controller handler, after middleware.

**Use cases:**

- JWT validation
- Role-based access control (RBAC)
- Permission checks
- API key validation

**Example - Auth Guard:**

```typescript
@Injectable()
export class JwtAuthGuard implements CanActivate {
  constructor(private jwtService: JwtService) {}

  canActivate(context: ExecutionContext): boolean {
    const request = context.switchToHttp().getRequest();
    const token = request.headers.authorization?.split(" ")[1];

    if (!token) {
      throw new UnauthorizedException("No token provided");
    }

    try {
      const payload = this.jwtService.verify(token);
      request.user = payload; // Attach user to request
      return true; // Allow access
    } catch (error) {
      throw new UnauthorizedException("Invalid token");
    }
  }
}

// Usage
@Controller("users")
export class UserController {
  @Get("profile")
  @UseGuards(JwtAuthGuard)
  getProfile(@Request() req) {
    return req.user;
  }
}
```

**Example - Roles Guard:**

```typescript
@Injectable()
export class RolesGuard implements CanActivate {
  constructor(private reflector: Reflector) {}

  canActivate(context: ExecutionContext): boolean {
    const requiredRoles = this.reflector.get<string[]>('roles', context.getHandler());
    if (!requiredRoles) {
      return true;
    }

    const request = context.switchToHttp().getRequest();
    const user = request.user;

    return requiredRoles.some(role => user.roles.includes(role));
  }
}

// Custom decorator
export const Roles = (...roles: string[]) => SetMetadata('roles', roles);

// Usage
@Post()
@UseGuards(JwtAuthGuard, RolesGuard)
@Roles('admin', 'moderator')
create(@Body() data: CreateDto) {
  return this.service.create(data);
}
```

**Pipes:**

**Purpose:** Validation and Transformation.

**When they run:** Right before controller handler, after guards.

**Use cases:**

- DTO validation (class-validator)
- Type casting (string → number)
- Data transformation
- Parse parameters

**Built-in Pipes:**

- `ValidationPipe` - Validate DTOs
- `ParseIntPipe` - Parse string to integer
- `ParseBoolPipe` - Parse string to boolean
- `ParseArrayPipe` - Parse array
- `ParseUUIDPipe` - Validate and parse UUID

**Example - Validation Pipe:**

```typescript
// DTO with validation
import { IsString, IsEmail, MinLength } from 'class-validator';

export class CreateUserDto {
  @IsString()
  @MinLength(3)
  name: string;

  @IsEmail()
  email: string;
}

// Global validation pipe (main.ts)
app.useGlobalPipes(new ValidationPipe({
  whitelist: true, // Strip non-DTO properties
  forbidNonWhitelisted: true, // Throw error if extra properties
  transform: true // Auto-transform to DTO types
}));

// Controller
@Post()
create(@Body() createUserDto: CreateUserDto) {
  // createUserDto is validated automatically
  return this.userService.create(createUserDto);
}
```

**Example - Custom Pipe:**

```typescript
@Injectable()
export class ParseIdPipe implements PipeTransform {
  transform(value: any, metadata: ArgumentMetadata) {
    const id = parseInt(value, 10);
    if (isNaN(id)) {
      throw new BadRequestException('Invalid ID format');
    }
    return id;
  }
}

// Usage
@Get(':id')
findOne(@Param('id', ParseIdPipe) id: number) {
  return this.userService.findOne(id);
}
```

**Error Handling:**

**HttpException:**

```typescript
@Post()
create(@Body() data: CreateDto) {
  if (!data.name) {
    throw new HttpException('Name is required', HttpStatus.BAD_REQUEST);
  }
  return this.service.create(data);
}

// Or use specific exceptions
throw new BadRequestException('Invalid data');
throw new UnauthorizedException('Not authenticated');
throw new ForbiddenException('Insufficient permissions');
throw new NotFoundException('User not found');
```

**Global Exception Filter:**

```typescript
@Catch()
export class AllExceptionsFilter implements ExceptionFilter {
  catch(exception: unknown, host: ArgumentsHost) {
    const ctx = host.switchToHttp();
    const response = ctx.getResponse();
    const request = ctx.getRequest();

    const status =
      exception instanceof HttpException
        ? exception.getStatus()
        : HttpStatus.INTERNAL_SERVER_ERROR;

    const message =
      exception instanceof HttpException
        ? exception.getResponse()
        : "Internal server error";

    response.status(status).json({
      statusCode: status,
      timestamp: new Date().toISOString(),
      path: request.url,
      message,
    });
  }
}

// Register globally (main.ts)
app.useGlobalFilters(new AllExceptionsFilter());
```

**Key Differences:**

| Feature        | Guards             | Pipes                      |
| -------------- | ------------------ | -------------------------- |
| **Purpose**    | Auth/Authorization | Validation/Transformation  |
| **Runs**       | Before pipes       | After guards               |
| **Return**     | boolean            | Transformed value or throw |
| **Common use** | JWT, roles         | DTO validation, parsing    |

**Red flags:**

- Using guards for validation (use pipes)
- Using pipes for authorization (use guards)
- Not understanding execution order
- Throwing errors without proper HTTP status codes
- Not using built-in pipes when available

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

## Decorator Execution Order

**Q:** In what order are decorators executed in NestJS controllers?

**A:**

For an incoming HTTP request, the execution order is:

1. **Middleware** - Logging, CORS, body parsing
2. **Guards** - Authentication, role-based authorization
3. **Interceptors (before handler)** - Transform request, add timing
4. **Pipes** - Validate/transform DTOs, parse params
5. **Controller method (handler)** - Business logic execution
6. **Interceptors (after handler)** - Transform response, add metadata
7. **Exception Filters** - Catch errors, format error responses
8. **Response sent to client** - Final HTTP response

**One-line summary:**  
**Middleware → Guards → Interceptors → Pipes → Controller → Interceptors → Exception Filters**

**Visual Flow:**

```
Request
  ↓
Middleware (logging, CORS, body parser)
  ↓
Guards (authentication, authorization)
  ↓
Interceptors - before (transform request, timing start)
  ↓
Pipes (validation, transformation, parsing)
  ↓
Controller Handler (your business logic)
  ↓
Interceptors - after (transform response, timing end)
  ↓
Exception Filters (error handling if exception thrown)
  ↓
Response
```

**Notes:**

- **Guards run BEFORE pipes** - Check authorization before validation
- **Pipes run RIGHT BEFORE** the controller method
- **Interceptors wrap** the execution (before & after)
- **Exception filters** catch errors thrown anywhere below them
- **Middleware** runs first, outside the NestJS pipeline

**Example:**

```typescript
@Controller("users")
@UseInterceptors(LoggingInterceptor)
export class UserController {
  @Post()
  @UseGuards(JwtAuthGuard, RolesGuard)
  @UsePipes(ValidationPipe)
  @UseInterceptors(TransformInterceptor)
  create(@Body() createUserDto: CreateUserDto) {
    return this.userService.create(createUserDto);
  }
}

// Execution order for POST /users:
// 1. Middleware (body parser, logger)
// 2. LoggingInterceptor.intercept() - before
// 3. JwtAuthGuard.canActivate()
// 4. RolesGuard.canActivate()
// 5. TransformInterceptor.intercept() - before
// 6. ValidationPipe.transform() on createUserDto
// 7. UserController.create() - handler
// 8. TransformInterceptor.intercept() - after
// 9. LoggingInterceptor.intercept() - after
// 10. Response sent
// (If error at any point: Exception Filter catches it)
```

**Practical implications:**

**Why Guards before Pipes:**

- Don't waste time validating if user not authorized
- Security check happens first

**Why Interceptors wrap everything:**

- Can measure execution time (before + after)
- Transform both request and response
- Add metadata to responses

**Why Exception Filters are last:**

- Catch errors from any layer
- Centralized error formatting
- Consistent error responses

**Common Interview Follow-up:**

**Q:** If a guard throws an exception, do pipes run?

**A:** No. If a guard throws, execution stops and goes directly to exception filters. Pipes never run.

**Q:** Can an interceptor transform the request before it reaches the controller?

**A:** Yes. The "before" part of an interceptor runs after guards and before pipes, so it can transform the request.

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

## Interceptors

**Q:** What are Interceptors in NestJS and when would you use them?

**A:**

**Definition:** Classes with `@Injectable()` that implement `NestInterceptor` interface. Execute logic before and after route handler execution.

**Execution:**

- Run **before** the handler (can transform request)
- Run **after** the handler (can transform response)
- Wrap handler execution using RxJS operators
- Have access to ExecutionContext and response stream

**Interface:**

```typescript
export interface NestInterceptor {
  intercept(context: ExecutionContext, next: CallHandler): Observable<any>;
}
```

**Common Use Cases:**

**1. Response Transformation:**

```typescript
@Injectable()
export class TransformInterceptor implements NestInterceptor {
  intercept(context: ExecutionContext, next: CallHandler): Observable<any> {
    return next.handle().pipe(
      map((data) => ({
        success: true,
        data,
        timestamp: new Date().toISOString(),
      })),
    );
  }
}

// Before: { id: 1, name: 'John' }
// After:  { success: true, data: { id: 1, name: 'John' }, timestamp: '2024-01-15T10:30:00.000Z' }
```

**2. Logging & Monitoring:**

```typescript
@Injectable()
export class LoggingInterceptor implements NestInterceptor {
  constructor(private logger: Logger) {}

  intercept(context: ExecutionContext, next: CallHandler): Observable<any> {
    const request = context.switchToHttp().getRequest();
    const { method, url } = request;
    const now = Date.now();

    this.logger.log(`Incoming request: ${method} ${url}`);

    return next.handle().pipe(
      tap(() => {
        const responseTime = Date.now() - now;
        this.logger.log(`Response sent: ${method} ${url} - ${responseTime}ms`);
      }),
    );
  }
}
```

**3. Caching:**

```typescript
@Injectable()
export class CacheInterceptor implements NestInterceptor {
  constructor(private cacheManager: Cache) {}

  async intercept(
    context: ExecutionContext,
    next: CallHandler,
  ): Promise<Observable<any>> {
    const request = context.switchToHttp().getRequest();
    const cacheKey = request.url;

    // Check cache
    const cachedResponse = await this.cacheManager.get(cacheKey);
    if (cachedResponse) {
      return of(cachedResponse); // Return cached data
    }

    // Cache miss - execute handler and cache result
    return next.handle().pipe(
      tap((response) => {
        this.cacheManager.set(cacheKey, response, { ttl: 60 });
      }),
    );
  }
}
```

**4. Error Handling & Transformation:**

```typescript
@Injectable()
export class ErrorsInterceptor implements NestInterceptor {
  intercept(context: ExecutionContext, next: CallHandler): Observable<any> {
    return next.handle().pipe(
      catchError((err) => {
        // Add context to error
        const request = context.switchToHttp().getRequest();
        console.error(`Error in ${request.url}:`, err);

        // Transform error
        return throwError(
          () =>
            new InternalServerErrorException({
              message: "Something went wrong",
              originalError: err.message,
            }),
        );
      }),
    );
  }
}
```

**5. Timeout Management:**

```typescript
@Injectable()
export class TimeoutInterceptor implements NestInterceptor {
  constructor(private readonly timeout: number = 5000) {}

  intercept(context: ExecutionContext, next: CallHandler): Observable<any> {
    return next.handle().pipe(
      timeout(this.timeout),
      catchError((err) => {
        if (err.name === "TimeoutError") {
          return throwError(
            () => new RequestTimeoutException("Request timeout"),
          );
        }
        return throwError(() => err);
      }),
    );
  }
}
```

**6. Add Metadata to Response:**

```typescript
@Injectable()
export class MetadataInterceptor implements NestInterceptor {
  intercept(context: ExecutionContext, next: CallHandler): Observable<any> {
    const request = context.switchToHttp().getRequest();

    return next.handle().pipe(
      map((data) => ({
        ...data,
        _metadata: {
          requestId: request.id,
          userId: request.user?.id,
          version: "1.0",
        },
      })),
    );
  }
}
```

**Key Methods & RxJS Operators:**

```typescript
// tap - perform side effects
.pipe(tap(data => console.log(data)))

// map - transform data
.pipe(map(data => ({ result: data })))

// catchError - handle errors
.pipe(catchError(err => throwError(() => new Error('Failed'))))

// timeout - set timeout
.pipe(timeout(5000))
```

**Scope:**

**Global:**

```typescript
// main.ts
app.useGlobalInterceptors(new LoggingInterceptor());
```

**Controller level:**

```typescript
@Controller("users")
@UseInterceptors(TransformInterceptor)
export class UserController {}
```

**Route level:**

```typescript
@Get()
@UseInterceptors(CacheInterceptor)
findAll() {}
```

**Difference from Middleware:**

| Feature                | Middleware        | Interceptors     |
| ---------------------- | ----------------- | ---------------- |
| **Access to**          | Request/Response  | ExecutionContext |
| **Can access**         | Route handler? No | Yes              |
| **Timing**             | Before guards     | After guards     |
| **Transform response** | No                | Yes              |
| **RxJS**               | No                | Yes              |

**Red flags:**

- Using interceptors for authentication (use Guards)
- Heavy business logic in interceptors (keep them thin)
- Not understanding execution order
- Blocking operations without proper async handling
- Swallowing errors without re-throwing

**Best Practices:**

- Keep interceptors focused (single responsibility)
- Use for cross-cutting concerns (logging, caching, transformations)
- Leverage RxJS operators effectively
- Handle errors appropriately
- Document custom interceptors

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

## Custom Decorators

**Q:** How do you create and use custom decorators in NestJS?

**A:**

**Types of Custom Decorators:**

1. **Parameter Decorators** - Extract data from request
2. **Method Decorators** - Add metadata to routes
3. **Class Decorators** - Apply metadata to entire controller

**Common Use Cases:**

**1. User Extraction (Parameter Decorator):**

```typescript
// Create decorator
import { createParamDecorator, ExecutionContext } from '@nestjs/common';

export const CurrentUser = createParamDecorator(
  (data: unknown, ctx: ExecutionContext) => {
    const request = ctx.switchToHttp().getRequest();
    return request.user; // Assumes user is added by auth guard
  }
);

// Usage
@Get('profile')
@UseGuards(JwtAuthGuard)
getProfile(@CurrentUser() user: User) {
  return user; // Clean! No need for @Request() req and req.user
}
```

**2. Extract Specific User Property:**

```typescript
export const UserId = createParamDecorator(
  (data: unknown, ctx: ExecutionContext) => {
    const request = ctx.switchToHttp().getRequest();
    return request.user?.id;
  }
);

// Usage
@Post('posts')
createPost(@UserId() userId: string, @Body() data: CreatePostDto) {
  return this.postService.create(userId, data);
}
```

**3. Extract with Data Parameter:**

```typescript
export const User = createParamDecorator(
  (data: string, ctx: ExecutionContext) => {
    const request = ctx.switchToHttp().getRequest();
    const user = request.user;

    // If data provided, return specific property
    return data ? user?.[data] : user;
  }
);

// Usage
@Get('email')
getEmail(@User('email') email: string) {
  return { email };
}

@Get('profile')
getProfile(@User() user: User) {
  return user;
}
```

**4. Authorization (Method Decorator):**

```typescript
import { SetMetadata } from '@nestjs/common';

export const Roles = (...roles: string[]) => SetMetadata('roles', roles);

// Usage with Guard
@Post()
@UseGuards(JwtAuthGuard, RolesGuard)
@Roles('admin', 'moderator')
create(@Body() data: CreateDto) {
  return this.service.create(data);
}
```

**5. Public Route (Skip Auth):**

```typescript
export const IS_PUBLIC_KEY = 'isPublic';
export const Public = () => SetMetadata(IS_PUBLIC_KEY, true);

// Modified Auth Guard
@Injectable()
export class JwtAuthGuard implements CanActivate {
  constructor(private reflector: Reflector) {}

  canActivate(context: ExecutionContext): boolean {
    const isPublic = this.reflector.get<boolean>(
      IS_PUBLIC_KEY,
      context.getHandler()
    );
    if (isPublic) {
      return true; // Skip auth for public routes
    }
    // ... normal auth logic
  }
}

// Usage
@Get('public-data')
@Public()
getPublicData() {
  return { message: 'Available to everyone' };
}
```

**6. Request Data Extraction:**

```typescript
export const IpAddress = createParamDecorator(
  (data: unknown, ctx: ExecutionContext) => {
    const request = ctx.switchToHttp().getRequest();
    return request.ip;
  }
);

export const UserAgent = createParamDecorator(
  (data: unknown, ctx: ExecutionContext) => {
    const request = ctx.switchToHttp().getRequest();
    return request.headers['user-agent'];
  }
);

// Usage
@Post('event')
logEvent(
  @IpAddress() ip: string,
  @UserAgent() userAgent: string,
  @Body() data: EventDto
) {
  return this.analyticsService.log({ ip, userAgent, ...data });
}
```

**7. Combine Multiple Decorators:**

```typescript
import { applyDecorators } from '@nestjs/common';

export function Auth(...roles: string[]) {
  return applyDecorators(
    UseGuards(JwtAuthGuard, RolesGuard),
    Roles(...roles),
    ApiBearerAuth(), // For Swagger
    ApiUnauthorizedResponse({ description: 'Unauthorized' })
  );
}

// Usage - Clean single decorator
@Post()
@Auth('admin')
create(@Body() data: CreateDto) {
  return this.service.create(data);
}
```

**8. Validation & Transformation:**

```typescript
export const ValidateId = createParamDecorator(
  (data: unknown, ctx: ExecutionContext) => {
    const request = ctx.switchToHttp().getRequest();
    const id = request.params.id;

    if (!id || isNaN(parseInt(id))) {
      throw new BadRequestException('Invalid ID format');
    }

    return parseInt(id, 10);
  }
);

// Usage
@Get(':id')
findOne(@ValidateId() id: number) {
  return this.service.findOne(id);
}
```

**9. Extract Headers:**

```typescript
export const AuthToken = createParamDecorator(
  (data: unknown, ctx: ExecutionContext) => {
    const request = ctx.switchToHttp().getRequest();
    const authHeader = request.headers.authorization;
    return authHeader?.split(" ")[1]; // Extract "Bearer TOKEN"
  },
);
```

**Benefits:**

- **Cleaner, more readable controllers**
- **Reusable logic** across routes
- **Type safety** with TypeScript
- **Separation of concerns** - Extract cross-cutting logic
- **Easier testing** - Controllers have less code
- **Better documentation** - Decorators self-document intent

**Composition:**

```typescript
// Combine multiple decorators
export function AdminOnly() {
  return applyDecorators(
    UseGuards(JwtAuthGuard, RolesGuard),
    Roles('admin'),
    UseInterceptors(LoggingInterceptor),
    ApiBearerAuth(),
    ApiOperation({ summary: 'Admin only endpoint' })
  );
}

@Post('sensitive')
@AdminOnly()
sensitiveOperation(@CurrentUser() user: User, @Body() data: any) {
  return this.service.doSensitiveOperation(user, data);
}
```

**Testing Custom Decorators:**

```typescript
describe("CurrentUser decorator", () => {
  it("should extract user from request", () => {
    const mockUser = { id: 1, email: "test@example.com" };
    const mockContext = {
      switchToHttp: () => ({
        getRequest: () => ({ user: mockUser }),
      }),
    } as ExecutionContext;

    const result = CurrentUser(null, mockContext);
    expect(result).toEqual(mockUser);
  });
});
```

**Red flags:**

- Overusing decorators for simple logic
- Not understanding decorator execution context
- Creating decorators with side effects
- Poor naming conventions
- Not leveraging built-in decorators when available
- Complex logic in decorators (should be thin wrappers)

**Best Practices:**

- Keep decorators simple and focused
- Use descriptive names
- Document complex decorators
- Combine related decorators with `applyDecorators`
- Extract reusable patterns
- Type parameters properly

[↑ Back to Top](#table-of-contents) | [← Back to Appendix](README.md#appendix)

---

[← Back to Main Appendix](README.md#appendix)
