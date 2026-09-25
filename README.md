# Workspace template

Seed for a multi-repo agentic workspace.

A workspace is a git repository that tracks only its own thin layer — the
manifest, agent instructions, and working context. The inner repositories
checked out inside it stay entirely separate: the workspace `.gitignore` denies
everything by default, so git never descends into them and no gitlink is ever
created. Clone the workspace anywhere, run one command, and the whole
constellation reassembles.

## Provisioning ritual

```sh
aw init <codename>.workspace   # instantiate this template, git init (no commit)
$EDITOR workspace.toml         # declare inner repos and skill opt-ins
bin/bootstrap                  # clone repos, apply config, link skills, report
```

The first commit is deliberately yours to make: review `git status --porcelain`
and `git ls-files` before it, because everything tracked here reaches the
workspace remote.

## Day to day

| Command           | Effect                                                               |
| ----------------- | -------------------------------------------------------------------- |
| `aw doctor`       | Environment and remote-reachability checks with remedies             |
| `aw status`       | Per-repo state, config drift, skill-link health                      |
| `aw sync`         | Fetch and report only — never merges, pulls, or checks out           |
| `aw fast-forward` | Fetch, then fast-forward each clean repository on its default branch |

## See also

`aw` lives in the [aw-cli](https://github.com/aw-tools/aw-cli) repository. The
contract every workspace conforms to, and the guide for people, live in
[agentic-workspace](https://github.com/aw-tools/agentic-workspace).

## Contributing

Not accepting external contributions yet. See
[CONTRIBUTING.md](CONTRIBUTING.md).

## Licence

Copyright 2026 Front Seed Labs Ltd.

Licensed under either [MIT](LICENSE-MIT) or [Apache-2.0](LICENSE-APACHE), at
your option. A workspace you create from this template is yours; the licence
covers the template itself.
