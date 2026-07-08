# Release & deploy (off-loop batched-release procedure)

Read this when a release is due. This procedure sits **off the per-ticket loop**: deploys
happen on demand and ship several merged tickets at once. It is also the one procedure
that touches production, so its contract is inverted:

> **The agent prepares and verifies; the human executes (or explicitly approves) every
> state-changing step.** The procedure's product is a precise, copy-pasteable runbook per
> release — not autonomy. This holds **regardless of what access the agent has**
> (capability ≠ permission — the human-gated list is a tooling-map fact).

> **Placeholders to pin on the first real release run** (each note self-destructs when
> filled): where the release record lives (`<TRACKER>` fix-version vs a `<DOCS>` release
> page); the exact `<CI>` job/workflow that builds the release; the tagging/versioning
> scheme on `<REGISTRY>`; the environment host(s) and the deploy mechanism (`<DEPLOY>`);
> and what access the agent actually has to each, verified live — not assumed. Pinning
> these on the first run, in this file, is the pattern: a runbook slot that stays "TBD"
> past its first use is a runbook that lies.

## A. Release scope & preconditions

- [ ] **Shipping set:** merged tickets since the last released version — `git log` on the
  mainline since the previous release tag, cross-checked in `<TRACKER>`: every shipping
  ticket is Done **and** its `<DOCS>` documentation exists (close-out step 4 should have
  ensured this; verify, don't assume).
- [ ] **Mainline green on `<CI>`** (the map's recorded definition of green).
- [ ] **`<QUALITY>` clean on the mainline** (every analysed project, the issue list — not
  just the gate).
- [ ] **Migration pre-flight — if `<MIGRATIONS>` is bound, the main release risk, never
  skipped:** migrations run at app start (or deploy time) on the target environment.
  List **every migration** shipping in this release (diff the migration log against the
  previous release tag) and flag destructive (drop/rename/type-change) or long-running
  (large-table index/backfill) ones **before anything is built**. Flagged migrations get
  an explicit go/no-go note and shape the rollback plan in stage D.
- [ ] **Record the release contents** (ticket list + intended version) in the release
  record (placeholder above).

## B. Build & publish

- [ ] **Check the pipeline's resource constraints first** — pin on first run whether the
  release build may run concurrently with regular CI builds (shared runners, fixed test
  ports, and small build hosts make concurrent heavy builds a known failure class; when
  in doubt, let the CI queue drain first).
- [ ] Trigger the release build on `<CI>` (job/workflow + version parameter per the
  pinned placeholder) and watch it to completion. Know **which gates the job itself
  enforces** (tests, quality gate, E2E against the built artifact, security scan) and
  **whether the publish step is gated or unconditional** — if the job pushes to
  `<REGISTRY>` without a manual approval step, **starting the job IS the go decision**
  for the publish, and the human makes that call.
- [ ] Outcome to verify: the artifact built and pushed to `<REGISTRY>` under the intended
  version, and the matching release tag pushed to `<VCS>`.

## C. Deploy to environment — every step human-gated

Whatever the mechanism bound in `<DEPLOY>`, the shape is constant. **Every state-changing
step below waits for explicit user confirmation before the agent runs it** — even where
the agent's access would allow running it unprompted.

- [ ] **C0 — rollback note ready first** (see stage D — written BEFORE anything changes).
- [ ] **C1:** point the environment at the new version (env file, values file, image
  tag — per the pinned mechanism).
- [ ] **C2:** fetch the new artifact while the old version still runs, then swap
  (pull → down/up, rolling update, PaaS promote — per the pinned mechanism), each as its
  own user-confirmed step.
- [ ] **C3 — the go/no-go moment:** tail the app logs until migrations complete and the
  app reports started. Migration error or startup failure → stop, execute the rollback
  note, diagnose offline.

## D. Verify & record

- [ ] **Rollback note (written before stage C ran):** the previous version identifier +
  the exact revert sequence. Honest caveat: if a schema migration already ran, rolling
  back the artifact alone may not suffice — that is precisely why stage A flags
  destructive migrations; for those, the rollback note must say what the DB story is
  (restore, compensating migration, or accept-forward).
- [ ] **Smoke check:** the login path(s) reachable, one known endpoint per app half
  responds (pin the exact URLs on first run).
- [ ] **`<TRACKER>`:** mark shipped tickets with the release/fix version.
- [ ] **Update the release record** from stage A with the final version + deploy
  timestamp.
- [ ] **Notify** per *Staying in touch* (`SKILL.md`) that the release is out.

---

Deliberately not modeled until a project needs it: multiple environments. Stage C names
the single current environment; a staging/prod split later just parameterizes the
host/target without restructuring this procedure.
