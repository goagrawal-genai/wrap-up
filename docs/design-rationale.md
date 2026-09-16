# Design rationale

Why `wrap-up` is six steps rather than one, what each step exists to prevent, and — more usefully —
**which obvious improvements were deliberately rejected.** This document exists so the skill itself
can stay short enough to re-read.

---

## The thesis

The bottleneck on cross-session agent quality is not **capture**. It is **curation**.

An agent starts every session from nothing except the artifacts it can re-read. So the state of those
artifacts *is* the quality of the next session. Left alone they decay in **two directions at once**,
and the two look like opposites:

| Decay | What it looks like |
|---|---|
| **Loss** | You settle something in conversation and never write it down. Weeks later the same question is re-argued from scratch, because nothing in the project knows it was answered |
| **Accretion** | Every pass appends a few more rules, each individually justified, until the rules file is too long for anyone — including the model — to re-read, and the status doc has been wrong often enough that everyone skips it |

Almost every "agent memory" feature optimises **capture**, which is the half that isn't broken.
Capture-oriented tooling structurally cannot fix accretion, because adding is the only verb it has.

So the design goal is not *remember more*. It is: **nothing learned is lost, and nothing learned has
to be re-learned.** Six steps, each countering a specific failure.

---

## Why six steps

### ① Probe before you recall — *counters loss through invisibility*

"Reflect on the session" sounds like the heart of a wrap-up. It is its least reliable input, because
recollection is biased toward the vivid and the recent.

The valuable items were consistently *not* remembered. They were already sitting somewhere unread: a
note captured mid-session and never filed; a task marked **done** whose evidence didn't support it; a
high-priority task marked **dropped with no reason recorded** — indistinguishable from an accidental
deletion, and something nobody would ever have looked at again.

**Design choice:** run the project's pending queue and its gate *before* scanning the conversation. A
captured-but-unfiled note is by definition the thing that would otherwise be lost, and you cannot
find it by remembering harder.

### ② Extract signal, not events — *counters accretion at the source*

The cheapest way to feel productive is to summarise. Summaries are long, feel valuable, and age
badly — they are transcript with extra steps.

**Design choice:** an explicit, closed list of what qualifies as durable (preferences and
corrections, decisions *with rationale*, recurring friction, open loops, stale artifacts, preventive
rules) and an explicit list of what does not (what we tried, one-off wording, temporary values,
anything already recorded accurately). A closed list is what makes "no" the default.

### ③ Route by role, not by filename — *counters "written down but never found"*

This is the difference between recorded and *retrievable at the moment it matters*. A decision put
somewhere decisions are not looked up is worse than an unrecorded one, because now you believe it is
captured.

**Design choice:** seven roles — instruction store, memory index, entry point, decision store,
reference store, pending queue, gate — discovered per project rather than assumed. Two consequences
that matter more than the abstraction: it must **say so when a role has no implementation** instead
of inventing a file, and it must use the **narrowest valid scope**, never promoting project-specific
or person-specific material into a shared store because the shared store was convenient.

### ④ Consolidate instead of accumulating — *counters accretion directly*

"Prune the stale" appears in a great deal of guidance and essentially never happens, because nothing
pushes back. Appending always feels safer than merging.

The first attempt was a threshold: consolidate if a rules file took appends in three consecutive
passes. Better than nothing, and arbitrary.

**The variable that actually matters is auto-loaded versus retrieved-on-purpose.** A 300-line rules
file injected into every session *and every subagent* is a cost paid on every request forever. A
3,000-line reference someone opens deliberately costs nothing until it is needed, and then earns its
length. Those are not the same artifact and must not obey the same rule.

**Design choice:** on an auto-loaded target the default action *flips* from append to consolidate.
Retiring a superseded rule counts as success. Supersede rather than delete where history carries
meaning — a decision that no longer governs is still needed to understand why things are as they are.

**Honest result:** applying this slowed growth on one real instruction file from roughly +20 lines
per pass to +4. Slowed, not reversed. What it did achieve was making the growth *visible*, which is
what eventually forces a genuine consolidation pass rather than another append.

### ⑤ Preventive rules, and review dates for weak signal — *counters two different failures*

**Lessons are inert.** "Be careful with shell quoting" reads as true and changes nothing, because it
does not say *when*. Rewritten as **TRIGGER** (the situation that should fire the check) / **REQUIRED
CHECK** / **FAILURE PREVENTED**, the same knowledge activates at the moment of the mistake. This is
testable: the shell-quoting rule above was written after a real incident and caught a recurrence the
following day.

**And inferences harden into facts.** One occurrence is not a pattern; something done twice is not
yet a preference. Wrap-up is precisely the moment where a thin observation gets promoted into an
instruction and then treated as settled.

**Design choice:** weakly-evidenced items route to the project's dated-review mechanism (an
assumption register, a dated action) rather than into the instruction store. If it cannot cite what
happened, it is not a rule yet.

### ⑥ Leave the entry point true, then verify — *counters silent authority*

A stale reference document and a stale *status* document fail differently. A stale reference is a
dead end: the reader notices and moves on. A stale status doc is **worse than absent**, because it is
authoritative — it gets acted on.

This came from a live failure. A wrap-up ran, improved five files, filed real dated actions, passed
the validator, committed cleanly, and reported accurately. It never touched the handoff. The next
session read that handoff first and got item counts well out of date and a summary that omitted the
only hard external deadline in the project.

The instructive part: that block was labelled **auto-generated**, and its generator's own comment
read *"the block is regenerated by the pulse, so it cannot itself drift."* It had drifted for weeks.
The scheduled job calling the generator had been disabled during an unrelated migration. **The only
symptom of a dead scheduler is content that looks fine and is old** — no error, no alert.

**Design choices:** the entry point is refreshed *last*, so it reflects the final state; asserted
numbers are re-checked against live data **even inside auto-generated blocks**; and the step ends
with real verification — re-run the gate, confirm pointers resolve, check the diff touched only what
was intended — plus a statement of what was deliberately *not* persisted, so the next session does
not redo the judgment. Reporting is not verifying. "Saved" is a claim about a file, not about intent.

---

## Rejected: typed schemas and confidence scores

Two independent AI reviews proposed normalising every candidate learning into a typed record —
`{type, evidence, confidence, scope, sensitivity, durability, lifecycle, action}` — before anything
is written.

Rejected, for three reasons.

**Confidence scores on prose are theatre.** What is `confidence: 0.7` on *"pronouns are a fact to
confirm, never infer"*? A model asked for a number will produce one and it will carry no information.
Worse, it manufactures the *appearance* of rigour, which is how a guess quietly starts being treated
as a measurement.

**The proposal is a human-review artifact.** Its job is to be read in thirty seconds and approved or
cut. Five metadata fields per item make that review worse. The binding constraint here is reviewer
attention, not schema rigour — metadata serves the machine at the reader's expense.

**Classification is not where the value is.** Every genuinely valuable finding across the sessions
that shaped this skill — a stale handoff, a disabled scheduler, a P0 dropped with no recorded reason,
a config-ordering bug that had silently disabled a documented remedy for months — came from
**probing and judgment**. None would have surfaced faster under classification; two would have
surfaced slower, because the ceremony consumes the attention that does the probing.

## Rejected: transactions, rollback and checkpoints

Also proposed: transaction-like application with rollback, partial-failure protocols, and a
checkpoint record carrying a source cursor and digest.

A skill is instructions read by a model, not a program run by a runtime. Each edit lands
independently; **there is no atomicity available to offer.** Promising rollback you cannot deliver is
worse than promising nothing.

The real rollback net already exists wherever this matters: version control, a pre-commit gate, and
explicitly staged paths. The skill's job is to *use* it — diff before committing — not to reimplement
it badly one layer up.

The checkpoint record is self-refuting for the same reason step ⑥ exists: it is another piece of
derived state that will drift, in a design that had *just* been bitten by exactly that.

## Rejected: an adapter layer

Also proposed: per-harness adapter files mapping logical roles to concrete paths.

The **abstraction** is right and was adopted — the skill routes by role and discovers the
implementation. The **layer** was not. An adapter file creates a second place where paths live, and
one hard-won lesson from the project this came from is that paths belong in exactly one place. For a
skill, the adapter *is* the project's own instruction file, which the skill reads first anyway.

There is a genuinely better answer, visible in published plugins that support several harnesses: they
ship **duplicated skill files per harness** — one directory per host — with CI keeping them in sync.
That is the useful insight the critiques were circling:

> **Portability is a packaging problem, not a prompt problem.**

Solve it in the repository layout, not by adding indirection to the instructions a model reads.

## Adopted from the critiques

Credit where due. These were real gaps:

| Adopted | Why it was right |
|---|---|
| Capability awareness + a `report-only` mode | The skill assumed it could write. In a read-only host it would half-apply and report success |
| Auto-loaded vs retrieved-on-purpose as the bloat test | Replaced an arbitrary threshold with the variable that actually drives cost |
| Supersede rather than delete where history carries meaning | A decision that no longer governs still explains why things are as they are |
| Idempotency on re-run | Wrap-up can run twice in a day; it must update its own prior entry, not add a second |
| Preventive rules as `trigger / check / prevented` | Sharper than "persist the lesson". A rule that names its trigger fires; a narrative does not |
| A deterministic policy when no user is present | "Show the proposal and wait" deadlocks under automation. Apply the safe items, defer the rest |
| Weak signal → dated review, not a rule | Stops one-off observations hardening into instructions |

---

## Deliberately out of scope

- **Continuous capture.** This is an end-of-session pass, not a background process. If a project needs
  mid-session capture it should have its own cheap append-only queue — which step ① then reads.
- **Deciding anything.** Wrap-up records decisions and surfaces contradictions; it does not resolve
  them. A contradiction between two stores is flagged for a human, never auto-reconciled.
- **Publishing.** It never pushes, syncs or sends. Committing is offered, never assumed.
- **Metrics on itself.** Tempting and unmeasurable without instrumentation nobody will build. "Items
  never referenced again" is unknowable in practice.

## The test for any future addition

> **An addition earns its place only if it changes behaviour at the moment of decision.**

Adopt what does. Reject what merely *describes* the work. Applied to the two reviews that shaped this
version, that test kept 10 of roughly 25 proposals — and adopting either wholesale would have roughly
tripled the artifact, reproducing the accretion half of the very decay it exists to counter.

One of those reviews said as much itself:

> *"Loading the entire conceptual explanation into every session would itself create the bloat the
> skill is designed to prevent."*

…and then supplied a specification several times the length of the artifact it was reviewing. Worth
watching for: a critique that is internally excellent but assumes a *runtime* where you have a
*model with judgment*.
