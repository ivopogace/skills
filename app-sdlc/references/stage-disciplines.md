# Stage disciplines — the tool-independent core of each stage

Read the section for the stage you are entering, when you enter it. These are the
disciplines the loop's stage table points at — distilled from a concrete suite's stage
drivers with the tool bindings removed, so they apply from day one on any project. When
the project grows its own stage driver (`references/growing-area-skills.md`), the driver
binds a section to tools, exact commands, and the project's own learned traps — it never
replaces the section; it sharpens it.

## Refine — grill the idea into shape

> **When this stage runs — and its artifact.** Refine's output is the **spec**
> (`docs/specs/`, source-of-intent — step 8), which is **always written** (SKILL.md rule
> 11); only its length flexes. For a fuzzy idea, this full interview produces it. Entering
> at an **already-written ticket** skips the *interview* but not the spec: the
> `references/ticket-intake-gate.md` grill is the refinement, and its outcome is captured
> in a short spec committed before the plan. A ticket-driven change with a plan doc but no
> spec is a **gap**, not a shortcut. Only true spikes/exploration have no spec.

1. **Facts from the code, decisions from the human.** Before the first question, ground
   the wish in the repo; anything the codebase answers is **looked up, never asked** —
   and when the wish rests on a false premise, open by correcting it with evidence.
   UI-shaped wishes: the design file (`<DESIGN>`) is a fact source too — pull it before
   asking layout questions; **no design exists → that is a refine finding** ("who owns
   UX for this?"), recorded in the spec's open questions, not left for the implementer
   to discover.
2. **One question at a time.** Ask, then **stop and wait**. A barrage is bewildering and
   lets the stakeholder skip the hard one. Keep the remaining branches in reserve;
   resolve dependencies between decisions in order.
3. **Every question carries your recommended answer.** You did the research, so you have
   a best hypothesis; name it and say why. The stakeholder can overrule; they cannot
   read your mind.
4. **Sharpen every fuzzy or overloaded term** the moment it appears. Propose the
   canonical term, get agreement, use it consistently from then on. Keep a glossary
   (one line per term); a stakeholder using a term against its entry is challenged on
   the spot. Resolved terms are added in the same session.
5. **Stress-test with concrete scenarios.** Invent the edge case that forces precision
   (the VIP customer, the deleted account, the second browser tab) and put it to the
   stakeholder as a mini-question with a recommendation.
6. **Cross-reference every claim with the code.** When the stakeholder states how the
   system behaves, verify; contradictions are surfaced immediately, not deferred.
7. **No enactment before confirmed shared understanding.** Refine produces decisions —
   no designs, no code; solution talk waits until the problem is pinned.
8. **Capture at decision time, not at the end.** Write each resolved decision into the
   spec as it lands — never a batch reconstruction. Decisions that are hard to reverse
   + surprising without context + a real trade-off get a short rationale paragraph; the
   rest one line. The spec is source-of-intent: committed before or with the ticket
   that cites it (loop rule 9).

## Ticket

1. **Duplicate search first** — a hit means ask, not create.
2. **Implementation-neutral, grounded in read code.** Read the named code paths before
   writing; never invent repro steps, stack traces, or root causes — unproven causes
   are written as "probably", or left at a neutral technical pointer.
3. **Slice vertically.** Default: one ticket = one **vertical tracer slice** — a narrow
   but complete path through every layer it touches (schema, backend, API client, UI,
   tests), demoable on its own, sized for one branch. Split only when each slice is
   independently demoable — and mind toolchain couplings (a frontend consuming a client
   generated from the live backend makes a frontend-only ticket unbuildable until the
   backend ticket merges). Declare blocking edges between sliced tickets, blockers
   created first.
4. **Present the slicing to the user before creating** (scope per ticket + edges) —
   granularity is their call.
5. **ACs are testable statements, one behavior each** — including the non-functional
   ones the loop needs (regenerated client, coverage of visibility rules, i18n keys).
   Where `<TRACKER>` keeps ACs in a dedicated field with quirks, pin the field
   mechanics as a map fact on first contact.
6. **Parent/epic assignment:** no clearly owning parent → an open question for the
   human, never a guess.
7. **After creation:** verification read (pull the ticket back; check it rendered),
   branch `feature/<ID>-<slug>` off the mainline, hand off to Plan. An existing ticket
   picked up later gets the intake grill gate, not blind trust.

## Plan

1. **Required artifacts** for any ticket big enough to plan: a plan doc in the repo
   (one location for all tickets), the branch, a **Skills consulted** line (the routing
   gate's audit log), a **risk register**, **open questions / assumptions**, an
   **execution-status table**, a **generalization-audit log** — and a **data-ownership
   contract** whenever the ticket touches data another system also writes. Empty
   sections get an explicit `N/A — <reason>`, never deleted silently.
2. **Draft, don't interview-by-default.** Fill every pre-phase section from ticket +
   spec + codebase, marking provenance; escalate only genuine gaps (sources silent,
   contradicting each other or the code, or a security-sensitive guess). **"Consulted"
   means applied, not name-dropped:** a listed area skill whose mechanically-checkable
   rules the draft violates is a worse finding than an empty line.
3. **ACs in Given/When/Then form, each naming its pinning test.** If a stakeholder
   can't read an AC and immediately tell pass from fail, rewrite it. Plain-prose ACs
   are wishes.
4. **Resolve every phase-blocking open question with the user before decomposition** —
   grounded in real files, 2–4 codebase-grounded options, one recommendation.
5. **Phase decomposition is human-gated.** Phase 0 does not start until the user has
   approved the decomposition (and the PR staging, if the work needs more than one PR).
   A fully-decomposed plan rolling straight into implementation is the canonical
   autonomy-drift anti-pattern this stage exists to prevent.
6. **During execution:** plan-doc updates land **in the same commit window** as the
   code; a **generalization-audit pass after every bug fix or new pattern** ("where
   else does this apply?" — run the search, decide per match, append to the log; bug
   classes survive whole phases when nobody asks); **spec drift → amend the spec**,
   never a silent workaround — and a *material* invalidation of the plan's intent (not a
   small drift: a false premise exposed, mainline moved the ground, a risk became fact)
   **re-enters at Refine** to correct the spec at its source (rule 11), not a patch
   buried mid-plan; the open-questions section is empty (or every remainder cites a
   follow-up ticket) before "done" is claimed.
7. **Plan freshness on resume.** A plan is a snapshot too. Before continuing to execute a
   plan that has sat idle, or after the mainline has moved under the branch, **re-validate
   it against current code** — a mini intake grill for the plan: are the ACs still
   correct, the risks still the risks, the open questions still open, has a sibling PR
   changed a contract it assumed? Materially stale → **re-enter at Refine** (correct the
   spec/ticket) before more code — the same loop `references/ticket-intake-gate.md` runs
   for a stale ticket, applied to the plan.

## Implement

1. **Routing gate first.** List the areas the change touches and load each area's skill
   *before* writing for that area (orchestrator, Skill-routing gate).
2. **Seams before tests.** A seam is the public interface a test observes behavior
   through — an inbound use-case interface, an endpoint, a component's public API. The
   plan names the seams under test per phase; if it doesn't, propose them and get them
   confirmed before the first test. Prefer existing seams; never verify through a side
   channel (asserting DB rows when the use case returns the answer).
3. **One behavior per cycle, full cycle every time:** write **one** failing test →
   **verify RED** (run it, watch it fail on the missing behavior) → minimal code to
   green → **verify GREEN** (same command) → refactor while green → next slice.
   Writing all test cases first and the whole implementation second is **horizontal
   slicing** — the tests verify imagined behavior, and none of them was ever seen to
   fail.
4. **What RED means:** a test authored first but first *run* after the implementation
   exists was never red — that is test-after with extra steps. A compile error is not
   RED; a mock-setup error is not RED; the assertion failing on the missing behavior is
   RED. If the language needs types to compile, write skeletons first (interface, empty
   impl throwing "not implemented"), then run.
5. **Scoped runs, from the `<TESTS>` map slot:** the single-test command between
   red/green cycles, the module-scope command at phase end, the full suite only at the
   pre-merge gate. Record the exact commands in the map — "run the tests" is not a
   command.
6. **Test quality:** reuse the project's shared fixtures before writing new ones; any
   test swapping global/static state (locale sources, time zones, clocks) must restore
   it after; refuse **tautological** tests (the assertion recomputes the expected value
   the way the code does — expected values come from an independent source),
   **implementation-coupled** tests (break on refactor though behavior held), and
   horizontal slicing.
7. **Phase done ≠ done:** module-scope regression green, not just the new test;
   whatever could **not** run locally named explicitly in the phase report (silence
   reads as "ran"); plan doc updated in the same commit window; the push's `<CI>` build
   checked against the map's definition of green. Any finding on this work re-enters
   **here** (re-entry rule).

## Debug

1. **The generic systematic-debugging discipline is the spine** — root cause before any
   fix, always. This section adds the loop's bindings; it does not restate the spine.
2. **Read the build honestly.** Green = the map's recorded definition; a "not red"
   result can hide failures where the pipeline soft-fails test stages — pull the actual
   per-test results, not just the result flag.
3. **Reproduce at the narrowest scope first** — single test, then module. Don't start
   the full stack to chase a unit-test failure.
4. **Passes locally, red in CI (or production)? The environment difference is the bug's
   habitat.** Find it and force it into a test — time zones (CI containers often run
   UTC), containerized vs native services, per-build ports, a simulated DOM vs a real
   browser — instead of shrugging "works on my machine".
5. **No red-capable command → no hypothesizing.** Name one command, already run at
   least once, that goes red on the exact symptom, before proposing any fix.
6. **Keep a known-flake-shapes list** in the case history (leaked static state,
   polluted local DBs, missing polyfills are the classic shapes) and check it before
   deep-diving.
7. **A diagnosis is not done until its fix re-enters at Implement** — regression test
   first (re-entry rule).

## Review

1. **Effort by risk class, detected from the diff before choosing.** High effort, no
   exceptions, for the project's rule-5 risk classes — auth/login paths,
   data-visibility boundaries between user classes or tenants (one user seeing
   another's data is the trust-breaking bug in most products), shared-ownership or
   externally-synced data, audit/compliance-relevant events — these are exactly where
   a plausible-looking diff hides a trust-breaking bug; medium for pure moves/renames
   whose structural tests are green; unsure → high. **Effort changes the fan-out,
   never whether the project's bank items are walked** — those run every time.
2. **Two axes, kept separate: Standards and Spec.** A diff can pass every convention
   and still build the wrong thing (and vice versa). Walk the ACs one by one against
   the diff: **missing/partial** (an AC with no implementing change), **wrong-looking**
   (behavior deviates — green tests that pin the deviating behavior are part of the
   finding), **scope creep** (behavior no AC asks for — name it; delete-vs-spec-amend
   is the author's call, surfacing it is the review's). Quote the AC verbatim in each
   finding.
3. **Process check: Skills-consulted vs diff, both directions.** An area's artifact in
   the diff with no matching consulted row is a finding; a listed skill whose checkable
   rules the diff violates is the same finding, worse — the line claimed discipline
   that didn't happen. Use the finding to trigger the *content* re-check of that area.
4. **Size check.** A diff over ~500 net lines, ~20 files, or spanning more than 2
   modules gets the question: *could this have shipped behind a default-off feature
   flag in smaller PRs?* Surface the answer as a process finding even when the code is
   fine — it is the input to the next ticket's slicing, not necessarily a merge blocker.
5. **Report with the axes visible.** Group spec findings under their own heading —
   never let convention findings bury a spec deviation; if a summary names a "worst
   issue", name one per axis. List unusually-passed high-risk items explicitly ("auth
   items walked — all endpoints carry role checks"). End with the hand-offs that apply
   (quality findings → the quality-gate procedure; schema questions → the DB area
   skill).

## Quality gate

The gate *procedure* (when it runs, what blocks merge) is `references/pr-gates.md` §2.
This is the finding-handling discipline behind it:

1. **Establish what the analysis represents before reading any number.** Where does
   this branch's analysis land in `<QUALITY>` (per-branch, or one slot where the last
   push wins — in which case the slot may currently hold *someone else's* code)? Which
   revision was analysed, and how far is it behind HEAD? How many separately analysed
   projects exist (backend/frontend halves)? Numbers without this context describe
   someone else's code.
2. **Know what "new" means.** Bucket the tool's new-code period against the branch
   divergence point: period after divergence → red is your session's scope; at
   divergence → the whole branch; before divergence → "new issues" is mostly
   pre-existing epoch noise — **negotiate scope before fixing anything**.
3. **Triage the analysis before the findings.** A failed gate with zero new issues and
   a collapsed coverage number is usually a **broken analysis** (aborted build, missing
   coverage report), not a code problem — the fix is a rerun/next push, never a
   threshold negotiation.
4. **Sibling audit before any fix-vs-suppress decision.** Same rule, same
   package/directory, other files: an entrenched style (several siblings firing the
   rule) gets a scoped, rationale-backed suppression decision — not a lone fix that
   diverges from its siblings and trades a quality finding for a review finding.
5. **Spec table before edits.** Per finding: rule, severity, sibling count, action
   (fix / suppress / accept), mechanism, reason — **the user reviews the table before
   any edit**. Refuse to suppress the severe classes (bugs, blockers, security
   findings): offer a fix, a documented inline exception, or plan-doc escalation.
6. **Wording discipline.** The server totals cannot move until the next analysis lands:
   say "reanalysis pending" until then, and only the post-push re-verification report
   may say the quality work is complete.

## Document

1. **Evidence over guesswork.** The merge commit is the durable evidence basis — the
   branch may be gone; the merge commit never is (its first-parent diff + full message).
   Anything inferred rather than confirmed is **labeled as an assumption in the page
   itself**. Never describe uncommitted changes as implemented behavior.
2. **Group by theme, not by file** — a changelog is not documentation. Lead with the
   problem and the outcome; pull user-facing wording from the i18n/message diffs, not
   from memory.
3. **A branch broader than the ticket:** document the ticket-relevant subset, name the
   accompanying changes in their own section, and say the scope was narrowed.
4. **Spec amendments must surface.** If implementation amended the spec, the page
   summarizes the amendments — otherwise it lies about shipped behavior. A clarification
   the implementation decided gets stated as the now-governing rule, marked whether the
   ticket ever confirmed it.
5. **Verify, link, then claim.** Read the published page back and check it rendered;
   link it from the ticket (and say which link mechanism was used — tools lie about
   their coverage); only then say "published". The remaining close-out items
   (pr-gates §3) stay with the close-out — don't absorb them, don't skip them.
