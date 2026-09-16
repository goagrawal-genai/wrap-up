---
name: wrap-up
description: Use at the end of a working session, at a milestone, or before a handoff — when the user says "wrap up", "wrap up the session", "close out", "hand off", "checkpoint", or "capture what we learned so the next session is smarter". NOT a transcript summary: a knowledge-maintenance pass. Probes the project's un-routed queues and validators before reflecting, extracts only durable signal (preferences, preventive rules from overturned claims, decisions, recurring friction, stale artifacts), routes each to the narrowest authorized home, CONSOLIDATES rather than endlessly appending to auto-loaded rule files, then REFRESHES THE ENTRY-POINT DOC (RESUME/HANDOFF/current-state) and verifies every write against the project's own gate. Degrades to report-only where it cannot write, and never claims persistence that did not occur.
---

# Wrap-up — end-of-session distillation & feedback loop

Turn what just happened into durable improvements to the setup, so the NEXT session
starts smarter. This is a **distillation ritual, not a transcript dump**: capture the
few reusable signals, route each to the right home, prune the stale, and keep every
artifact high-signal — **bloat makes future sessions dumber, not smarter.**

## Why this works
An agent has no cross-session memory except the artifacts that get re-loaded or
re-read next time. So "get smarter" = improve those artifacts. Wrap-up is the
deliberate loop that does it.

## The seven roles (find the project's implementation; do not assume file names)
Everything below routes by **role**, never by a hard-coded path. Discover what this
project actually uses, and if a role has no implementation, say so instead of inventing one.

| Role | Typical implementations |
|---|---|
| **Instruction store** | `CLAUDE.md`, `AGENTS.md`, `.cursorrules`, a system prompt, an agent profile |
| **Memory index** | `MEMORY.md`, a memory directory, a memory API, a vector store |
| **Entry point / handoff** | `RESUME.md`, `HANDOFF.md`, a "current state" section, a task-state record |
| **Decision store** | a decision register, ADRs under `docs/decisions/`, a changelog, an issue tracker |
| **Reference store** | `reference/`, `docs/`, a wiki, a knowledge base |
| **Pending queue** | a journal or inbox file, an "open/unrouted" list, a ticket backlog |
| **Gate** | tests, lint, a schema or link checker, a `validate`/`doctor`-style script |

## When to use
- The user says "wrap up", "wrap up the session", "close out", "hand off", or
  "capture what we learned".
- After a milestone, a correction-heavy session, or before a long gap.
- NOT after every routine task — only when there is durable learning worth persisting.
  A short status summary is the right answer more often than a full pass.

## Before anything: read the ground rules
1. Read the project's **instruction store** for its **hard rules** — especially data and
   privacy handling, what must NOT be written where, and any version-control restrictions.
2. Read the **memory index**, the **entry point**, and the newest **decision store** entries —
   so you **UPDATE existing entries rather than duplicating them**.
3. Project policy always outranks this skill. If they conflict, the project wins and you say so.

> **Hard guardrail — PII & scope.** Never write project-confidential data (names, IDs,
> comp, performance, personal notes) into GLOBAL / user-level files or cross-project
> memory — that leaks it across contexts. Project PII stays only in that project's
> designated PII files. If the project marks something off-record/sensitive, do **not**
> persist it raw — persist only the sanitized, decision-relevant signal. When unsure, ask.

## Invariants
Non-negotiable, whatever the harness:
- **Never claim persistence that did not occur.** Report only writes you verified. "Saved" is a
  claim about the file, not about your intent.
- **Narrowest valid scope.** Broaden (project → user → global) only on affirmative evidence that
  it applies there. Convenience of the destination is not evidence.

## Process
0. **Find the boundary, check what you can actually do, and probe before you reflect.**
   - **Scope to since the last wrap-up.** Look for its marker (a `wrap-up …` commit, a dated
     ledger entry, a marker line) and consider only signal *since* then. Wrap-up may run twice
     in a day; re-proposing what you already persisted wastes the user's review.
   - **Probe the project's own queues first.** If it has an un-routed capture queue (a journal,
     inbox, "pending"/"open" list) or a health/validate command, run those *before* scanning the
     conversation. The highest-value items usually surface there, not from recollection — a
     captured-but-unfiled note is by definition the thing that would otherwise be lost.
   - **If you cannot write here → REPORT-ONLY.** Say so up front and emit the proposal as a
     paste-able block with its destinations named. Do not narrate it as though it landed.
   - **If no user is available** (cron, autonomous run): apply the safe, reversible, clearly-routed
     items, **defer** the rest with a reason, and never block the whole wrap-up on one ambiguous
     item. Deadlocking on a confirmation nobody is there to give is a failure, not caution.
1. **Reflect on the session.** Scan the conversation (and, if useful, the session
   transcript / ledger) for DURABLE signal in these buckets:
   - **Behavioral corrections / preferences** — where the user corrected how you worked
     or confirmed an approach ("do X not Y", "always/never…", tone, format). *Strongest signal.*
   - **Preventive rules — from anything you or the user got wrong.** Factual claims of yours that
     the user, the data, or a subagent overturned. Distinct from the bucket above: that one is
     *how* you work, this one is *what you got wrong*. Write it in three parts —
     **TRIGGER** (the situation that should fire the check) · **REQUIRED CHECK** (what to do) ·
     **FAILURE PREVENTED** (what goes wrong otherwise) — never as a story about the mistake.
     *A rule that names its trigger actually fires; a narrative sits there being true.*
   - **Recurring friction / patterns** — anything you did more than once or had to
     re-explain; candidates to automate or encode.
   - **New durable facts / decisions / authorizations** — with their rationale.
   - **Workflows worth encoding** — a repeated task that should become a skill or command.
   - **Stale / wrong artifacts** — now out of date or contradicted; fix or prune them. Don't wait
     for these to appear: **probe** for them. Highest-yield probes — an entry marked
     done/closed/dropped whose evidence doesn't support it (especially one **dropped with no
     recorded reason**), a "current state" or summary block whose numbers no longer match the
     data, and a documented remedy nobody has confirmed actually works.
   Ignore transient task detail, one-offs, and anything already recorded.
2. **Route each item to its RIGHT home** (table below). Prefer UPDATE over a new file; dedup.
   **New-file test:** create one only if the result is a reference someone would open *on
   purpose*. Otherwise append to the nearest existing home.
   **Append-vs-consolidate test — this is what keeps the ritual from eating itself. The variable
   is AUTO-LOADED vs RETRIEVED-ON-PURPOSE, not line count.** A 300-line rules file injected into
   every session costs more than a 3,000-line reference someone opens deliberately. So:
   - **Auto-loaded target** (`CLAUDE.md`/`AGENTS.md`/system-prompt-adjacent): consolidate hard. The
     default action **flips from append to consolidate** — merge overlapping rules, retire
     superseded ones, *then* add. Prefer replacing a rule over standing a fifth one beside it.
     A hub that has taken appends in **≥3 consecutive wrap-ups** is a warning signal, not the test.
   - **Retrieved-on-demand target** (`reference/`, a wiki, docs): relax. Length is cheap here;
     findability and accuracy are what matter.
   **Supersede, don't delete, where history carries meaning.** A decision that no longer governs
   is still needed to understand why things are as they are — edit it in place and mark what
   replaced it. Never leave two rules that half-agree: that is worse than either alone.
   **Be idempotent.** Before writing, check for *your own* prior entry and update it rather than
   adding a second. Re-running over the same inputs must not duplicate.
   **Weak signal → review date, not a rule.** If the evidence is one occurrence or an inference,
   route it to the project's dated-review mechanism (assumption register, dated action) instead of
   hardening it into an instruction. Wrap-up is where inferences get promoted to facts by accident.
   Wrap-up is the main engine of bloat in any repo that uses it — if nothing pushes back, this
   skill slowly makes sessions dumber while reporting success.
3. **Draft a tight proposal** — one bullet per item: `what → destination → one-line change`,
   plus **one clause on scope + sensitivity** (project vs global, sensitive vs safe). A sentence,
   not a schema — the proposal is a human-review artifact and metadata fields degrade it. Group by
   destination. Keep it short: a handful of high-value entries beats a long list.
4. **Confirm, then apply.** Show the proposal; on the user's OK, make the edits. If the project
   has persistence tooling (a register CLI, a note/ledger command, a memory API), use it rather
   than hand-writing prose into a file it manages. (If the user says "just do it", skip the confirm
   for low-risk edits but still report; **always** confirm before touching the instruction store's
   hard rules or anything global.)
5. **Refresh the entry point. This is the deliverable, not a nice-to-have.**
   If an entry-point artifact exists — often the very file the instruction store tells a resuming
   session to read *first* — update it: re-run whatever regenerates its computed blocks, then
   rewrite its "latest / where we are" narrative to today. Sanity-check every number it asserts
   against live data, **including blocks labelled auto-generated**: a generator that isn't actually
   wired into a schedule drifts silently, and its own comments will still promise it can't.
   > **A wrap-up that improves five reference files and leaves the entry point stale has made the
   > next session WORSE, not better** — it now opens on a confident, dated, wrong summary and
   > trusts it. If you change nothing else, change this file.
6. **Verify, then report.** Reporting is not verifying. Re-run the project's **gate** after
   writing, confirm every new pointer or link resolves, and check your diff touched only what you
   intended. Then report exactly what you wrote where — and say what you deliberately did **not**
   persist, and why, so the next session doesn't redo the judgment. Where version control exists,
   offer to commit (staging paths explicitly, never `-A`); **the VCS is the rollback net**, so diff
   before committing rather than inventing a transaction the harness cannot honour. If no gate
   exists, re-read what you wrote and say plainly that nothing else was verifiable.
   **Two checks before anything durable lands:**
   - *Would a future reader know when this STOPS applying?* An always-true-sounding rule with no
     expiry condition outlives its situation and then quietly misleads.
   - *Is this an inference dressed as a fact?* One occurrence is not a pattern; a thing you did
     twice is not yet a preference. If it can't cite what happened, it belongs in the review queue.

## Routing table
Route by **role** (see the table at the top), not by path. Examples are illustrative only.

| Signal | Destination role |
|---|---|
| How to WORK (rules, preferences, lessons, tone) — **project-specific** | **Instruction store**, in its working-principles section |
| How to WORK — **cross-project**, true in any repo | **Memory index** or the user-level instruction store |
| Durable decision + rationale, or running narrative | **Decision store** |
| Current state, open loops, next action | **Entry point / handoff** |
| Reusable domain knowledge or playbook | **Reference store** |
| A repeated multi-step workflow | Propose a new **skill / command / template** — don't inline it |
| Anything about a specific person | The project's designated people store — **never** a global or shared one |
| Weakly-evidenced item | **Pending queue** with a review date, not the instruction store |
| Stale or incorrect entry | Edit or supersede it **at its source** |

Prefer an existing destination over a new one. Create a new artifact only when no destination
fits, its audience and lifecycle are genuinely distinct, and a future reader would open it on
purpose.

## Principles
- **Distill, don't dump.** A few high-value, reusable entries; never paste the transcript.
- **Dedup + prune.** Update what exists; remove what's stale. Keep artifacts high-signal.
- **Leave the entry point true.** The last thing you touch should be the first thing the next
  session reads. Everything else is optional; this isn't.
- **Subtract as readily as you add.** A wrap-up whose net effect is "+40 lines" every time is
  failing, however good each line is. Retiring a superseded rule is as valuable as writing a new one.
- **Close every correction loop.** A correction the user made should become a rule so it never
  recurs — that is the core of the feedback loop. Same for your own overturned claims: encode the
  check that would have caught it.
- **Right home, right scope.** Project-specific → the project; cross-project → global. Sensitive
  data → only where the project designates. Never mix these up.
- **Respect the project's guardrails above all else** — privacy, off-record material, and any
  restriction on publishing, pushing or syncing.

## Failure modes (seen in the wild)
| Symptom | What actually happened |
|---|---|
| Five files improved, next session still confused | The entry-point doc was never refreshed (step 5) |
| Rules file grows every session, nobody reads it | Append was always the default; consolidate never fired (step 2) |
| The same insight gets "discovered" repeatedly | It went into a new file instead of the hub, or the un-routed queue was never probed (step 0) |
| A stale entry survives audit after audit | Staleness was treated as something you notice, not something you probe for (step 1) |
| "Persisted" items that aren't in the file | Reported without re-running the gate (step 6) |
