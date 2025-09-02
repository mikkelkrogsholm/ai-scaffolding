---
description: Generate project-specific agents, commands, and output styles
argument-hint: [project-name] [project-type]
---

# Scaffold Project-Specific Tools

Generate customized agents, slash commands, and output styles for your project type.

## What Gets Created

Based on project type **$2**, I'll create in `projects/$1/.claude/`:

### For Web Projects
**Agents:**
- `frontend-developer` - React/Vue/Angular specialist
- `api-designer` - REST/GraphQL endpoint creator
- `database-architect` - Schema and query optimizer
- `ui-reviewer` - Accessibility and UX checker
- `performance-optimizer` - Bundle size and speed

**Commands:**
- `/create-component` - Generate component with tests
- `/add-api-endpoint` - Scaffold new API route
- `/optimize-bundle` - Analyze and reduce bundle size
- `/add-feature` - Complete feature with frontend+backend

**Output Style:**
- Component generation format
- API response formatting
- Error message standards
- Logging format

### For API Projects
**Agents:**
- `endpoint-creator` - RESTful endpoint designer
- `schema-validator` - Input/output validation
- `auth-specialist` - Authentication/authorization
- `api-documenter` - OpenAPI/Swagger generator

**Commands:**
- `/add-endpoint` - Create new API endpoint
- `/add-middleware` - Generate middleware
- `/generate-sdk` - Create client SDK
- `/add-validation` - Add request validation

### For CLI Projects
**Agents:**
- `command-builder` - CLI command creator
- `argument-parser` - Input handling specialist
- `help-writer` - Documentation generator
- `test-automator` - CLI testing specialist

**Commands:**
- `/add-command` - Create new CLI command
- `/add-flag` - Add command flag/option
- `/generate-completion` - Bash/ZSH completions
- `/add-interactive` - Add interactive prompts

### For Library Projects
**Agents:**
- `api-designer` - Public API designer
- `docs-writer` - API documentation
- `example-creator` - Usage examples
- `version-manager` - Semver compliance

**Commands:**
- `/add-method` - Add public method
- `/add-example` - Create usage example
- `/check-breaking` - Check for breaking changes
- `/generate-types` - TypeScript definitions

### For Writing Projects
**Agents:**
- `outline-developer` - Chapter/section planner
- `consistency-checker` - Style/tone validator
- `fact-checker` - Research validator
- `editor` - Grammar and flow improver

**Commands:**
- `/add-chapter` - Create chapter template
- `/check-consistency` - Validate style guide
- `/word-count` - Progress tracking
- `/generate-toc` - Table of contents

### For Research Projects
**Agents:**
- `hypothesis-tester` - Statistical analysis
- `literature-reviewer` - Prior work analyzer
- `data-validator` - Data quality checker
- `results-interpreter` - Finding synthesizer

**Commands:**
- `/add-hypothesis` - Define new hypothesis
- `/analyze-data` - Run analysis pipeline
- `/generate-figures` - Create visualizations
- `/check-methodology` - Validate approach

## Output Style Configuration

Each project gets a custom `.claude/output-style.yaml`:
```yaml
project_type: $2
output_format:
  code_style: [verbose|concise|minimal]
  explanation_level: [detailed|balanced|brief]
  comment_frequency: [heavy|moderate|minimal]
  error_handling: [explicit|standard|minimal]
  testing_approach: [tdd|bdd|standard]
```

## Custom Hooks

Project-specific hooks in `.claude/hooks/`:
- Pre-commit validation
- Post-generate formatting
- Quality checks
- Documentation updates

## Integration

These tools integrate with:
- Project's PRD for context
- ARCHITECTURE for technical decisions  
- BEST-PRACTICES for standards
- PATTERNS for approved approaches

Ready to generate project-specific tools!