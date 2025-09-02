---
description: Generate tech-stack specific best practices
argument-hint: [project-name] [language] [framework]
---

# Scaffold Best Practices for Your Stack

Generate BEST-PRACTICES.md in `projects/$1/docs/`

## Stack Information

**Project Name:** $1
**Language:** $2
**Framework:** $3

## Best Practices Generation

Based on @guides/BEST-PRACTICES.md, I'll create stack-specific guidance for:

### Code Organization
- Recommended folder structure for $2
- File naming conventions
- Module organization patterns
- Maximum file sizes

### $1 Language Specifics
- Idiomatic $1 patterns
- Language-specific gotchas to avoid
- Performance tips for $1
- Testing patterns for $1

### $2 Framework Patterns
- Framework conventions
- Recommended architectural patterns
- Common pitfalls and solutions
- Performance optimization

### Standards Included
- Function and variable naming
- Error handling patterns
- Async/await best practices
- Dependency injection approaches
- Testing strategies
- Documentation requirements

### Code Quality Rules
- Linting configuration
- Type safety (if applicable)
- Code complexity limits
- Test coverage requirements

## Tool Configuration

I'll also suggest configurations for:
- Linter rules
- Formatter settings
- Pre-commit hooks
- CI/CD checks

Your practices document will be immediately actionable and enforcement-ready.