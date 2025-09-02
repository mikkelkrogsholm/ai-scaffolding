# The Ultimate Guide to ANTI-PATTERNS.md - What NOT to Do and Why

## What ANTI-PATTERNS.md is for

This document catalogs common coding mistakes that seem like good ideas but lead to maintenance nightmares, bugs, and technical debt. Each anti-pattern here is **FORBIDDEN** in the codebase. If you see these patterns, refactor them immediately.

---

## Core principle

**Every anti-pattern was once someone's "clever" solution.** They often work initially but create exponential problems as the codebase grows. This guide helps you recognize and avoid these traps.

---

## Architectural Anti-Patterns

### 🚫 Big Ball of Mud
**What it looks like:** No clear structure, everything connects to everything
**Why it happens:** Lack of planning, continuous "quick fixes"
**The damage:** Impossible to understand, modify, or test

```typescript
// ❌ ANTI-PATTERN: Everything in one place
class App {
  users = [];
  products = [];
  orders = [];
  
  constructor() {
    // Database connection
    this.db = new Database();
    
    // HTTP server
    this.server = express();
    
    // Email service
    this.mailer = nodemailer.createTransport();
    
    // Payment processing
    this.stripe = new Stripe();
    
    // Analytics
    this.analytics = new Analytics();
    
    // ... 500 more lines of initialization
  }
  
  async handleRequest(req, res) {
    // 1000 lines of mixed concerns
    if (req.path === '/user') {
      // User logic mixed with...
      const user = await this.db.query('SELECT...');
      // Email logic...
      await this.mailer.send(...);
      // Payment logic...
      await this.stripe.charge(...);
      // Analytics...
      this.analytics.track(...);
    }
    // ... continues forever
  }
}

// ✅ CORRECT: Separated concerns
class UserController {
  constructor(
    private userService: UserService,
    private emailService: EmailService
  ) {}
  
  async createUser(req: Request, res: Response) {
    const user = await this.userService.create(req.body);
    await this.emailService.sendWelcome(user);
    res.json(user);
  }
}
```

### 🚫 God Object
**What it looks like:** One class that does everything
**Why it happens:** Continuous feature additions to existing class
**The damage:** Violates single responsibility, impossible to test

```typescript
// ❌ ANTI-PATTERN: God class
class UserManager {
  // Authentication
  login() {}
  logout() {}
  resetPassword() {}
  
  // User CRUD
  createUser() {}
  updateUser() {}
  deleteUser() {}
  
  // Email
  sendWelcomeEmail() {}
  sendPasswordReset() {}
  sendNewsletter() {}
  
  // Reporting
  generateUserReport() {}
  exportToExcel() {}
  
  // Billing
  chargeUser() {}
  refundUser() {}
  
  // ... 50 more unrelated methods
}

// ✅ CORRECT: Single responsibility
class AuthenticationService { /* auth methods */ }
class UserRepository { /* CRUD operations */ }
class EmailService { /* email methods */ }
class ReportingService { /* reporting methods */ }
class BillingService { /* billing methods */ }
```

### 🚫 Spaghetti Code
**What it looks like:** Tangled control flow with goto-like jumps
**Why it happens:** No planning, patch upon patch
**The damage:** Impossible to follow logic flow

```typescript
// ❌ ANTI-PATTERN: Spaghetti logic
function processOrder(order) {
  if (order.status === 'new') {
    goto validateItems;
  } else if (order.status === 'pending') {
    goto checkPayment;
  }
  
  validateItems:
  for (let item of order.items) {
    if (!item.inStock) {
      goto handleOutOfStock;
    }
  }
  goto calculatePrice;
  
  handleOutOfStock:
  // ... logic
  if (canBackorder) {
    goto calculatePrice;
  } else {
    goto cancelOrder;
  }
  
  calculatePrice:
  // ... more jumps
}

// ✅ CORRECT: Clear control flow
class OrderProcessor {
  async process(order: Order): Promise<ProcessedOrder> {
    const validation = await this.validate(order);
    if (!validation.isValid) {
      return this.handleInvalidOrder(validation);
    }
    
    const pricing = await this.calculatePricing(order);
    const payment = await this.processPayment(order, pricing);
    
    return this.finalizeOrder(order, payment);
  }
}
```

---

## Code Organization Anti-Patterns

### 🚫 Copy-Paste Programming
**What it looks like:** Same code duplicated everywhere
**Why it happens:** "It's faster to copy than to refactor"
**The damage:** Bugs must be fixed in multiple places

```typescript
// ❌ ANTI-PATTERN: Duplicated code
class UserController {
  async getUser(req, res) {
    try {
      const user = await db.query('SELECT * FROM users WHERE id = ?', [req.params.id]);
      if (!user) {
        res.status(404).json({ error: 'Not found' });
        return;
      }
      logger.info(`User ${req.params.id} retrieved`);
      res.json(user);
    } catch (error) {
      logger.error(`Error getting user: ${error}`);
      res.status(500).json({ error: 'Server error' });
    }
  }
  
  async getProduct(req, res) {
    try {
      const product = await db.query('SELECT * FROM products WHERE id = ?', [req.params.id]);
      if (!product) {
        res.status(404).json({ error: 'Not found' });
        return;
      }
      logger.info(`Product ${req.params.id} retrieved`);
      res.json(product);
    } catch (error) {
      logger.error(`Error getting product: ${error}`);
      res.status(500).json({ error: 'Server error' });
    }
  }
  // ... same pattern repeated 20 more times
}

// ✅ CORRECT: DRY (Don't Repeat Yourself)
class BaseController {
  protected async getById<T>(
    tableName: string,
    id: string,
    entityName: string
  ): Promise<T | null> {
    try {
      const result = await db.query(
        `SELECT * FROM ${tableName} WHERE id = ?`,
        [id]
      );
      if (!result) {
        throw new NotFoundError(`${entityName} not found`);
      }
      logger.info(`${entityName} ${id} retrieved`);
      return result;
    } catch (error) {
      logger.error(`Error getting ${entityName}: ${error}`);
      throw error;
    }
  }
}
```

### 🚫 Magic Numbers and Strings
**What it looks like:** Hardcoded values with no explanation
**Why it happens:** "I'll remember what this means"
**The damage:** Nobody knows what the values mean

```typescript
// ❌ ANTI-PATTERN: Magic values everywhere
function calculatePrice(user, product) {
  let price = product.price;
  
  if (user.type === 1) {  // What's 1?
    price *= 0.8;  // Why 0.8?
  } else if (user.type === 2) {  // What's 2?
    price *= 0.9;  // Why 0.9?
  }
  
  if (price > 100) {  // Why 100?
    price -= 5;  // Why 5?
  }
  
  if (user.accountAge > 365) {  // Days? Years?
    price *= 0.95;  // Another magic number
  }
  
  return price * 1.2;  // What's this 1.2?
}

// ✅ CORRECT: Named constants
const UserType = {
  PREMIUM: 1,
  GOLD: 2
} as const;

const Discount = {
  PREMIUM: 0.2,
  GOLD: 0.1,
  LOYALTY: 0.05,
  BULK_PURCHASE: 5
} as const;

const Threshold = {
  BULK_PURCHASE_MIN: 100,
  LOYALTY_DAYS: 365
} as const;

const TAX_RATE = 0.2;

function calculatePrice(user: User, product: Product): number {
  let price = product.price;
  
  if (user.type === UserType.PREMIUM) {
    price *= (1 - Discount.PREMIUM);
  } else if (user.type === UserType.GOLD) {
    price *= (1 - Discount.GOLD);
  }
  
  if (price > Threshold.BULK_PURCHASE_MIN) {
    price -= Discount.BULK_PURCHASE;
  }
  
  if (user.accountAge > Threshold.LOYALTY_DAYS) {
    price *= (1 - Discount.LOYALTY);
  }
  
  return price * (1 + TAX_RATE);
}
```

### 🚫 Dead Code
**What it looks like:** Commented out code, unused functions
**Why it happens:** "We might need this later"
**The damage:** Confusion, maintenance burden

```typescript
// ❌ ANTI-PATTERN: Graveyard of dead code
class UserService {
  async createUser(data) {
    // Old validation logic - keeping just in case
    // if (data.email.indexOf('@') === -1) {
    //   throw new Error('Invalid email');
    // }
    // if (data.password.length < 8) {
    //   throw new Error('Password too short');
    // }
    
    // Deprecated method - don't remove yet!
    // this.oldCreateUser(data);
    
    // New validation
    this.validateUser(data);
    return this.repository.save(data);
  }
  
  // Not used anymore but afraid to delete
  oldCreateUser(data) {
    // 200 lines of obsolete code
  }
  
  // Was used for a feature we removed
  exportToXML() {
    // More dead code
  }
}

// ✅ CORRECT: Delete dead code (Git remembers everything)
class UserService {
  async createUser(data: CreateUserDto): Promise<User> {
    this.validateUser(data);
    return this.repository.save(data);
  }
}
```

---

## Error Handling Anti-Patterns

### 🚫 Swallowing Exceptions
**What it looks like:** Empty catch blocks
**Why it happens:** "I'll handle this properly later"
**The damage:** Silent failures, impossible debugging

```typescript
// ❌ ANTI-PATTERN: Silent failures
async function saveUser(user) {
  try {
    await database.save(user);
    await emailService.sendWelcome(user);
    await analytics.track('user.created', user);
  } catch (error) {
    // Swallowed! No one will ever know what went wrong
  }
  return 'success';  // Lies!
}

// ✅ CORRECT: Handle or propagate
async function saveUser(user: User): Promise<void> {
  try {
    await database.save(user);
  } catch (error) {
    logger.error('Failed to save user', { error, userId: user.id });
    throw new DatabaseError('User save failed', error);
  }
  
  try {
    await emailService.sendWelcome(user);
  } catch (error) {
    // Non-critical, log but don't fail
    logger.warn('Welcome email failed', { error, userId: user.id });
  }
  
  try {
    await analytics.track('user.created', user);
  } catch (error) {
    // Non-critical, log but don't fail
    logger.warn('Analytics tracking failed', { error });
  }
}
```

### 🚫 Using Exceptions for Control Flow
**What it looks like:** Throwing errors for non-error cases
**Why it happens:** Misunderstanding exceptions
**The damage:** Performance hit, confusing code

```typescript
// ❌ ANTI-PATTERN: Exceptions as control flow
function findUser(id) {
  try {
    const user = database.getUser(id);
    return user;
  } catch (UserNotFoundException) {
    // Using exception for normal case
    return createGuestUser();
  }
}

function checkPermission(user, resource) {
  if (!hasAccess(user, resource)) {
    throw new NoPermissionException();  // Normal flow, not exceptional
  }
}

// ✅ CORRECT: Exceptions for exceptional cases only
function findUser(id: string): User | null {
  const user = database.getUser(id);
  return user || createGuestUser();
}

function checkPermission(user: User, resource: Resource): boolean {
  return hasAccess(user, resource);
}
```

---

## Performance Anti-Patterns

### 🚫 N+1 Queries
**What it looks like:** Database query in a loop
**Why it happens:** Not thinking about database round trips
**The damage:** Exponential performance degradation

```typescript
// ❌ ANTI-PATTERN: N+1 queries
async function getUsersWithPosts() {
  const users = await db.query('SELECT * FROM users');
  
  for (const user of users) {
    // N additional queries!
    user.posts = await db.query(
      'SELECT * FROM posts WHERE user_id = ?',
      [user.id]
    );
  }
  
  return users;
}

// ✅ CORRECT: Single query with join or batch fetch
async function getUsersWithPosts() {
  const users = await db.query(`
    SELECT u.*, p.*
    FROM users u
    LEFT JOIN posts p ON p.user_id = u.id
  `);
  
  // Or batch fetch
  const users = await db.query('SELECT * FROM users');
  const userIds = users.map(u => u.id);
  const posts = await db.query(
    'SELECT * FROM posts WHERE user_id IN (?)',
    [userIds]
  );
  
  // Map posts to users
  return users.map(user => ({
    ...user,
    posts: posts.filter(p => p.user_id === user.id)
  }));
}
```

### 🚫 Premature Optimization
**What it looks like:** Complex code for imaginary performance
**Why it happens:** "This might be slow someday"
**The damage:** Unreadable code for no benefit

```typescript
// ❌ ANTI-PATTERN: Over-optimized for no reason
function findMax(arr) {
  // "Optimized" with bit shifting and manual loop unrolling
  let max = arr[0];
  let i = 1;
  let len = arr.length;
  let len8 = len >> 3 << 3;  // What?
  
  while (i < len8) {
    // Manual loop unrolling for "performance"
    if (arr[i] > max) max = arr[i];
    if (arr[i+1] > max) max = arr[i+1];
    if (arr[i+2] > max) max = arr[i+2];
    if (arr[i+3] > max) max = arr[i+3];
    if (arr[i+4] > max) max = arr[i+4];
    if (arr[i+5] > max) max = arr[i+5];
    if (arr[i+6] > max) max = arr[i+6];
    if (arr[i+7] > max) max = arr[i+7];
    i += 8;
  }
  
  while (i < len) {
    if (arr[i] > max) max = arr[i];
    i++;
  }
  
  return max;
}

// ✅ CORRECT: Clear and simple (JavaScript engine optimizes this)
function findMax(arr: number[]): number {
  return Math.max(...arr);
}
```

---

## Testing Anti-Patterns

### 🚫 No Tests or Meaningless Tests
**What it looks like:** Tests that don't test anything
**Why it happens:** "Coverage requirements"
**The damage:** False confidence

```typescript
// ❌ ANTI-PATTERN: Useless tests
describe('UserService', () => {
  it('should work', () => {
    expect(true).toBe(true);  // What?
  });
  
  it('should create user', () => {
    const service = new UserService();
    // No assertions!
  });
  
  it('should not throw', () => {
    expect(() => {
      new UserService();
    }).not.toThrow();  // Testing constructor? Really?
  });
});

// ✅ CORRECT: Meaningful tests
describe('UserService', () => {
  describe('createUser', () => {
    it('should create user with valid data', async () => {
      const service = new UserService(mockRepo);
      const userData = { email: 'test@example.com', name: 'Test' };
      
      const user = await service.createUser(userData);
      
      expect(user.email).toBe(userData.email);
      expect(mockRepo.save).toHaveBeenCalledWith(userData);
    });
    
    it('should reject duplicate emails', async () => {
      const service = new UserService(mockRepo);
      mockRepo.findByEmail.mockResolvedValue(existingUser);
      
      await expect(
        service.createUser({ email: 'existing@example.com' })
      ).rejects.toThrow('Email already exists');
    });
  });
});
```

### 🚫 Testing Implementation, Not Behavior
**What it looks like:** Tests break when refactoring
**Why it happens:** Testing how, not what
**The damage:** Brittle tests that resist change

```typescript
// ❌ ANTI-PATTERN: Testing implementation details
it('should call private methods in correct order', () => {
  const service = new UserService();
  const spy1 = jest.spyOn(service, '_validateEmail');
  const spy2 = jest.spyOn(service, '_hashPassword');
  const spy3 = jest.spyOn(service, '_saveToDatabase');
  
  service.createUser(userData);
  
  expect(spy1).toHaveBeenCalledBefore(spy2);
  expect(spy2).toHaveBeenCalledBefore(spy3);
});

// ✅ CORRECT: Test behavior/outcomes
it('should create user with hashed password', async () => {
  const service = new UserService(mockRepo, mockHasher);
  mockHasher.hash.mockResolvedValue('hashed_password');
  
  await service.createUser({
    email: 'test@example.com',
    password: 'plaintext'
  });
  
  expect(mockRepo.save).toHaveBeenCalledWith(
    expect.objectContaining({
      email: 'test@example.com',
      password: 'hashed_password'  // Test outcome, not how
    })
  );
});
```

---

## Security Anti-Patterns

### 🚫 SQL Injection Vulnerabilities
**What it looks like:** String concatenation for SQL
**Why it happens:** Not understanding SQL injection
**The damage:** Complete database compromise

```typescript
// ❌ ANTI-PATTERN: SQL injection waiting to happen
function getUser(userId) {
  const query = `SELECT * FROM users WHERE id = ${userId}`;
  return db.query(query);
  // userId = "1; DROP TABLE users;" -- Boom!
}

function searchUsers(name) {
  const query = "SELECT * FROM users WHERE name LIKE '%" + name + "%'";
  return db.query(query);
  // name = "'; DROP TABLE users; --" -- Boom again!
}

// ✅ CORRECT: Parameterized queries
function getUser(userId: string) {
  const query = 'SELECT * FROM users WHERE id = ?';
  return db.query(query, [userId]);
}

function searchUsers(name: string) {
  const query = 'SELECT * FROM users WHERE name LIKE ?';
  return db.query(query, [`%${name}%`]);
}
```

### 🚫 Storing Sensitive Data in Code
**What it looks like:** Passwords, API keys in source
**Why it happens:** "It's convenient"
**The damage:** Security breach, leaked credentials

```typescript
// ❌ ANTI-PATTERN: Secrets in code
const config = {
  database: {
    host: 'prod.database.com',
    user: 'admin',
    password: 'Super$ecret123!',  // NO!
  },
  stripe: {
    secretKey: 'sk_live_abcd1234...',  // NO!
  },
  jwt: {
    secret: 'my-jwt-secret'  // NO!
  }
};

// ✅ CORRECT: Environment variables
const config = {
  database: {
    host: process.env.DB_HOST,
    user: process.env.DB_USER,
    password: process.env.DB_PASSWORD,
  },
  stripe: {
    secretKey: process.env.STRIPE_SECRET_KEY,
  },
  jwt: {
    secret: process.env.JWT_SECRET
  }
};

// With validation
if (!config.database.password) {
  throw new Error('DB_PASSWORD environment variable is required');
}
```

---

## Concurrency Anti-Patterns

### 🚫 Race Conditions
**What it looks like:** Shared state without synchronization
**Why it happens:** Not thinking about concurrent access
**The damage:** Intermittent bugs, data corruption

```typescript
// ❌ ANTI-PATTERN: Race condition
let counter = 0;

async function incrementCounter() {
  const current = counter;  // Read
  await someAsyncOperation();
  counter = current + 1;  // Write (but counter might have changed!)
}

// If two calls happen simultaneously:
// Both read counter = 0
// Both set counter = 1
// Lost update!

// ✅ CORRECT: Atomic operations or locks
class Counter {
  private value = 0;
  private lock = new AsyncLock();
  
  async increment(): Promise<void> {
    await this.lock.acquire();
    try {
      this.value++;
    } finally {
      this.lock.release();
    }
  }
}

// Or use database atomic operations
async function incrementCounter(id: string): Promise<void> {
  await db.query(
    'UPDATE counters SET value = value + 1 WHERE id = ?',
    [id]
  );
}
```

---

## Common Smells That Lead to Anti-Patterns

1. **"We'll fix it later"** → Technical debt accumulation
2. **"It works on my machine"** → Environment-specific code
3. **"Just this once"** → Beginning of copy-paste programming
4. **"It's only temporary"** → Permanent temporary solutions
5. **"Nobody will ever..."** → Someone will
6. **"We don't have time to test"** → Bugs in production
7. **"The user will never do that"** → They will

---

## Refactoring Priority

When you find these anti-patterns, prioritize fixes by:

1. **Critical:** Security vulnerabilities, data corruption risks
2. **High:** Performance bottlenecks, error swallowing
3. **Medium:** Code duplication, poor structure
4. **Low:** Style issues, naming problems

---

## Prevention Checklist

Before committing code, check:

- [ ] No hardcoded secrets or magic values
- [ ] No empty catch blocks
- [ ] No SQL string concatenation
- [ ] No copy-pasted code blocks
- [ ] No god objects or mega-functions
- [ ] No N+1 query patterns
- [ ] Tests actually test something
- [ ] No premature optimization
- [ ] Clear separation of concerns
- [ ] Errors are handled or propagated

---

## Final Warning

Every anti-pattern in this document was written by a developer who thought they were being clever. Don't be that developer. When in doubt, choose boring, simple, and clear over clever and complex.