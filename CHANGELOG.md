# Changelog

This file lists one entry per governed change to this template. An instance
replays the entries it has not applied yet, in order, to bring its own copy of
the governed layer up to date. It is not a release-notes file, and it does not
follow the Keep a Changelog convention: that format answers "should I upgrade?";
this one answers "how do I apply this?"

For the replay procedure, see the `Replaying governed changes` section of
[`CONTRACT.md`](CONTRACT.md) in this repository.

## What counts as a governed change

A governed change is any commit to this template that touches a tracked path
surviving materialisation. That is everything tracked, minus the paths listed in
[`.seedignore`](.seedignore), minus the seed-only skeletons an instance owns
from the moment it is created and never syncs again: `workspace.toml`,
`README.md`, `tmp/.keep`, and the four register skeletons `context/STATE.md`,
`context/DECISIONS.md`, `context/FINDINGS.md`, and `context/NOTES.md`.

Governed does not mean identical. An instance may edit its own copy of a
governed file. Governance means a change arrives with instructions for applying
it, not that the two copies converge.

## Ordering

Entries are listed newest first, but applied oldest first, in ascending id
order, with no skipping.

## Baseline

No history is retro-filled. An instance's baseline is derived from its recorded
provenance, not assumed to be zero. The `Recorded provenance` section of
[`CONTRACT.md`](CONTRACT.md) owns the fields `aw init` records.

An instance whose manifest carries no `applied` marker takes as its baseline the
newest entry id present in the template at its recorded `sha` — everything at or
below that id is already in the files it was created with. An instance replays
only entries above that baseline.

The `Already applied when` skip test remains the safety net for a partially
applied or hand-edited instance, not the mechanism that makes the baseline work.

## Versions and entry ids

A version tag, such as `v0.1.0`, is what a human pins with
`aw init --template <url>@<ref>`, and what `aw`'s built-in default names. One
tag may carry several entries. An entry id, such as `1`, is what an instance
replays from, because an instance can sit part-way through a release and must
still resume correctly from its own position.

## Entry format

Each entry is a level-three section beneath `## Entries`, headed
`N — <imperative summary>`, where `N` is a plain integer id, monotonic, never
reused and never renumbered. An entry carries the following fields, in this
order:

- `Date` — when the change landed here.
- `Template ref` — the commit or pull request that landed it, so a reader can
  see the diff. Never a substitute for following the steps.
- `Paths` — the governed paths touched.
- `Kind` — `replace` (copy this template's version of the file, then strip the
  decision citations, which address this template's own register and mean
  nothing in an instance's) or `adapt` (the instance authors an equivalent
  change itself; the entry says what must be true afterwards).
- `Change` — one short paragraph, in plain words, saying what changed and why.
- `Migration` — ordered imperative steps, including any backfill of content the
  instance already holds. This is the field the whole file exists for: an
  instance several releases late must be able to apply the entry without diffing
  anything.
- `Already applied when` — the skip test: an observable condition or a command,
  so replay is idempotent and a half-applied instance can tell where it stopped.
- `Verify` — a check that fails before the entry is applied and passes after.
  Name `bin/lint-artefacts` only where the lint actually enforces this change;
  otherwise state what to look for.
- `If your copy has diverged` — present only for files instances commonly edit;
  states what to preserve and what must change regardless.

An entry says how to apply a change, never how to undo one; reversal is out of
scope.

## Entries`, headed

`N — <imperative summary>`, where `N` is a plain integer id, monotonic, never
reused and never renumbered. An entry carries the following fields, in this
order:

- `Date` — when the change landed here.
- `Template ref` — the commit or pull request that landed it, so a reader can
  see the diff. Never a substitute for following the steps.
- `Paths` — the governed paths touched.
- `Kind` — `replace` (copy this template's version of the file, then strip the
  decision citations, which address this template's own register and mean
  nothing in an instance's) or `adapt` (the instance authors an equivalent
  change itself; the entry says what must be true afterwards).
- `Change` — one short paragraph, in plain words, saying what changed and why.
- `Migration` — ordered imperative steps, including any backfill of content the
  instance already holds. This is the field the whole file exists for: an
  instance several releases late must be able to apply the entry without diffing
  anything.
- `Already applied when` — the skip test: an observable condition or a command,
  so replay is idempotent and a half-applied instance can tell where it stopped.
- `Verify` — a check that fails before the entry is applied and passes after.
  Name `bin/lint-artefacts` only where the lint actually enforces this change;
  otherwise state what to look for.
- `If your copy has diverged` — present only for files instances commonly edit;
  states what to preserve and what must change regardless.

An entry says how to apply a change, never how to undo one; reversal is out of
scope.

## Entry length

Every field is capped, and `bin/lint-changelog` enforces the caps. A word is one
whitespace-delimited field; the `- **Label:**` itself does not count.

| Field                       | Cap                 |
| --------------------------- | ------------------- |
| `Change`                    | 80 words            |
| `Migration`                 | 60 words _per step_ |
| `Already applied when`      | 60 words            |
| `Verify`                    | 60 words            |
| `If your copy has diverged` | 80 words            |

`Migration` is capped per step and not in total, so a complex change buys more
steps rather than fatter ones: the pressure is toward structure, which an
instance replaying late can follow, and away from prose it has to interpret. A
step is one numbered item, counted whole, and carries no nested list.

The caps exist because the first entry sets the house style for every entry
after it, and prose grows unless something counts it. A change too complex for
an 80-word `Change` is a change that should not be one entry. Rationale belongs
in the pull request and in `context/artefacts.md`, which an instance has; an
entry answers how to apply the change, and nothing else.

## Entries

### 1 — Require `remits` on every engagement register entry

- **Date:** 2026-09-14
- **Template ref:** `feat/engagement-remits`
- **Paths:** `context/artefacts.toml`, `bin/lint-artefacts`,
  `context/artefacts.md`, `AGENTS.md`
- **Kind:** `replace` for `bin/lint-artefacts`; `adapt` for
  `context/artefacts.toml`, `context/artefacts.md` and `AGENTS.md`
- **Change:** Engagements now declare `remits`, a multi-valued tag naming the
  standing areas of responsibility their work counts against. Spin-offs
  accumulate and nothing records that they belong to the same area, so
  collecting past work on one is a memory exercise. The key is required on every
  entry, open or closed: an explicit `[]` turns an omission into a decision.
  `bin/lint-artefacts --remits` lists the register keyed by remit.
- **Migration:**
  1. Copy this template's `bin/lint-artefacts` over yours and strip the
     `(decision NNN)` citations, substituting your own register's numbers or
     omitting them. If your copy has diverged, see the note below instead.
  2. Add the `remits = []` line to the engagement-register comment block in your
     `context/artefacts.toml`, matching this template's.
  3. Choose your vocabulary before touching a single entry, and record it in
     your decisions register: nothing in the model stores it and the lint cannot
     check membership against it. This template's `context/artefacts.md` carries
     the guidance on picking names; two to four is the expected size.
  4. Backfill every entry in your register, open and closed alike, in one
     commit. Read each engagement's ledger or plan before tagging it rather than
     inferring from the slug; an engagement genuinely under no standing area
     takes `remits = []`, which is a decision, not a skip. The array goes on one
     line, the same constraint `depends-on` already carries.
  5. Add the `Remits — what an engagement counts against` section to your
     `context/artefacts.md`, after `Routing — the subject owns the record`. The
     definition sentence and the thread-split rule are quoted verbatim in this
     template's copy; take them as they stand.
  6. Add the remits bullet to your workspace instructions, after the bullet on
     closing an engagement's episodic artefacts.
- **Already applied when:** `grep -q '^remits = ' context/artefacts.toml` finds
  a line and `bin/lint-artefacts --remits` exits 0 with a listing rather than a
  usage error. On a register with no engagements the first test fails and the
  second is the whole check.
- **Verify:** `bin/lint-artefacts --all` exits 0, and
  `bin/lint-artefacts --remits` lists every engagement in your register at least
  once — engagements with an empty list appear under the literal remit `-`, so
  the line count equals the entry count plus one per extra remit. Removing any
  one entry's `remits` key makes `--all` exit non-zero naming that entry.
- **If your copy has diverged:** instances commonly edit `bin/lint-artefacts`.
  Preserve your own checks and take four things from this template's version
  regardless: the `ER` line in the registry-flattening awk pass, the
  presence-and-shape check that follows the `depends-on` check, the `--remits`
  mode and its entry in the argument dispatch, and the first-appearance notice
  among the trailing stderr warnings. The notice must stay non-blocking — it
  must never touch `FAIL`.
