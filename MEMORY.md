# MEMORY.md — Hyperion-XI
Durable facts about this repo. Dated; newest first. Updated at session wrap-up.

## 2026-10-05
- **Renamed Hyperion-OXiLm → Hyperion-XI** (GitHub repo + local clone `~/repos/Hyperion-XI`; the old URL redirects).
- AGENTS.md is the only instruction file (CLAUDE.md content moved there).
- `.github/workflows/notify-vault.yml` pings the vault when AGENTS.md/RESUME.md change on main.

## 2026-10-01
- Renamed from `horizons-ui` (the UI shell, formerly Horizons UI). Public; auto-merge and auto-delete on; CI check `check` required on main.
- App code imported as a snapshot of `main@c2d9b6a` (2026-09-13). The full 194-commit history lives in `raw-databank/horizons-ui-full-history.bundle`.
- `build-apk.yml` is the stripped workflow (no NDK/ORT/ort_engine), per the 2026-09-30 decision. The old "Novus Agenti / Omni Claw" CLAUDE.md is kept at `docs/legacy/`.
- CI needs `permissions: pull-requests: read` for gitleaks-action (it returned 403 without it).
- Plan: split into 3 apps (Hyperion UI shell / AEthX-AEsc / CloviX-AEyre); strip daemon/, ort_engine, Clifford. The NPU route for apps is the GenieX Android SDK (`com.qualcomm.qti:geniex-android`).

## 2026-10-23
- PR #4 (merged 2026-10-21) imported the Sep-13 snapshot; PR #3 (gitleaks CI) is merged. No open PRs.
- Branch protection on main requires status check `check` (read via `gh api .../protection`).
- AGENTS.md H1 title still says "Hyperion-OXiLm" after the 2026-10-05 rename to Hyperion-XI.
- On 2026-10-23 the operator dropped OmniRoute, mem0, Terrestrial Brain, OpenWiki and Qwen3-VL from the stack; AGENTS.md "Memory" section still describes OmniRoute routing (flagged in PENDING.md, not edited).
