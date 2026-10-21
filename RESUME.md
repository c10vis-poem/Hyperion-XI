# RESUME.md — Hyperion-OXiLm (formerly horizons-ui)

Rewritten every session.

## 2026-10-01
- Repo renamed `horizons-ui` → `Hyperion-OXiLm` (naming decision 2026-10-01).
- App code imported as a **snapshot** of `main@c2d9b6a` (2026-09-13). The full 194-commit history stays in `raw-databank/horizons-ui-full-history.bundle`.
- `.github/workflows/build-apk.yml` replaced by the stripped workflow (no NDK / ORT / ort_engine), per the 2026-09-30 decision.
- The old app `CLAUDE.md` ("Novus Agenti / Omni Claw", build state 2026-08-08) is kept as `docs/legacy/CLAUDE-novus-agenti-2026-09-13.md`.
- gitleaks: the snapshot is clean.

## Next
1. Pick the Æsc package name (permanent).
2. Split the Sep-13 code into 3 modules: Hyperion UI shell / AEthX-AEsc / CloviX-AEyre.
3. Strip `daemon/`, `ort_engine`, Clifford, DaemonLauncher, NpuClient.
4. The operator makes the repo public → CI becomes required (CI PR #3 is open).
