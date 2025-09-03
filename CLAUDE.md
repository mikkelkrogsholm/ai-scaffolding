# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

This is a meta-framework scaffolding system that generates comprehensive project foundations for any type of project (coding, writing, research). It creates not just documentation templates but complete AI toolkits with specialized agents, commands, and output styles.

## Critical Architecture

### Project Creation Flow
1. User runs `/scaffold-all "[project-name]" "[type]"` from scaffolding repo
2. System creates `projects/[project-name]/` directory structure
3. Generates foundation documents in `projects/[project-name]/docs/`
4. Copies project-specific tools from `templates/[type]/.claude/` to project
5. Result: Complete project with documentation AND specialized AI toolkit

### Directory Structure
```
scaffolding/                    # This repository
├── guides/                     # Documentation templates (PRD, ARCHITECTURE, etc.)
├── .claude/                    # Scaffolding-level agents and commands
│   ├── agents/                # Project architect agents
│   └── commands/              # Scaffolding commands
├── templates/                  # Project-type specific tools
│   ├── web/.claude/           # Web project agents/commands/styles
│   ├── writing/.claude/       # Writing project tools
│   └── research/.claude/      # Research project tools
└── projects/                   # Generated projects (gitignored)
```

## Key Concepts

### Parallel Execution Philosophy
All generated TASKS.md files emphasize parallel work streams. The `parallel-optimizer` agent and `/analyze-parallelism` command are critical for maximizing concurrent execution and minimizing project timelines.

### Two-Tier Tool System
1. **Scaffolding tools** (in `.claude/`): For creating and managing projects
2. **Project tools** (in `templates/*/`): Copied to each project for domain-specific work

### Guide Dependencies
When modifying guides, maintain this precedence order:
PRD > RULES > ARCHITECTURE > CODING_STANDARDS > PATTERNS

## Primary Commands

### Project Scaffolding
- `/scaffold-all "[name]" "[type]"` - Creates complete project with all documents and tools
- `/scaffold-prd "[name]" "[type]"` - Creates just PRD for a project
- `/scaffold-tasks "[name]"` - Generates TASKS.md from existing PRD

### Analysis Commands
- `/analyze-parallelism` - Finds parallel execution opportunities
- `/detect-antipatterns` - Scans for code issues
- `/validate-docs` - Checks document consistency

## Working with Templates

When creating new project types:
1. Create `templates/[type]/.claude/` directory structure
2. Add specialized agents in `templates/[type]/.claude/agents/`
3. Add domain commands in `templates/[type]/.claude/commands/`
4. Create `templates/[type]/.claude/output-style.yaml`

## Important Agents

### Project Architects (for scaffolding)
- `code-project-architect` - Creates software project foundations
- `writing-project-architect` - Creates writing project structures
- `research-project-architect` - Creates research project frameworks

### Optimization Agents
- `task-decomposer` - Breaks requirements into parallel tasks (MUST USE after PRD)
- `parallel-optimizer` - Maximizes concurrent execution
- `document-validator` - Ensures cross-document consistency

## Development Workflow

When enhancing the scaffolding system:
1. Test new templates in `templates/` directory first
2. Update relevant guides in `guides/` to reflect changes
3. Ensure new patterns align with existing philosophy (parallelization, quality, consistency)
4. Generated projects go in `projects/` (gitignored) - never commit actual projects

## Quality Standards

All generated documents must:
- Include measurable acceptance criteria
- Support parallel execution where possible
- Follow patterns from PATTERNS.md
- Avoid anti-patterns from ANTI-PATTERNS.md
- Cross-reference related documents correctly

## Project-Specific Tool Generation

Each scaffolded project receives:
- Domain-specific agents (e.g., `frontend-developer` for web projects)
- Custom commands (e.g., `/create-component` for web, `/add-chapter` for writing)
- Tailored output-style.yaml configuration

These are copied from `templates/[type]/.claude/` to `projects/[name]/.claude/`