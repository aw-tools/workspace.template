# Workspace instructions

An agentic workspace: the root git repository tracks only the workspace layer.
Inner repositories are separate checkouts, invisible to it.

## Orientation

Read `context/STATE.md` first — it is the single artefact that resumes a cold
session, then read what it points to.

This checkout may be one of several, and other sessions push to the same
remotes. A stale read is indistinguishable from a current one, so refresh before
relying on what you read:

- **At session start** — fetch the workspace and every member repository, then
  fast-forward the workspace before reading `STATE.md`.
- **Before editing a register** (`STATE.md`, `DECISIONS.md`, `FINDINGS.md`, a
  ledger) — fetch the workspace, fast-forward, and re-read the section you are
  about to change.
- **Before asserting a member repository's state** — fetch that repository
  first. Never quote a branch position, a merge or a CI result from memory.
- **On resuming after a gap, or when the human has worked elsewhere** — refresh
  in full.

Never fast-forward over divergence: if the workspace and its remote have both
moved, stop and say so. If a fetch fails, say so and treat everything that
follows as possibly stale.

If this workspace opts into a skills corpus carrying `aw-orienting`, that skill
holds the full procedure.

## Layout

| Path             | Purpose                                                |
| ---------------- | ------------------------------------------------------ |
| `workspace.toml` | Manifest — inner repositories, identity, skill opt-ins |
| `garden.yaml`    | Hand-editable garden commands; includes `.aw/`         |
| `context/`       | Working context for this workspace (tracked)           |
| `.skills/`       | Workspace-local skills; highest link precedence        |
| `bin/bootstrap`  | Provisioning entry point (delegates to `aw`)           |
| `.aw/`           | Generated from the manifest; untracked, never edited   |
| `tmp/`           | Scratch; untracked                                     |
| `<repo>/`        | Inner repositories; each its own git repo, untracked   |

## The durability layer

This repo holds the _record_ of the work, not the work: the plan, `STATE.md`,
decisions, working context, friction notes — everything about the inner repos
that must not live inside them. Its job is to keep that meta-state durable and
portable, so a clone plus `aw bootstrap` reassembles the constellation and the
thread of ongoing work.

- **Commit state the moment it must be durable — no questions asked.** Commit is
  unconditional (the artefact lifecycle needs git as its archive); if an origin
  is configured, push in the same breath. A fresh workspace has none, so it
  commits only — adding an origin is the opt-in to auto-push. Commits are
  signed, linear, direct to the default branch.
- The authorisation covers _this_ layer only, never the inner member repos, and
  never secrets (`context/README.md`'s do-not-store list is absolute).

## STATE.md is the handover

`STATE.md` is the single artefact that resumes a cold session. Maintain it as
part of the work, not a report written afterwards.

- **Update it as you go and before reporting back.** Anything not in it is lost
  on restart.
- **Keep it resume-state only:** decisions → `context/DECISIONS.md` (numbered,
  append-only), durable facts → `context/FINDINGS.md`, run detail → the
  engagement's ledger. If the owning topic has no ledger, **open one** —
  `STATE.md` is not a fallback home for run detail.
- Record verification verbatim (command + output); rewrite stale sections rather
  than layering corrections.

## Working agreements

- When a discovery conflicts with a settled decision or the plan, stop and raise
  it as a decision gate with evidence — do not decide unilaterally.
- Explanations on request are plain, low on jargon, no metaphors.
- Commit inner-repo work inside the repo, never from the workspace repo.
- Secrets never enter `context/` or any tracked file; throwaway files go in
  `tmp/`.

## Artefact invariants

The context corpus is governed by the artefact model: `context/artefacts.toml`
is the registry (authority), `context/artefacts.md` holds kind purposes and
templates, `bin/lint-artefacts` enforces both at commit time.

- Every context artefact declares `kind` and `status` in frontmatter; legal
  values come from the registry. Class is implied by kind, never declared.
- The root of `context/` holds standing artefacts only, uppercase-named
  (`STATE.md`, `DECISIONS.md`, `FINDINGS.md`, `NOTES.md`). Everything episodic
  and ephemeral lives in `context/engagements/<topic>/` as `<kind>-<slug>.md` —
  no subdirectories **for model entities**, no renames on plurality or closure.
  Two declared non-entity directories are permitted: `attachments/` (tracked
  derivation input, no `kind`/`status`, graduates like an ephemeral) and `tmp/`
  (untracked scratch). `artefacts.md` carries the handling rules.
- **The model governs `context/` only.** `.skills/`, `.claude/` and `.agents/`
  are agent surfaces, out of scope by directory: their markdown carries
  frontmatter of a foreign schema (`name`, not `kind`), so the lint skips
  everything beneath them and a workspace-local skill needs no artefact
  frontmatter.
- Ephemeral artefacts (handovers, reports, probes) nest per engagement;
  **graduation is deletion** — record the residue, delete the file, git is the
  archive.
- **A record belongs to the topic that owns its subject, not the one where it
  surfaced.** The standing registers are exempt: `DECISIONS.md` and
  `FINDINGS.md` are corpus-scoped and the decision register is append-only, so
  an entry is _attributed_ to a topic, never relocated or renumbered into one. A
  subject with no topic stays an open item in `STATE.md`.
- **Closing an engagement closes its episodic artefacts** — when an engagement's
  registry status leaves `open`, every plan, spec, and ledger in its directory
  moves to `closed` in the same commit; the lint blocks a closed engagement that
  still holds an `open` episodic artefact.

## Formatting

The workspace layer is formatted by dprint to `.editorconfig` + `dprint.json`;
the inverted `.gitignore` scopes it to the layer, so inner repos are untouched.
A pre-commit hook enforces it — activate once per checkout with
`bin/install-hooks`.

The hook is **mandatory and must never be bypassed**: no `--no-verify`, no
unformatted commits. If it blocks, run `dprint fmt` and re-commit.

## Gates

- **Workspace-lifecycle gates** (uniform): never make the first commit of a
  not-yet-existing member repo, and never create a remote, without the owner's
  sign-off.
- **Per-repo delivery gates** (variable): each member repo declares its own
  delivery model — a worker's autonomy within it (commit, push a branch, draft
  PR, ready-for-review, merge) — in that repo's own instruction files. The
  workspace recognises, never controls: honour the repo's _current_ model, cache
  no copy. Absent one, default conservative — commit, push a feature branch,
  open a draft PR; stop at ready-for-review and merge.
- **Landing cleans up the checkout**: once a member repository's PR lands,
  restore that checkout without being asked — drop the parallel worktree if the
  work ran in one, or if it ran in the main worktree, check out the default
  branch, fast-forward it and delete the merged local branch. A checkout left on
  a merged branch with a gone upstream reads to the next session as live work.
  Hygiene, not a gate: it follows the merge the owner performed and needs no
  further sign-off.

Orchestrated multi-worker engagements are opt-in: adopt a skills repository
carrying an orchestration kernel skill (the manifest's `skills` opt-in) to
enable them; a solo workspace never needs it.

## Project notes

<!-- Fill in: what this workspace is for, scope, people, guardrails. -->
