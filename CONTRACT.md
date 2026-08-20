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

- `AGENTS.md`, with `CLAUDE.md` as a symlink to it;
- `README.md`;
- `bin/bootstrap`;
- `context/README.md`;
- `garden.yaml`, containing only the include for `.aw/trees.yaml`;
- `tmp/.keep`;
- `.claude/settings.json`, disabling background-session worktree isolation so a
  dispatched worker may use the dedicated file tools in the checkout it was
  given. Left on, the guard refuses `Write` and `Edit` while leaving shell
  writes untouched, which costs a worker its safest editing tools without
  containing anything.

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
repository CI, `CONTRACT.md`, and `.seedignore` while allowing formatting
configuration and hooks to propagate.

## Recorded provenance

`aw init` writes provenance into the materialised instance's `workspace.toml`;
the template does not ship this block:

```toml
[template]
url = "<template URL>"
ref = "<ref as typed>"
sha = "<resolved commit SHA>"
```

The block reserves a future `contract` field. Its representation is not yet
defined.

## Template references

`--template <url>@<ref>` accepts a tag, commit SHA, or branch. The `ref` value
is recorded as typed, and `sha` records the commit it resolved to.

A bare URL resolves to the remote's default-branch `HEAD`, with the resolved
commit recorded in `sha`. The template baked into `aw` is pinned to a tag rather
than following a moving branch.
