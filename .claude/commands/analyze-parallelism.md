---
description: Analyze tasks for parallel execution opportunities
---

# Analyze Task Parallelism

Review existing tasks and identify maximum parallelization opportunities.

## Analysis Steps

1. Read current TASKS.md if it exists
2. Identify all task dependencies
3. Find independent task groups
4. Calculate critical path
5. Suggest optimal team allocation

## Parallelization Metrics

Calculate and report:
- Parallelism rate (% of tasks that can run in parallel)
- Critical path length
- Optimal team size for maximum efficiency
- Potential timeline with different team sizes

## Optimization Suggestions

Provide recommendations for:
- Breaking down tasks that block parallelization
- Reordering tasks to reduce dependencies
- Creating mock interfaces to enable parallel work
- Using feature flags for independent development

## Output

Generate a parallelization report showing:
- Current parallelization efficiency
- Bottlenecks and blockers
- Recommended changes for better parallelization
- Timeline comparison (sequential vs parallel)