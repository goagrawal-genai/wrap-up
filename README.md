<p align="center">
  <strong>Your wrap-up is probably making the next session worse.</strong>
</p>

<p align="center">
  A knowledge-maintenance pass for the end of an agent session — not a transcript summary.
</p>

<p align="center">
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-MIT-blue.svg?style=flat" alt="License: MIT"></a>
  <img src="https://img.shields.io/badge/version-0.1.0-green.svg?style=flat" alt="Version 0.1.0">
  <img src="https://img.shields.io/badge/skill-wrap--up-orange.svg?style=flat" alt="Skill: wrap-up">
</p>

---

## The problem

You finish a long session. You ask the agent to wrap up. It dutifully improves five reference
files, appends four new rules to your instruction file, and reports success.

Then the next session opens, reads your handoff doc first — and finds a **confident, dated,
wrong** summary of where the work stands. It trusts it. You have made things worse while feeling
productive.

Three failure modes, all common, all silent:

| Symptom | What actually happened |
|---|---|
| Five files improved, next session still confused | The handoff doc was never refreshed |
| Rules file grows every session; nobody reads it any more | Append was always the default. Consolidate never fired |
| The same insight gets "discovered" three times | The un-routed queue was never probed — only memory was |

## What this does differently

**1. It refreshes the entry point, and treats that as the deliverable.**
The last thing it touches is the first thing the next session reads. It re-runs whatever generates
your "current state" block and rewrites the narrative to today — then sanity-checks every number
against live data, *including blocks labelled auto-generated*, because a generator that isn't
actually wired into a schedule drifts silently while its own comments promise it can't.

**2. It subtracts. Most memory tooling only adds.**
The consolidate-vs-append test is keyed to what actually costs you: **auto-loaded vs
retrieved-on-purpose.** A 300-line rules file injected into every session and every subagent costs
far more than a 3,000-line reference someone opens deliberately. So it consolidates hard on the
first and relaxes on the second. Retiring a superseded rule counts as a win.

**3. It probes before it reflects.**
Un-routed queues and your validator first; recollection second. A captured-but-unfiled note is by
definition the thing that would otherwise be lost — you don't find those by remembering.

**4. Mistakes become triggers, not stories.**
Anything you or the agent got wrong is written as **TRIGGER → REQUIRED CHECK → FAILURE
PREVENTED**. A rule that names its trigger actually fires. A narrative just sits there being true.

**5. Weak signal gets a review date, not a rule.**
One occurrence is not a pattern. Wrap-up is exactly where inferences get promoted to facts by
accident, so weakly-evidenced items are routed to a dated review instead of hardened into
instructions.

**6. It won't lie to you about what it saved.**
It re-runs your gate, confirms pointers resolve, checks the diff scope, and states what it
deliberately did *not* persist. Where it can't write at all, it says so and emits a paste-able
proposal instead of narrating a save that never happened.

## Install

```
/plugin marketplace add goagrawal-genai/wrap-up
/plugin install wrap-up
```

Then, at the end of a session:

```
wrap up
```

Or invoke it directly with `/wrap-up`.

## How it routes

It works from **seven roles**, discovering how *your* project implements each one rather than
assuming file names:

| Role | Typical implementations |
|---|---|
| Instruction store | `CLAUDE.md`, `AGENTS.md`, `.cursorrules`, an agent profile |
| Memory index | `MEMORY.md`, a memory directory, a memory API |
| Entry point / handoff | `RESUME.md`, `HANDOFF.md`, a "current state" section |
| Decision store | a decision register, ADRs, a changelog, an issue tracker |
| Reference store | `reference/`, `docs/`, a wiki |
| Pending queue | a journal or inbox file, an "open items" list |
| Gate | tests, lint, a schema or link checker, a `validate`-style script |

If a role has no implementation, it says so rather than inventing one.

## Design notes

Longer-form reasoning — including which "obvious" improvements were deliberately rejected and
why — lives in [`docs/design-rationale.md`](docs/design-rationale.md). Short version: a skill is
read by a model with judgment, not executed by a runtime, so typed schemas, confidence scores on
prose, and prompt-level "transactions" cost more than they return.

The skill is deliberately kept short enough to re-read. **A skill too long to re-read gets
skimmed** — which is the exact failure it exists to prevent.

## Scope and safety

- It reads your project's rules first, and **project policy always outranks the skill**.
- It uses the **narrowest valid scope** and will not promote project-confidential material into
  global or shared stores.
- It never publishes, pushes or syncs anything. Committing is offered, never assumed, and paths
  are staged explicitly.

## Contributing

Issues and PRs welcome — particularly **failure modes you've hit in the wild.** The
"Failure modes" table in the skill is the most useful part of it, and it grows from real reports.

## License

MIT — see [LICENSE](LICENSE).
