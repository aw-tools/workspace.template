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
provenance, not assumed to be zero: `aw init` writes a `[template]` block into
the materialised instance's `workspace.toml`, recording the template `url`, the
`ref` as typed, and the resolved commit `sha`.

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

## Entries

No entries exist yet. The first entry accompanies the next governed change to
this template. The first entry replaces this paragraph; each later entry is
added above the previous newest.
