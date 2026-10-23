# RESUME.md: Hyperion-XI

Rewritten 2026-10-23. Older entries are in git history.

## Current state
- Repo is `c10vis-poem/Hyperion-XI` (formerly `horizons-ui`, then `Hyperion-OXiLm`; renamed 2026-10-05 per MEMORY.md).
- Main holds the Sep-13 app code snapshot (`main@c2d9b6a`), the stripped `build-apk.yml` (no NDK/ORT/ort_engine), `ci.yml` (gitleaks, required check `check`), and `notify-vault.yml`.
- No open PRs.

## Changed this week (2026-10-18..2026-10-23)
- #4 `feat: import Sep-13 app code snapshot into Hyperion-OXiLm` (merged 2026-10-21): app code imported, old CLAUDE.md kept at `docs/legacy/CLAUDE-novus-agenti-2026-09-13.md`, RESUME rewritten, AGENTS.md title changed.
- #3 (`ci: standard CI with gitleaks secret scan`) is MERGED (verified via `gh pr view 3`), so CI is in place.

## Open items, in order
1. Pick the Æsc package name (permanent).
2. Split the Sep-13 code into 3 modules: Hyperion UI shell / AEthX-AEsc / CloviX-AEyre.
3. Strip `daemon/`, `ort_engine`, Clifford, DaemonLauncher, NpuClient.
4. Reconcile `NovAExopia/horizons-ui/` with this standalone repo (see PENDING.md).
5. Operator decision on stack routing: AGENTS.md still routes memory through OmniRoute/mem0/terrestrial-brain, which the operator dropped 2026-10-23 (flagged in PENDING.md).

## Next steps
Start with item 1, then the module split. Follow AGENTS.md git workflow: PR, secret scan, green CI, auto-merge, leave branch in place.

## Sources
Read: AGENTS.md, RESUME.md, MEMORY.md, PENDING.md, `git show HEAD` for #4, `pending/POINTER.md`, PR #3 state, PR #4 body, branch protection (required check `check`).
Not read: the imported app code, `.github/workflows/*` contents, `docs/legacy/`, `raw-databank` (not cloned here).
