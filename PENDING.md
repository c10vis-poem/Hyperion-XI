# PENDING.md — horizons-ui

Durable cross-session backlog. Not rewritten each session — items persist until resolved or explicitly dropped.

- **Fresh rebuild, nothing built yet.** Prior repo's history archived in
  `raw-databank` (full-history bundle + current-at-wipe-time file snapshot).
  Salvage post-mortem (what NOT to repeat — the half-finished GenieX
  migration, silently-swallowed exceptions) is in `raw-databank/README-SALVAGE.md`.
- **`NovAExopia/horizons-ui/` subfolder still exists**, built from the
  now-superseded Document 05 nested-repo model. Needs reconciling against
  this fresh standalone repo once real work starts here.

## Added 2026-10-23
- **FLAG: AGENTS.md "Memory" section routes through OmniRoute / mem0 / terrestrial-brain** (`http://localhost:20128/mcp`, bootstrap via aesop-xi). The operator dropped these from the stack on 2026-10-23. Not edited; needs an operator decision on replacement text.
- AGENTS.md title reads "Hyperion-OXiLm" but the repo was renamed Hyperion-XI on 2026-10-05; fix the title.
- Item 1 above ("Fresh rebuild, nothing built yet") is partly stale: PR #4 imported the Sep-13 app code snapshot. Module split and daemon/ort_engine strip are still open (see RESUME.md).
