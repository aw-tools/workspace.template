---
kind: doctrine
status: live
---

# Artefact registry — prose companion

The machine authority is `context/artefacts.toml`: classes, their status
machines, and each kind's class, home, and naming scheme live **there**, read by
`bin/lint-artefacts` at commit time. This file carries what data cannot: purpose
per kind and the templates. It references the data file and never embeds a copy
of it — one authority, no drift.

The corpus is topic-first: standing and binding artefacts sit directly in the
root of `context/` under uppercase names; everything episodic and ephemeral
lives one topic directory below, in `context/engagements/<topic>/`, named
`<kind>-<slug>.md`. Frontmatter is the programmatic authority for `kind` and
`status`; the filename prefix is the human aid, and the lint checks they agree.

## Kinds

- **state** — the singleton handover (`STATE.md`). What a cold session needs to
  resume. Rewritten in place, never appended to.
- **doctrine** — how this workspace works: delivery model, roadmap, friction
  notes, this file. Rewritten in place as understanding changes.
- **decision** — a numbered, binding ruling with rationale, in the single
  append-only register `DECISIONS.md`. Entry discipline: the ruling, the
  load-bearing rationale, one line per rejected alternative — thirty lines or
  fewer; evidence is referenced, never embedded.
- **spec** — what and why, with acceptance criteria, before implementation.
  Authority ends at its application's exit gate.
- **plan** — units, dependencies, sequencing for a multi-unit effort. Same fate
  as spec: spent at the exit gate, residue lives in decisions and the ledger.
- **ledger** — the live run record of an engagement: lane map, unit and gate
  status, verification verbatim. Closes with the engagement.
- **handover** — the brief that launches a worker session on one unit. Expires
  when the unit merges.
- **report** — the worker's result coming back: what was done, what needs a
  decision, what is deferred or blocked.
- **probe** — a ground-truth investigation: verdict, findings, what needs a
  human decision. Same lifecycle as a report.

## Templates

An artefact declares its `kind` and inherits whatever the template below
currently says — templates live here so they cannot drift per file. Statuses
shown are the opening state; the legal set is the class's machine in the TOML.

### doctrine

```markdown
---
kind: doctrine
status: live
---

# <Topic>

<What this governs and why it exists. Rewrite in place; never append
corrections.>
```

### spec

```markdown
---
kind: spec
status: open
---

# <Topic> — spec

<Context: what problem, why now, which decision authorised it.>

## Requirements

## Acceptance criteria

## Out of scope
```

### plan

```markdown
---
kind: plan
status: open
---

# <Effort> — plan

<Which spec/decision this executes. Engagement name for spawned ephemera.>

## Decisions binding this plan

## Units

### <U1 — name>

- <scope>
- Acceptance: <verifiable criterion>

## Sequencing

## Out of scope
```

### ledger

```markdown
---
kind: ledger
status: open
---

# <Engagement> — run ledger

<Plan it executes. Lane map if parallel.>

## Board

| Unit | State | PR | Notes |
| ---- | ----- | -- | ----- |

## Verification (verbatim)
```

### handover

```markdown
---
kind: handover
status: open
unit: <ID>
---

# <Unit> — worker handover

## Execution posture

## Scope

## Objective

## Acceptance

## Out of scope

## Read-first (in order)

## Gates that still bind

## Verification bar (verbatim in the report)

## Report-back contract
```

### report

```markdown
---
kind: report
status: open
unit: <ID>
---

# <Unit> — worker report

## Done

## Decisions needed

## Deferred

## BLOCKED
```

### probe

```markdown
---
kind: probe
status: open
---

# <Question> — probe

## Verdict

## Findings

## What needs a human decision
```

`state` and `decision` have no template: `STATE.md` is a maintained singleton,
and decisions are numbered entries in the `DECISIONS.md` register, whose header
carries the entry discipline.
