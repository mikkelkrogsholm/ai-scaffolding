---
description: Generate TASKS.md from PRD requirements
argument-hint: [project-name]
---

# Generate TASKS.md from PRD

Analyze the PRD in `projects/$1/docs/PRD.md` and create TASKS.md with parallel work streams.

**Project Name:** $1

## Instructions

1. Read @guides/PRD.md and @guides/TASKS.md to understand the formats
2. Extract all requirements from the PRD
3. Break down each requirement into 0.5-2 day tasks
4. Identify dependencies between tasks
5. Group independent tasks into parallel streams
6. Create a complete TASKS.md following the template

## Task Decomposition Rules

- Each task should be completable in 0.5-2 days
- Include clear acceptance criteria for each task
- Mark tasks that can run in parallel
- Identify integration points between streams
- Estimate realistic time for each task

## Output Format

Create a TASKS.md with:
- Task registry table with all columns
- Parallel work streams clearly defined
- Dependency graph (ASCII or description)
- Sprint allocation suggestions
- Risk identification for critical path

Focus on maximizing parallelization to reduce overall timeline.

$ARGUMENTS