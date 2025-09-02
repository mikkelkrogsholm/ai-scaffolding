# The Ultimate Guide to BEST-PRACTICES.md - Code Craftsmanship Standards

## What BEST-PRACTICES.md is for

This document defines how we write code, not just what it does. It's the difference between code that works and code that works, scales, and doesn't make future developers (including yourself) cry. These are proven patterns that reduce bugs, improve readability, and make codebases maintainable for years.

---

## Core principles

1. **Separation of Concerns:** Each module/class/function does ONE thing well
2. **Small is Beautiful:** Short files, short functions, short lines
3. **Explicit > Implicit:** Clear names, obvious flow, no magic
4. **Composition > Inheritance:** Build with blocks, not hierarchies
5. **Fail Fast, Succeed Clearly:** Validate early, return early
6. **Test as You Go:** Not after, not later - NOW
7. **Optimize for Reading:** Code is read 10x more than written

---

## File organization standards

### Maximum file lengths
```
Controllers: 100 lines max
Services: 200 lines max
Components: 150 lines max
Utilities: 100 lines max
Tests: 300 lines max (can be longer due to test data)
```

### File naming conventions
```
✅ DO:
user.controller.ts       - REST endpoints only
user.service.ts          - Business logic
user.repository.ts       - Data access
user.validator.ts        - Input validation
user.types.ts           - Type definitions
user.test.ts            - Tests
user.mock.ts            - Test mocks

❌ DON'T:
userStuff.ts            - Vague naming
index.ts                - When it does more than export
utils.ts                - Grab-bag of random functions
helpers.ts              - Just another utils.ts
everything.ts           - 1000+ line monsters
```

### Folder structure patterns

#### Backend (Node/Python/Go)
```
src/
├── controllers/        # HTTP layer only
│   └── user.controller.ts
├── services/          # Business logic
│   └── user.service.ts
├── repositories/      # Data access
│   └── user.repository.ts
├── models/           # Data models
│   └── user.model.ts
├── validators/       # Input validation
│   └── user.validator.ts
├── middleware/       # Cross-cutting concerns
│   └── auth.middleware.ts
├── utils/           # Pure functions
│   └── crypto.util.ts
└── types/           # Shared types
    └── user.types.ts
```

#### Frontend (React/Vue/Angular)
```
src/
├── components/        # Presentational components
│   └── UserCard/
│       ├── UserCard.tsx
│       ├── UserCard.styles.ts
│       └── UserCard.test.tsx
├── containers/       # Smart components
│   └── UserProfile/
├── hooks/           # Custom hooks
│   └── useUser.ts
├── services/        # API calls
│   └── userService.ts
├── store/           # State management
│   └── userSlice.ts
├── utils/           # Helpers
└── types/           # TypeScript types
```

---

## Function & method standards

### Function length
```typescript
// ✅ GOOD: Single responsibility, <20 lines
function calculateDiscount(price: number, tier: CustomerTier): number {
  if (tier === CustomerTier.GOLD) {
    return price * 0.2;
  }
  if (tier === CustomerTier.SILVER) {
    return price * 0.1;
  }
  return 0;
}

// ❌ BAD: Multiple responsibilities, too long
function processOrder(order: Order): void {
  // Validate
  if (!order.items) { /* ... */ }
  // Calculate price
  let total = 0;
  for (const item of order.items) { /* ... */ }
  // Apply discount
  if (order.customer.tier) { /* ... */ }
  // Send email
  emailService.send(/* ... */);
  // Update inventory
  inventory.reduce(/* ... */);
  // Log
  logger.info(/* ... */);
  // ... 100 more lines
}
```

### Parameter count
```typescript
// ✅ GOOD: 3 or fewer parameters
function createUser(email: string, name: string, role: Role): User

// ✅ GOOD: Object for many params
function createUser(data: CreateUserDto): User

// ❌ BAD: Too many parameters
function createUser(
  email: string, 
  name: string, 
  role: Role,
  department: string,
  manager: string,
  startDate: Date,
  salary: number
): User
```

### Early returns
```typescript
// ✅ GOOD: Fail fast
function processPayment(payment: Payment): Result {
  if (!payment.isValid()) {
    return Result.error('Invalid payment');
  }
  
  if (!payment.hasFunds()) {
    return Result.error('Insufficient funds');
  }
  
  // Happy path
  return Result.success(payment.process());
}

// ❌ BAD: Nested nightmare
function processPayment(payment: Payment): Result {
  if (payment.isValid()) {
    if (payment.hasFunds()) {
      // Happy path buried deep
      return Result.success(payment.process());
    } else {
      return Result.error('Insufficient funds');
    }
  } else {
    return Result.error('Invalid payment');
  }
}
```

---

## Naming conventions

### Variables & functions
```typescript
// ✅ GOOD: Clear, specific names
const userEmail = 'john@example.com';
const isLoggedIn = true;
const hasPermission = checkPermission(user, resource);
function calculateTotalPrice(items: Item[]): number
function sendEmailToUser(user: User, template: string): void

// ❌ BAD: Vague, abbreviated, or misleading
const e = 'john@example.com';  // What's 'e'?
const flag = true;              // What flag?
const check = checkPermission(); // Check what?
function calc(x: any[]): number  // Calc what?
function process(data: any): void // Too generic
```

### Classes & interfaces
```typescript
// ✅ GOOD: Noun for classes, descriptive
class UserRepository { }
class EmailService { }
interface PaymentGateway { }
type UserCredentials = { }

// ❌ BAD: Verbs, generic, or Hungarian notation
class ProcessUser { }      // Verb, not noun
class Manager { }         // Manager of what?
interface IUser { }       // Don't prefix with 'I'
type TString = string;    // Don't prefix with 'T'
```

### Constants
```typescript
// ✅ GOOD: SCREAMING_SNAKE_CASE for true constants
const MAX_RETRY_ATTEMPTS = 3;
const DEFAULT_TIMEOUT_MS = 5000;
const API_BASE_URL = 'https://api.example.com';

// ✅ ALSO GOOD: Object freeze for config
const CONFIG = Object.freeze({
  maxRetries: 3,
  timeoutMs: 5000,
  apiUrl: 'https://api.example.com'
});

// ❌ BAD: Unclear if constant
const maxRetries = 3;  // Looks mutable
let MAX_RETRIES = 3;   // Actually mutable!
```

---

## Error handling patterns

### Explicit error handling
```typescript
// ✅ GOOD: Explicit error types
class ValidationError extends Error {
  constructor(public field: string, public value: any) {
    super(`Invalid ${field}: ${value}`);
  }
}

function parseUser(data: unknown): Result<User, ValidationError> {
  if (!isValidEmail(data.email)) {
    return Result.error(new ValidationError('email', data.email));
  }
  return Result.success(new User(data));
}

// ❌ BAD: Generic errors
function parseUser(data: any): User {
  if (!data.email) {
    throw new Error('Bad input'); // What's bad about it?
  }
  return new User(data);
}
```

### Never swallow errors
```typescript
// ✅ GOOD: Handle or re-throw
try {
  await saveUser(user);
} catch (error) {
  logger.error('Failed to save user', { error, userId: user.id });
  throw new ServiceError('User save failed', error);
}

// ❌ BAD: Silent failure
try {
  await saveUser(user);
} catch (error) {
  // Silently swallowed!
}
```

---

## Dependency patterns

### Dependency injection
```typescript
// ✅ GOOD: Dependencies injected
class UserService {
  constructor(
    private repository: UserRepository,
    private emailService: EmailService
  ) {}
  
  async createUser(data: CreateUserDto): Promise<User> {
    const user = await this.repository.save(data);
    await this.emailService.sendWelcome(user);
    return user;
  }
}

// ❌ BAD: Hidden dependencies
class UserService {
  async createUser(data: CreateUserDto): Promise<User> {
    const repository = new UserRepository(); // Hidden!
    const emailService = new EmailService(); // Hidden!
    const user = await repository.save(data);
    await emailService.sendWelcome(user);
    return user;
  }
}
```

### Interface segregation
```typescript
// ✅ GOOD: Small, focused interfaces
interface Reader {
  read(id: string): Promise<Data>;
}

interface Writer {
  write(data: Data): Promise<void>;
}

class Repository implements Reader, Writer {
  // Implementation
}

// ❌ BAD: Fat interface
interface DataManager {
  read(id: string): Promise<Data>;
  write(data: Data): Promise<void>;
  delete(id: string): Promise<void>;
  backup(): Promise<void>;
  restore(): Promise<void>;
  validate(): Promise<boolean>;
  // ... 20 more methods
}
```

---

## Async/await patterns

### Proper async handling
```typescript
// ✅ GOOD: Clean async/await
async function fetchUserData(userId: string): Promise<UserData> {
  const user = await userService.getUser(userId);
  const posts = await postService.getUserPosts(userId);
  const profile = await profileService.getProfile(userId);
  
  return { user, posts, profile };
}

// ✅ BETTER: Parallel when possible
async function fetchUserData(userId: string): Promise<UserData> {
  const [user, posts, profile] = await Promise.all([
    userService.getUser(userId),
    postService.getUserPosts(userId),
    profileService.getProfile(userId)
  ]);
  
  return { user, posts, profile };
}

// ❌ BAD: Callback hell / Promise chains
function fetchUserData(userId: string, callback: Function) {
  userService.getUser(userId).then(user => {
    postService.getUserPosts(userId).then(posts => {
      profileService.getProfile(userId).then(profile => {
        callback({ user, posts, profile });
      });
    });
  });
}
```

---

## Testing patterns

### Test structure
```typescript
// ✅ GOOD: Arrange-Act-Assert pattern
describe('UserService', () => {
  describe('createUser', () => {
    it('should create a user with valid data', async () => {
      // Arrange
      const userData = { email: 'test@example.com', name: 'Test' };
      const repository = new MockUserRepository();
      const service = new UserService(repository);
      
      // Act
      const result = await service.createUser(userData);
      
      // Assert
      expect(result.email).toBe(userData.email);
      expect(repository.saveCalled).toBe(true);
    });
  });
});

// ❌ BAD: No structure, multiple assertions mixed
it('test user stuff', async () => {
  const service = new UserService();
  const user1 = await service.createUser({ email: 'a@b.com' });
  expect(user1).toBeTruthy();
  const user2 = await service.getUser(user1.id);
  expect(user2.email).toBe('a@b.com');
  await service.deleteUser(user1.id);
  expect(await service.getUser(user1.id)).toBeNull();
});
```

### Test naming
```typescript
// ✅ GOOD: Descriptive test names
describe('PaymentProcessor', () => {
  it('should successfully process valid payment', () => {});
  it('should reject payment with insufficient funds', () => {});
  it('should retry failed payments up to 3 times', () => {});
});

// ❌ BAD: Vague test names
describe('PaymentProcessor', () => {
  it('works', () => {});
  it('handles errors', () => {});
  it('test 3', () => {});
});
```

---

## Code comments

### When to comment
```typescript
// ✅ GOOD: Explain WHY, not WHAT
// We need to retry 3 times because the payment gateway
// occasionally returns false negatives during high load
const MAX_RETRIES = 3;

// Using a WeakMap here to prevent memory leaks when
// components are destroyed but cache entries remain
const cache = new WeakMap();

// ❌ BAD: Obvious comments
// Set user name to "John"
user.name = "John";

// Increment counter by 1
counter++;

// This is a for loop
for (let i = 0; i < 10; i++) {
```

### Documentation comments
```typescript
// ✅ GOOD: JSDoc for public APIs
/**
 * Calculates compound interest
 * @param principal - Initial amount in cents
 * @param rate - Annual interest rate as decimal (0.05 = 5%)
 * @param time - Time period in years
 * @param compound - Compounding frequency per year
 * @returns Final amount in cents
 * @throws {ValidationError} If any parameter is negative
 * @example
 * calculateInterest(100000, 0.05, 10, 12) // Monthly compounding
 */
function calculateInterest(
  principal: number,
  rate: number,
  time: number,
  compound: number
): number
```

---

## Performance patterns

### Avoid premature optimization
```typescript
// ✅ GOOD: Clear, then optimize if needed
function findUser(users: User[], email: string): User | undefined {
  return users.find(u => u.email === email);
}

// ❌ BAD: Premature optimization
function findUser(users: User[], email: string): User | undefined {
  // "Optimized" with binary search on unsorted array!
  let left = 0, right = users.length - 1;
  // ... complex but broken implementation
}
```

### Measure before optimizing
```typescript
// ✅ GOOD: Profile first
const start = performance.now();
const result = processLargeDataset(data);
const duration = performance.now() - start;

if (duration > 1000) {
  logger.warn(`Slow processing: ${duration}ms`);
  // Now we know it needs optimization
}
```

---

## Security patterns

### Input validation
```typescript
// ✅ GOOD: Validate all external input
function createUser(input: unknown): User {
  const validated = userSchema.parse(input); // Throws if invalid
  return new User(validated);
}

// ❌ BAD: Trust external input
function createUser(input: any): User {
  return new User(input); // SQL injection waiting to happen
}
```

### Never log sensitive data
```typescript
// ✅ GOOD: Sanitize before logging
logger.info('User login', { 
  userId: user.id,
  email: maskEmail(user.email),
  timestamp: Date.now()
});

// ❌ BAD: Logging passwords/tokens
logger.info('User login', {
  email: user.email,
  password: user.password,  // NEVER!
  token: user.authToken     // NEVER!
});
```

---

## Anti-patterns to avoid

### God objects
```typescript
// ❌ BAD: Class that does everything
class UserManager {
  createUser() {}
  deleteUser() {}
  validateUser() {}
  sendEmail() {}
  generateReport() {}
  backupDatabase() {}
  renderUI() {}
  // ... 50 more methods
}

// ✅ GOOD: Separated concerns
class UserRepository { /* CRUD operations */ }
class UserValidator { /* Validation logic */ }
class EmailService { /* Email operations */ }
class ReportGenerator { /* Reporting */ }
```

### Magic numbers/strings
```typescript
// ❌ BAD: Magic values
if (user.age > 17) { // What's special about 17?
  if (user.type === 'p') { // What's 'p'?
    return price * 0.8; // Why 0.8?
  }
}

// ✅ GOOD: Named constants
const MINIMUM_AGE = 18;
const PREMIUM_USER_TYPE = 'premium';
const PREMIUM_DISCOUNT = 0.2;

if (user.age >= MINIMUM_AGE) {
  if (user.type === PREMIUM_USER_TYPE) {
    return price * (1 - PREMIUM_DISCOUNT);
  }
}
```

### Callback hell
```typescript
// ❌ BAD: Nested callbacks
getUserData(userId, (err, user) => {
  if (err) return handleError(err);
  getUserPosts(user.id, (err, posts) => {
    if (err) return handleError(err);
    getPostComments(posts, (err, comments) => {
      if (err) return handleError(err);
      // Finally do something...
    });
  });
});

// ✅ GOOD: Async/await
try {
  const user = await getUserData(userId);
  const posts = await getUserPosts(user.id);
  const comments = await getPostComments(posts);
  // Do something...
} catch (error) {
  handleError(error);
}
```

---

## Copy-paste checklist

```markdown
## Code Review Checklist

### File Organization
- [ ] Files under 200 lines
- [ ] Single responsibility per file
- [ ] Clear, descriptive filenames
- [ ] Proper folder structure

### Functions
- [ ] Under 20 lines each
- [ ] Single responsibility
- [ ] 3 or fewer parameters
- [ ] Early returns for error cases
- [ ] Descriptive names

### Error Handling
- [ ] All errors caught or thrown
- [ ] Specific error types
- [ ] Proper logging
- [ ] No silent failures

### Testing
- [ ] Unit tests for new code
- [ ] Arrange-Act-Assert pattern
- [ ] Descriptive test names
- [ ] Edge cases covered

### Security
- [ ] Input validation
- [ ] No sensitive data in logs
- [ ] SQL injection prevention
- [ ] XSS prevention

### Performance
- [ ] No premature optimization
- [ ] Async operations parallelized
- [ ] Database queries optimized
- [ ] Caching where appropriate

### Documentation
- [ ] Public APIs documented
- [ ] Complex logic explained
- [ ] README updated
- [ ] Examples provided
```

---

## Final note

Great code reads like well-written prose. It tells a story, has a clear flow, and doesn't make the reader guess. Every variable name, every function, every file structure decision should make the next developer's life easier. That next developer is probably you in six months.