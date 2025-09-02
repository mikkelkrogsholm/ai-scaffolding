# Project-Specific Tools Guide

## Overview

When you scaffold a project, you don't just get documentation - you get a complete toolkit of AI agents, slash commands, and output styles specifically tailored to your project type. This ensures Claude Code behaves optimally for your specific domain.

## 🤖 Project-Specific Agents

Each project type gets specialized agents that understand the domain:

### Web Development Projects

| Agent | Specialization | Use Cases |
|-------|---------------|-----------|
| `frontend-developer` | React, Vue, Angular, responsive design | Creating components, state management, accessibility |
| `api-designer` | REST, GraphQL, authentication | Designing endpoints, validation, documentation |
| `database-architect` | Schema design, optimization, migrations | Data modeling, query optimization, indexing |
| `performance-optimizer` | Bundle size, loading speed, caching | Code splitting, lazy loading, optimization |
| `ui-reviewer` | UX, accessibility, responsive design | Design reviews, WCAG compliance, usability |

### Writing Projects

| Agent | Specialization | Use Cases |
|-------|---------------|-----------|
| `editor` | Grammar, style, consistency | Line editing, copy editing, proofreading |
| `outline-developer` | Structure, flow, organization | Chapter planning, section organization |
| `consistency-checker` | Style guide, terminology, tone | Ensuring uniform voice and terminology |
| `fact-checker` | Research validation, citations | Verifying claims, checking sources |
| `narrative-coach` | Story flow, engagement, pacing | Improving readability and engagement |

### Research Projects

| Agent | Specialization | Use Cases |
|-------|---------------|-----------|
| `hypothesis-tester` | Statistical analysis, validation | Testing assumptions, analyzing results |
| `literature-reviewer` | Prior work, citations, gaps | Finding related work, identifying gaps |
| `data-validator` | Data quality, cleaning, integrity | Checking data consistency, finding outliers |
| `methodology-expert` | Research design, validity | Ensuring rigorous methodology |
| `results-interpreter` | Analysis, visualization, insights | Making sense of findings |

### API/Backend Projects

| Agent | Specialization | Use Cases |
|-------|---------------|-----------|
| `endpoint-creator` | RESTful design, CRUD operations | Creating consistent API endpoints |
| `schema-validator` | Input/output validation | Request/response validation |
| `auth-specialist` | Authentication, authorization, security | JWT, OAuth, permissions |
| `api-documenter` | OpenAPI, Swagger, examples | Generating API documentation |
| `integration-tester` | API testing, mocking | Testing integrations, contracts |

## ⚡ Custom Slash Commands

Project-specific commands that accelerate development:

### Web Project Commands

| Command | Function | Example |
|---------|----------|---------|
| `/create-component` | Generate complete component with tests | `/create-component UserCard functional` |
| `/add-api-endpoint` | Create API route with validation | `/add-api-endpoint users POST` |
| `/add-page` | Create new page/route | `/add-page dashboard` |
| `/optimize-bundle` | Analyze and optimize bundle size | `/optimize-bundle` |
| `/add-feature` | Complete feature (frontend + backend) | `/add-feature user-notifications` |
| `/generate-tests` | Create test suite for component/service | `/generate-tests UserService` |

### Writing Project Commands

| Command | Function | Example |
|---------|----------|---------|
| `/add-chapter` | Create chapter with template | `/add-chapter 5 "The Turning Point"` |
| `/check-consistency` | Validate against style guide | `/check-consistency` |
| `/word-count` | Track progress against targets | `/word-count --by-chapter` |
| `/generate-outline` | Create detailed outline | `/generate-outline part-2` |
| `/add-character` | Create character profile (fiction) | `/add-character "Jane Smith"` |
| `/timeline-check` | Validate chronology (fiction) | `/timeline-check` |

### Research Project Commands

| Command | Function | Example |
|---------|----------|---------|
| `/add-hypothesis` | Define new hypothesis | `/add-hypothesis "correlation between X and Y"` |
| `/analyze-data` | Run analysis pipeline | `/analyze-data dataset1.csv` |
| `/generate-figures` | Create visualizations | `/generate-figures results.json` |
| `/literature-search` | Find related papers | `/literature-search "machine learning bias"` |
| `/check-methodology` | Validate research approach | `/check-methodology` |
| `/generate-abstract` | Create paper abstract | `/generate-abstract` |

## 🎨 Output Style Configuration

Each project type has a tailored `output-style.yaml` that controls how Claude Code generates and formats content:

### Web Project Output Style

```yaml
code_style:
  verbosity: balanced
  comments:
    frequency: moderate
    style: jsdoc
  naming:
    components: PascalCase
    functions: camelCase
  formatting:
    indent: 2
    quotes: single
    semicolons: true

components:
  structure:
    separate_styles: true
    separate_tests: true
  defaults:
    include_props_interface: true
    include_error_boundary: true

testing:
  framework: jest
  style: bdd
  coverage_target: 80
```

### Writing Project Output Style

```yaml
writing_style:
  voice:
    person: third
    tense: past
    formality: professional
  tone:
    primary: informative
    secondary: engaging
  language:
    complexity: intermediate
    reading_level: grade_10

structure:
  paragraphs:
    length: medium
    style: varied
  sentences:
    length: varied
    complexity: mixed

content:
  detail_level: balanced
  examples:
    frequency: regular
    style: practical
```

### Research Project Output Style

```yaml
research_style:
  writing:
    formality: academic
    objectivity: high
    citations: required
  
  analysis:
    statistical_rigor: high
    visualization: included
    reproducibility: emphasized
    
  documentation:
    methodology: detailed
    assumptions: explicit
    limitations: acknowledged
```

## 🔧 How Templates Work

### Template Structure

```
templates/
├── web/
│   └── .claude/
│       ├── agents/
│       │   ├── frontend-developer.md
│       │   └── api-designer.md
│       ├── commands/
│       │   ├── create-component.md
│       │   └── add-api-endpoint.md
│       └── output-style.yaml
├── writing/
│   └── .claude/
│       ├── agents/
│       │   ├── editor.md
│       │   └── outline-developer.md
│       ├── commands/
│       │   └── add-chapter.md
│       └── output-style.yaml
└── research/
    └── .claude/
        ├── agents/
        ├── commands/
        └── output-style.yaml
```

### Customization Process

1. **Templates are copied** to your project during scaffolding
2. **You can customize** them for your specific needs
3. **Version control** tracks your customizations
4. **Updates don't override** your changes

### Creating Custom Templates

To add your own project type:

1. Create a new folder in `templates/[your-type]/`
2. Add `.claude/agents/` with specialized agents
3. Add `.claude/commands/` with useful commands
4. Add `.claude/output-style.yaml` with preferences
5. Use `/scaffold-all "project" "your-type"`

## 📊 Benefits of Project-Specific Tools

### Immediate Productivity
- No need to configure Claude Code for each project
- Domain-specific commands ready to use
- Agents understand your project type

### Consistency
- Same tools across team members
- Standardized output formatting
- Predictable AI behavior

### Quality
- Best practices built into commands
- Agents enforce standards
- Output style maintains consistency

### Efficiency
- Common tasks automated
- Less prompt engineering needed
- Faster development cycles

## 🚀 Usage Examples

### Web Project Workflow
```bash
# Scaffold the project
/scaffold-all "my-app" "web"

# Navigate to project
cd projects/my-app

# Use project-specific commands
/create-component Header functional
/add-api-endpoint products GET
/optimize-bundle

# Agents work automatically
# frontend-developer creates components
# api-designer handles endpoints
# performance-optimizer improves speed
```

### Writing Project Workflow
```bash
# Scaffold the project
/scaffold-all "my-book" "writing"

# Navigate to project
cd projects/my-book

# Use writing commands
/add-chapter 1 "The Beginning"
/check-consistency
/word-count

# Agents assist automatically
# editor reviews your drafts
# outline-developer plans structure
# consistency-checker maintains style
```

## 🔄 Evolving Your Tools

As your project grows, you can:

1. **Add new agents** in `.claude/agents/`
2. **Create new commands** in `.claude/commands/`
3. **Refine output style** in `.claude/output-style.yaml`
4. **Share improvements** back to templates

## 📝 Command Development Guide

### Creating a New Command

```markdown
---
description: What this command does
argument-hint: [arg1] [arg2]
allowed-tools: Read, Write, Edit
---

# Command Title

Description of what happens when this command runs.

## Parameters
- $1: First argument description
- $2: Second argument description

## Process
1. Step one
2. Step two
3. Step three

## Output
What the user receives
```

### Creating a New Agent

```markdown
---
name: agent-name
description: When to use this agent
tools: Read, Write, Edit, Bash
---

You are a [role] specializing in [domain].

## Your Expertise
- Skill 1
- Skill 2

## When Invoked
1. What you do first
2. What you do second

## Quality Standards
- Standard 1
- Standard 2
```

## 🎯 Best Practices

1. **Use project-specific commands** instead of general ones
2. **Let specialized agents** handle domain tasks
3. **Trust the output style** for consistency
4. **Customize gradually** as you learn what works
5. **Share useful tools** back to the template library

Your project-specific tools transform Claude Code from a general assistant into a domain expert for your exact needs!