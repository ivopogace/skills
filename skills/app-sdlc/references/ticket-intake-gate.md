# Ticket-intake grill gate (mandatory when entering at an existing ticket)

Read this at plan entry, whenever work starts from an already-written ticket.

> A written ticket is a **snapshot of intent at creation time, not ground truth.** By the
> time you pick it up the code may have moved, a sibling branch may have changed a
> contract, or the ticket may simply have missed something nobody thought of at creation.
> **Before** you author the plan doc for an existing ticket you **MUST** grill it. This
> is a **gate, not a suggestion** — "the ticket looked complete" is never a reason to
> skip (that is exactly when stale ACs slip through).

## How the gate runs — every time work starts from an existing ticket

1. **Trigger.** Any time you enter the loop at an **existing** ticket (the user says
   "implement/work on <ID>") rather than refining a fresh idea, this gate is **due
   before the Plan stage**. Refining a brand-new idea already runs the grill at the
   Refine stage — this gate catches work that *skips* Refine by starting from a ticket.
   **This path still produces a spec** (rule 11 — always), just not from a fresh Refine
   interview: this grill *is* the refinement, and its outcome is written into a short spec
   (existing-ticket flavor — state the confirmed intent, fold in the reconciliation) and
   committed **before the plan**. The ticket stays the tracker record; the spec is the
   durable source-of-intent the plan and review cite.

2. **Grill the ticket against current reality.** Pull the ticket + comments + linked
   issues + parent epic from `<TRACKER>`, then interrogate it:
   - **Stale-by-default:** treat inlined example values and screenshots as stale until
     cross-checked against the actual code. Are the acceptance criteria still **correct,
     complete, and testable today**?
   - **What's in flight right now?** List open PRs on `<VCS>` and active sibling
     branches and check for overlap: shared files, shared modules — and if the map binds
     a `<MIGRATIONS>` tool, the **migration-collision check**: the next
     changeset/version slot must be free on the mainline *and* unclaimed by any open
     PR's diff. If a collision is possible, record in the plan doc **who renumbers**
     (default: whoever merges second) and expect a merge-from-mainline before the PR.
   - **Which module should own each piece?** Sanity-check the intended placement against
     the project's module/package structure *before* planning. Catching a misplacement
     here is a sentence in the plan; catching it at review is a diff.
   - **Shared-ownership tripwire:** if the ticket touches data that another system also
     writes (sync jobs, imports, a second app on the same tables), the **field-ownership
     question** (who owns which field) is due **now, at intake** — not at review. The
     plan doc's ownership-contract section records the answer.
   - **Division of labor:** answer the **discoverable/factual** questions yourself from
     the code and mark each "← confirm?"; escalate the **intent/product** decisions to
     the user as blocking questions (notify first per *Staying in touch*). Never
     auto-fill a decision the human owns.

3. **Reconcile before building.** Fold the outcome into the plan doc's **Open questions**
   and **Acceptance criteria**. If the ticket is materially stale, say so and **update
   the ticket (or record the drift in a comment)** before you plan against it. A surprise
   caught at this gate is far cheaper than one caught at the review gate or in
   production. **When the grill *materially* rewrites intent** — a false premise corrected
   (the requested mechanism doesn't fit the code), scope added or cut — the corrected
   intent goes into the **spec** (rule 11) **and** onto the ticket **before planning**,
   never left only in the plan doc or the conversation. A reader of the ticket must see
   the corrected intent, not just the implementer.

## Proportional, never skipped

A one-line/copy fix needs only a quick sanity read, not a full interview; a ticket
touching the project's high-risk classes (auth, data-visibility boundaries,
shared-ownership data — bound at onboarding, loop rule 5) gets the full grill. The size
flexes; the gate does not.
