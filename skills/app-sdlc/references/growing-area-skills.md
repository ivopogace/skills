# Growing the suite: stage skills, area skills, and the case history

Read this after onboarding (the map is filled) and again each time the routing gate fires
for an area that has no skill yet. The template ships the loop, the gates, and the stage
disciplines; the project-specific skills **grow on first contact, not all up front** — a
stub written before the project has taught you anything encodes nothing and will be
trusted anyway.

## What the full-grown suite looks like

The reference implementation this template was lifted from grew into one orchestrator +
16 skills — roughly one **stage driver** per loop stage and one **area skill** per
routing-table row:

- **Stage drivers** (tool-bound versions of the stage disciplines): refine interview,
  ticket authoring, plan-doc, implement (the TDD binding + exact `<TESTS>` commands),
  debug, review overlay, quality-gate procedure, docs authoring. Each driver **starts
  from its `references/stage-disciplines.md` section** and adds the tool bindings, exact
  commands, field mechanics, and the project's own learned traps.
- **Area skills** (the routing-table rows): backend-language conventions, module
  architecture, ORM/persistence, API surface, auth, DB+`<MIGRATIONS>`, frontend
  framework, local-run.

That is the *asymptote*, not the starting requirement. Grow toward it one skill at a
time, each triggered by real work.

## When to create a skill (and when not)

Create the area/stage skill the **first time non-trivial work touches its row** — before
writing, per the routing gate. Non-trivial here means: the work would get a plan doc
(loop rule 6), or it touches one of the area's conventions-with-teeth. Below that
threshold the row still fires, but it routes to the project's agent-instruction files
instead of triggering skill creation.

Do **not** create a skill to hold what those instruction files already state (the skill
*binds and enforces* conventions; it doesn't duplicate them), nor for one-off knowledge,
nor as an empty placeholder "for completeness".

**Discover before you create.** Before authoring a new area/stage skill, **search what
already exists** — the harness's available-skills list, user-global `~/.claude/skills`,
plugin skills, other adopted projects. A hit is a **human decision, not an auto-reuse and
not an auto-create**: ask whether to

- **Map** — route the routing-table row at the existing skill as-is (reuse by reference);
- **Extend** — the existing skill + a project delta (common when a global skill carries
  the framework idioms but not this repo's specifics; the delta lives in the project skill
  or, for a maintained upstream library, the vendoring path above);
- **Create** — a fresh project-local skill (nothing suitable exists, or the user wants one).

Only create blind when the search comes up genuinely empty. **Record which of the three
the user chose in the routing-table row**, so it is auditable and not re-litigated next
ticket. Silently forking a skill that already exists is the miss this step prevents — two
copies of the same area skill drift apart and the review gate then checks against the
wrong one.

## What an area skill must encode

The test: **would loading this skill before writing have prevented a real or likely
miss?** Each skill carries:

1. **The load-trigger** — precisely when the routing gate fires this row (in the
   frontmatter description: "Use when touching X … Load before the first edit, not
   after").
2. **The conventions with teeth** — the non-obvious, violation-prone rules for the area:
   the "never edit an applied migration", the "never hand-edit generated code", the
   "this test runner, never that one". Each stated as a rule the review gate can check.
3. **The traps** — the area's known ways a plausible-looking change goes wrong, each a
   named cause→effect chain ("header set but cookie absent → the valid other cookie is
   ignored → 401") backed by a case-history entry (below).
4. **Exact commands** — scoped test/build/run commands for the area, from the `<TESTS>`
   slot, copy-pasteable.

**Pin conventions to concrete repo artifacts, not abstractions.** The grown skills'
strongest habit: every rule cites a file path (`verified in security/<Filter>.java`), a
canonical example class to copy ("precedent: `<UseCase>` — copy that shape"), or a test
to keep green by name ("the boundary regression test is `<TestClass>.<method>`"). An
abstract rule invites reinterpretation; a pinned one can be checked.

## House format (match the suite's shape)

The grown skills share an invariant skeleton — use it for every new skill:

1. Frontmatter: `name` + `description` — third person, **triggering conditions only**
   ("Use when …" with concrete nouns, closing with the load-timing clause: "Load before
   the first edit / the debugging session / drafting"), no workflow summary. Vendored
   content adds `license` + provenance metadata.
2. Title with an em-dash tagline naming the skill's central mental model, not just its
   topic ("— the header→cookie gating and its traps").
3. **Announce at start:** line ("Loaded <skill> — <one-line purpose>."). The
   announcement is what makes the routing gate auditable.
4. A **division-of-labor paragraph**: which canonical doc this skill defers to ("the
   conventions doc is canonical; this skill does not restate it"), which review-gate
   items enforce the same ground, which sibling skill owns the neighboring concern.
   Every skill sits on a **prevent → enforce → route** triangle: it prevents the miss
   before writing, names the gate item that catches it anyway, and routes what it
   doesn't own.
5. Themed body sections: decision/lookup tables for choices (`| Thing | Package | Rule |`),
   numbered procedures for operations (triage ladders, startup orders), gate language
   where a gate is meant ("this is a gate, not a suggestion"; necessary-vs-sufficient
   framing for anything that looks done before it is).
6. A **red-flags table** (`| Thought | Reality |`) built from *observed*
   rationalizations — the left column a tempting wrong belief in the agent's own voice,
   the right a terse correction. Add rows when a session actually rationalizes, not
   speculatively.
7. `references/` for heavy per-topic material; the SKILL.md stays scannable.
8. An italic **provenance footer** after a `---`: what the skill absorbed, what was
   deliberately dropped, upstream attribution/license, and a "verified <date>" stamp.

Cross-reference the orchestrator's re-entry rule instead of restating it; cite sibling
skills as bare skill names with a `→` when scoping out.

**Expected size:** a mature area skill is roughly **80–120 lines of SKILL.md**,
self-contained. References come in three tiers: none (most), one or two hand-written
deep-dive files (~50–180 lines each, e.g. a testing-modes or login-flows recipe), or a
**vendored upstream library** — when a maintained external skill covers the area's
generic ground, vendor it wholesale under `references/upstream/` with its LICENSE,
pin it to the version the repo actually uses, **never edit vendored files**, re-sync
only wholesale inside an upgrade ticket, and keep a short routing map curating
high-value vs skip files. The SKILL.md then carries only the project delta.

## The case history — rules keep their incidents

Create `references/case-history.md` next to the orchestrator **at the first incident**,
and add an entry every time an incident produces or sharpens a rule:

```
## <short incident name>
What happened → the rule it produced (and which skill/table row now carries it).
```

**A rule without its incident is the first thing a future session will rationalize
away.** When you add a rule to any skill in the suite, link its incident; when an
incident recurs despite a rule, the rule's wording failed — sharpen it, don't repeat it.

Typical first entries (every project hits its own versions): the merge that silently
re-broke a suite → the close-out's "mainline green after merge" item; the two look-alike
environments (ports, instances, profiles) → the local-run skill; the auth change whose
failure mode was invisible in review → the high-effort-no-exceptions review class; the
agent that consumed ambiguity instead of surfacing it → the plan stage's human gates.

## Maintenance

- **Each new area skill adds/updates its routing-table row** in `SKILL.md` — the gate
  can only fire on rows that exist.
- **Substrate staleness is a close-out item** (pr-gates §3.5): a merged change that
  invalidates something a skill states patches the skill in the same PR or immediately
  after.
- Retired bindings (a tool swap, a migrated CI) update the map **and** every skill that
  bound to the old tool — grep the suite for the old name before calling the swap done.

## Red flags

| Thought | Reality |
|---|---|
| "I'll stub all 16 skills now so the suite looks complete" | Empty stubs get trusted. Grow on first contact. |
| "No project skill for this area → create one" | Search first (global/plugin/other project). A fork of an existing skill is a maintenance leak — map or extend, and let the human choose. |
| "The conventions doc already says this, skill later" | Fine — until the first non-trivial change in the area. Then the row fires. |
| "I'll add the rule without the incident, it's obvious" | Unanchored rules get rationalized away. Link the case. |
| "The routing table row can wait until the skill is good" | The row fires the skill's creation. Row first. |
| "Small fix in the area, no need to create the skill yet" | Small fixes re-enter at Implement like everything else — if the row would fire, it fires. |
| "The rule is clear, no need to cite a file" | Pin it to a path, a precedent class, or a named test — abstract rules get reinterpreted. |
| "I'll tweak one file inside the vendored upstream" | Never. Wholesale re-sync inside an upgrade ticket, or carry the delta in SKILL.md. |
