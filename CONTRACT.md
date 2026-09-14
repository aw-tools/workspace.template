# Workspace template contract

`aw init --template <url>` materialises a workspace from a Git repository that
conforms to this contract.

## Enforced minimum

After cloning, `aw init` checks for:

- a deny-all `.gitignore`, which ignores everything before selectively allowing
  the workspace layer;
- a `workspace.toml` manifest.

Initialisation fails loudly when either requirement is missing. A
contract-version marker is reserved for a later revision; its representation and
enforcement are deliberately deferred.

## Expected instance layer

The rest of the starting layer is guidance rather than an enforced file list:

- `.claude/settings.json`, disabling background-session worktree isolation so a
  dispatched worker may use the dedicated file tools in the checkout it was
  given. Left on, the guard refuses `Write` and `Edit` while leaving shell
  writes untouched, which costs a worker its safest editing tools without
  containing anything;
- `AGENTS.md`, with `CLAUDE.md` as a symlink to it;
- `README.md`;
- `bin/bootstrap`;
- `context/README.md`;
- `garden.yaml`, containing only the include for `.aw/trees.yaml`;
- `tmp/.keep`.

This template also ships the artefact-governance layer, so a materialised
workspace is governed from birth:

- `bin/lint-artefacts` and its pre-commit step, enforcing the model at commit
  time;
- `context/artefacts.toml` (registry, empty engagement register) and
  `context/artefacts.md` (prose companion);
- register skeletons `context/STATE.md`, `DECISIONS.md`, `FINDINGS.md`, and
  `NOTES.md` — headers and entry discipline only, no content.

Custom templates may add other workspace-layer files while retaining the
enforced minimum.

## Excluding template-only paths

Use [`.seedignore`](.seedignore) for files that belong to the template
repository but must not appear in materialised workspaces. It uses
gitignore-style patterns, one per line. During materialisation, `aw init`
removes matching paths and then removes `.seedignore` itself.

This template's live `.seedignore` is the reference example. It excludes its
repository CI, `CHANGELOG.md`, `CONTRACT.md`, and `.seedignore` while allowing
formatting configuration and hooks to propagate.

## Recorded provenance

`aw init` writes provenance into the materialised instance's `workspace.toml`;
the template does not ship this block:

```toml
[template]
url = "<template URL>"
ref = "<ref as typed>"
sha = "<resolved commit SHA>"
applied = <entry id>  # not written by aw init today; see below
```

`applied` is the newest changelog entry the instance has applied. It is a plain
TOML integer, never a quoted string: entry ids carry no zero-padding precisely
so that this is an integer, and so that `"0003"` and `"3"` cannot become two
spellings of one value.

`aw init` does not write `applied` today. An instance maintains it by hand as it
replays, and an instance whose block carries no `applied` key falls back to the
baseline derived from `sha`, under
[Replaying governed changes](#replaying-governed-changes) below.

The block reserves a future `contract` field. Its representation is not yet
defined.

## Template references

`--template <url>@<ref>` accepts a tag, commit SHA, or branch. The `ref` value
is recorded as typed, and `sha` records the commit it resolved to.

A bare URL resolves to the remote's default-branch `HEAD`, with the resolved
commit recorded in `sha`. The template baked into `aw` is pinned to a tag rather
than following a moving branch.

## Replaying governed changes

This template records every governed change to it as an entry in its
`CHANGELOG.md`. An instance brings its own copy of the governed layer up to date
by replaying the entries it has not applied yet, oldest first. Neither the
changelog nor this document is materialised, so an instance reads both from the
template repository rather than from its own tree.

1. **Clone the template** at the `url` your `workspace.toml`'s `[template]`
   block records, into untracked scratch — `tmp/` in your workspace is the
   intended home — or fetch into that clone if one survives from a previous
   replay. You need it before anything else, because the starting point is read
   out of the template's own history: clone with full history, not a shallow
   clone. If the whole `[template]` block is absent, the workspace was built by
   hand rather than by `aw init` and there is no provenance to fetch from: say
   so, and settle the starting point with the human before replaying anything.
   Delete the clone once the replay finishes.
2. **Find your starting point.** Read `applied` from the `[template]` block. If
   the key is present, its value is your starting point. If it is absent, derive
   the baseline: the newest entry id present in the template's `CHANGELOG.md` at
   the commit your `sha` records. Everything at or below that id is already in
   the files you were created with. If that commit carries no entries, or no
   `CHANGELOG.md` at all, your baseline is `0` and every entry replays.
3. **Read `CHANGELOG.md` at the template's default branch, and select the
   entries above your starting point**, in ascending id order. Do not skip one,
   and do not reorder.
4. **For each entry, in order:**
   - run its `Already applied when` test; if it passes, the entry is already in
     your copy, so make no changes and go straight to setting `applied` below;
   - otherwise carry out its `Migration` steps. `Kind: replace` means copy the
     template's version of the file, then strip the `(decision NNN)` citations,
     which address the template's own register and mean nothing in yours:
     substitute your own register's numbers, or omit the citation. `Kind: adapt`
     means author the equivalent change yourself, to the state the entry says
     must hold afterwards;
   - run its `Verify` check and confirm it passes;
   - set `applied` to that entry's id in your `workspace.toml`, and commit
     before starting the next entry; if you skipped the entry, that marker bump
     is the whole commit.
5. **Stop and ask the human** whenever an entry's `If your copy has diverged`
   note applies to your copy, and whenever a `Verify` check fails. Do not
   improvise past either.

Replay is one entry at a time and one commit at a time. A batch commit across
several entries destroys the resumability the whole design buys: an interrupted
replay must resume where it stopped, rather than start again from the beginning.
