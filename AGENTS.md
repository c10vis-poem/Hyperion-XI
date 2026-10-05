# Horizons UI — master visual presentation shell

Canon name: **Horizons UI**. Rebuilt from scratch 2026-09-15 — the prior
38-PR history (broken NPU-runtime architecture, half-finished GenieX
migration) is archived, not carried forward. Full history + last snapshot:
`raw-databank/horizons-ui-full-history.bundle` and `raw-databank/horizons-ui-full/`.
Salvage rationale and post-mortem: `raw-databank/README-SALVAGE.md`.

## What this is

Part of the 3-APK architecture alongside Æsc (terminal) and Æyre (voice/vision).
See `NovAExorpus/NAMING-CANON.md`.

## Operator Rule 1 — no action without an explicit prompt

A skipped or unanswered question is NOT consent. No action — reading,
searching, or anything else — without an explicit prompt or permitted
request. State-changing or not, it doesn't matter.

## Operator Rule 2 — read this file and RESUME.md first

Before doing anything else in this repo, read this AGENTS.md and RESUME.md.
Standing convention across the operator's repos for months — step one,
every session, no exceptions.

## Git workflow

PR required. No direct pushes to main. Before every push, scan the diff for
secrets/keys and refuse to push if any are found. On green CI, auto-merge
into `main` immediately. Leave the branch in place after merge; do not delete it.

The point of this workflow is that everything reaches `main` — a branch
that never gets a PR opened, or a PR that never gets merged, is a failure
of this rule, not a valid alternative to it. Don't let work sit stranded.

## Memory — runtime infrastructure (references aesop-xi)

The whole stack's runtime memory infrastructure (mem0, terrestrial-brain,
OmniRoute, reasoning-bank, continual-harness) lives canonically in
**aesop-xi** — see `~/repos/aesop-xi/AGENTS.md` §Runtime memory stack.
Not duplicated here.

Every agent in this repo — regardless of harness (Claude Code, Codex, dsh,
Prime Agent, Hermes) — reaches memory via one MCP endpoint:
`http://localhost:20128/mcp` (OmniRoute). Never call mem0 or
terrestrial-brain directly; that bypasses OmniRoute's async memory tap
and the observation layer.

**Bootstrap this repo**: `bash tools/bootstrap.sh` — thin wrapper that
calls aesop-xi's canonical bootstrap first.
