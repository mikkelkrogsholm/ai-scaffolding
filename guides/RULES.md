You want RULES.md to be the non-negotiable “law of the land.” It should be short, testable, and written with MUST/NEVER language (not “should”). Anything that can’t be enforced automatically goes in Standards or Patterns—not here.

Here’s what a great RULES.md covers, plus a drop-in template.

## What to include (and why)

1. **Purpose & scope**
   Say what this file is for and what it isn’t. Keep it about hard constraints.

2. **Authority & precedence**
   Define the order of truth. Example: PRD > RULES.md > Architecture > Coding Standards > Patterns. State that RULES wins in conflicts.

3. **Tech & architecture constraints**
   Languages, versions, frameworks allowed/forbidden. Layering rules (who may import whom). One or two core architecture constraints (e.g., ports-and-adapters, no service talking directly to the database of another service).

4. **Security & privacy (hard requirements)**
   Secrets handling, data classification, encryption, input validation, authZ (authorization) boundaries, dependency scanning, SBOM (software bill of materials), incident reporting windows.

5. **Data rules**
   Source of truth per entity, migration rules, PII handling, retention/deletion, backups and restores.

6. **Dependencies & supply chain**
   Allowed package registries, license allowlist/denylist, pinning policy, update cadence, forbidden libraries, process for adding a new lib (ADR required).

7. **Error handling & observability**
   Required error shape, never-swallow rules, structured logging fields, no PII in logs, mandatory metrics/traces.

8. **Performance & cost budgets**
   Per-endpoint latency ceilings (p90/p95), memory caps, max external calls, LLM cost/latency ceilings. Red lines, not aspirations.

9. **Testing & quality gates (as rules)**
   What blocks a merge: unit/integration/contract tests present, minimal coverage, snapshot limits, flaky tests policy.

10. **CI/CD & release**
    What must pass, who can approve, branch protections, no manual prod changes (infrastructure as code only), feature flag rules.

11. **UI/Design hard rules**
    Only design tokens from the tokens file, accessibility targets (WCAG level), forbidden inline styles, dark-mode constraints.

12. **AI agent guardrails**
    What the AI may/may not do: read these files first, refuse tasks that violate RULES, never add dependencies or create new services without ADR approval, never send secrets to tools, obey rate limits, break work into reviewed tasks.

13. **Governance**
    How RULES changes: ADR + designated approvers, versioning, sunset dates on temporary exceptions.

14. **Appendix: machine-enforced policies**
    Link to the actual configs that enforce the rules (linters, scanners, CI workflows). This keeps RULES.md small and real.

---

## Drop-in RULES.md (copy, then tailor)

```markdown
# RULES

**Purpose.** This file defines non-negotiable constraints for this codebase.  
**Precedence.** When documents disagree: PRD > RULES.md > ARCHITECTURE.md > CODING_STANDARDS.md > PATTERNS.md.  
**Enforcement.** CI blocks merges that violate these rules. Temporary exceptions require an ADR with an expiry date.

---

## 1) Tech & Architecture

- We **MUST** use: **TypeScript 5.x**, **Node 20.x**, **PostgreSQL 15+**.
- We **MUST NOT** use: runtime `any`, reflection-based DI, or service-to-service DB access.
- Layering: UI → Application → Domain → Infrastructure. Higher layers **MUST NOT** import lower-level implementations directly; depend on interfaces.
- Network: Services **MUST** communicate via HTTP/JSON; gRPC is forbidden unless an ADR approves it.

## 2) Security & Privacy

- Secrets **MUST NOT** be committed. Use the secrets manager; local dev uses `.env.example` without values.
- All external input **MUST** be validated at the boundary.
- AuthN/AuthZ: Every write endpoint **MUST** check authorization; no “read as write” shortcuts.
- Data classes: { public | internal | confidential | sensitive }. Sensitive data **MUST** be encrypted at rest and in transit.
- Logs **MUST NOT** contain PII. Keys: `traceId`, `userId?`, `requestId`, `severity`, `event`.
- We **MUST** run SAST, DAST, secret scanning, dependency audit, and produce an SBOM on each release.

## 3) Data Rules

- Single source of truth per entity is the service that owns it. Cross-service reads go through that service’s API.
- Schema changes **MUST** be via migrations committed to the repo. Down migrations are required.
- Retention: PII **MUST** follow the retention table in `SECURITY_PRIVACY.md`. Deletion **MUST** be irreversible.

## 4) Dependencies & Supply Chain

- Registries: npmjs.com (scoped), GitHub Packages. Direct Git URLs are forbidden.
- Licenses allowed: MIT, Apache-2.0, BSD-2/3. Copyleft (e.g., GPL) is forbidden unless legal approves via ADR.
- Version policy: all runtime deps **MUST** be pinned; weekly audit fixes are mandatory.
- Adding a dependency **REQUIRES** an ADR with: purpose, footprint, risks, removal plan.

## 5) Error Handling & Observability

- Errors **MUST** bubble with a typed shape: `{ code, message, details?, correlationId }`.
- We **MUST NOT** swallow errors. `catch` blocks either rethrow or return a typed error and log at `error`.
- Structured logs (JSON) only. No `console.log` in production code.
- Metrics **MUST** include: request count, duration, error rate, external-call duration, queue lag. Traces **MUST** propagate `traceId`.

## 6) Performance & Cost Budgets

- API latency ceilings: p95 ≤ **250 ms** for read, **400 ms** for write. Exceeding budgets blocks release.
- DB queries **MUST NOT** exceed **100 ms** p95 without an index plan.
- LLM calls: p95 latency ≤ **2.5 s**, cost ≤ **$0.02** per user action. Streaming is mandatory for long responses.
- Background jobs **MUST** be idempotent; retries with exponential backoff; max 5 attempts.

## 7) Testing & Quality Gates

- Every PR **MUST** include tests that cover the changed behavior.
- Minimum coverage: **80%** lines and **90%** of critical paths (see `EVALS_AND_TESTING.md`).
- Contract tests **MUST** pin external API assumptions.
- Snapshots are limited to stable, human-readable outputs; UI pixel snapshots are forbidden.

## 8) CI/CD & Release

- Protected branches: `main`, `release/*`. Squash merges only.
- Required checks: build, lint, typecheck, tests, security scan, license check, size budget (UI bundles), SBOM.
- Deployments **MUST** be automated. Manual changes to prod are forbidden.
- Feature flags **MUST** default to off and be removable; stale flags are removed monthly.

## 9) UI/Design (hard rules)

- UI **MUST** use tokens from `/docs/design/TOKENS.json`. Hard-coded colors, spacing, or fonts are forbidden.
- Accessibility: WCAG **2.2 AA** minimum. Keyboard access and visible focus rings are mandatory.
- Dark mode **MUST** be supported for any new component if an equivalent exists.

## 10) AI Agent Guardrails

- Agents **MUST** read, in order: `PRD.md` → `RULES.md` → `ARCHITECTURE.md` → `CODING_STANDARDS.md` → `/docs/design/TOKENS.json`.
- Agents **MUST** propose a plan and wait for approval before adding files, deleting files, or changing schemas.
- Agents **MUST NOT** add dependencies, create services, or modify CI without an approved ADR.
- Agents **MUST** refuse tasks that would violate RULES and ask for clarification when requirements are ambiguous.
- Agents **MUST NOT** send secrets or production data to tools or external services.

## 11) Governance

- Changes to RULES require: ADR + approval from **{Role/Names}**. Each change includes a review date.
- Temporary exceptions require an ADR with an expiry date; CI enforces expiry.

---

## Appendix: Machine-Enforced Policies

These configs **MUST** align with the above and are treated as part of RULES:

- `/config/eslint/*`, `/config/ruff.toml`, `/config/mypy.ini`
- `/config/stylelint.config.cjs` (forbids non-token colors)
- `/config/semgrep.yml`, `/config/depcheck.yml`, `/config/license-rules.yml`
- `.github/workflows/*.yml` (required checks), `/config/sbom.yml`
- `/config/opa/*.rego` (optional policy as code)

```

### Tips that keep RULES.md effective

* Keep it under \~300–500 lines; push explanations to Standards/Architecture.
* Use MUST/NEVER verbs and measurable numbers (p95 ≤ 250 ms), not vibes.
* Back every rule with a **config** or **checklist** the bot and CI can run.
* Add an expiry date to exceptions, and let CI fail when they lapse.

If you want, I can tailor the template to your stack (e.g., TS + Node + Postgres + React) and pre-generate the matching CI/linter configs so the rules are actually enforced rather than just laminated.
