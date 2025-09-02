Here’s the spine of a **great `ARCHITECTURE.md`**—what to include, why it’s there, and how to keep it useful for both humans and AI coders.

# Purpose (one paragraph)

What you’re building, for whom, and the single most important constraint (speed, reliability, cost, privacy, etc.). State **in-scope** and **out-of-scope** in one short list each. This stops scope-creep before it starts.

# System at a glance (1 page max)

* **High-level diagram** (simple boxes and arrows).
* **Key components** with one-line responsibilities.
* **Data stores** and what lives where.
* **External dependencies** (APIs, queues, identity provider).
* **Runtime** (where it runs) and **deployment shape** (single service, microservices, serverless, etc.).
  Keep it short enough that a new dev—or an AI agent—can read and “load the world” in minutes.

# Constraints & quality goals

Plain language targets that drive design:

* Performance targets (for example: “p95 request under 300 ms, homepage under 2.0 s”).
* Reliability targets (uptime goals, recovery time, recovery point).
* Security & privacy (data classes, encryption, retention).
* Cost ceilings (for example: “keep monthly inference under €X”).
* Accessibility and internationalization if you ship UI.
  Each goal should be testable or monitored.

# Domain model (the nouns)

* Core entities (User, Project, Invoice…), their relationships, and invariants (“an Invoice must belong to exactly one Project”).
* Link or embed an ER diagram or a short table of fields + types.
* Note any multi-tenancy or partitioning rules.

# Component responsibilities (the verbs)

For each component/service/module:

* **Responsibility** (what it owns).
* **Interfaces** (public functions, events, endpoints).
* **Data it reads/writes** (tables, topics, buckets).
* **Scaling characteristics** (CPU heavy? IO heavy?).
* **Failure posture** (What happens when it’s down?).
  Avoid internal trivia; focus on *contracts*.

# Data flow for key user journeys

Pick 3–5 critical paths (signup, checkout, report generation). For each:

1. Trigger → steps → side effects.
2. Where we validate inputs and enforce permissions.
3. Where we emit logs/metrics/traces.
4. Timeouts, retries, and idempotency notes.
   Sequence diagrams (even ASCII or Mermaid) are gold here.

# External integrations

* Provider, purpose, endpoints used.
* Auth method (keys, OAuth, service account).
* Rate limits and backoff rules.
* Mock/contract-test strategy and sandbox info.
* “What if it’s down?” (circuit breaker, queue, cached fallback).

# Storage & schema evolution

* Engines used (relational, document, cache), why each.
* Sharding/partitioning rules and hot-key risks.
* Migrations: process, tooling, and rollback plan.
* Backups and restore drill (how often you test it).

# Security model

* Identity (who are you) and authorization (what can you do).
* Data classification (public, internal, confidential, sensitive).
* Secrets management (where secrets live, never in repo).
* Input validation at trust boundaries.
* Threats you actively mitigate (injection, SSRF, replay, privilege escalation) and how.
* Audit trails: what’s recorded and where.

# Reliability & operations

* Service level objectives and error budgets.
* Health checks (liveness/readiness), dependency health.
* Observability: required logs, metrics, traces, correlation IDs.
* Runbook pointers (common failures, dashboards, on-call workflow).
* Incident tagging and postmortem template.

# Performance & capacity

* Expected traffic profile (peaks, batch jobs, cron).
* Caching strategy (keys, TTLs, cache-busting rules).
* Concurrency limits, queue sizes, backpressure.
* Known hotspots and how to detect regressions.

# Build, deploy, and environments

* CI steps (lint → typecheck → tests → security scans → artifact).
* CD strategy (blue/green, canary, feature flags).
* Environment matrix (local, staging, prod) and what differs.
* Infra as code location and ownership.

# API contracts

* Link to OpenAPI/GraphQL schema (source of truth).
* Versioning policy (how breaking changes are rolled out).
* Error shape (uniform structure) and error codes table.
* Pagination, sorting, filtering conventions.

# UI architecture (if you have a UI)

* App shell vs. feature modules; routing strategy.
* State management (what is global vs local, persistence rules).
* Data-fetching boundaries (components don’t talk to the network directly; services do).
* Performance budgets (bundle size limits, image policy).
* Link to **design tokens** and component library contracts.

# Background work

* Job types (immediate, delayed, scheduled).
* Idempotency keys, retry policy, and poison-queue handling.
* Exactly-once vs at-least-once stance (be explicit).

# Cost & scaling plan

* Largest cost drivers (compute, storage, egress, inference).
* How cost scales with users/traffic and which levers you pull first.

# Change log & decisions

* Link to **ADRs** (Architecture Decision Records).
* “When to write an ADR” rule (new dependency, pattern, or external service).

# Testing strategy (how we know it works)

* Unit, integration, contract, and end-to-end tests: what each must cover.
* Test data strategy (fixtures, generators, anonymization for prod copies).
* Performance and security tests (load, fuzzing, dependency scanning).

# Deprecation & compatibility

* Policy for removing endpoints, fields, or events (timeline and comms).
* Feature flag lifecycle.

# Glossary

One table that decodes project jargon and acronyms. Your future self will thank you.

---

## “Make it real” tips

* **Keep the main file short.** Put details in linked docs (`/design/TOKENS.json`, `/openapi.yaml`, `/DECISIONS/*`).
* **Favor contracts over prose.** Tables, examples, and small diagrams beat paragraphs.
* **Be executable where possible.** Link to scripts, dashboards, and checklists the team actually uses.
* **Add a “Read me next” box** at the top with the 5 most important links.
* **Update flow:** Every merged ADR or schema change must update `ARCHITECTURE.md` in the same pull request.

---

## Copy-paste starter (minimal, agent-friendly)

```
# Architecture

## Purpose
<what we’re building, for whom, primary constraint>

## System at a glance
- Diagram: <link or Mermaid>
- Components: <A, B, C – one line each>
- Data stores: <dbs, caches>
- External deps: <apis, idp, queue>
- Runtime & deploy: <where/how>

## Constraints & Goals
- Performance: <p95, budgets>
- Reliability: <SLOs>
- Security/Privacy: <data classes, retention>
- Cost: <caps or targets>

## Domain Model
<entities + relationships; link to ERD>

## Components
<Component A> — responsibility, interface, data, scaling, failure posture  
<Component B> — …

## Key Flows
<signup> → steps, validation, authz, side-effects, retries/idempotency  
<flow 2> → …

## Storage & Schema
<engine choices, migrations, backups/restore plan>

## Security
<authn/authz model, secrets, threats mitigated, audit>

## Reliability & Observability
<health checks, logs, metrics, traces, dashboards>

## Performance & Capacity
<caching, concurrency, backpressure, hotspots>

## Build & Deploy
<ci pipeline, cd strategy, env matrix, IaC path>

## APIs
<link to OpenAPI/GraphQL, error shape, versioning>

## UI Architecture (if applicable)
<state strategy, data boundaries, budgets, link to tokens>

## Background Jobs
<queues, retries, idempotency>

## Testing Strategy
<unit/integration/contract/e2e; perf/security>

## Cost & Scaling
<drivers, projections, levers>

## Decisions
<link to ADRs>

## Deprecation Policy
<how we remove or change contracts>

## Glossary
<plain-English definitions>
```

Treat `ARCHITECTURE.md` as the map, not the territory: brief, contractual, and linked to living sources. Next natural step is to tailor this template to your stack (for example: TypeScript + Next.js, Python + FastAPI, Go + gRPC) and pre-fill the “contracts” sections so coders—human and AI—can’t wander off the path.
