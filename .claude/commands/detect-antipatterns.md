---
description: Scan code for anti-patterns and suggest fixes
allowed-tools: Read, Grep, Glob
---

# Detect Anti-Patterns in Codebase

Scan your code for common anti-patterns and provide remediation suggestions.

## Anti-Pattern Detection

Based on @guides/ANTI-PATTERNS.md, I'll search for:

### Architectural Issues
- God objects (classes with too many responsibilities)
- Big ball of mud (no clear structure)
- Spaghetti code (tangled control flow)

### Code Smells
- Copy-paste programming (duplicated code)
- Magic numbers and strings
- Dead code (commented out or unused)
- Long methods (>20 lines)
- Long files (>200 lines)

### Error Handling Issues
- Empty catch blocks (swallowed exceptions)
- Generic error messages
- Using exceptions for control flow

### Performance Problems
- N+1 query patterns
- Premature optimization
- Synchronous operations that could be parallel

### Security Vulnerabilities
- SQL string concatenation
- Hardcoded secrets
- Missing input validation
- Sensitive data in logs

## Analysis Output

For each detected anti-pattern:
1. **Location**: File and line number
2. **Severity**: Critical, High, Medium, Low
3. **Description**: What's wrong
4. **Impact**: Potential problems
5. **Fix**: Specific refactoring suggestion
6. **Example**: How it should look

## Prioritized Action Plan

I'll provide a remediation plan ordered by:
1. Security vulnerabilities (fix immediately)
2. Data corruption risks
3. Performance bottlenecks
4. Maintainability issues

Ready to scan your codebase for anti-patterns!