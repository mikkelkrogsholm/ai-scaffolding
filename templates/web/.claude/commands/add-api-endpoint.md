---
description: Create a new API endpoint with validation and tests
argument-hint: [resource-name] [method: GET|POST|PUT|DELETE]
---

# Add API Endpoint

Generate a complete API endpoint with validation, authentication, and tests.

## Endpoint Configuration

**Resource:** $1
**Method:** $2
**Path:** /api/$1

## What Gets Created

```
src/
├── routes/
│   └── $1.routes.ts         # Route definitions
├── controllers/
│   └── $1.controller.ts     # Request handling
├── services/
│   └── $1.service.ts        # Business logic
├── repositories/
│   └── $1.repository.ts     # Data access
├── validators/
│   └── $1.validator.ts      # Input validation
├── tests/
│   ├── $1.controller.test.ts
│   ├── $1.service.test.ts
│   └── $1.integration.test.ts
└── docs/
    └── $1.openapi.yaml       # API documentation
```

## Generated Features

### Route Handler
- RESTful conventions
- Async error handling
- Request/response logging
- Rate limiting
- Cache headers

### Validation
- Input sanitization
- Type checking
- Business rule validation
- Error messages
- SQL injection prevention

### Authentication/Authorization
- JWT verification
- Role-based access
- Resource ownership checks
- API key validation (if applicable)

### Service Layer
- Business logic separation
- Transaction management
- Event emission
- Cache invalidation
- External API calls

### Repository Layer
- Database queries
- Query optimization
- Connection pooling
- Transaction support
- Soft deletes

### Testing
- Unit tests for each layer
- Integration tests
- API contract tests
- Performance tests
- Security tests

## OpenAPI Documentation

Automatically generates:
```yaml
paths:
  /api/$1:
    $2:
      summary: $2 $1
      tags: [$1]
      security:
        - bearerAuth: []
      parameters: []
      requestBody: {}
      responses:
        200:
          description: Success
        400:
          description: Bad Request
        401:
          description: Unauthorized
        404:
          description: Not Found
        500:
          description: Server Error
```

## Security Features

- Input validation
- SQL injection prevention
- XSS protection
- CSRF tokens (for mutations)
- Rate limiting
- Audit logging

## Performance Optimizations

- Database query optimization
- Response caching
- Pagination
- Field filtering
- Lazy loading
- Connection pooling

The endpoint follows all patterns from PATTERNS.md and security rules from RULES.md!