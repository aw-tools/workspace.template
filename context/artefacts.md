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

## Routing — the subject owns the record

A record belongs to the topic that owns its **subject**, not the one where it
surfaced. A decision taken in a meeting, or a finding raised in a review, is
filed against the topic it acts on; the topic where it came up keeps one line
stating what was ruled plus the pointer, and nothing more.

Where a record is raised is not a property of the record. A cross-cutting source
— a one-to-one, a workshop, a review — therefore pulls records away from the
topics that own them unless it is routed **item by item**, keeping only what is
genuinely its own. Meetings are cross-cutting by nature, so this is systematic
rather than incidental.

Two boundaries:

- **The standing registers are exempt.** `DECISIONS.md` and `FINDINGS.md` are
  corpus-scoped by construction and the decision register is append-only, so an
  entry is _attributed_ to a topic, never relocated into one.
- **A subject with no topic stays an open item in `STATE.md`.** Do not create a
  topic to receive a single record.

If the owning topic has no ledger, open one. `STATE.md` is not a fallback home
for run detail.

## Engagement lifecycle

An engagement's directory location follows its registry status, never the other
way round. An **open** engagement lives at `context/engagements/<name>/`; once
its status turns **closed**, the directory moves to `context/archive/<name>/` in
the same commit that closes its episodic artefacts. `bin/lint-artefacts`
enforces both directions: an open engagement found under `context/archive/`, or
a closed one still under `context/engagements/`, is a finding. Archiving is not
mandatory — a closed engagement whose ephemera all graduated by deletion is free
to have no directory at all, exactly as before.

## Non-entity directories

Two directories may sit inside a topic beside its artefacts, both declared in
`artefacts.toml` under `[subdirectory.*]`:

- **`attachments/`** — tracked. Source material an artefact is **derived from**:
  transcripts, whiteboard exports, meeting notes, third-party documents. It
  carries no `kind` and no `status` and is not lintable, because a `.json`
  export has nowhere to put frontmatter.
- **`tmp/`** — untracked working scratch.

The no-subdirectory rule binds model **entities**, whose address is their
filename; these are not entities, so they are outside it. Nesting inside them is
unconstrained.

Handling attachments:

- **Prefer citing over copying.** If the original has a durable home under your
  control, cite it and commit nothing. Copy only when the source belongs to
  someone else and can be edited or revoked.
- **Its life is the derived artefact's need for it, not the topic's.** Once the
  derived artefact is self-sufficient, an attachment **graduates like an
  ephemeral** — delete the file, git is the archive.
- **Never cite another topic's `attachments/`.** Copy the excerpt into your own
  artefact, or cite the external source.

## Agent surfaces are out of scope

Three directories in the workspace layer belong to agent tooling rather than to
this model, and the lint skips everything beneath them:

- **`.skills/`** — workspace-local skills.
- **`.claude/`** and **`.agents/`** — per-tool agent configuration: subagent
  definitions, commands, output styles.

Their markdown carries frontmatter of a **foreign schema** — a skill declares
`name` and `description`, never `kind` and `status` — so there is nothing here
for the model to check and no authority for it to check against. The exclusion
is by directory, not by filename: the whole tree is the tooling's, and a
`SKILL.md` basename rule would also excuse a stray one sitting inside
`context/`.

Skills that reach a workspace by symlink from a member repository are invisible
to the lint regardless, because git tracks the link and not the target; this
rule is what admits workspace-local ones kept in the workspace's own history.

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

### state — open-item entry

`STATE.md` is a maintained singleton, but each entry under its `## Open items`
section has a fixed shape, enforced by `bin/lint-artefacts`:

```markdown
### <engagement-slug>

- Now: <where it stands, present tense>
- Next: <the immediate next action>
- Blocked: <gate or dependency, or —>
- Ledger: <engagements/<slug>/ledger-run.md, or — for a subject with no topic>
```

Now, Next and Blocked carry at most 50 tokens each (a token is one
whitespace-delimited field); Ledger is one line. The heading slug must name an
open engagement in the registry unless the entry carries `Ledger: —` (a subject
with no topic). Write fields in plain language — an engagement's own vocabulary
stays in the plan or ledger the entry points at. No fenced code blocks anywhere
in `STATE.md`: verbatim output belongs to the owning ledger.

`decision` has no template: decisions are numbered entries in the `DECISIONS.md`
register, whose header carries the entry discipline.
