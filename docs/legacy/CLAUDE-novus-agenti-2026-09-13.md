# CLAUDE.md — Novus Agenti / Omni Claw

## RULE 0 — VERIFY AGAINST LIVE STATE BEFORE TRUSTING ANY DOC (mandatory, first, every session, no exceptions)

This file and the vault describe *intent*. `origin/main` and the open PR list
are the only things that describe what is *actually true right now*, and they
drift apart constantly: 19+ sessions have each branched from `main`, built real
work, opened a **draft** PR, and stopped — so `main` never accumulates and this
file's own "State of the Union" is written from whatever the union of branches
looked like on the day it was last edited, not from what's on `main` today. A
recent-looking SOTU date does not mean it is still accurate — it can go stale
within 48 hours. It has.

Before reading anything else as fact, in this order:
1. `git fetch origin && git log --oneline -10 origin/main` — get the real
   current tip and its last few merges.
2. List open PRs (GitHub MCP `list_pull_requests`, `state=open`). This is
   where undocumented reality lives — a PR can sit unmerged for days holding
   real fixes this file never mentions. (It has happened: a PR shipped fixes
   for bugs the operator was still hitting on device, unmerged for two days,
   while this file said nothing about it.)
3. Compare: does the highest PR number this file's SOTU claims landed actually
   match `origin/main`'s merge history? If `main` has moved past the SOTU,
   say so out loud, then re-derive current state from the live repo — grep the
   actual consumer of a feature — before claiming anything works, is broken,
   or is missing.
4. Never tell the operator a feature is "done," "broken," or "unbuilt" on the
   strength of prose alone. Grep the code. A comment is not code, a PR body is
   not the diff, and a doc dated two days ago can already be wrong.

This is not optional context-gathering. It is the one standing rule the
operator has asked every session to follow, every single time, without
exception — treat skipping it as a hard failure of the session, not a shortcut.

> **RESUME PROMPT — COPY THIS BLOCK VERBATIM TO START ANY NEW SESSION**
>
> ```
> FIRST: do RULE 0 above — fetch origin/main, list open PRs, verify the SOTU
> below against live state before trusting a word of it. Say what's stale
> before doing anything else.
>
> Project: Novus Agenti (Omni Claw) — on-device agentic AI assistant.
> App repo: c10vis-poem/Horizons-UI   Vault: c10vis-poem/OBSIDIAN-Master_Wiki
> Protocol: c10vis-poem/aesop         GenieX fork: c10vis-poem/GenieX
>
> ### THIS FILE IS THE SINGLE SOURCE. THE VAULT IS NOT REQUIRED READING.
> The vault repo is `c10vis-poem/nova-corpus` (NOT `OBSIDIAN-Master_Wiki` —
> that name is stale; the repo was renamed/restructured and its OWN root
> CLAUDE.md is dated 2026-07-31 and doesn't even mention its current `canon/`
> layout). Operator directive 2026-08-08: no more "read this doc but not
> that doc" chains across repos. This file must stand on its own.
> **Do not open the vault by default.** If something genuinely isn't
> answerable from this file, say so explicitly and name the exact gap before
> going to look — don't silently go read 337 files "to be safe."
> If you do end up in the vault: `canon/STATE-OF-EXISTENCE.md` (its build-state
> ledger) is ITSELF confirmed stale as of 2026-08-08 — it predates PRs #33/#34
> merging and already contradicts this file's own verified SOTU below on
> multiple rows (cloud connectors, Moonshine STT, greenLight() coverage). Its
> own canon/CLAUDE.md says "read everything, completely, before acting" —
> that instruction is what produced the 337-file sprawl; do not repeat it here.
>
> ### BRANCH REALITY — verified 2026-08-08, trust this over older docs
> `origin/main` == `2c64796`. PRs #33 and #34 (see below) are MERGED, not
> open — if you're about to say either one is "unmerged, read the diff before
> touching Router/voice," fetch first, you're looking at a stale copy of this
> file. It HAS the correct frozen HomeGrid (blob 618cf4b6). Verify with:
>   git fetch origin && git rev-parse origin/main:horizons/src/main/java/com/horizons/ui/HomeGrid.kt
>
> ### THE ACTUAL DISEASE: STILL ACTIVE, NOT HISTORICAL
> Every session branched from main, built something real, opened a draft PR,
> got CI green, and stopped. So main accumulates slowly and each session
> risks rediscovering or rebuilding what already exists elsewhere. This is
> why the docs and the code disagree: the docs describe the UNION of open
> branches, any session sees only main. It is a merge problem, not a docs
> problem, and it is STILL HAPPENING: PR #35 (insets fix + bulk storage
> scanner, targets real device symptoms) sat open, unmerged, mergeable-clean,
> for 2+ days before the operator even knew about it. Check the open PR list
> yourself (RULE 0) — do not trust this paragraph's PR numbers to still be
> the current open set by the time you read this.
>
> Known as of 2026-08-08: SIX open PRs actively edit HomeGrid.kt and would
> OVERWRITE THE FREEZE: #30 #27 #26 #24 #22 #20 ← do not merge without
> operator sign-off. PR #35 (`claude/novus-device-file-loading-ij4k4r`) is
> open, non-draft, mergeable-clean, and does NOT touch HomeGrid — contains
> the insets/systemBarsPadding fix and a MANAGE_EXTERNAL_STORAGE-based bulk
> file scanner for SettingsPane. Needs an explicit operator merge decision;
> nobody has authority to merge it silently. PR #36 (this session) adds the
> RULE 0 verification requirement above — same rule applies to it: confirm
> its actual state via `list_pull_requests` rather than trusting this line.
>
> ### HARD STOPS
> HomeGrid.kt is FROZEN at 984b061 / blob 618cf4b6. Never edit it, for any
> reason, without explicit operator sign-off. The home screen is DONE.
> If a doc disagrees with the screen, the doc is wrong.
> Never push main. No --no-verify / push --force / reset --hard.
> A skipped or unanswered question is NOT consent. Take no action without
> an explicit order.
>
> ### ARCHITECTURE — operator-confirmed 2026-08-06
> Runtime pathways, both LIVE, selected by GenieX `plugin_id`:
>   llama_cpp — GGUF (Q4_0), NPU via ggml-hexagon / GPU OpenCL / CPU
>   qairt     — QAIRT .bin shards + geniex.json, NPU ONLY, max performance
> TFLite / LiteRT / QAT are INPUT FORMATS to the AI Hub compile, which emits
> the per-chipset bundle qairt loads. "The GGML route" = the llama.cpp
> RUNTIME. It is NOT Google LiteRT — LiteRT appears in these docs only as
> the REJECTED in-process option.
> Quantization + model selection: SETTLED. Q4_0. Do not reopen or re-analyse.
>
> MODEL RESIDENCY: every model gets its own isolated device folder and loads
> by absolute path. The APK NEVER downloads weights. Drag-and-drop to swap.
> Same storage either way — downloading only buys a boot-time failure mode.
>
> VOICE: in-process on the sherpa-onnx AAR, on device, through the APK.
> **NEVER on the NPU** — the actions model and query model already take turns
> there. This closes blueprint §8.1. Termux+proot voice was a temporary
> stopgap and is discarded; port its parameters, not its architecture.
> Measured int8 sizes: Whisper tiny.en 104MB · Moonshine tiny 124MB ·
> Whisper base.en 161MB · Moonshine base 287MB. (STATE-OF-EXISTENCE §2's
> "Moonshine is materially smaller" is FALSE — base is 78% larger.)
>
> WIRED != LAUNCHED. The APK should be capable of everything on the phone —
> sockets, the harness, permissions, an initial runtime for its own backend,
> NPU manager + file search, WebView/Chromium hooks, API + cloud inference,
> OpenRouter fallback. What is forbidden is LOADING AND LANDING with it.
>
> DAEMON SPLIT (operator, 2026-08-06): a dying process cannot report its own
> death, so OOM recovery REQUIRES an external observer. Separate: LLM
> inference (+ vision co-located), and the watchdog/recovery in :clifford.
> In-process: voice, sockets, permissions, file search, UI. The NPU manager
> stays in-APK for coordination but must NOT own OOM detection.
>
> ### AUTHORITY MODEL — series circuit
> Settings supplies (no authority to run) · Terminal forges parameters only,
> never executes · MONITOR is the switch, verifies LIVE at flip time, stores
> nothing, dispatches · Router is fuse box + breaker, carries current,
> DOESN'T ARGUE · Archives stores verified profiles, recovery restores here.
> The Router NEVER says no. A failed flip is a circuit that didn't energise,
> not an app throwing a wall. The operator EXPLICITLY REJECTED the hardened
> gatekeeper. Rule 7a: behavioural metaphors are NEVER compiled. Rule 7b:
> the supplied visual references ARE literal build specs.
>
> Use /memory to reload. HF_TOKEN / QAI_HUB_API_TOKEN from environment.
> Never hardcode them.
> ```

The vault (`c10vis-poem/nova-corpus`, previously "OBSIDIAN-Master_Wiki" —
that name is retired) still exists and still holds detail this file
summarizes rather than reproduces in full (see "Deep reference" at the
bottom). It is not required reading. Open it only when this file explicitly
points you there for one named document.

## RULE 0 — VERIFY AGAINST LIVE STATE BEFORE TRUSTING ANY DOC (mandatory, first, every session, no exceptions)

Even this file goes stale. Before treating anything below as current fact:

1. `git fetch origin && git log --oneline -10 origin/main` — get the real tip.
2. List open PRs (GitHub MCP `list_pull_requests`, `state=open`).
3. If `origin/main` has moved past what's described below, say so out loud,
   then re-derive current state from the live repo — grep the actual
   consumer of a feature, read the actual diff — before claiming anything
   works, is broken, or is missing.
4. Never call a feature "done," "broken," or "unbuilt" from prose alone. A
   comment is not code, a PR body is not the diff, a doc from two days ago
   can already be wrong. This has happened repeatedly and cost real time.

## Operator Rule 1 — read this file and RESUME.md first

Before doing anything else in this repo, read this CLAUDE.md and RESUME.md
(complements RULE 0 above: RULE 0 says verify live state before trusting
this doc, Rule 1 says read it — and RESUME.md — before anything else).
Standing convention across the operator's repos for months.

## Branch strategy — operator directive, 2026-08-08

**Going forward: two branches, period.** `main` (live, everything merges
here fast) and the frozen HomeGrid safety copy (`FROZEN-correct-home-screen-984b0610`
/ `RELEASE-correct-home-screen-984b0610`, both at blob `618cf4b6`, do not touch).

Everything else — the 19+ branches that accumulated from sessions each
opening a draft PR and never merging — is being killed. As of tonight: 11
stale PRs closed and their branches marked for deletion (#12 #13 #14 #16 #19
#20 #22 #24 #26 #27 #30 — six of those, #20 #22 #24 #26 #27 #30, were
HomeGrid-editing attempts that would have overwritten the freeze; none of
them are coming back). **PR #35** (this branch, `claude/novus-device-file-loading-ij4k4r`)
carries real fixes — `.systemBarsPadding()` insets fix + `StorageScanner.kt`
bulk file import — and is intentionally **not touched or merged by this
commit**; that decision is the operator's, still pending. **PR #36**
(`claude/app-ui-ux-issues-epl6dg`) carries this same RULE 0 doc work plus an
app-wide font-size floor fix; the operator may or may not act on it
separately.

**No more long-lived feature branches.** A session's work either merges to
`main` same-day or gets explicitly closed. Nothing sits open "to be safe."

## Hard stops

- `HomeGrid.kt` is **FROZEN** at commit `984b061` / blob `618cf4b6`. Never
  edit it, for any reason, without explicit operator sign-off. If it's
  implicated in a failure: stop, report, wait.
- Never push `main` without explicit permission. No `--no-verify`,
  `push --force`, `reset --hard` without confirming first.
- **A skipped or unanswered question is NOT consent.** Take no action
  without an explicit order.
- Don't trigger the dormant compile pipeline pre-emptively.
- Before every push, scan the diff for secrets/keys and refuse to push if any
  are found. On green CI, auto-merge into `main` immediately. Leave a branch
  in place once merged — the deletion policy above targets abandoned/stale
  branches, not just-merged ones.

## Verified build state — as of 2026-08-08, checked against live code, not prose

| Area | State | Where |
|---|---|---|
| HomeGrid home screen | built, frozen, correct | `HomeGrid.kt`, blob `618cf4b6` |
| Router / Monitor / Terminal / Settings panels | built, exist, render | `ui/panels/*.kt` |
| Router `loadConfig()` (replaces old `switchOn()` gate) | merged (PR #33/#34) | `RouterPane.kt` |
| Moonshine STT engine (class) | merged (PR #33), **not wired to any UI** | `core/stt/MoonshineSttEngine.kt` |
| Kokoro TTS (class) | exists, **not wired to any UI** | `core/voice/KokoroModelManager.kt` |
| `HorizonsVoiceInteractionService` | exists, 25 lines, **not called from `MainActivity`** | `assist/HorizonsVoiceInteractionService.kt` |
| **Voice, end to end** | **not reachable from the app** — classes exist, nothing turns them on | grep confirms zero `SttEngine`/`startListening`/`VoiceInteraction` refs in `MainActivity.kt` |
| Terminal panel | shells out to a **separately-installed Termux app** via `RUN_COMMAND` intent; says "Termux not installed" if it's absent | `ui/panels/TerminalPanel.kt` (1079 lines), `core/shell/TaskerBridge.kt` |
| **In-app conversational/CLI agent** | **does not exist.** No describe→draft→confirm→run→auto-fix loop, no in-process agent. Spec for one exists (see below), zero code | — |
| Cloud connectors (OpenRouter, SambaNova, custom) | built, SSE streaming | `core/llm/CloudLlmRuntime.kt` |
| `greenLight()` four-check gate | all four exist | `core/state/RuntimeDefStore.kt:119-149` |
| Silero VAD | fetched by CI now (was silently degrading to RMS before PR #34) | — |
| Font sizes app-wide | fixed on PR #36 (was 8-11sp dominant, floor now 12sp) — **not yet merged** | `ui/panels/*.kt` |
| Shared `Typography` scale | still does not exist — `HorizonsTheme.kt` only defines colors, every panel hardcodes its own sizes | `ui/theme/HorizonsTheme.kt` |
| Monitor pane zoom / expand | not built. Spec calls for pinch-to-zoom + inset padding (see below) | — |
| Wallpaper-uploadable backgrounds (Horizons/Artifacts/Settings/Chat, semi-transparent overlay) | not built, no code | — |
| Vault/not-vault visual indicator | not built | — |
| **OmniRoute** (AI gateway, 160+ providers, one endpoint) | forked to `c10vis-poem/OmniRoute`, **zero references anywhere in `horizons/src`.** Not deployed on-device, not called by `CloudLlmRuntime` or anything else | — |
| **Dual-agent memory system** (Mem0 + OB1 + reasoning-bank) | forked to `c10vis-poem/mem0`, `OB1`, `reasoning-bank` under AESOP. **Zero references anywhere in `horizons/src` or `knowledge/`.** Specced, never touched by code | — |
| Popup / long-press help UI | not built anywhere. Spec exists (long-press → description, see UX-RULES below) | — |
| Live chaptered user manual inside Terminal | not built | — |

## The three LOCKED visual tile specs — design settled, build not started

These are not concepts — the operator supplied reference images and said
"this is what it's going to be," which makes them specifications (Rule 7b:
visual references are literal build targets, not styling direction). Full
text lives in the vault (`canon/horizons-ui/{ROUTER-STEREO-STACK,MONITOR-ARCADE-CABINET,TERMINAL}-SPEC.md`)
on an **unmerged vault branch** (`claude/novus-device-file-loading-ij4k4r`,
same name as this Horizons-UI PR — a companion docs PR, also unmerged as of
tonight). Summary, since that branch is where the *current* spec text lives,
not the vault's `main`:

**Router = component stereo stack** (Aiwa NSX-V20 reference). CD deck top =
models (animated tray, spinning carousel, tap a disc → green LCD-style
fine-tune popup with `Model_`/`Engine_`/`Runtime_`/`Config._` picker rows and
`[load] swap [save] edit` — picker-only, no free text). Tuner deck middle =
runtime parameters (temperature, verbosity, cores, hardware target
npu/hybrid/gpu/cpu, cloud-vs-local toggle, voice DSP). Cassette deck bottom =
two wells, Browse (reaches Settings/Archives) and Load (plates a runtime;
execution modes: double-agent ping-pong, single chatbot, mixture-of-agents,
cloud connector, terminal agent). All six home tiles push to the Router.
Overflow bounces to origin tile + GOAT face, never a hard failure.

**Monitor = arcade cabinet** (neon upright, CRT-oscilloscope screen). Lit
marquee, CRT screen (browser/library/green-lights), instruction placard,
control deck. Pop-out tabs: CONSOLE/TERMINAL/BROWSER (already built
functionally, styling not done). **Pinch-to-zoom on the Monitor face** (or
press-to-zoom fallback on the Floating Live Tile) plus **inset padding**
against status/gesture bars — this is the fix for "Monitor stuck tiny" and
"panels running under the bars."

**Terminal = matrix-cascade console** (`c10vis-poem/fakesteak` reference,
own fork). `drawMatrixRain` already exists in code — the gap is layering: a
black console panel floating over visible rain (top edges, underneath,
through the tile below), not a flat paint. Console surface itself reads as
a CRT oscilloscope (green graticule, amber waveform, idle = flat line, active
= live trace responding to commands/mic/token-stream) — this is the answer
to "terminal's screensaver." Terminal also gets: real Termux `RUN_COMMAND_SERVICE`
integration (currently a stub that just says "not installed"), and a
**describe → draft → confirm → run → auto-fix on-device agent** (user
describes intent in natural language or picks from a menu, the model drafts
the shell command, user approves/edits, it runs with live streamed
output, failures auto-generate a corrective draft) — reference pattern:
`c10vis-poem/c10vis-llm-hub`. This is the "actual working terminal with an
actual agent" gap. Terminal can also port itself to become the Router's
active agent when the Router is idle.

## What's genuinely broken vs. what's just unbuilt

Broken (code exists, does the wrong thing): font sizes too small (fix on
PR #36, unmerged), no shared Typography scale, panels run under system
bars (fix exists on PR #35, unmerged), 20 of 44 reference images in the
vault's `HOME-REDESIGN-SPEC.md` are uploaded but never wired into the doc
(all the "Copy of ..." files, numbered 25-44).

Unbuilt (no code, spec exists): Router/Monitor/Terminal visual rebuilds,
wallpaper-uploadable panels, popup/help UI, live user manual, OmniRoute
integration, Mem0/OB1 memory system integration, Monitor zoom.

Unwired (code exists, never connected): voice (STT/TTS classes present,
`MainActivity` never calls them), Terminal's Termux bridge (present, requires
a separately-installed app, no in-process agent).

## Architecture — settled, do not re-litigate

- Runtime pathways, both live, selected by GenieX `plugin_id`: `llama_cpp`
  (GGUF Q4_0, NPU via ggml-hexagon / GPU OpenCL / CPU) and `qairt` (QAIRT
  `.bin` shards + `geniex.json`, NPU only). TFLite/LiteRT/QAT are input
  formats to the AI Hub compile, not a runtime choice. Quantization: Q4_0,
  settled.
- Model residency: every model in its own device folder, loaded by absolute
  path. APK never downloads weights. Drag-and-drop to swap.
- Voice: in-process on the sherpa-onnx AAR, on device. **Never on the NPU**
  — the actions and query models already take turns there.
- WIRED != LAUNCHED: the APK should be *capable* of everything on the
  phone; what's forbidden is loading and landing with it by default.
- Daemon split: LLM inference (+vision) separate from the watchdog/recovery
  in `:clifford` — a dying process can't report its own death.
- Authority model (series circuit): Settings supplies, no run authority.
  Terminal forges/configures/executes, pushes to Router. Monitor is the
  switch — verifies live at flip time, stores nothing, dispatches. Router
  is fuse box + breaker — carries current, never argues, never says no. A
  failed flip is a circuit that didn't energize, not a wall the app throws
  up. Archives stores verified profiles.
- No typing outside Terminal/browser; everywhere else is picker/button-driven
  (workbench UX rule). Long-press any control anywhere → plain-language
  description. Zoom on Home and Monitor. Inset-cropping on every room except
  Home, which alone draws edge-to-edge.

## Build / CI

AGP 8.8.0 · Kotlin 2.1.0 · compileSdk 35 · minSdk 31 · JDK 17 · arm64-v8a
only. Signing: `release/debug.keystore` (committed by design). `build-apk.yml`
cross-compiles `ort_engine` via CMake/NDK, builds the APK, publishes both
plus `libonnxruntime.so` to a `debug-<branch>` GitHub Release on every push
— that's how you get a sideloadable build per branch/PR.

## Brand

Background `#222C34` · Surface `#35414A` · Primary teal `#2DD4D9` ·
Highlight teal `#4FE7EC` · Icon backplate `#050709` · Action yellow
`#F5C518`. Backdrop: pure Compose `Brush.radialGradient`, not XML shape.

## Device

Motorola Razr Ultra 2025 · SM8750 · 16GB · Hexagon HTP v79. Phone only, no
laptop. No tokens/long URLs in paste-able commands; keep shell commands
short.

## Deep reference (optional, not required reading)

Full text of the LOCKED tile specs, the master build blueprint, the
parameter-packet design, and session-by-session history live in
`c10vis-poem/nova-corpus`. Open a *named* file there only when this doc
sends you to one — don't re-read the corpus "to be safe." As of tonight the
vault's own `main` is one commit behind its most current real content; the
companion branch `claude/novus-device-file-loading-ij4k4r` (unmerged) is
where the current Router/Monitor/Terminal spec text and `STATE-OF-EXISTENCE.md`
updates actually are. That vault branch needs an operator merge decision
same as this repo's PRs did — same disease, same fix.
