Here’s the spine of a high-leverage `CLAUDE.md`—the “work contract” that keeps Claude Code fast, safe, and on-rails.

## What `CLAUDE.md` is for

It tells Claude **what to read first, what rules are hard limits, what tools it may use, and the exact loop it must follow** before touching code. Claude Code can read your repo, edit files, run commands, and make commits—so this file is your governor and guardrails. ([docs.anthropic.com][1], [Anthropic][2])

## The essential sections

1. **Reading order & canonical sources**
   A short list—PRD, ARCHITECTURE, RULES, DESIGN SYSTEM, TOKENS.json—linked in the order Claude must consult them. Add “if conflict, RULES beats all.” ([docs.anthropic.com][3])

2. **Operating constraints (non-negotiables)**
   Languages and versions, allowed libs, security/privacy rules, perf budgets, logging/telemetry requirements, dependency policy (when ADRs are required).

3. **Work loop (plan → implement → verify → document)**
   Force Claude to: propose a plan; get confirmation; implement in small diffs; write/extend tests; self-check against acceptance criteria; update docs/ADRs; open a PR with a checklist. Anthropic’s agentic-coding guidance stresses structured, deliberate steps over “just code.” ([Anthropic][4])

4. **Task format & definition of done**
   A tiny template (context, input/output contract, acceptance tests, out-of-scope). Claude should refuse or ask clarifying questions if any field is missing.

5. **Tooling & data access (MCP)**
   List which MCP servers Claude may use (e.g., `@docs`, `@jira`, `@postgres`) and safe usage rules. Show how to reference resources inline, e.g.
   `Compare @docs:file://PRD.md with @docs:file://ARCHITECTURE.md` or `@postgres:schema://users`. These `@server:resource` mentions are auto-fetched in Claude Code. ([docs.anthropic.com][5], [modelcontextprotocol.io][6])

6. **Coding standards & patterns**
   Point to `CODING_STANDARDS.md`, `PATTERNS.md`, `ANTI_PATTERNS.md`. Require ADRs for new frameworks or cross-cutting changes.

7. **UI/design rules (if there’s a front end)**
   Link `design/DESIGN_SYSTEM.md` and `design/TOKENS.json`. State: “Never hard-code colors; use tokens. Enforce accessibility targets (WCAG 2.2 AA).”

8. **Refusal & escalation policy**
   When requirements conflict, when security is at risk, or when acceptance tests are vague, Claude must stop and ask. If a change violates RULES, refuse.

9. **Commit/PR protocol**
   Branch naming, commit style, PR template, checklists (tests, logs, docs, ADR updated), and what to include in the PR description.

10. **Safety rails for agentic behavior**
    No new services/dependencies without ADR; smallest viable change first; avoid migrations without rollbacks; never touch secrets; cost/latency budgets. Anthropic’s best-practices repeatedly encourage conservative, reviewable steps. ([Anthropic][4])

---

## A compact `CLAUDE.md` you can drop in

```md
# CLAUDE.md — Project Work Contract

## Read These First (in order)
1. @docs:file://docs/PRD.md
2. @docs:file://docs/ARCHITECTURE.md
3. @docs:file://docs/RULES.md
4. @docs:file://docs/CODING_STANDARDS.md
5. @docs:file://docs/PATTERNS.md and @docs:file://docs/ANTI_PATTERNS.md
6. (If UI) @docs:file://docs/design/DESIGN_SYSTEM.md and @docs:file://docs/design/TOKENS.json

If documents conflict: RULES > ARCHITECTURE > PRD > STANDARDS.

## Non-Negotiable Constraints
- Runtime & language: Node 20, TypeScript 5 (strict). Approved libs only (see RULES).
- Security: never commit secrets; validate all external input at boundaries; no PII in logs.
- Budgets: backend p95 ≤ 250 ms, frontend LCP ≤ 2.5 s; function memory ≤ 256 MB.

## Workflow (Follow Exactly)
1) Plan: propose a short plan and list files you’ll edit; wait for approval if instructed.
2) Implement: smallest viable change; keep diffs focused; update or add tests first.
3) Verify: run tests; compare output to PRD acceptance criteria; check logs/metrics hooks.
4) Document: update ADRs if you added deps or changed contracts; update readmes.
5) PR: open a PR with the checklist below.

## Task Description Template (must be present)
- Context:
- Goal:
- API/Contract:
- Acceptance tests:
- Out of scope:

If any field is missing or ambiguous → ask before coding.

## Tools & Data Access (MCP)
- Allowed: @docs, @jira, @postgres.
- Reference resources inline (auto-attached): e.g., Compare @postgres:schema://users with @docs:file://docs/DATA_MODEL.md.
- Forbidden: creating new external services without an ADR.

## UI/Design Rules (if applicable)
- Use only tokens from TOKENS.json; do not hard-code colors or spacing.
- Meet WCAG 2.2 AA; keep a visible focus ring; ensure keyboard paths.

## Commit & PR
- Commit style: conventional commits.
- PR must include: scope, approach, trade-offs, screenshots/logs; check all boxes:
  [ ] Tests added/updated  [ ] Budgets respected  [ ] No secrets/PII  [ ] Docs/ADR updated

## Refusal & Escalation
- Refuse changes that violate RULES or budgets.
- Escalate when requirements conflict or acceptance tests are incomplete.

## Anti-Overengineering
Prefer the smallest change that passes the tests and meets acceptance criteria.
```

---

## Why this shape works

* Claude Code **acts** on your repo; a crisp contract limits scope creep and sets a reviewable loop. ([docs.anthropic.com][1], [Anthropic][2])
* Using MCP `@server:resource` references lets Claude pull the exact docs, schemas, or tickets you name—reducing “guessing.” ([docs.anthropic.com][5])
* Anthropic’s agentic-coding guidance favors deliberate planning, small steps, and explicit checks; `CLAUDE.md` encodes that behavior. Community guides echo the same pattern and explicitly suggest creating `CLAUDE.md`. ([Anthropic][4], [Reddit][7])

If you want, I can tailor this template to your stack (TypeScript/Python/Go) and fold in your design tokens and acceptance-test format so the rules are immediately enforceable.

[1]: https://docs.anthropic.com/en/docs/claude-code/overview?utm_source=chatgpt.com "Claude Code overview"
[2]: https://www.anthropic.com/claude-code?utm_source=chatgpt.com "Claude Code: Deep coding at terminal velocity ..."
[3]: https://docs.anthropic.com/en/docs/claude-code/common-workflows?utm_source=chatgpt.com "Common workflows"
[4]: https://www.anthropic.com/engineering/claude-code-best-practices?utm_source=chatgpt.com "Claude Code: Best practices for agentic coding"
[5]: https://docs.anthropic.com/en/docs/claude-code/mcp?utm_source=chatgpt.com "Connect Claude Code to tools via MCP"
[6]: https://modelcontextprotocol.io/?utm_source=chatgpt.com "Model Context Protocol: Introduction"
[7]: https://www.reddit.com/r/ClaudeAI/comments/1k5slll/anthropics_guide_to_claude_code_best_practices/?utm_source=chatgpt.com "Anthropic's Guide to Claude Code: Best Practices for ..."
