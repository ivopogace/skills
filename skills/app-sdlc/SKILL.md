---
name: app-sdlc
description: This skill should be used when adopting a disciplined SDLC on a project (first act — the tooling-map onboarding interview), or when starting or continuing feature work in a repo that has adopted app-sdlc — refining a fuzzy idea, creating or picking up a ticket, planning or implementing a change, reacting to CI or quality-gate findings, opening or merging a PR, documenting a merged ticket, or preparing a release. Also use when the user asks how we work in this repo.
---

# app-sdlc — tool-agnostic SDLC orchestrator (template)

This is the **orchestrator** for how a project builds software. It does not do the work
itself — it names which discipline drives each stage and how the stages connect.
Stage procedures live in `references/` (index at the bottom) — read each when its stage
arrives, not before.

**Announce at start:** "Using app-sdlc to drive the workflow."

**This is a TEMPLATE.** It is the generalized skeleton of one concrete, fully-bound SDLC
suite; every place that suite named a tool, this file names the **capability** and defers
the binding to a slot in the tooling map below. The loop shape, the gates, and the
re-entry rule are tool-independent and are the point — they hold whatever the map says.
Where a concrete tool appears anywhere in this suite it is either a *suggested option* in
the map or explicitly labeled **"Example (one filled-in binding)"** — never the canonical
choice.

## Tooling map — the template's first act

> **Onboarding gate.** If any slot below still reads **TBD** and the current task touches
> that slot's stage, STOP and run the onboarding interview
> (`references/onboarding-interview.md`) for at least the slots the task needs — one
> decision at a time, each question carrying a recommended answer grounded in repo
> evidence. Record answers in the **Chosen** column; from then on, every `<TOKEN>` in
> this suite reads as the chosen tool. **This gate precedes every other gate** — even
> pulling a ticket needs `<TRACKER>` bound. Slots whose stage is not yet reached may be
> deferred explicitly (e.g. `<DEPLOY>`/`<REGISTRY>` until the first release) — a deferred
> slot stays TBD and re-fires this gate when its stage arrives.

| Token | Capability | Suggested options (recommended default first) | Chosen |
|---|---|---|---|
| `<VCS>` | Version control + PR host | GitHub · GitLab · Bitbucket | TBD |
| `<CI>` | CI runner / build gate | `<VCS>`-native CI (GitHub Actions / GitLab CI) · Jenkins | TBD |
| `<TRACKER>` | Issue tracker | `<VCS>`-native issues · Jira · Linear | TBD |
| `<QUALITY>` | Quality / static-analysis gate | SonarQube · Qodana · none | TBD |
| `<DOCS>` | Docs / wiki (ticket documentation home) | Markdown-in-repo · Confluence · Notion | TBD |
| `<MIGRATIONS>` | DB schema migrations | whatever the repo already uses (Liquibase / Flyway) · none | TBD |
| `<DEPLOY>` | Deploy target + mechanism | no default — ask (compose host · k8s · PaaS) | TBD |
| `<DESIGN>` | Design-file source for UI-shaped work | Figma · none (then "who owns UX?" is a standing refine question) | TBD |
| `<AREAS>` | Language/framework area skills | derived from the stack (e.g. a backend-lang row, a frontend-framework row, a DB row, an auth row) | TBD |
| `<NOTIFY>` | Notification channel to a walked-away user | push-notification tool if the harness has one · none | TBD |
| `<TESTS>` | Test runners + **exact scoped-run commands** per stack | per stack (record the one-test and one-module commands, not just the tool) | TBD |
| `<E2E>` | End-to-end / browser testing | Playwright · Cypress · none | TBD |
| `<REGISTRY>` | Artifact / container registry + versioning scheme | `<VCS>`-host registry · dedicated registry · none | TBD |

Four map facts worth recording alongside the choices (the onboarding interview pins the
full list): how "green" is *actually* read on `<CI>` (result flag alone, or result + a
test-count check — pipelines that soft-fail test stages make "not red" hide failures),
**how the agent reads `<CI>` results** (API + token, MCP, `<VCS>` commit statuses, or
human-mediated), which steps stay **human-gated regardless of the agent's access**
(capability ≠ permission; see the release procedure), and **where `<QUALITY>` analyses
land** (per-branch or one shared slot; how many analysed projects).

## The loop

```
   stale intent → re-refine: ticket (intake grill) · plan (resume / mainline drift)
┌┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┐   (dashed = conditional feedback, not the default path)
▼                  ┆
refine → ticket → plan → implement → CI gate → PR → review → quality gate → merge → document
                            ▲                                                  ┆
                            └┄┄┄┄┄ findings re-enter (review AND quality) ┄┄┄┄┄┘

release-deploy: off-loop procedure, invoked when a release is due (batches merged tickets)
```

**Solid = the path every ticket takes; dashed = conditional feedback.** Two such edges:
the **stale-intent edge** (top) fires when an existing **ticket** is stale (the intake-grill
gate) *or* a **plan** is found stale on resume / after mainline drift (§Plan) — either
bounces back to re-refinement (correct the spec, update the ticket) before more
planning/code (rule 11); fresh, well-formed work skips it and flows straight through.
Review/quality **findings re-enter at Implement** (bottom) *only when there are findings* —
the re-entry rule.

**The loop is a loop, not a line — the re-entry rule (canonical statement).** Any fix —
a review finding, a quality-gate finding, a red-CI fix, a reviewer's later comment — is a
change, and a change **re-enters the loop at Implement**: run the **Skill-routing gate**
for what the fix touches (load that area's skills *before* editing), build it
**test-first** (the test asserting the fixed behavior comes before the fix), get `<CI>`
green again on the fix push, and have the changed surface **re-reviewed** — re-review
means re-running the review gate on the new diff, not replying to the reviewer's comment.
Being small, or arriving after a green build or a finished review, is **not** an
exemption — the single most common process miss is treating a post-review or
post-quality-gate fix as exempt: a patch written without the area skill, a merge sold on
the *previous* green build. Everywhere else this suite says "re-enters at Implement", it
means this paragraph.

| Stage | What happens | Discipline (`references/stage-disciplines.md`) | Tooling |
|---|---|---|---|
| **Refine** | Sharpen a fuzzy idea into a precise, sliceable change, grounded in the actual code; facts from the repo, decisions from the human. **Produces the spec** (source-of-intent, **always written** — rule 11). Entering at a written ticket → the **intake gate** *is* the refinement and feeds a short spec, rather than a fresh interview (see Plan row) | §Refine | `<DESIGN>` for UI-shaped wishes |
| **Ticket** | Create/update the tracker ticket with testable acceptance criteria; branch `feature/<ID>-<slug>` off the mainline | §Ticket | `<TRACKER>` |
| **Plan** | Plan doc: testable ACs, risk register, open questions; phase decomposition is human-gated. Entering at an *existing* ticket → **ticket-intake gate** first (`references/ticket-intake-gate.md`) | §Plan | `<TRACKER>` |
| **Implement** | Build test-first, one behavior at a time; **Skill-routing gate** per touched area (below); exact scoped test commands from `<TESTS>` | §Implement | — |
| **CI gate** | `<CI>` builds per push. **Green = the map's recorded definition** (result + zero failed tests where the pipeline can hide them). Check the push's build before the next phase; red → systematic debugging, not guess-and-push | §Debug | `<CI>` |
| **PR** | Merge latest mainline into the branch first — integrate with full discipline (routing gate for what the integration touches) — then open the PR on `<VCS>` | — | `<VCS>` |
| **Review** | **Mandatory gate** on the PR diff — procedure: `references/pr-gates.md` §1. Findings re-enter at Implement | §Review | `<VCS>` + review tooling |
| **Quality gate** | **Mandatory gate.** A green gate is necessary, not sufficient — pull the actual new-issue list from `<QUALITY>` and clear every entry: `references/pr-gates.md` §2. (`<QUALITY>` = none → the review gate absorbs this, explicitly) | §Quality gate | `<QUALITY>` |
| **Merge** | Only after CI green + review gate run + quality list cleared → merge (**squash commit by default** — one commit per ticket on the mainline, unless house conventions bind another method), then the close-out checklist: `references/pr-gates.md` §3 | — | `<VCS>` |
| **Document** | Ticket documentation in `<DOCS>`, linked back from the ticket. **Unconditional** — part of every merged ticket's definition of done | §Document | `<DOCS>` |

**Stage drivers grow; disciplines ship now.** The tool-independent core of each stage's
discipline is in `references/stage-disciplines.md` — read the relevant section when the
stage arrives, from day one. The full project-bound stage *drivers* (the discipline plus
the tool bindings, exact commands, field mechanics, and the project's own learned traps)
grow on first contact (`references/growing-area-skills.md`). Until a stage's driver
exists, the stage runs on its discipline section plus the generic skills the harness
already carries (test-driven development, systematic debugging, plan writing/executing) —
name which generic skill covers which stage at onboarding, so "driver TBD" never silently
means "no discipline".

## Ticket-intake grill gate (summary)

A written ticket is a snapshot of intent at creation time, not ground truth. Before
authoring the plan doc for an existing ticket, grill it against today's code, the
in-flight PRs, and module ownership. Full procedure: `references/ticket-intake-gate.md`.

## Skill-routing gate (mandatory — load *before* writing)

> This is a **gate, not a suggestion.** Before authoring a plan section or a line of
> code for an area, **load that area's skill(s) first (MUST)** and **announce which ones
> were loaded**. One ticket usually trips several rows. Skipping a row — writing the artifact
> first, loading the skill after — is a process miss the review gate flags.

The routing table itself is project-specific: it is **seeded at onboarding from the
`<AREAS>` slot** and grows one row per area skill (`references/growing-area-skills.md`).
Until an area's skill exists, the row still fires — it routes to the area's conventions
doc or triggers **discovery** (search for an existing skill first → **map / extend /
create**, `references/growing-area-skills.md`), never a blind create. The template ships
the skeleton rows every project has some version of:

| If the change touches… | Load BEFORE writing (MUST) | What the row must encode once bound |
|---|---|---|
| **DB schema / a migration / an index / a query** | the `<MIGRATIONS>`+DB area skill | migration-tool conventions (never edit an applied migration); index plan; ownership contracts on shared/synced tables |
| **Backend module/architecture structure** | the architecture area skill | module boundaries, allowed dependency directions, event-vs-call rules |
| **Any backend code** | the backend-language conventions skill | language idioms, logging/i18n/security-event conventions, test-fixture reuse |
| **The API surface (endpoint/DTO contract)** | the API-surface area skill | contract-first order of operations; generated-client rules (never hand-edit generated code) |
| **Auth / security** | the auth area skill | the project's auth mechanism and its known traps; always in the review gate's high-effort class |
| **The frontend** | the frontend-framework area skill | framework idioms, test runner (`<TESTS>`), i18n conventions |
| **Running/debugging locally** | the local-run area skill | how to start the stack, known port/instance confusions, login flows |
| **Anything, always** | plan-doc (plan) · test-first (build) · review overlay (review) | the always-on spine |

**How the gate runs — three steps, every time:**

1. **Detect.** List what the change touches. Evidence is grep + the package/module
   structure + `git diff --stat`, not memory. A mixed-scope ticket almost always trips
   several rows — load all of them.
2. **Load + announce.** Load each triggered skill *before* authoring that part and say so
   out loud. If the artifact was written before loading the row, the gate already failed —
   redo it. **If a triggered row has no project skill yet, search for an existing one
   first** (harness available-skills list, user-global `~/.claude/skills`, plugins, other
   adopted projects) before creating — a match is a **map / extend / create** decision for
   the human, never a silent fork (`references/growing-area-skills.md` → *Discover before
   you create*).
3. **Record.** Name each loaded skill and what it changed in the plan doc's **Skills
   consulted** line (right-sized work with no plan doc → the same line goes in the PR
   description). The review gate checks that line against the diff: an area's artifact
   in the diff with no matching row in *Skills consulted* is a finding.

The gate fires at the plan stage, the implement stage, **and the review-fix stage** —
fixing a finding is implementation (re-entry rule). Re-loading is cheap; when in doubt, load.

## Staying in touch (notifications)

SDLC stretches often run while the user has walked away (a CI build, a review fan-out, a
release image build) — when the workflow needs them, reach out via `<NOTIFY>`; don't go
silent and wait.

- **Notify *before* any blocking question** (a question prompt alone does not reach a
  user who stepped away), and when a stage finishes and the loop awaits their gate
  decision (plan approval, PR opened, review findings ready, release go/no-go).
- **Never ping during live back-and-forth** — the trigger is "they may have walked away
  and something is waiting," not every question.
- **Toolset honesty:** if `<NOTIFY>` is unavailable or fails, say so in the reply and
  continue — never silently half-do it.

## Rules of the loop

1. **One ticket, one branch:** `feature/<ID>-<slug>` off the mainline (the scheme is a
   default — rebind at onboarding if house style differs); reference the ticket ID in
   commits.
2. **The CI gate is non-negotiable — and "green" means the map's recorded definition.**
   It runs per push, not per PR. After each push that claims a phase done, check that
   push's build before the next phase.
3. **The review gate is non-negotiable.** Green CI is not a review — don't merge or call
   a ticket done until the gate ran and findings are resolved or deferred
   (`references/pr-gates.md` §1).
4. **The quality gate is non-negotiable, and its findings re-enter** (re-entry rule;
   procedure `references/pr-gates.md` §2).
5. **The plan owns the risks.** A ticket touching the project's named risk classes (auth,
   data-visibility boundaries, externally-synced data — bound at onboarding) states in
   its plan doc how the invariant holds — review checks it.
6. **Right-size it.** A one-line/copy fix skips the plan doc — **never the spec (rule 11)
   or the review gate**. What shrinks with size is length, not which artifacts exist.
7. **An existing ticket gets grilled before it gets planned**
   (`references/ticket-intake-gate.md`). Don't trust a ticket because it reads complete.
8. **Findings re-enter at Implement** — the re-entry rule (The loop), verbatim; it also
   covers red-CI fixes and later reviewer comments.
9. **Source-of-intent documents live in the repo, not the conversation.** Any spec/plan
   that tickets or docs reference must be committed before or with the artifacts that
   cite it.
10. **Documentation is part of done — unconditionally.** A merged ticket without its
    `<DOCS>` page is not finished, regardless of how small or internal the change is.
11. **Every change gets a spec — length flexes, existence does not.** The spec
    (`docs/specs/`) is the source-of-intent, **always written and committed before the
    work it governs**: before the ticket for new/greenfield work; before the plan when
    entering at an existing ticket; before implementation for a change small enough to
    skip the plan doc. Size is proportional to the change: a fuzzy/greenfield idea earns a
    full spec from the Refine interview; a well-formed existing ticket earns a short spec
    that states the intent and folds in the intake-grill outcome (rule 7); a one-line fix
    earns a few sentences. The spec is **never skipped** — not for existing tickets, not
    for trivial fixes — so a grill that corrects a false premise (the requested mechanism
    doesn't fit the code) is recorded there, not buried in the plan doc or the
    conversation. The **plan doc is the separate "how"** (ACs, risks, phases); the spec is
    the "what/why". The only work with no spec is a true **spike/exploration** — it has no
    fixed intent to capture (see *When NOT to use*).

## The substrate this suite reads

- The project's agent-instruction files (root + per-workspace) — the canonical
  conventions the area skills bind to.
- `docs/specs/` (spec docs) and `docs/plans/` (plan docs) — or the project's equivalent,
  recorded at onboarding; one location regardless of sub-workspace.

## When NOT to use

- Trivial fixes (typo, one-liner, dependency bump): commit directly, skip the plan doc and
  most ceremony — but still write the **few-sentence spec** (rule 11: length shrinks, the
  spec does not).
- Spikes / exploration: branch `spike/<topic>`, **no spec** (no fixed intent yet), no plan
  doc, no gates except common sense.
- Hotfix: standard `bugfix/<ID>-<slug>` branch — a **short spec** still (rule 11), plus the
  PR description and a retro note in `<DOCS>`.

## Red flags — loop level

| Thought | Reality |
|---|---|
| "PR opened + CI green — basically done" | The review and quality gates haven't run. That pair is the trap, not the finish line. |
| "The fix is tiny, straight to merge" | Fixes re-enter at Implement — routing gate, test-first, re-review. Size is not an exemption. |
| "The quality gate is green, so quality is done" | Pull the issue list. Gates pass with reported issues sitting below the fail thresholds. |
| "The ticket reads complete, skip the grill" | That is exactly when stale ACs slip through. Intake gate first. |
| "The agent has deploy access, so it deploys" | Capability ≠ permission — the human-gated list is a map fact. |
| "The repo obviously uses X, no need to confirm the slot" | Repos carry dead config. Confirm in one line, then bind. |
| "No quality tool here, so skip that stage" | Bind `none` explicitly; the review gate absorbs the duty and says so out loud. |
| "Merged — ticket done" | Close-out remains: mainline green, docs page, deferred findings propagated. Merging is not the last step. |

## References

- `references/onboarding-interview.md` — the template's first act: fill the tooling map.
- `references/stage-disciplines.md` — the tool-independent core of each stage's discipline; read per stage from day one.
- `references/ticket-intake-gate.md` — read at plan entry whenever work starts from an existing ticket.
- `references/pr-gates.md` — read the moment a PR exists: Review gate, Quality gate, Merge close-out (incl. definition of done).
- `references/release-deploy.md` — read when a release is due (off-loop, batches merged tickets).
- `references/growing-area-skills.md` — how to spawn the per-area and per-stage skills and the case-history discipline.
