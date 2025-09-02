---
description: Generate a comprehensive PRD for your project
argument-hint: [project-name] [project-type]
---

# Scaffold Product Requirements Document

Generate a complete PRD in `projects/$1/docs/PRD.md`

**Project Name:** $1
**Project Type:** $2

## Project Context

First, tell me about your project:
- What type of project? (web app, API, library, etc.)
- What problem does it solve?
- Who are the users?
- What are the key features?

## PRD Generation

Based on @guides/PRD.md template, I'll create a PRD with:

1. **Problem & Outcome** - Clear problem statement and measurable outcomes
2. **Users & Insights** - Target users and evidence
3. **Scope & Non-Goals** - What's in and out
4. **Solution Sketch** - High-level approach
5. **Functional Requirements** - Specific features
6. **Acceptance Criteria** - Testable requirements
7. **Non-Functional Requirements** - Performance, security, accessibility
8. **Metrics & Instrumentation** - Success measurement
9. **Dependencies, Constraints, Risks** - Known challenges
10. **Plan, Rollout & Safeguards** - Delivery strategy
11. **Ownership & Decisions** - Roles and responsibilities

## Customization

The PRD will be tailored for:
- $ARGUMENTS project type
- Industry best practices
- Compliance requirements if applicable
- Scale considerations

Would you like to provide specific requirements or should I generate a template based on the project type?