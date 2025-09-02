# The Ultimate Guide to TASKS.md - Turning Plans into Parallel Action

## What TASKS.md is for

TASKS.md transforms your PRD's requirements into executable, parallel work streams. It's the bridge between "what we're building" (PRD) and "who's doing what today" (sprint planning). This document enables multiple developers to work simultaneously without blocking each other.

---

## Core principles

1. **Small chunks win:** Tasks should be 0.5-2 days max. If bigger, decompose further.
2. **Parallel by default:** Identify independent work streams from day one.
3. **Dependencies explicit:** Every blocker is mapped; no surprise waits.
4. **Acceptance = testable:** Each task has clear, verifiable completion criteria.
5. **Traceable to value:** Every task links back to a PRD requirement.
6. **Owner-ready:** Tasks are specific enough that any team member can grab and go.

---

## The TASKS.md structure

### 1) Task Metadata Table

The heart of TASKS.md - a single source of truth for all work:

```markdown
| ID | Title | PRD-Link | Size | Dependencies | Can-Parallel | Owner | Status | Acceptance Criteria |
|----|-------|----------|------|--------------|--------------|-------|--------|-------------------|
| T001 | Create user model | PRD-A1 | 0.5d | None | Yes | - | Pending | Schema migrated, CRUD tests pass |
| T002 | Build auth service | PRD-A1 | 1d | T001 | Yes | - | Pending | JWT issued, refresh works |
| T003 | Login UI component | PRD-A1 | 1d | None | Yes | - | Pending | Form validates, submits, shows errors |
```

**Column definitions:**
- **ID:** Unique identifier (T001, T002...)
- **Title:** Clear, action-oriented description
- **PRD-Link:** Which requirement this satisfies
- **Size:** Time estimate (0.5d, 1d, 2d max)
- **Dependencies:** What must complete first (use IDs)
- **Can-Parallel:** Can this run alongside its siblings?
- **Owner:** Who's working on it (- if unassigned)
- **Status:** Pending | In-Progress | Review | Complete | Blocked
- **Acceptance:** Specific, testable criteria

### 2) Parallel Work Streams

Group independent tasks into streams that can run simultaneously:

```markdown
## Parallel Streams

### Stream A: Authentication (Backend)
**No external dependencies - Start immediately**
- T001: Create user model
- T002: Build auth service  
- T004: Session management
- T005: Password reset flow

### Stream B: UI Foundation
**No external dependencies - Start immediately**
- T003: Login UI component
- T006: Registration form
- T007: Password reset UI
- T008: Error display component

### Stream C: Data Layer
**No external dependencies - Start immediately**
- T009: Database schema
- T010: Migration scripts
- T011: Seed data
- T012: Backup strategy

### Integration Points (After streams complete)
- T013: Connect auth UI to backend (Requires: Stream A + B)
- T014: End-to-end auth flow (Requires: T013)
```

### 3) Dependency Graph

Visual or text representation of task relationships:

```markdown
## Dependency Chain

```
T001 (user model) 
  ├── T002 (auth service)
  │   ├── T013 (integration)
  │   └── T014 (e2e tests)
  └── T004 (sessions)

T003 (login UI) 
  ├── T013 (integration)
  └── T014 (e2e tests)

T009 (database) - standalone
T010 (migrations) - standalone
```
```

### 4) Sprint Allocation

How to distribute tasks across sprints:

```markdown
## Sprint Planning

### Sprint 1 (All parallel)
- Developer 1: T001, T002, T004 (Auth backend)
- Developer 2: T003, T006, T007 (Auth UI)  
- Developer 3: T009, T010, T011 (Database)

### Sprint 2 (Integration)
- All devs: T013, T014 (Integration & testing)
- Start next feature streams
```

### 5) Task Templates

Detailed template for complex tasks:

```markdown
## Task Detail: T002 - Build auth service

**Requirement:** Users can authenticate with email/password (PRD-A1)

**Inputs:**
- User model (T001)
- Password requirements from RULES.md

**Outputs:**
- JWT token generation
- Refresh token mechanism
- Validation endpoints

**Technical Approach:**
1. Implement JWT service
2. Create login endpoint
3. Add refresh logic
4. Build validation middleware

**Acceptance Tests:**
- [ ] Login returns JWT with correct claims
- [ ] Refresh token extends session
- [ ] Invalid credentials return 401
- [ ] Tokens expire correctly
- [ ] Rate limiting active

**Risks:**
- Token storage strategy (cookies vs localStorage)
- Session length vs security trade-off
```

---

## Task decomposition patterns

### Pattern 1: Vertical Slices
Split features into full-stack slices:
```
Feature: User Profile
├── T020: Profile model + API
├── T021: Profile UI component  
├── T022: Profile edit form
└── T023: Profile picture upload
```

### Pattern 2: Horizontal Layers
Split by technical layer:
```
Feature: Shopping Cart
├── Database Layer (T030-T033)
├── Service Layer (T034-T037)
├── API Layer (T038-T041)
└── UI Layer (T042-T045)
```

### Pattern 3: Risk-First
Tackle unknowns early:
```
1. Spike tasks (research/POC)
2. Core risky features
3. Standard features
4. Polish tasks
```

---

## Estimation guidelines

**0.5 day tasks:**
- Single file changes
- Simple CRUD operations
- Basic UI components
- Configuration updates

**1 day tasks:**
- Multi-file features
- Service with tests
- Complex UI with state
- API endpoint with validation

**2 day tasks (max):**
- Full feature slice
- Complex integrations
- Performance optimizations
- Data migrations

**Red flags (decompose these):**
- "Implement entire auth system" → 10+ smaller tasks
- "Build the UI" → Break into components
- "Database layer" → Split by entity

---

## Parallel optimization rules

### Can run in parallel:
- Different bounded contexts (user vs. product)
- Different layers (UI vs. database)
- Different features without shared state
- Test suites for different modules

### Must be sequential:
- Schema → Repository → Service → Controller
- Authentication → Authorization
- Core models → Business logic
- API contracts → Client implementation

### Parallel accelerators:
- Mock interfaces for development
- Feature flags for isolation
- Separate git branches
- Independent test databases

---

## Status tracking

### Task states:
```
Pending → In-Progress → Review → Complete
                ↓
            Blocked → Unblocked
```

### Daily updates:
```markdown
## 2024-01-15 Status

**Completed today:**
- T001: User model ✓
- T003: Login UI ✓

**In progress:**
- T002: Auth service (75%, JWT done, refresh tomorrow)
- T009: Database schema (50%, reviews needed)

**Blocked:**
- T013: Waiting on T002 completion

**Tomorrow's plan:**
- Complete T002
- Start T004, T010
```

---

## Integration with other docs

### From PRD → TASKS:
1. Each PRD requirement generates 1+ tasks
2. Acceptance criteria derive from PRD criteria
3. Priority follows PRD priority

### To SPRINT:
1. Tasks feed sprint planning
2. Parallel streams inform team allocation
3. Dependencies set sprint boundaries

### With ARCHITECTURE:
1. Technical tasks follow architecture layers
2. Component boundaries guide task splits
3. Integration points become explicit tasks

---

## Anti-patterns to avoid

❌ **Task too large:** "Build authentication" (break it down!)
❌ **Vague acceptance:** "Make it work" (define "work")
❌ **Hidden dependencies:** Surprise blocks kill parallelism
❌ **Serial thinking:** Not looking for parallel opportunities
❌ **Missing integration tasks:** Forgetting to connect streams
❌ **No owner assignment:** Tasks in limbo
❌ **Skipping estimates:** Can't plan sprints without sizes

---

## Copy-paste template

```markdown
# TASKS

## Overview
Total tasks: X | Estimated days: Y | Parallel streams: Z

## Task Registry

| ID | Title | PRD-Link | Size | Dependencies | Can-Parallel | Owner | Status | Acceptance Criteria |
|----|-------|----------|------|--------------|--------------|-------|--------|-------------------|
| T001 | | | | | Yes/No | - | Pending | |

## Parallel Streams

### Stream A: [Name]
**Dependencies:** None - Start immediately
- T00X: 
- T00X: 

### Stream B: [Name]
**Dependencies:** None - Start immediately
- T00X:
- T00X:

### Integration Points
**Dependencies:** Stream A + B must complete
- T0XX:

## Dependency Graph
```
[ASCII or Mermaid diagram]
```

## Sprint Allocation

### Sprint 1
- Dev 1: [Stream A tasks]
- Dev 2: [Stream B tasks]
- Dev 3: [Stream C tasks]

## Daily Status
[Updated each standup]

## Risks & Mitigations
[Critical path items, bottlenecks]
```

---

## Automation opportunities

### Commands to build:
- `/decompose-requirement [PRD-ID]` - Generate tasks from requirement
- `/find-parallels` - Identify parallel opportunities
- `/estimate-sprint` - Calculate sprint capacity
- `/assign-tasks` - Balance work across team
- `/visualize-dependencies` - Generate dependency graph
- `/find-blockers` - Identify critical path

### Agents to create:
- `task-decomposer` - Breaks down PRD into tasks
- `parallel-optimizer` - Maximizes parallel execution
- `sprint-planner` - Allocates tasks to sprints
- `blocker-detector` - Identifies bottlenecks

---

## Success metrics

- **Parallelism rate:** % of tasks that can run in parallel (target: >60%)
- **Block frequency:** How often devs are blocked (target: <10%)
- **Estimation accuracy:** Actual vs. estimated time (target: ±20%)
- **Task completion rate:** Tasks done per sprint (trend: increasing)
- **Integration success:** First-try integration rate (target: >80%)

---

## Final note

Great TASKS.md turns a sequential 3-month project into a parallel 1-month sprint. The secret: aggressive decomposition + parallel thinking + explicit dependencies. Every morning, every developer should know exactly what they can work on without waiting for anyone else.