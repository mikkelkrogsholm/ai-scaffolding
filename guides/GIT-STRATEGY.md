Here’s the short answer: a great `GIT-STRATEGY.md` tells humans and AI exactly how code moves from idea → main → release, with zero guesswork. It defines the branch model, how to name things, how to write commits, when to open PRs, what checks must pass, how to cut releases, and what to do when things go wrong.

Below is a clean, copy-pasteable template you can drop into `/docs/GIT-STRATEGY.md`. It balances clarity for teammates and hard rails for AI agents.

# GIT Strategy

Purpose: make changes safe, fast, and traceable. Humans and AI follow the same rules.

## 1) Principles

* **Trunk first**: `main` is always releasable.
* **Short-lived branches**: small, focused changes; merge quickly.
* **Single source of truth**: no hidden work; everything via Pull Requests (PRs).
* **Automate gates**: tests, lint, type-checks, security scans.
* **Auditable history**: clear commit messages, tagged releases, documented reversions.

## 2) Branch & Release Model

Default: **trunk-based development** (short feature branches → PR → `main`).
We use temporary release branches only when stabilizing major versions.

**Branches**

* `main`: protected; always green (all checks passing).
* `feature/<ticket-id>-<slug>`: e.g. `feature/1234-add-billing-form`.
* `release/<version>`: e.g. `release/1.4.x` (only when preparing a minor/major release).
* `hotfix/<slug>`: emergencies from `main` (and backported if needed).

**Why trunk-based?** Less merge pain, faster feedback, fewer zombie branches.

## 3) Naming Rules

* **Branch**: `{type}/{ticket}-{short-description}` (all lowercase, hyphens).

  * `type` ∈ {`feature`, `chore`, `fix`, `spike`, `doc`, `hotfix`}.
* **Tags**: `vMAJOR.MINOR.PATCH` (example: `v1.7.3`).

## 4) Commits (Conventional Commits)

Use the format:
`<type>(scope): <short summary>`
Types: `feat`, `fix`, `chore`, `refactor`, `perf`, `test`, `docs`, `build`, `ci`.

Examples:

* `feat(auth): add magic-link login`
* `fix(api): return 400 for invalid filters`
* `chore(deps): bump axios to 1.7.0`

Why: consistent messages = easy changelogs and clean history.

## 5) Pull Requests (PRs)

* **Size**: keep PRs under \~300 lines changed when possible.
* **Description template** must include:

  * Problem statement, solution summary, screenshots (if UI), risks/trade-offs.
  * “Definition of Done” checklist (tests, docs, telemetry, migration notes).
* **Approvals**: at least 1 reviewer; **CODEOWNERS** must approve protected areas.
* **CI gates**: lint, types, unit/integration tests, security scan must pass.
* **Merge window**: 24 hours max from last review to merge (don’t let PRs rot).

Default merge strategy: **Squash & merge** (keeps `main` linear and readable).
Allowed alternatives:

* **Rebase & merge** for stacks of tiny commits.
* **Merge commit** only when merging a `release/*` branch.

Auto-delete branches after merge.

## 6) Releases & Versioning

* **Semantic Versioning (SemVer)**:

  * MAJOR: breaking changes
  * MINOR: new features, no breaks
  * PATCH: bug fixes only
* **Cutting a release**

  1. Create `release/<version>` from `main` when stabilizing bigger drops.
  2. Only allow fixes on the release branch; merge back to `main` after tagging.
  3. Tag `vX.Y.Z` on the merge commit to `main`.
  4. Generate changelog from Conventional Commits.
* **Hotfixes**: branch from `main` → PR → tag `vX.Y.Z+1` → cherry-pick to `release/<version>` if needed.

## 7) Backports & Cherry-picks

* Only backport critical fixes (security, data corruption).
* Use `git cherry-pick -x <sha>` so the original commit is referenced.
* Label PRs with `backport-to:1.4.x` (or similar) for visibility.

## 8) Repo Hygiene & Safety

* **Protected branches**: `main`, any `release/*`. No direct pushes. Force-push disabled.
* **History rewrites**: never on protected branches. Local cleanup allowed before PR (rebase/squash locally).
* **Secrets**: never commit secrets. Pre-commit hooks run secret scan; CI blocks on hits.
* **Large files**: use Git LFS for binaries/assets > 10 MB; otherwise don’t commit them.
* **.gitignore**: keep it tight; no generated artifacts in the repo.
* **.gitattributes**: normalize line endings, mark binary files, configure LFS patterns.

## 9) Code Owners & Reviews

* Use `CODEOWNERS` to require domain experts for critical paths (security, infra, payments).
* Review checklist covers: correctness, tests, performance, security, observability, docs.
* Block on failing checks or missing required approvals.

## 10) AI Agent Rules (Claude/GitHub Copilot etc.)

AI can assist; it does not bypass guardrails.

* **Before coding**: agents must read `PRD.md`, `RULES.md`, `CODING_STANDARDS.md`, and the open issue.
* **Branching**: agents create `feature/<ticket>-<slug>`; never push to `main`.
* **Commits**: one logical change per commit; follow Conventional Commits; include reasoning in body if non-obvious.
* **PRs**: agents must open PRs as **draft** first with plan → tests → implementation; humans flip to “ready”.
* **Forbidden**: force-push, adding new dependencies without ADR, committing generated lockfiles without explanation, committing secrets or large binaries.
* **Size limits**: max 500 changed lines per PR for agent-authored work unless explicitly approved.

## 11) Monorepo Notes (if applicable)

* Use workspaces (e.g., npm/pnpm) and **atomic PRs** across packages.
* **Path-based CODEOWNERS** per package.
* CI runs affected-graph tests only; releases can be independent per package (tag as `package@vX.Y.Z`).

## 12) Automation & Config

* **Branch protection**: required status checks, required reviews, linear history.
* **Templates**: `.github/PULL_REQUEST_TEMPLATE.md`, `ISSUE_TEMPLATE/`.
* **Hooks**: `pre-commit` for lint, types, secret scan; server-side hooks in CI for final enforcement.
* **Bots**: Dependabot/Renovate PRs are limited, labeled, and batched weekly.
* **Changelog**: generated from Conventional Commits on release.

## 13) Playbooks (common tasks)

**Start a feature**

```bash
git checkout -b feature/1234-add-billing-form
# work
git commit -m "feat(billing): add form with client-side validation"
git push -u origin feature/1234-add-billing-form
# open PR (draft) and fill template
```

**Fix a bug**

```bash
git checkout -b fix/2311-handle-empty-dob
git commit -m "fix(profile): handle empty date-of-birth"
git push && open PR
```

**Cut a release**

```bash
git checkout -b release/1.5.x
# stabilize, only fixes
# merge release -> main when green
git tag v1.5.0
git push origin v1.5.0
```

**Emergency hotfix**

```bash
git checkout -b hotfix/payments-timeout main
# fix + tests
git commit -m "fix(payments): increase gateway timeout to 10s"
git push && open PR (label: hotfix)
# merge, tag v1.5.1, then cherry-pick to release/1.5.x if needed
```

**Revert safely**

```bash
git revert <bad-merge-sha> -m 1  # for merge commits
git push && open PR "revert: <title>"
```

**Bisect to find a bad commit**

```bash
git bisect start
git bisect bad   # current bad
git bisect good <known-good-tag>
# run tests each step, mark good/bad, stop when culprit found
```

## 14) Metrics we watch

* Lead time from PR open → merge.
* PR size (lines changed).
* Time to first review.
* % of PRs merged green on first try.
* Revert rate and hotfix count per release.

## 15) Exceptions

* Document any exception in the PR description and, if recurring, add an ADR in `/docs/DECISIONS/`.
* Strategy changes require team agreement and an ADR.

---

### PR Description Template (drop into `.github/PULL_REQUEST_TEMPLATE.md`)

```
## Why
(What problem does this solve?)

## What
(Short summary of the change)

## How
(Key implementation choices; risks/trade-offs)

## Tests
(Show tests added/updated; link to CI run)

## Screenshots / Recordings
(If UI change)

## Checklist
- [ ] Follows RULES & CODING_STANDARDS
- [ ] Telemetry/logs added where needed
- [ ] No new deps without ADR
- [ ] Docs updated (if applicable)
```

If you want, I can generate this as a ready-to-commit `GIT-STRATEGY.md` plus the matching PR template, CODEOWNERS skeleton, and recommended branch protection settings tailored to your repo stack (TypeScript/Python/Go, monorepo or not).
