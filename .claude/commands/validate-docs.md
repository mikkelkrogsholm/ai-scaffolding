---
description: Validate consistency across all project documents
allowed-tools: Read, Glob
---

# Validate Document Consistency

Check all project documents for conflicts and inconsistencies.

## Validation Checks

### Cross-Document Consistency
- PRD requirements mapped to TASKS
- ARCHITECTURE aligns with RULES
- PATTERNS used match ARCHITECTURE
- No ANTI-PATTERNS in approved PATTERNS
- CLAUDE.md references all key documents

### Terminology Consistency
- Same terms used across documents
- Acronyms defined consistently
- Entity names match everywhere
- No conflicting definitions

### Coverage Validation
- Every PRD requirement has tasks
- All tasks trace to requirements
- Acceptance criteria defined for all features
- Test strategy covers all requirements
- Every architectural component documented

### Rule Enforcement
- RULES.md constraints are measurable
- Performance budgets consistent
- Security requirements aligned
- All rules have enforcement mechanisms

### Completeness Checks
- No TBD or TODO items
- All sections populated
- Examples provided where needed
- External references valid

## Validation Report

I'll generate a report showing:
- ✅ Valid items
- ⚠️ Warnings (should fix)
- ❌ Errors (must fix)
- 📊 Coverage statistics
- 🔧 Suggested fixes

## Auto-Fix Capability

For common issues, I can:
- Align terminology
- Add missing cross-references
- Update outdated information
- Generate missing sections

Ready to validate your project documentation!