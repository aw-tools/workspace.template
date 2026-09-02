---
kind: state
status: live
---

# State

The single artefact that resumes a cold session.

## Keeping this file

Read it whole; write it plain. This file is consumed in its entirety, every time
— keep it short enough for that, and write every field in plain language a
reader can follow without knowing an engagement's own vocabulary. The jargon
lives in the plan or ledger an item points at, never here.

An update replaces an item's fields with the present state. If your edit only
adds text, you are writing a changelog — stop and rewrite. Decisions belong in
`DECISIONS.md`, durable facts in `FINDINGS.md`, friction in `NOTES.md`, run
detail and verbatim verification in the owning engagement's ledger: this file
points, never quotes. Anything not captured here (or in the registers it points
to) is lost when the session restarts.

Open items keep the field shape — Now / Next / Blocked / Ledger: Now, Next and
Blocked carry at most 50 tokens each, Ledger one line (template in
`context/artefacts.md`), enforced by `bin/lint-artefacts`. Blocked names the
external gate or dependency the item waits on, never a restatement of Next.
Order items by liveness — in flight first, ready next, parked or gated after,
no-topic subjects last. The caps bound each item, not how many there are, so
total length is held by discipline alone. Closing an engagement deletes its item
in the closing commit, residue routed first. A subject with no topic may hold an
item with `Ledger: —`.

## Where things stand

<One line per active workstream: where it is, what unblocks it next.>

## Read-first map

<The handful of artefacts a fresh session should open, in order.>

## Open items

<One entry per open engagement, in the four-field shape:>

### <engagement-slug>

- Now: <where it stands, present tense>
- Next: <the immediate next action>
- Blocked: <gate or dependency, or —>
- Ledger: <the engagement's ledger, or its plan or spec while no ledger exists,
  or — for a subject with no topic>
