# The Ultimate Guide to PATTERNS.md - Proven Solutions to Common Problems

## What PATTERNS.md is for

This document catalogs battle-tested design patterns that solve recurring problems elegantly. These aren't theoretical - they're practical patterns that have proven their worth in production. Each pattern here is pre-approved for use in the codebase.

---

## Core principles

1. **Favor composition over inheritance** - Build with blocks, not hierarchies
2. **Program to interfaces, not implementations** - Depend on contracts, not concrete classes
3. **Single responsibility** - Each class/module solves one problem
4. **Open/closed principle** - Open for extension, closed for modification
5. **Dependency inversion** - High-level modules shouldn't depend on low-level modules

---

## Creational Patterns

### Factory Pattern
**Problem:** Creating objects without specifying exact classes
**When to use:** Multiple object types with shared interface, complex creation logic

```typescript
// ✅ USE THIS PATTERN
interface PaymentProcessor {
  process(amount: number): Promise<PaymentResult>;
}

class StripeProcessor implements PaymentProcessor {
  async process(amount: number): Promise<PaymentResult> {
    // Stripe-specific implementation
  }
}

class PayPalProcessor implements PaymentProcessor {
  async process(amount: number): Promise<PaymentResult> {
    // PayPal-specific implementation
  }
}

class PaymentProcessorFactory {
  static create(type: PaymentType): PaymentProcessor {
    switch (type) {
      case PaymentType.STRIPE:
        return new StripeProcessor();
      case PaymentType.PAYPAL:
        return new PayPalProcessor();
      default:
        throw new Error(`Unknown payment type: ${type}`);
    }
  }
}

// Usage
const processor = PaymentProcessorFactory.create(PaymentType.STRIPE);
await processor.process(100);
```

### Builder Pattern
**Problem:** Constructing complex objects step-by-step
**When to use:** Objects with many optional parameters, avoiding constructor telescoping

```typescript
// ✅ USE THIS PATTERN
class EmailBuilder {
  private email: Partial<Email> = {};

  to(address: string): this {
    this.email.to = address;
    return this;
  }

  from(address: string): this {
    this.email.from = address;
    return this;
  }

  subject(subject: string): this {
    this.email.subject = subject;
    return this;
  }

  body(body: string): this {
    this.email.body = body;
    return this;
  }

  attachment(file: File): this {
    this.email.attachments = [...(this.email.attachments || []), file];
    return this;
  }

  build(): Email {
    if (!this.email.to || !this.email.from || !this.email.subject) {
      throw new Error('Required fields missing');
    }
    return this.email as Email;
  }
}

// Usage - clean and readable
const email = new EmailBuilder()
  .to('user@example.com')
  .from('noreply@company.com')
  .subject('Welcome!')
  .body('Thanks for signing up')
  .attachment(pdfFile)
  .build();
```

### Singleton Pattern (Use Sparingly!)
**Problem:** Ensuring only one instance exists
**When to use:** Database connections, configuration, caches
**Warning:** Can make testing difficult, use dependency injection instead when possible

```typescript
// ✅ USE THIS PATTERN (but prefer DI)
class DatabaseConnection {
  private static instance: DatabaseConnection;
  private connection: Connection;

  private constructor() {
    // Private constructor prevents direct instantiation
  }

  static getInstance(): DatabaseConnection {
    if (!DatabaseConnection.instance) {
      DatabaseConnection.instance = new DatabaseConnection();
      DatabaseConnection.instance.connect();
    }
    return DatabaseConnection.instance;
  }

  private connect(): void {
    this.connection = createConnection(config);
  }

  query(sql: string): Promise<any> {
    return this.connection.query(sql);
  }
}

// Better alternative: Dependency Injection
class BetterDatabaseConnection {
  constructor(private connection: Connection) {}
  
  query(sql: string): Promise<any> {
    return this.connection.query(sql);
  }
}
```

---

## Structural Patterns

### Repository Pattern
**Problem:** Abstracting data access logic
**When to use:** Always for database operations

```typescript
// ✅ USE THIS PATTERN
interface UserRepository {
  findById(id: string): Promise<User | null>;
  findByEmail(email: string): Promise<User | null>;
  save(user: User): Promise<User>;
  delete(id: string): Promise<void>;
}

class PostgresUserRepository implements UserRepository {
  constructor(private db: Database) {}

  async findById(id: string): Promise<User | null> {
    const result = await this.db.query(
      'SELECT * FROM users WHERE id = $1',
      [id]
    );
    return result.rows[0] ? this.mapToUser(result.rows[0]) : null;
  }

  async findByEmail(email: string): Promise<User | null> {
    const result = await this.db.query(
      'SELECT * FROM users WHERE email = $1',
      [email]
    );
    return result.rows[0] ? this.mapToUser(result.rows[0]) : null;
  }

  async save(user: User): Promise<User> {
    const result = await this.db.query(
      'INSERT INTO users (id, email, name) VALUES ($1, $2, $3) RETURNING *',
      [user.id, user.email, user.name]
    );
    return this.mapToUser(result.rows[0]);
  }

  async delete(id: string): Promise<void> {
    await this.db.query('DELETE FROM users WHERE id = $1', [id]);
  }

  private mapToUser(row: any): User {
    return new User(row.id, row.email, row.name);
  }
}

// In-memory implementation for testing
class InMemoryUserRepository implements UserRepository {
  private users: Map<string, User> = new Map();

  async findById(id: string): Promise<User | null> {
    return this.users.get(id) || null;
  }

  async findByEmail(email: string): Promise<User | null> {
    return Array.from(this.users.values())
      .find(u => u.email === email) || null;
  }

  async save(user: User): Promise<User> {
    this.users.set(user.id, user);
    return user;
  }

  async delete(id: string): Promise<void> {
    this.users.delete(id);
  }
}
```

### Adapter Pattern
**Problem:** Making incompatible interfaces work together
**When to use:** Integrating third-party libraries, legacy code

```typescript
// ✅ USE THIS PATTERN
// Our interface
interface Logger {
  debug(message: string, meta?: any): void;
  info(message: string, meta?: any): void;
  error(message: string, meta?: any): void;
}

// Third-party logger with different interface
class WinstonLogger {
  log(level: string, message: string, meta?: any): void {
    // Winston-specific implementation
  }
}

// Adapter to make Winston work with our interface
class WinstonLoggerAdapter implements Logger {
  constructor(private winston: WinstonLogger) {}

  debug(message: string, meta?: any): void {
    this.winston.log('debug', message, meta);
  }

  info(message: string, meta?: any): void {
    this.winston.log('info', message, meta);
  }

  error(message: string, meta?: any): void {
    this.winston.log('error', message, meta);
  }
}

// Usage - can swap implementations without changing code
const logger: Logger = new WinstonLoggerAdapter(new WinstonLogger());
logger.info('User logged in', { userId: '123' });
```

### Decorator Pattern
**Problem:** Adding behavior to objects without modifying them
**When to use:** Adding features like caching, logging, validation

```typescript
// ✅ USE THIS PATTERN
interface DataService {
  getData(id: string): Promise<Data>;
}

class ApiDataService implements DataService {
  async getData(id: string): Promise<Data> {
    const response = await fetch(`/api/data/${id}`);
    return response.json();
  }
}

// Decorator adds caching
class CachedDataService implements DataService {
  private cache = new Map<string, Data>();

  constructor(private service: DataService) {}

  async getData(id: string): Promise<Data> {
    if (this.cache.has(id)) {
      return this.cache.get(id)!;
    }

    const data = await this.service.getData(id);
    this.cache.set(id, data);
    return data;
  }
}

// Decorator adds logging
class LoggedDataService implements DataService {
  constructor(
    private service: DataService,
    private logger: Logger
  ) {}

  async getData(id: string): Promise<Data> {
    this.logger.info(`Fetching data for ${id}`);
    const start = Date.now();
    
    try {
      const data = await this.service.getData(id);
      this.logger.info(`Data fetched in ${Date.now() - start}ms`);
      return data;
    } catch (error) {
      this.logger.error(`Failed to fetch data for ${id}`, error);
      throw error;
    }
  }
}

// Stack decorators
const service = new LoggedDataService(
  new CachedDataService(
    new ApiDataService()
  ),
  logger
);
```

### Facade Pattern
**Problem:** Simplifying complex subsystems
**When to use:** Hiding complexity, providing simple API

```typescript
// ✅ USE THIS PATTERN
// Complex subsystem
class VideoConverter {
  convertToMp4(file: File): File { /* ... */ }
}

class AudioExtractor {
  extractAudio(video: File): AudioTrack { /* ... */ }
}

class SubtitleGenerator {
  generate(audio: AudioTrack): Subtitles { /* ... */ }
}

class Thumbnailer {
  createThumbnail(video: File): Image { /* ... */ }
}

// Facade provides simple interface
class VideoProcessingFacade {
  private converter = new VideoConverter();
  private audioExtractor = new AudioExtractor();
  private subtitleGen = new SubtitleGenerator();
  private thumbnailer = new Thumbnailer();

  async processVideo(file: File): Promise<ProcessedVideo> {
    // Hide complexity behind simple method
    const mp4 = this.converter.convertToMp4(file);
    const audio = this.audioExtractor.extractAudio(mp4);
    const subtitles = this.subtitleGen.generate(audio);
    const thumbnail = this.thumbnailer.createThumbnail(mp4);

    return {
      video: mp4,
      subtitles,
      thumbnail
    };
  }
}

// Usage - complexity hidden
const processor = new VideoProcessingFacade();
const result = await processor.processVideo(uploadedFile);
```

---

## Behavioral Patterns

### Strategy Pattern
**Problem:** Selecting algorithms at runtime
**When to use:** Multiple ways to accomplish same task

```typescript
// ✅ USE THIS PATTERN
interface PricingStrategy {
  calculate(basePrice: number, user: User): number;
}

class RegularPricing implements PricingStrategy {
  calculate(basePrice: number, user: User): number {
    return basePrice;
  }
}

class PremiumPricing implements PricingStrategy {
  calculate(basePrice: number, user: User): number {
    return basePrice * 0.8; // 20% discount
  }
}

class StudentPricing implements PricingStrategy {
  calculate(basePrice: number, user: User): number {
    return basePrice * 0.5; // 50% discount
  }
}

class PriceCalculator {
  private strategy: PricingStrategy;

  setStrategy(strategy: PricingStrategy): void {
    this.strategy = strategy;
  }

  calculate(basePrice: number, user: User): number {
    return this.strategy.calculate(basePrice, user);
  }
}

// Usage - switch strategies dynamically
const calculator = new PriceCalculator();

if (user.isStudent) {
  calculator.setStrategy(new StudentPricing());
} else if (user.isPremium) {
  calculator.setStrategy(new PremiumPricing());
} else {
  calculator.setStrategy(new RegularPricing());
}

const finalPrice = calculator.calculate(100, user);
```

### Observer Pattern
**Problem:** Notifying multiple objects about state changes
**When to use:** Event systems, model-view patterns, pub/sub

```typescript
// ✅ USE THIS PATTERN
interface Observer {
  update(event: Event): void;
}

class EventEmitter {
  private observers: Map<string, Observer[]> = new Map();

  subscribe(eventType: string, observer: Observer): void {
    if (!this.observers.has(eventType)) {
      this.observers.set(eventType, []);
    }
    this.observers.get(eventType)!.push(observer);
  }

  unsubscribe(eventType: string, observer: Observer): void {
    const observers = this.observers.get(eventType);
    if (observers) {
      const index = observers.indexOf(observer);
      if (index > -1) {
        observers.splice(index, 1);
      }
    }
  }

  emit(eventType: string, event: Event): void {
    const observers = this.observers.get(eventType);
    if (observers) {
      observers.forEach(observer => observer.update(event));
    }
  }
}

// Example observers
class EmailNotifier implements Observer {
  update(event: Event): void {
    if (event.type === 'user.registered') {
      this.sendWelcomeEmail(event.data.user);
    }
  }

  private sendWelcomeEmail(user: User): void {
    // Send email
  }
}

class AnalyticsTracker implements Observer {
  update(event: Event): void {
    this.track(event.type, event.data);
  }

  private track(eventType: string, data: any): void {
    // Track analytics
  }
}

// Usage
const emitter = new EventEmitter();
emitter.subscribe('user.registered', new EmailNotifier());
emitter.subscribe('user.registered', new AnalyticsTracker());

// When user registers
emitter.emit('user.registered', {
  type: 'user.registered',
  data: { user: newUser }
});
```

### Chain of Responsibility Pattern
**Problem:** Passing requests along a chain of handlers
**When to use:** Validation chains, middleware, approval workflows

```typescript
// ✅ USE THIS PATTERN
abstract class ValidationHandler {
  private nextHandler?: ValidationHandler;

  setNext(handler: ValidationHandler): ValidationHandler {
    this.nextHandler = handler;
    return handler;
  }

  async handle(request: Request): Promise<ValidationResult> {
    const result = await this.validate(request);
    
    if (!result.isValid) {
      return result;
    }

    if (this.nextHandler) {
      return this.nextHandler.handle(request);
    }

    return ValidationResult.success();
  }

  protected abstract validate(request: Request): Promise<ValidationResult>;
}

class AuthenticationHandler extends ValidationHandler {
  protected async validate(request: Request): Promise<ValidationResult> {
    if (!request.token) {
      return ValidationResult.failure('No authentication token');
    }
    
    const isValid = await this.verifyToken(request.token);
    return isValid 
      ? ValidationResult.success()
      : ValidationResult.failure('Invalid token');
  }

  private async verifyToken(token: string): Promise<boolean> {
    // Token verification logic
  }
}

class AuthorizationHandler extends ValidationHandler {
  protected async validate(request: Request): Promise<ValidationResult> {
    const hasPermission = await this.checkPermissions(
      request.user,
      request.resource
    );
    
    return hasPermission
      ? ValidationResult.success()
      : ValidationResult.failure('Insufficient permissions');
  }

  private async checkPermissions(user: User, resource: string): Promise<boolean> {
    // Permission checking logic
  }
}

class RateLimitHandler extends ValidationHandler {
  protected async validate(request: Request): Promise<ValidationResult> {
    const isWithinLimit = await this.checkRateLimit(request.user);
    
    return isWithinLimit
      ? ValidationResult.success()
      : ValidationResult.failure('Rate limit exceeded');
  }

  private async checkRateLimit(user: User): Promise<boolean> {
    // Rate limiting logic
  }
}

// Usage - build validation chain
const validationChain = new AuthenticationHandler();
validationChain
  .setNext(new AuthorizationHandler())
  .setNext(new RateLimitHandler());

const result = await validationChain.handle(request);
```

### Command Pattern
**Problem:** Encapsulating requests as objects
**When to use:** Undo/redo, queuing operations, logging

```typescript
// ✅ USE THIS PATTERN
interface Command {
  execute(): Promise<void>;
  undo(): Promise<void>;
}

class CreateUserCommand implements Command {
  private user?: User;

  constructor(
    private userService: UserService,
    private userData: CreateUserDto
  ) {}

  async execute(): Promise<void> {
    this.user = await this.userService.create(this.userData);
  }

  async undo(): Promise<void> {
    if (this.user) {
      await this.userService.delete(this.user.id);
    }
  }
}

class UpdateUserCommand implements Command {
  private previousData?: User;

  constructor(
    private userService: UserService,
    private userId: string,
    private updates: Partial<User>
  ) {}

  async execute(): Promise<void> {
    this.previousData = await this.userService.findById(this.userId);
    await this.userService.update(this.userId, this.updates);
  }

  async undo(): Promise<void> {
    if (this.previousData) {
      await this.userService.update(this.userId, this.previousData);
    }
  }
}

class CommandExecutor {
  private history: Command[] = [];
  private currentIndex = -1;

  async execute(command: Command): Promise<void> {
    await command.execute();
    
    // Remove any commands after current index (for redo consistency)
    this.history = this.history.slice(0, this.currentIndex + 1);
    
    this.history.push(command);
    this.currentIndex++;
  }

  async undo(): Promise<void> {
    if (this.currentIndex >= 0) {
      const command = this.history[this.currentIndex];
      await command.undo();
      this.currentIndex--;
    }
  }

  async redo(): Promise<void> {
    if (this.currentIndex < this.history.length - 1) {
      this.currentIndex++;
      const command = this.history[this.currentIndex];
      await command.execute();
    }
  }
}

// Usage
const executor = new CommandExecutor();

// Execute commands
await executor.execute(new CreateUserCommand(userService, userData));
await executor.execute(new UpdateUserCommand(userService, userId, updates));

// Undo last command
await executor.undo();

// Redo
await executor.redo();
```

---

## Architectural Patterns

### Model-View-Controller (MVC)
**Problem:** Separating concerns in applications
**When to use:** Web applications, APIs

```typescript
// ✅ USE THIS PATTERN
// Model - Business logic and data
class UserModel {
  constructor(
    private id: string,
    private email: string,
    private name: string
  ) {}

  validate(): boolean {
    return this.email.includes('@') && this.name.length > 0;
  }

  toJSON(): object {
    return { id: this.id, email: this.email, name: this.name };
  }
}

// View - Presentation layer
class UserView {
  render(user: UserModel): string {
    return `
      <div class="user">
        <h2>${user.name}</h2>
        <p>${user.email}</p>
      </div>
    `;
  }

  renderError(error: string): string {
    return `<div class="error">${error}</div>`;
  }
}

// Controller - Orchestrates Model and View
class UserController {
  constructor(
    private userService: UserService,
    private userView: UserView
  ) {}

  async getUser(req: Request, res: Response): Promise<void> {
    try {
      const user = await this.userService.findById(req.params.id);
      if (!user) {
        res.status(404).send(this.userView.renderError('User not found'));
        return;
      }
      res.send(this.userView.render(user));
    } catch (error) {
      res.status(500).send(this.userView.renderError('Server error'));
    }
  }
}
```

### Service Layer Pattern
**Problem:** Organizing business logic
**When to use:** Always in multi-layer architectures

```typescript
// ✅ USE THIS PATTERN
class UserService {
  constructor(
    private userRepository: UserRepository,
    private emailService: EmailService,
    private eventEmitter: EventEmitter
  ) {}

  async createUser(data: CreateUserDto): Promise<User> {
    // Business logic validation
    if (await this.userRepository.findByEmail(data.email)) {
      throw new BusinessError('Email already exists');
    }

    // Create user
    const user = new User(generateId(), data.email, data.name);
    await this.userRepository.save(user);

    // Side effects
    await this.emailService.sendWelcome(user);
    this.eventEmitter.emit('user.created', { user });

    return user;
  }

  async updateUser(id: string, updates: UpdateUserDto): Promise<User> {
    const user = await this.userRepository.findById(id);
    if (!user) {
      throw new NotFoundError('User not found');
    }

    // Apply business rules
    if (updates.email && updates.email !== user.email) {
      if (await this.userRepository.findByEmail(updates.email)) {
        throw new BusinessError('Email already taken');
      }
    }

    // Update and save
    Object.assign(user, updates);
    await this.userRepository.save(user);

    return user;
  }
}
```

### Unit of Work Pattern
**Problem:** Managing database transactions
**When to use:** Complex operations spanning multiple repositories

```typescript
// ✅ USE THIS PATTERN
interface UnitOfWork {
  userRepository: UserRepository;
  orderRepository: OrderRepository;
  commit(): Promise<void>;
  rollback(): Promise<void>;
}

class PostgresUnitOfWork implements UnitOfWork {
  userRepository: UserRepository;
  orderRepository: OrderRepository;
  private transaction: DatabaseTransaction;

  constructor(private db: Database) {}

  async begin(): Promise<void> {
    this.transaction = await this.db.beginTransaction();
    this.userRepository = new PostgresUserRepository(this.transaction);
    this.orderRepository = new PostgresOrderRepository(this.transaction);
  }

  async commit(): Promise<void> {
    await this.transaction.commit();
  }

  async rollback(): Promise<void> {
    await this.transaction.rollback();
  }
}

// Usage - all or nothing
class OrderService {
  async createOrder(userId: string, items: Item[]): Promise<Order> {
    const uow = new PostgresUnitOfWork(this.db);
    await uow.begin();

    try {
      // Multiple operations in single transaction
      const user = await uow.userRepository.findById(userId);
      const order = new Order(user, items);
      await uow.orderRepository.save(order);
      
      user.incrementOrderCount();
      await uow.userRepository.save(user);

      await uow.commit();
      return order;
    } catch (error) {
      await uow.rollback();
      throw error;
    }
  }
}
```

---

## Testing Patterns

### Test Data Builder
**Problem:** Creating test data flexibly
**When to use:** All test files

```typescript
// ✅ USE THIS PATTERN
class UserBuilder {
  private user: Partial<User> = {
    id: 'test-id',
    email: 'test@example.com',
    name: 'Test User'
  };

  withId(id: string): this {
    this.user.id = id;
    return this;
  }

  withEmail(email: string): this {
    this.user.email = email;
    return this;
  }

  withName(name: string): this {
    this.user.name = name;
    return this;
  }

  asPremium(): this {
    this.user.isPremium = true;
    return this;
  }

  build(): User {
    return new User(this.user);
  }
}

// Usage in tests - readable and flexible
const premiumUser = new UserBuilder()
  .withEmail('premium@example.com')
  .asPremium()
  .build();

const regularUser = new UserBuilder().build();
```

---

## When to use which pattern

| Problem | Pattern to Use |
|---------|---------------|
| Creating objects based on type | Factory |
| Complex object construction | Builder |
| Abstracting data access | Repository |
| Adding features without modifying | Decorator |
| Incompatible interfaces | Adapter |
| Algorithm selection at runtime | Strategy |
| Request handling chain | Chain of Responsibility |
| Undo/redo functionality | Command |
| Event handling | Observer |
| Simplifying complex subsystems | Facade |
| Business logic organization | Service Layer |
| Transaction management | Unit of Work |

---

## Copy-paste starter patterns

Pick the patterns that match your needs and copy them as starting points. Remember: patterns are tools, not rules. Use them when they solve real problems, not just because they exist.