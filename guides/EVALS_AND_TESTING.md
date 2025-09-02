Here’s a no-nonsense blueprint you can drop straight into `EVALS_AND_TESTING.md`. It keeps humans and AI on the same rails, covers normal software tests, and adds proper evaluations for AI features.

# EVALS\_AND\_TESTING

## 1) Purpose

What this file is for: to prove the product meets the PRD, keeps meeting it after changes, and doesn’t quietly rot. Tests check code; evals check behavior and quality (including AI outputs).

## 2) Traceability to the PRD

List every PRD acceptance criterion and how it’s verified.

| PRD ID | Criterion (plain English)                | Verification (test/eval name) | Type       | Threshold          |
| ------ | ---------------------------------------- | ----------------------------- | ---------- | ------------------ |
| PRD-A1 | “User can reset password via email link” | `auth_reset_password_e2e`     | End-to-end | 100% pass          |
| PRD-B3 | “Answer must cite sources”               | `qa_citations_eval`           | LLM eval   | ≥ 95% with sources |

Keep this table current. If it’s not here, it’s not a requirement—or it’s untested.

## 3) Test types (what we run and why)

Explain like you would to a new teammate:

* **Unit tests:** Tiny, fast checks of one function or class. Mock external stuff.
* **Integration tests:** A few components together (service + database). Real I/O allowed.
* **Contract tests:** Our API and the client agree on request/response shapes. Prevents breaking changes.
* **End-to-end (E2E):** Clicks and keystrokes from UI down to the database. Realistic, slower, fewer.
* **Data & migrations tests:** Schemas, migrations, seed data, and reports produce expected results.
* **UI tests:**

  * Interaction (does it work),
  * Visual regression (screenshots haven’t drifted),
  * Accessibility (keyboard, screen reader, color contrast).
* **Performance tests:** Latency, throughput, memory, bundle size. Define budgets.
* **Security & supply chain:** Static analysis, dependency audit, secrets scan.
* **AI/LLM evals:** Quality and safety of model outputs (see next section).

## 4) AI/LLM evaluations (how we judge AI output)

General rule: prefer objective checks over vibes.

**Eval set design**

* **Golden cases:** Fixed inputs with expected outputs or a scoring rubric.
* **Distribution:** Include frequent cases, edge cases, and “gotchas” users will hit.
* **Change policy:** Add new cases for new behavior; don’t delete failing ones to “fix” the dashboard.

**Scoring methods**

* **Exact/structured match:** JSON schema validation, regex on required fields, presence of citations/IDs.
* **Task success:** Did the generated code pass its unit tests? Did the agent complete the workflow?
* **Semantic match:** Compare meaning, not wording (embedding similarity or judge-model w/ rubric).
* **Cost & speed:** Tokens, API calls, wall-clock; budgets per feature.

**Agent/code-gen specific**

* **pass\@k:** If we allow multiple attempts, what fraction eventually passes the tests?
* **Sandboxed execution:** Run generated code against the project’s tests with time/memory limits.
* **Tool-use correctness:** Count valid vs wrong tool calls; verify parameters satisfy contracts.

**Safety evals**

* **Prompt-injection resistance:** Library of attack strings; must refuse or sanitize.
* **Toxicity/PII leakage:** No slurs, no private data in outputs.
* **Factuality (when required):** Cite from allowed sources; flags when citations are missing or irrelevant.

**Quality gates (examples)**

* Core Q\&A feature: ≥ 92% rubric score, ≤ 1% safety violations, p95 latency ≤ 1.2s.
* Code-gen tasks: pass\@3 ≥ 80%, zero filesystem writes outside `/src`, cost ≤ 3k tokens/task.

## 5) Tooling & how to run

Document the exact commands. Keep it boring and copy-pastable.

* **Local quick checks**

  * `make test:unit`
  * `make test:int`
  * `make test:e2e` (spins up services via `docker compose`)
  * `make eval:llm` (runs eval set, writes `reports/evals.json`)
* **Pre-commit hooks**
  Lint, typecheck, formatting, secrets scan.
* **CI workflow**

  1. Install deps & cache
  2. Unit + integration tests
  3. Contract & schema checks
  4. E2E (smoke subset on PRs; full nightly)
  5. LLM evals (fast subset on PRs; full nightly)
  6. Upload coverage and eval reports; enforce gates

## 6) Environments and determinism

* **Seeds & randomness:** Fix seeds for tests; log seeds for evals so failures can be replayed.
* **Flaky tests policy:** If flaky, mark with `@flaky` and open an issue; two weeks to deflake or delete.
* **External calls:** Use mocks or test doubles by default; allow real calls only in dedicated suites.

## 7) Data management

* **Test data sources:** Synthetic by default; anonymized production snapshots allowed with sign-off.
* **Versioning:** Every dataset/eval set has a version and changelog.
* **PII rules:** No real emails, tokens, or IDs in fixtures; secrets pulled from a test secrets vault.

## 8) Performance & cost budgets

State hard numbers so machines can enforce them.

* **Backend:** p95 latency per endpoint, max memory, max queries per request.
* **Frontend:** Max JS bundle, image sizes, interaction latency.
* **AI:** Max tokens per request, retries budget, total eval run cost ceiling.

## 9) Reporting & dashboards

* **Artifacts:** `reports/coverage.xml`, `reports/evals.json`, `reports/perf.csv`.
* **Dashboards:** CI summary comment on PRs + a simple web dashboard (serve the JSONs) for trend lines.
* **Trend watching:** Track moving averages and alert on regressions > X%.

## 10) Gating policy (what blocks a merge)

* Any failing unit/integration/contract test.
* E2E smoke failing on PR → block.
* Eval thresholds missed for changed areas → block.
* Budgets exceeded (performance, bundle size, tokens) → block.
* Security checks failing (critical/high) → block.

Severity ladder for exceptions (who can approve, how long the waiver lasts).

## 11) Red-teaming (break it on purpose)

* **Schedule:** Weekly automated adversarial run; manual hit-list before each major release.
* **Libraries:** Prompt-injection strings, jailbreak sets, offensive content tests.
* **Exit criteria:** Zero critical jailbreaks; all high issues have documented mitigations.

## 12) Maintenance rules

* New feature → new tests/evals before merge.
* Bug fix → regression test first, then fix.
* When eval sets change, bump dataset version and note why in `EVALS_CHANGELOG.md`.
* When introducing a new dependency or pattern, link to its ADR.

---

## Appendices (copy-paste templates)

### A) Minimal eval case (YAML)

```yaml
id: qa_citation_001
feature: answer_with_sources
input:
  question: "What is the warranty for Model X?"
context:
  allowed_sources:
    - "/docs/warranty.md"
expectations:
  must_include:
    - citation: true
    - link_domain: "ourdocs.example"
scoring:
  checks:
    - name: has_citation
      type: regex
      pattern: "\\[\\d+\\]"
      weight: 0.6
    - name: valid_source
      type: contains
      value: "ourdocs.example"
      weight: 0.4
thresholds:
  pass_score: 0.95
```

### B) Judge-model rubric (plain text)

```
Score 1.0: Fully answers the question, cites at least one allowed source, cites nothing else.
Score 0.5: Partially answers; cites allowed and non-allowed sources mixed.
Score 0.0: No answer or no citation or invented sources.
```

### C) Code-generation eval (JSON)

```json
{
  "id": "codegen_parse_csv",
  "task": "Write function parseCsv(str) returning array of rows.",
  "runner": { "timeout_ms": 8000, "attempts": 3 },
  "success": { "type": "tests_pass", "suite": "tests/codegen/parse_csv.test.*" },
  "budgets": { "max_tokens": 3000 }
}
```

### D) CI gate (pseudo-config)

```yaml
gates:
  unit_integrations: "all_pass"
  e2e_smoke: "all_pass"
  evals_changed_areas: "score >= 0.92"
  perf: "p95_ms <= 1200"
  ai_cost: "tokens <= 3_000_per_request"
```

### E) Visual regression guard (explain once)

* Golden screenshots live in `ui/__screenshots__`.
* On difference > 0.1% pixels, PR fails; update goldens only with design approval.

### F) Accessibility checklist (quick)

* Keyboard path for every action.
* Visible focus ring; logical tab order.
* Color contrast: text ≥ 4.5:1; large text/icons ≥ 3:1.
* ARIA only when needed; prefer native semantics.

---

## Folder layout (tests + evals)

```
/tests
  unit/
  integration/
  contract/
  e2e/               # smoke + full
  perf/
  security/
  data_migrations/
/evals
  cases/             # YAML/JSON cases
  rubrics/
  datasets/
  runners/           # scripts that call the model/agent
  reports/           # JSON/CSV outputs (CI artifacts)
```

---

## Ground rules for contributors (human or AI)

* Read PRD → RULES → CODING\_STANDARDS before touching tests.
* When touching a feature, run `make test:affected` and `make eval:affected`.
* If a gate fails, fix the code or update the requirement; don’t change the threshold to green-wash.

---

That’s the spine of a great `EVALS_AND_TESTING.md`: clear mapping to requirements, precise definitions, runnable commands, objective gates, and ready-to-use templates. Next logical step is turning this into a concrete file for your repo (TypeScript, Python, or Go) and wiring the CI gates plus a tiny eval runner so Claude can execute it autonomously.
