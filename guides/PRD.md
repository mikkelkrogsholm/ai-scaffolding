# The Ultimate Guide to a Great PRD

*(PRD = Product Requirements Document)*

A PRD is not a spec dumpster. It’s a sharp, living brief that tells the team **what problem we’re solving, for whom, why it matters, and how we’ll know we succeeded**—plus just enough detail to build the first slice safely.

---

## Core principles

1. **Problem first.** Describe the user pain and the outcome, not the solution UI.
2. **Just enough detail.** Write the minimum that lets design/engineering start without guessing. Link deeper docs.
3. **Testable.** Every requirement is verifiable (acceptance criteria).
4. **Measurable.** Define success metrics and how you’ll capture them.
5. **Shared understanding.** Create it with design and engineering; don’t write alone.
6. **Living document.** Update it as decisions change. Keep a decision log and change log.

---

## The PRD structure (lean and complete)

### 1) Problem & Outcome (the “why”)

* **Problem statement:** One paragraph: who’s stuck, what they can’t do, and the impact.
* **Target outcome:** The measurable change you expect (business and user).
* **Opportunity size:** Plain numbers (e.g., “Affects \~38% of weekly active users”).

> *Starter:* “Sales managers can’t filter leads by intent. This causes manual exports and \~2 hours wasted per manager per week. We aim to cut lead triage time by 30% within 60 days.”

---

### 2) Users & Insights (evidence, not vibes)

* Primary user and context.
* Top 3–5 insights from interviews/analytics; link the source.
* Edge users or excluded users (if relevant).

---

### 3) Scope & Non-Goals

* **In scope:** Bulleted list of capabilities.
* **Out of scope:** Call out tempting things you’re **not** doing (for now). This prevents scope creep.

---

### 4) Solution Sketch (direction, not pixel-perfect)

* One diagram or flow + a few wireframes. Link to full designs.
* Key states: empty, loading, error, success.

---

### 5) Functional Requirements

Write as bullet points in plain language. Each requirement must map to acceptance criteria (next section).

> *Example:* “Users can filter leads by ‘intent score’ and ‘last activity’ in the Leads table.”

---

### 6) Acceptance Criteria (how we verify it works)

Use testable statements. The **Given / When / Then** format keeps it crisp.

* **Given** I’m on the Leads table with data
  **When** I add a filter “intent score ≥ 70”
  **Then** only rows with intent ≥ 70 are shown; the filter chip is visible; the URL includes the filter.

* **Given** I have 0 matching results
  **When** I apply a filter
  **Then** I see a friendly empty state with a “Clear filters” action.

Include **edge cases** (timeouts, partial data, pagination, permissions).

---

### 7) Non-Functional Requirements (the silent killers)

State the bar explicitly. Examples to pick from:

* **Performance:** Initial load ≤ 2.0 s p50, ≤ 4.0 s p95 on 3G; filter apply ≤ 500 ms p95.
* **Reliability:** 99.9% monthly availability; retries with backoff for upstream 5xx.
* **Accessibility:** WCAG 2.1 AA; keyboard nav for all interactive elements; focus states shown.
* **Privacy & Security:** Data stays in region X; no new personal data collected; logs redact emails.
* **Compliance:** GDPR lawful basis = legitimate interest; DSR (data subject request) path unchanged.
* **Compatibility:** Support Chrome/Edge/Firefox last 2; iOS/Android app versions N and N-1.
* **Localization:** English and Danish; dates localized; text fits 30% expansion.
* **Observability:** Emit events `leads_filter_applied`, `leads_table_load`; logs include request ID.

---

### 8) Metrics & Instrumentation Plan

* **North-star metric:** e.g., “Lead triage time per user (median)”.
* **Supporting metrics:** Filter usage %, time to first result, error rate.
* **Targets:** “Activation +10% within 60 days of 50% rollout.”
* **How measured:** Event names, properties, dashboards, owner responsible.

---

### 9) Dependencies, Constraints, Risks

* **Dependencies:** Upstream services, libraries, data pipelines, other teams.
* **Constraints:** Budget, licensing limits, architecture choices.
* **Risks & mitigations:** e.g., “Upstream intent service latency → cache last result and show stale badge.”

---

### 10) Plan, Rollout & Safeguards

* **Milestones:** M0 decision, M1 prototype, M2 beta, M3 GA (general availability).
* **Rollout:** Feature flag, % ramp, cohorts, success gates to proceed.
* **Safeguards:** Kill switch, rollback steps, data migrations and reversibility.

---

### 11) Ownership & Decisions

* **Owners:** Product (D), Engineering, Design, Data, QA—name the decider.
* **Decision log:** Date, decision, options considered, rationale, link to discussion.

---

### 12) Change Log (keep it alive)

* Date + short note of what changed and why. Helps downstream teams stay in sync.

---

## Copy-paste templates

### A) One-page PRD (for small/medium features)

```
Title
Owner(s) / Approver (Decider) • Status • Target Release

1) Problem & Outcome
- Problem:
- Outcome targets:

2) Users & Insights
- Primary user:
- Top insights (links):

3) Scope & Non-Goals
- In:
- Out:

4) Solution Sketch
- Flow + key states (links):

5) Requirements
- [R1] ...
- [R2] ...

6) Acceptance Criteria (Given/When/Then)
- ...

7) Non-Functional Requirements
- Performance:
- Reliability:
- Accessibility:
- Privacy/Security:
- Compatibility/Localization:
- Observability:

8) Metrics & Instrumentation
- North-star:
- Targets:
- Events/Dashboards/Owner:

9) Dependencies, Constraints, Risks
- ...

10) Plan, Rollout, Safeguards
- Milestones:
- Rollout plan:
- Kill switch / rollback:

11) Ownership & Decision Log
- Owners:
- Decisions:
   - [YYYY-MM-DD] Decision — Rationale — Link

12) Change Log
- [YYYY-MM-DD] Change summary
```

### B) Expanded PRD (for complex/regulatory work)

Add: data model (ERD link), API contract (request/response), legal review notes, threat model summary, migration plan, support/enablement plan, pricing/packaging note.

---

## Writing prompts (to avoid fluff)

* **If we do nothing, what worsens in 90 days?**
* **What can the first slice ship without?** Move the rest to non-goals.
* **How will a tester prove this works?** Write that as acceptance criteria.
* **Which number will move first?** Make it a target, not a hope.
* **What would make us roll back in one hour?** Define that threshold now.

---

## Example slice (so you can see the level)

**Feature:** Lead table filtering by intent score

* **Problem & Outcome:** Managers waste \~2 h/week hand-sorting leads. Reduce triage time 30% within 60 days at 50% rollout.
* **Scope:** Add filters for `intent_score`, `last_activity_at`; save last used filters per user.
* **Non-Goals:** New scoring model, export changes, mobile offline.
* **Acceptance criteria:**

  * *Given* a user opens the Leads page with >1000 rows
    *When* they set “intent score ≥ 70”
    *Then* results update within 500 ms p95 and the URL reflects the filter.
  * *Given* no rows match
    *When* filters are applied
    *Then* show an empty state with “Clear filters”, keyboard accessible.
* **NFR:** WCAG 2.1 AA; 99.9% availability; anonymize emails in logs; browser support last 2 versions.
* **Metrics:** `leads_filter_applied` (props: `score_min`, `duration_ms`); median triage time; error rate < 0.5%.
* **Dependencies:** Intent service v3; Analytics SDK v2.
* **Risks:** Upstream latency → cache last result for 10 min with “stale” badge.
* **Plan:** Behind flag; ramp 5%→25%→50% with success gates; kill switch `FF_LEADS_FILTERS`.

---

## Checklists

**10-minute PRD health check**

* Problem and outcome fit on half a page.
* Each requirement has acceptance criteria.
* NFRs exist and are concrete.
* Non-goals listed.
* Metrics + event names + owner defined.
* Decision log started.
* Rollout plan includes kill switch.

**Pre-implementation**

* Designs cover empty/loading/error states and keyboard paths.
* API contracts reviewed; sample payloads captured.
* Data privacy reviewed; retention unchanged or documented.
* Observability agreed (events, logs, dashboards).

**Pre-release**

* Success gates met on canary.
* Support/Docs briefed; known limitations written.
* Rollback rehearsed; one command/flag to disable.

---

## Common failure modes (and how to dodge them)

* **Novel without numbers:** Lovely narrative, no targets. → Add explicit metrics and thresholds.
* **Feature salad:** Big list, no clear problem. → Rewrite the problem and cut scope.
* **Spec freeze:** “We signed off; never change it.” → Keep a visible change log; treat the PRD as living.
* **Hidden constraints:** Surprise legal/infra blockers. → Add dependencies/constraints early and tag owners.
* **NFR amnesia:** Fast demo, slow product. → Write performance/accessibility/privacy bars up front.

---

## Team rituals that keep the PRD alive

* **Write it together:** Product + Design + Engineering co-edit.
* **PRD walk-through:** 30 minutes with the whole team; capture decisions directly into the doc.
* **“Two-pings rule”:** If someone pings the PM twice for clarity, the PRD gets updated.
* **Weekly PRD sweep:** 10 minutes to update the decision and change logs.
* **Link issues both ways:** From PRD to tickets and tickets back to PRD.

---

## Sizing your PRD to the job

* **Micro-PRD (≤2 days work):** Problem, outcome, 3–5 acceptance criteria, NFR note, rollout. One page max.
* **Standard PRD:** The structure above.
* **Heavyweight PRD (regulated/data migrations):** Add formal API contracts, data lineage, threat model, rollback-by-step, legal sign-offs.

---

## Final note

A great PRD is a **contract for learning**: tight on problem and success, flexible on solution. Keep it short, testable, and measurable—and keep it alive as the team learns. When in doubt, make the non-functional requirements explicit and trim everything else.
