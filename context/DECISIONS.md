---
kind: decision
status: active
---

# Decisions register

Numbered, binding, append-only. Each entry is a top-level numbered list item;
entry numbering is stable across sessions and the corpus references entries as
"decision N" — never renumber. Where an entry overrides a plan, mark it as such.
Where a later entry M changes an earlier entry N, wholly or in part, M carries
`**Supersedes:** N.` and N gains `**Superseded by:** M.`; marks may be added to
any entry, and a recorded entry's ruling and reasons never change; only its form
may, in a declared format change. `context/artefacts.md` carries the form, that
change, and when a ruling enters. This file itself stays `active`.

**Entry discipline:** a new entry records the ruling, the load-bearing
rationale, and one line per alternative set aside, which stays open — target
**30 lines or fewer**. Measurements, tables, verbatim output, and correction
records live in the engagement's ledger or probe and are referenced, not
embedded.
