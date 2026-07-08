# PR gates: Review → Quality gate → Merge close-out

Read this the moment a PR exists (or before calling a ticket "done"). Three procedures,
in order. Wherever a fix is required, the **re-entry rule** applies — the canonical
statement lives in `SKILL.md` ("The loop"); this file cites it rather than restating it.
The finding-handling *disciplines* behind §1 and §2 (risk classes, the two axes, the
sibling audit, the spec table) are `references/stage-disciplines.md` §Review and
§Quality gate — procedure here, discipline there.

## 1. Review gate (mandatory — between PR and merge)

> The review stage is a **gate, not a label on the diagram.** Opening a PR and getting
> `<CI>` green are **necessary but not sufficient** — neither is the review. A ticket is
> **not done** and **must not be merged** until the review gate has run and its findings
> are resolved or explicitly deferred. "PR opened + CI green" is the trap, not the
> finish line. And remember what green means here: the map's recorded definition —
> pipelines that soft-fail test stages make "not red" hide failures.

**How the gate runs — every PR, before merge:**

1. **Trigger.** The moment a PR exists (or before you would call a ticket "done"), the
   review gate is **due**. Do not wait to be asked.
2. **Run the review — right-sized, never skipped.** Review the full branch diff
   (`mainline...HEAD`) with the project's review overlay loaded, so the project-specific
   bank items are walked on top of the generic ones. Announce it. Pick the review effort
   by risk class (stage-disciplines §Review; the classes are bound at onboarding, loop
   rule 5): **high effort, no exceptions** for auth/login, data-visibility boundaries,
   shared-ownership data, audit-relevant events; medium for pure moves/renames with
   green structural tests; unsure → high. Effort changes the fan-out, never whether the
   bank items are walked.
3. **Resolve — back through the loop, not around it.** A finding fix is implementation
   work: it **re-enters at Implement per the re-entry rule** (`SKILL.md`, "The loop") —
   area skills loaded first, test-first, `<CI>` green again on the fix push, and the
   changed surface **re-reviewed** (re-run the review on the new diff, or at minimum
   re-walk the overlay items for the touched area — replying to the reviewer is not a
   re-review). Additionally:
   - Update the plan doc's **Skills consulted** line with any new area a fix pulled in.
   - Out-of-scope findings → a follow-up ticket in `<TRACKER>` with a one-line rationale.
   - Record the outcome (findings + fixes + skills loaded) in the plan doc's review note
     or the PR.
4. **Only then merge.** Merge is reached only when CI is green **and** the review gate
   has run **and** findings are resolved/deferred **and the fix round itself cleared the
   loop.** Merge method: **squash commit by default** — one commit per ticket on the
   mainline, referencing the ticket ID — unless the project's house conventions bind a
   different method (record any deviation in the tooling map).

**Definition of done for a ticket:** the **spec committed** (rule 11 — source-of-intent,
including any grill/implementation amendments) **and** CI green **and** review gate run
**and** quality gate green **with its reported new-issue list cleared** (not merely a green
gate) **and** findings resolved/deferred **and** the ticket's acceptance criteria verified
**and** (post-merge) the close-out below completed, documentation included. Missing any one
means the ticket is still in flight — say so rather than reporting it done.

## 2. Quality gate (mandatory — before merge)

> **A green quality gate is necessary, NOT sufficient.** Gates pass while the analyzer
> still reports new issues and duplications that sit below the fail thresholds — so the
> gate conclusion is not proof of "no new issues". You MUST pull the actual reported
> issue list from `<QUALITY>` and **clear every entry before merge, even when the gate
> is green.** Note also: many CI pipelines report a failed quality gate as a warning
> rather than a build failure — a build that "ran through" says nothing about the
> analyzer.

If the map binds `<QUALITY>: none`, this section collapses into the review gate — say so
explicitly when running §1 ("no static-analysis gate on this project; review absorbs
it"), and treat the first quality-shaped incident as the trigger to revisit the binding.

**How the gate runs — every PR, after CI + the Review gate, before merge:**

1. **Trigger.** Establish (once, at onboarding — re-verify when it matters) *where* the
   analysis for this branch actually lands in `<QUALITY>`: per-branch analyses, or
   everything under one default slot regardless of the git branch — in which case pin
   the analysed revision before and after every comparison (the slot may flip to a
   colleague's branch between your queries). Resolve what the numbers represent before
   reading any number. Projects with separately analysed halves (backend/frontend) have
   **multiple analyses — check all of them.**
2. **Read the findings — the actual list, not just the gate conclusion.** For **every**
   analysed project on the branch: gate status, then the new/unresolved issue list, then
   the new-code measures (coverage, duplication). Triage **every** entry — bug,
   vulnerability, code smell, security hotspot, duplicated block, coverage shortfall. A
   green gate with a non-empty list is **not** done. (First: the broken-analysis triage
   and the "what does *new* mean" bucketing — stage-disciplines §Quality gate.)
3. **Resolve — back through the loop, not around it.**
   - A finding that changes implemented logic **re-enters at Implement per the re-entry
     rule** (`SKILL.md`, "The loop").
   - A coverage gap on new code → the missing test IS the fix (still test-first).
   - **Sibling audit before fixing one file in isolation** — a point-fix that diverges
     in style from its siblings trades a quality finding for a review finding
     (stage-disciplines §Quality gate).
   - A false positive / won't-fix → **prefer an in-code fix that also satisfies the
     analyzer**. Only when a code fix would genuinely degrade the design: mark it
     resolved in `<QUALITY>` **with a written rationale**, and record that decision in
     the plan doc's quality note. That rationale-backed resolution IS the only deferral
     path this gate grants — it is what close-out §3.3 propagates to a follow-up ticket.
     An issue that is neither fixed nor rationale-resolved blocks merge; "file a ticket
     and merge anyway" without the recorded rationale is not a path.
   - Each fix push re-triggers `<CI>` **and** the analysis — **re-check both before
     merging** (being small or post-green is not an exemption; re-entry rule).
4. **Only then merge.** "CI green + reviewed + issue list cleared on every analysed
   project," never "the gate went green."

## 3. Merge close-out (mandatory — after the merge, before calling the ticket done)

Merging is not the last step; the close-out is. Every item, every merge:

1. **`<CI>` green on the mainline** after the merge — not just the branch head build.
   Merges silently re-breaking suites is a documented failure class (start the project's
   case history with the first instance; see `references/growing-area-skills.md`).
2. **Tracker lifecycle:** transition the ticket per house conventions, tick the parent
   epic's checklist if any, and note the merge commit on the ticket.
3. **Propagate deferred findings.** Anything the review or quality gate deferred or
   rejected-with-rationale gets **written onto a follow-up ticket now** — a deferred
   finding that lives only in a transcript is lost by the next session.
4. **Run the Document stage — unconditionally.** Ticket documentation in `<DOCS>` per
   the project's template (discipline: stage-disciplines §Document), plus the link back
   from the ticket. A merged ticket without its docs page is not finished, regardless
   of size.
5. **Substrate staleness check.** Did the change invalidate anything the project's
   agent-instruction files or an area skill **states** — a convention, a port, a
   filename cited as an example? Patch it in the same PR or immediately after.
6. **Notify** per *Staying in touch* (`SKILL.md`) that the ticket is closed out.
