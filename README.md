# claude-skills

Personal [Claude Code](https://claude.com/claude-code) skills used by Ivo Pogace. Skills
install under `~/.claude/skills/` and are auto-discovered by Claude Code; each subdirectory
is one skill with a `SKILL.md` (YAML frontmatter: `name`, `description`).

Today this repo is effectively **one thing: the software development lifecycle** — the
[`app-sdlc`](app-sdlc/SKILL.md) suite. The rest of this README describes that lifecycle.

---

## The lifecycle in one picture

```
   stale intent → re-refine: ticket (intake grill) · plan (resume / mainline drift)
┌┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┐   (dashed = conditional feedback, not the default path)
▼                  ┆
refine → ticket → plan → implement → CI gate → PR → review → quality gate → merge → document
                            ▲                                                  ┆
                            └┄┄┄┄┄ findings re-enter (review AND quality) ┄┄┄┄┄┘

release-deploy: an off-loop procedure, invoked when a release is due (batches merged tickets)
```

**Solid = the path every ticket takes; dashed = conditional feedback.** The **stale-intent
edge** fires when an existing **ticket** is stale (the intake-grill gate) *or* a **plan** is
found stale on resume / after mainline drift — either sends you back to re-refinement (fix
the intent, update the ticket/spec) before more planning or code; fresh, well-formed work
flows straight through. Review/quality **findings re-enter at Implement** only when there
are findings.

`app-sdlc` is a **tool-agnostic orchestrator**: it says *which discipline drives each stage
and how the stages connect*, without hard-coding tools. Every tool is a slot in a **13-slot
tooling map** (`<VCS>`, `<CI>`, `<TRACKER>`, `<QUALITY>`, `<DOCS>`, `<MIGRATIONS>`,
`<DEPLOY>`, `<DESIGN>`, `<AREAS>`, `<NOTIFY>`, `<TESTS>`, `<E2E>`, `<REGISTRY>`). Adopting
it on a project starts with an **onboarding interview** that fills the map from repo
evidence + human decisions; from then on every `<TOKEN>` reads as the chosen tool.

## The stages

| Stage | What happens |
|---|---|
| **Refine** | Sharpen a fuzzy idea into a precise, sliceable change, grounded in the code. Produces the **spec** (source-of-intent). |
| **Ticket** | Create/update the tracker ticket with testable acceptance criteria; branch `feature/<ID>-<slug>`. |
| **Plan** | Plan doc: testable ACs, risk register, open questions. Phase decomposition is **human-gated**. Existing ticket → **intake-grill gate** first. |
| **Implement** | Build **test-first**, one behavior at a time, under the **skill-routing gate**. |
| **CI gate** | Builds per push; "green" means the map's recorded definition (result **plus** zero hidden failed tests). |
| **PR → Review → Quality gate → Merge** | Three mandatory gates before merge (below). |
| **Document** | Ticket documentation, unconditionally — part of every ticket's definition of done. |

## The three non-negotiable gates

1. **CI gate** — runs per push, not per PR. Red → systematic debugging, not guess-and-push.
2. **Review gate** — a mandatory review of the full branch diff, effort scaled by risk
   class. "PR opened + CI green" is the trap, not the finish line. (`<QUALITY>`=none → the
   review gate explicitly absorbs the quality duty.)
3. **Quality gate** — a green gate is *necessary, not sufficient*: pull the actual
   new-issue list and clear every entry.

**Findings re-enter the loop at Implement** — the re-entry rule. A fix (review finding,
quality finding, red-CI fix, later reviewer comment) is a change: routing gate first,
test-first, CI green again, and the changed surface **re-reviewed**. Being small or arriving
after a green build is not an exemption.

## Cross-cutting disciplines

- **The spec is universal.** Every change gets a spec (source-of-intent), always committed
  before the work it governs — length flexes with size, existence does not. Only spikes
  are exempt.
- **Skill-routing gate.** Before writing for an area, load that area's skill first. When a
  row has no project skill yet, **discover before you create**: search existing skills
  (global / plugin / other projects) and let the human choose **map / extend / create** —
  never a silent fork.
- **Grow on first contact.** The suite ships the loop, gates, and stage disciplines; the
  project-specific stage drivers and area skills grow one at a time, triggered by real work
  — never stubbed up front.
- **Source-of-intent lives in the repo**, not the conversation. Specs and plans are
  committed before or with the artifacts that cite them.
- **Documentation is part of done** — unconditionally.

## When *not* to use it

Trivial fixes (still a few-sentence spec), spikes/exploration (no spec — no fixed intent
yet), and hotfixes take lighter paths. The full ceremony is for real, sliceable change.

## Suite structure

| File | Role |
|---|---|
| [`app-sdlc/SKILL.md`](app-sdlc/SKILL.md) | The orchestrator: tooling map, the loop, the rules, the skill-routing gate. |
| [`references/onboarding-interview.md`](app-sdlc/references/onboarding-interview.md) | First act — fill the tooling map from repo evidence. |
| [`references/stage-disciplines.md`](app-sdlc/references/stage-disciplines.md) | The tool-independent core of each stage (read per stage). |
| [`references/ticket-intake-gate.md`](app-sdlc/references/ticket-intake-gate.md) | Grill an existing ticket against today's code before planning. |
| [`references/pr-gates.md`](app-sdlc/references/pr-gates.md) | Review gate → quality gate → merge close-out + definition of done. |
| [`references/release-deploy.md`](app-sdlc/references/release-deploy.md) | Off-loop batched-release runbook (human-gated). |
| [`references/growing-area-skills.md`](app-sdlc/references/growing-area-skills.md) | How stage-driver and area skills grow, and the discover-before-create rule. |

`app-sdlc` is a **template**: it is the generalized skeleton lifted from a concrete,
fully project-bound SDLC suite. It is adopted **per project** (its onboarding interview
binds the map, seeds the routing table, and grows the project's own skills), so the copy
here stays tool-agnostic while each project carries its own bound instance.

## History

The former seed skills were absorbed into a project's in-repo skill suite and deleted here
in 2026-07. Their last state is archived at tag `seeds-archive-2026-07-07`.

## Installing

`app-sdlc` ships as a **tool-agnostic template**, not a ready-to-run bound instance — it
carries the loop, gates, and stage disciplines, but no project-specific tools or area
skills. Clone it, then **adopt it per project**: its onboarding interview fills the 13-slot
tooling map from repo evidence, seeds the routing table, and grows the project's own skills
on first contact. Every `<TOKEN>` stays a placeholder until you bind it.

Clone into `~/.claude/skills/`:

```bash
git clone https://github.com/ivopogace/skills.git ~/.claude/skills
```

Then run the onboarding interview (see
[`references/onboarding-interview.md`](app-sdlc/references/onboarding-interview.md)) in a
target project to bind the template to that repo.

## License

Released under the [MIT License](LICENSE).
