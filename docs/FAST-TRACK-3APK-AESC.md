# Fast track: Æsc terminal daemon, APK ×3 (Hyperion), and the plugin cut

Written 2026-11-04 from the Findings ingest (dumbass RESUME set, rev.eng.ify, _config, RESUME.txt 1–5, the Cloudflare set, the Argon plugin-consolidation report, memory-as-skill brief, rea_deconstruction specs, Hyperion-XI and AEthX-AEsc RESUME/PENDING). One file, three parts. Each part ends in a loop-until-done workflow with a verifier and a budget.

---

## Part A. Æsc terminal daemon: the shortest path

### What already exists (do not rebuild)
| Asset | Where | State |
|---|---|---|
| NPU proof: Qwen3.5 2B Q4_0 at 21.2 tps and 4B at 7.6 tps fully on HTP0 (GenieX v0.9.3, llama_cpp + GGML Hexagon) | `AEthX-AEsc/docs/npu-proof/2026-10-23.md` | done |
| Bridge daemon `aesopd.py`, supervised `llamad` with Hexagon offload, `protocol/bridge-protocol.md`, `termux-helper` skill | `Aesop-Xi/deploy/phone/` | written, never reviewed |
| Sep-13 app snapshot with `daemon/` (CMake), `horizons/` UI shell, NpuClient, DaemonLauncher | `Hyperion-XI` main | imported, not split |
| prime-agent RLM core: persistent IPython REPL, `rlm()` recursive handle, daemon JSON-RPC endpoints (`/sessions/tree`, `/attach`, `/intervene`, `/observe`) | `NovA-prime-agent` fork, strip table in `rea_deconstruction_specs_prime_agent_and_ecc_aesop.md` | strip script written, not run |
| ADB loopback design (127.0.0.1:5555, AF_UNIX + SCM_RIGHTS handshake) | `AEthX-AEsc/adb_loopback/POINTER.md`, Joy-of-cooking 03 | design only |

### The one blocker that matters
The watchdog dies under Android process management. Everything else waits on a `ForegroundService` that owns the daemon process. Build that first; nothing else in Æsc is blocked on anything else.

### Five-step order (one step per session, each a Loop-Until-Done)
1. **ForegroundService shell.** Kotlin service in `AEthX-AEsc/core` that starts, supervises and restarts one child process, posts a persistent notification, requests battery-optimization exemption. Verifier: survives screen-off 30 min and a task-kill on the test phone. Budget: 1 session.
2. **Daemon process = llamad + bridge.** Review `Aesop-Xi/deploy/phone/` prior art, keep `llamad` (NPU) and `aesopd.py` (bridge) as the two children of step 1. Do not write a third daemon. Verifier: 2B model answers over the bridge socket from inside the service. Budget: 1 session.
3. **REPL layer.** Run `rea_strip_prime_and_ecc.sh`, keep only `repl/`, `rlm/`, `daemon/` from prime-agent, point its OpenAI client at the local bridge (or OmniRoute `:20128/v1` if kept, see Part C). Pre-import `claw.*` and `ask_claude()` into the REPL namespace. Verifier: `rlm("list files", tier="npu_2b")` returns a handle and a result. Budget: 1 session.
4. **ADB loopback.** Wireless-debugging pairing once by the operator; the service opens the loopback client; one `getprop` round trip through adbd. Verifier: `adb shell` command issued from the REPL returns stdout. Budget: 1 session.
5. **Terminal UI.** PTY multiplexer tile in the Hyperion shell bound to the service socket (replaces Termux). Verifier: operator types in the tile, sees daemon output, kills and restarts the service from the tile. Budget: 1 session.

Installations agent (Œræcle) is step 0 running in parallel: the Env Setup Wizard pattern pointed at `AEthX-AEsc/docs/BUILD-ORDER.md` and the NPU install plan §6 from step 8. It writes `scripts/setup.sh` and proves a fresh clone builds. It is a skill plus one script, not an APK.

---

## Part B. APK ×3 = Hyperion, with the four-tier model workflow

### The three packages
| APK | Repo | Owns | Talks over |
|---|---|---|---|
| 1 Terminal daemon (Æsc) | `AEthX-AEsc` | ForegroundService, llamad/NPU, bridge, REPL, ADB loopback, local SQLite session store | AF_UNIX socket, permission-protected Binder |
| 2 Media (Æyre) | `CloviX-AEyre` | mic, Silero VAD, Moonshine/Qwen STT, TTS, camera, screen capture | Binder to APK 1, webhook bus to APK 3 |
| 3 UI shell (Hyperion) | `Hyperion-XI` | tiles: chat, terminal, agents-view, graph, approvals, model picker, archives; WebView browser with DOM interceptor | local WebSocket JSON-RPC frames |

Hyperion-XI open items 1 to 3 (package name, module split, strip `daemon/`/ort_engine/Clifford/NpuClient) are the first PR here. The split is: `horizons/` stays, `daemon/` moves to Æsc as the reference for step 2 above, ort_engine and Clifford are deleted (post-mortem in `raw-databank/README-SALVAGE.md` says do not repeat the half-finished GenieX migration).

### The four tiers that drive it (from RESUME.txt 1–5 plus rev.eng.ify §3.1)
| Tier | Seat | Role | Quota reality |
|---|---|---|---|
| 1 Architect | Gemini 4 Argon in the Gemini app, business Workspace seat (@clovispoem.com, $20) | design, planning, long-context corpus reads, outputs a ready-to-execute plan | full paid access in the app only |
| 2 Bridge | Workspace Developer Mode MCP endpoints + Cloudflare edge MCP Worker (R2 vault, KV pointer manifest, bearer or Access auth) | data retrieval, enterprise context, the vault reachable from any device 24/7 | free tier Workers 100k req/day |
| 3 Operator | Antigravity CLI (agy) in Termux, later inside Æsc | file writes, diffs, git, local execution from Tier 1's plan | agy OAuth ignores Workspace licenses, drops to Starter quota; fallback `GEMINI_API_KEY` from AI Studio on the business account |
| 4 Builder | Claude Code (Pro) as primary harness; the Æsc REPL calls it via `ask_claude()` (Mode A) | custom plugins, hooks, workflows, the board of directors tournament | Pro session limits; dual-account round-robin was a Gemini-era proposal, not implemented |
| local | Qwen3.5 2B executor and 4B/9B assistant on HTP0 via GenieX | VAD, STT triage, file moves, offline queries | measured 21.2 tps / 7.6 tps |

Cloudflare sits above all four: R2 holds the vault markdown and graph JSON, KV holds `manifest.jsonl` pointers and session state, one Worker is the MCP gateway (`read_vault_note`, `query_pointer_manifest`, `search_memory`), Tailscale carries the home nodes (Jetson Orin Nano Super headless, Rubik Pi 3 Dragonwing display and orchestration) under it. Deterministic hooks stay local, never on the edge.

### Board of directors (one workflow file, `NovusAExenti/workflows/board.js`)
Tournament pattern. Seats: Architect Claude Fable 5.1, Adversary DeepSeek V4.1 Flash, Arbitrator Gemini 4 Argon. Input: one spec. Fan-out to the three seats, each returns a plan. Adversary attacks both others. Arbitrator scores. Jev `consistency_choice` (abstain under 0.60) breaks ties. Output: agreements, contradictions, unique-to-each, blind spots. Operator reads only contradictions and blind spots. Budget: 9 agent calls, one round, no re-entry without `#board-again`.

### Loop-until-done shape for every workflow above
```
plan (Tier 1) -> execute bounded step (Tier 3 or 4) -> verifier node (test, device check, or Jev Noul) -> pass: next step / fail: retrieve strategy from reasoning bank, retry within budget -> ledger receipt to all four streams -> stop when verifier passes or budget spent
```
Four-stream receipts always (episodic, governing, code structure, knowledge graph), `not_applicable` allowed, never skipped.

---

## Part C. Plugins: keep, implement, ignore, change

Argon's report reduces the 500-plugin sample to 7 pillars and one maxim: hooks remove a decision from the model, skills add one, subagents quarantine one. Applied to what is actually in your stack:

### KEEP as-is (out-of-the-box, no reverse engineering)
| Plugin | Pillar | Why |
|---|---|---|
| code-review-graph (Tree-sitter SQLite) | 3 | stateful, high-frequency: stays an MCP server behind a `toolFilter` allowlist |
| Graphify + Obsidian plugin | 3, 2 | vault-side graph, Obsidian render, notebooklm-py feed; run as git-hook pre-indexer, graph JSON to R2 |
| Playwright MCP | 3 | the WebView DOM interceptor for Gemini/Claude session capture; stateful, keep as server |
| superpowers (TDD, debugging, verification-before-completion, writing-plans) | 5, 8 | process skills, already synced, zero hooks that block |
| task-observer | 7 | observation ledger; already the agreed Gate 4 |
| claude-jev (TypeSafe Jev) | 1, 5 | the router and verifier primitive for every gate; Noul/Choice/Score |
| building-with-typesafe-jev | 1 | the cookbook skill for the above |
| plugin-dev, skill-creator, mcp-server-dev | 1, 7 | the tooling that builds everything in IMPLEMENT |

### IMPLEMENT (yours to build; this is the "finish Argon's plans" list)
| Package | From | Shape |
|---|---|---|
| `nvaex-memory` core plugin | memory-as-skill v-next brief | pointers only, `memory/layers.jsonl` router, SessionStart read-only, Stop nudge-only, flag-file kill switch; borrows anchor-memory linter, continuity ledger (strip auto-commit), whydone ask-mode preview, context-distill YAML handoff |
| `nvaex-gates` plugin | Aesop-Xi #85 RULES.md | the ten rules, `run-gate.sh`, `gate` CLI, shipped as a plugin so it syncs to every session |
| `nvaex-router` (Jev skill-suggestion + model-route) | jev-skill-suggestion mod, ECC `model-route`, `cost-aware-llm-pipeline` | skills `user-invocable-only`, Choice over the listing, gate 0.30, fits 0.30, shortlist 3; model tier binding npu_2b / npu_4b / jetson / openrouter / claude_cli |
| `nvaex-corpus-ingest` + `nvaex-wiki-compiler` | fusion-response build list, OpenWiki skills, llm-wiki plugin | 25-file batches, 5-doc-type taxonomy, staging only, cross-auditor and red-auditor before promotion; manifest JSONL + file pointers, not pxpipe, for local models |
| `nvaex-board` workflow | this doc Part B | one `.js` workflow, Tournament pattern |
| ECC packs 1–5 (88 skills) | rea_deconstruction specs | five JEV-indexed packs in `NovusAExenti/skills/ecc-packs/`, each a plugin with `manifest.jsonl` pointer rows; drop the other 198 skills |
| Scout + blackboard harness | Argon Blueprint 3 | one Scout (haiku/sonnet, read-only) writes `context-brief.json`; single-writer `events.jsonl`; workers write atomic JSON through `json-write.sh` |
| Cloudflare edge MCP Worker | Cloudflare Memory Stack guide + agent_os blueprint | `wrangler.toml` R2 binding `VAULT_BUCKET`, KV `POINTER_KV`, `MCP_AUTH_TOKEN` secret, three tools |

### CHANGE before use
| Thing | Change |
|---|---|
| OmniRoute | Keep only as the thin OpenAI-compatible REST gateway on `:20128` with memory extraction; strip the dashboard, Redis, Bifrost. Note the conflict: Hyperion-XI and AEthX-AEsc AGENTS.md say OmniRoute was dropped 2026-10-23; the Joy-of-cooking 07 answer says keep it. Operator decision recorded here as KEEP-THIN until you say otherwise. |
| Mem0 + Terrestrial Brain | one Postgres (pgvector, SHA-256 CAS trigger) locally, PGLite in `.agent/` per repo; drop the Mem0 dashboard and JWT stack from the ai-developer-stack compose |
| Reasoning Bank, Continual Harness | reconstructed modules, not the upstream products: concise strategy cards, candidate-then-approve adaptation, never self-approving (Understood.txt layer 4) |
| ContinualHarnessHeart python | design notes only; remove the fake RTK/Headroom/Fabric/Context7 integrations, `process.env`, fixed CDP address, prompt-hash execution skipping, pre-verification SUCCESS events |
| 1MCP gateway | adopt only at `@1mcp/agent` 0.40.0+ (CVE-2026-108586), with explicit `toolFilter` includes |
| toolrouter | keep connected, but behind the Jev router; it is 9k tokens of catalog, not a replacement for your own packages |
| ECC monolith | never install the 203 MB cache; extract `COMMAND-REGISTRY.json`, `honey-crush`, `nexus-mapper`, `ecc-planner`, `px-reader` only |
| Prime Agent | strip benchmarks (80 percent of weight) and the React dashboard; keep REPL, `rlm()`, daemon endpoints, continual `/refine` with Gate 4 on every diff |

### IGNORE
| Thing | Why |
|---|---|
| pxpipe / Render MCP PNG exploit for local models | you chose manifest JSONL + file pointers; keep pxpipe only as an optional frontier-model lane |
| OB1 | intrusive orchestration, bypassed in the Recap of Engineering Decisions |
| Hermes / DSH as master | Claude Code is the primary harness (fusion-response correction) |
| OpenWiki TUI as executor | replaced by the wiki-compiler plugin |
| Dual Claude Pro headless CDP round-robin, account rotation | compliance risk; use backoff and queuing (Understood.txt layer 6) |
| Holograph | unverified repo, no implementation to name; park until the source is in hand |
| The ~380 remaining directory plugins (notifiers, games, cost bars, video, marketing) | none map to a pillar you lack; the directory stays as reference in `Copy of plugin_directory.md` |
| OpenRouter chats: Jev `ingest`/`compare` CLI, pxpipe as T6 ingest step, PGLite `.db` committed in-repo, qmd/LightRAG/MemOS/Memsearch/Neo4j/Tars/smolagents pile, "lightweight scrapers" role for the Rubik Pi | digest in; the first two are hallucinated or already rejected, the rest were never adopted into your own asset list |

---

### From the OpenRouter chats (Oct 13, Oct 28 x2), what survives
Accepted and already in this plan: 5+1 tier vault with MAP.md first; 0.8B executor / 9B query / GLM fallback lanes; Claude Pro for coding, Gemini for ingestion, OpenRouter for specifics; NPU manager in APK 1 with a lease protocol; SRVG scoring and the ms-swift `loss_scale` mapping; four-stream receipts with `not_applicable`; secrets as `secret_ref` pointers, `storageState.json` never in git; llm-wiki-compiler core with nocode gates.

Still open, raised there and unanswered: Red Agent annotate-and-reject vs rewrite (the Oct 28 verifier answer says never rewrite RLVR data); Halogen's real identity (three incompatible guesses, default slot `extract.typed`); QWENSST vs SSTR split; phase order (secrets, CAS, storage, compaction, QWENSST, Jev, dispatch, mining). The Jev endpoint conflict (OpenRouter `/api/alpha/decisions` vs `api.typesafe.ai/v1`) is resolved by the claude-jev plugin: TypeSafe is the endpoint, Choice/Noul/Score/Box are the only primitives.

## What this file does not decide
- Æsc package name (Hyperion-XI open item 1). Operator picks.
- OmniRoute keep-thin vs drop. Recorded as keep-thin; operator confirms.
- Whether `nvaex-memory` syncs from the claude.ai account or installs from the NovAExopia marketplace. Brief leans account upload with source in NovAExopia.
