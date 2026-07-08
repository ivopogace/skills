# Onboarding interview — filling the tooling map

Read this when any tooling-map slot reads **TBD** and work is about to touch that slot's
stage. The interview is the template's first act on a real project: it converts the
capability slots in `SKILL.md` into concrete tool bindings. Until the map is filled, the
suite has discipline but no hands.

> **Cadence contract (same as the refine stage):** one decision at a time; every question
> carries your recommended answer and the why; facts come from the repo, decisions from
> the human. A barrage of thirteen tool questions is bewildering and lets the user skip
> the hard ones.

## Step 0 — evidence sweep (before the first question)

Anything the repo answers is **looked up, never asked**. Sweep first, so every question
arrives pre-filled with a recommendation grounded in evidence:

| Slot | Look for |
|---|---|
| `<VCS>` | the `origin` remote URL; PR templates (`.github/`, `.gitlab/`) |
| `<CI>` | `.github/workflows/`, `.gitlab-ci.yml`, `Jenkinsfile`, `azure-pipelines.yml` |
| `<TRACKER>` | ticket-ID patterns in `git log` (e.g. `ABC-123`), issue links in READMEs |
| `<QUALITY>` | `sonar-project.properties`, `qodana.yaml`, quality steps inside the CI config |
| `<DOCS>` | `docs/` conventions, wiki links in READMEs or PR templates |
| `<MIGRATIONS>` | `**/db/changelog/**`, `**/db/migration/**`, migration deps in the build files |
| `<DEPLOY>` | `Dockerfile`, `docker-compose*.yml`, `helm/`, `k8s/`, deploy stages in CI |
| `<DESIGN>` | design links in tickets/READMEs/PR templates; a design MCP in the harness |
| `<AREAS>` | build files + lockfiles → the stack (backend lang, frontend framework, DB) |
| `<NOTIFY>` | which notification tool the harness actually exposes (check, don't assume) |
| `<TESTS>` | test blocks in build files, `package.json` scripts, existing CI test stages |
| `<E2E>` | `playwright.config.*`, `cypress/`, e2e stages in CI |
| `<REGISTRY>` | image names in CI/deploy configs, `docker push` targets |

A slot the sweep answers unambiguously (there *is* a CI config and it runs on every push)
still gets **confirmed, not assumed** — but as a one-line "the repo says X, I'll bind X —
ok?" folded into the interview, not an open question.

## Step 1 — the interview

Ask slot by slot, in dependency order (`<VCS>` first — its answer sets the default for
`<CI>`, `<TRACKER>`, `<REGISTRY>`). For each slot:

1. State the capability and why the loop needs it (one sentence).
2. Offer the suggested options from the map, **recommended default first, with the why**
   ("the repo already has a CI config for X, so X is the zero-setup default"). The user
   can overrule; they cannot read your mind.
3. Record the answer **immediately** in the map's *Chosen* column (edit `SKILL.md` as
   each decision lands — never a batch reconstruction at the end).

`none` is a legitimate binding — but record its consequence next to it (e.g.
`<QUALITY>: none → review gate absorbs static-analysis duty, revisit at first quality
incident`; `<DESIGN>: none → "who owns UX?" is a standing refine question`). An empty
cell and a considered "none" are different states.

Four extra facts to pin during the interview (they live under the map in `SKILL.md`):

- **What "green" means on `<CI>`** — result flag alone, or result + explicit
  failed-test-count check. Ask to see the pipeline config: a pipeline that soft-fails
  test stages (`|| true`, `continue-on-error`) makes "not red" hide failures, and the
  CI-gate rule must say so.
- **How the agent reads `<CI>` results** — direct API + token, an MCP server, commit
  statuses on `<VCS>` (e.g. PR checks), or human-mediated (no access bound: the agent
  stops and asks for the build result instead of assuming green). Check what the harness
  actually exposes, don't assume. The chosen path must be able to satisfy the recorded
  green definition — a VCS status flag alone cannot prove "0 failed tests" when the
  pipeline soft-fails test stages.
- **Which steps stay human-gated regardless of the agent's access** — capability ≠
  permission. Default: everything in the release procedure's deploy stage, plus anything
  the user names. Write the list down; "the agent could, therefore the agent may" is the
  drift this line exists to prevent.
- **Where `<QUALITY>` analyses land** (if bound): per-branch analyses or everything under
  one default slot, and how many separately analyzed projects exist (backend/frontend
  halves). The quality gate (pr-gates §2.1) reads this fact; pin it here or, at the
  latest, on the first quality-gate run.

## Step 2 — after the map fills

1. **Seed the routing table.** For each `<AREAS>` entry, add/adjust a row in the
   Skill-routing gate table and decide whether the area skill exists yet
   (`references/growing-area-skills.md` — skills are **discovered-or-created** on first
   contact, not all up front; an existing skill elsewhere is mapped/extended, not forked).
2. **Name the stage coverage.** For each loop stage, say which generic harness skill plus
   which `references/stage-disciplines.md` section covers it until a project-bound stage
   driver grows — "driver TBD" must never silently mean "no discipline".
3. **Pin the substrate paths.** Where do spec docs, plan docs, and the glossary live in
   *this* repo? Record in `SKILL.md` → "The substrate this suite reads".
4. **Rename & adopt.** If the project wants its own suite name (the reference
   implementation renamed the orchestrator after itself), rename the skill directory and
   the announce line now — the template name should not survive adoption.
5. **Commit.** The filled map is a source-of-intent document (loop rule 9): commit the
   edited suite to the project before driving the first ticket with it.

## Example (one filled-in binding — NOT the canonical choice)

The reference implementation this template was lifted from bound the map as: Bitbucket
(`<VCS>`), Jenkins multibranch (`<CI>`, green = success **and** 0 failed tests because
the pipeline soft-failed test stages), Jira (`<TRACKER>`), SonarQube (`<QUALITY>`, two
projects — backend and frontend — both checked), Confluence (`<DOCS>`, ticket
documentation under a fixed parent page), Liquibase (`<MIGRATIONS>`), docker-compose on
a named VM (`<DEPLOY>`, every step human-gated), Figma (`<DESIGN>`), Java/Spring +
TS/Angular area skills (`<AREAS>`), a push-notification tool (`<NOTIFY>`), Gradle +
Vitest with exact scoped commands (`<TESTS>`), Cypress (`<E2E>`), and a private Docker
registry with `versionX.Y.Z` tags (`<REGISTRY>`). Every one of those was a *choice
recorded at onboarding*, not a template constant.

## Red flags

| Thought | Reality |
|---|---|
| "I'll ask all thirteen slots in one message" | One decision at a time, dependency order. |
| "The repo has a CI config, no need to ask" | Confirm in one line — repos carry dead config. |
| "Here are the options" (no recommendation) | You swept the repo — recommend, with the why. |
| "I'll fill the map at the end of the interview" | Record each answer as it lands. |
| "No quality tool configured, skip the slot" | Bind `none` explicitly + its consequence. |
| "The agent has deploy access, so deploy is agent-run" | Capability ≠ permission — the human-gated list is a map fact. |
| "User said 'skip the ceremony, just implement'" | Bind inline the minimum slots the task touches (one-line confirms), record them in the map — an all-TBD map makes every later gate unenforceable. |
