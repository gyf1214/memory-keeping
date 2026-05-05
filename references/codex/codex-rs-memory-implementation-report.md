# Codex-RS Memory Implementation Report

Scope: local `codex-rs` memory implementation in this repository, as inspected on 2026-05-04.

Version: 0.125.0 

## Short Answer

Codex memory is a global, `CODEX_HOME`-scoped memory workspace plus sqlite-backed job state. The regular agent receives a developer-prompt section that embeds a truncated `memory_summary.md` and tells it how to read `MEMORY.md`, `rollout_summaries/`, and `skills/` if relevant. The harness does not inject the full memory corpus into every prompt. It only injects the summary and read instructions.

Memory generation is done by the harness in the background when a root session starts. It runs a two-phase pipeline: phase 1 extracts per-thread raw memories from stored rollout files, then phase 2 spawns an internal consolidation sub-agent that updates the on-disk memory files.

## 1. Memory Schema And Location

Primary location:

- `memory_root(codex_home) = codex_home.join("memories")`; see `codex-rs/core/src/memories/mod.rs:105`.
- Extension inputs live beside it at `codex_home/memories_extensions`; see `codex-rs/core/src/memories/mod.rs:27-30` and `:113-115`.
- In workspace-write mode, `codex_home/memories` is added to writable roots so memory maintenance can run without extra approval; see `codex-rs/core/src/config/mod.rs:1740-1746`.

On-disk artifact layout used by prompts:

- `memory_summary.md`: compact summary. This is the only memory content embedded directly in normal agent developer instructions, truncated to 5,000 tokens.
- `MEMORY.md`: searchable registry and primary file to query.
- `skills/<skill-name>/SKILL.md`, plus optional `scripts/`, `examples/`, `templates/`.
- `rollout_summaries/*.md`: per-rollout recaps/evidence.
- `raw_memories.md`: merged phase-1 raw memories.

This layout is described in `codex-rs/core/templates/memories/read_path.md:21-33`. `raw_memories.md` is generated with sections like `## Thread <id>`, `updated_at`, `cwd`, `rollout_path`, `rollout_summary_file`, then the raw memory body; see `codex-rs/core/src/memories/storage.rs:62-95`. Rollout summary files include `thread_id`, `updated_at`, `rollout_path`, `cwd`, optional `git_branch`, then summary text; see `codex-rs/core/src/memories/storage.rs:22-39`.

DB schema:

- `stage1_outputs(thread_id, source_updated_at, raw_memory, rollout_summary, generated_at, ...)`; initial schema is in `codex-rs/state/migrations/0006_memories.sql:1-11`.
- `jobs(kind, job_key, status, worker_id, ownership_token, lease_until, retry_at, input_watermark, last_success_watermark, ...)` coordinates phase-1 and phase-2 work; see `codex-rs/state/migrations/0006_memories.sql:13-31`.
- Later columns add `rollout_slug`, `usage_count`, `last_usage`, `selected_for_phase2`, `selected_for_phase2_source_updated_at`.
- `threads.memory_mode` defaults to `enabled`; see `codex-rs/state/migrations/0018_phase2_selection_snapshot.sql:1-3`.

Phase-1 model output schema is strict JSON:

```json
{
  "rollout_summary": "string",
  "rollout_slug": "string|null",
  "raw_memory": "string"
}
```

See `codex-rs/core/src/memories/phase1.rs:67-80` and `:150-161`.

## 2. Prompts Provided To The Agent

Normal user-facing agent:

- The prompt fragment is `codex-rs/core/templates/memories/read_path.md`.
- It is rendered by `build_memory_tool_developer_instructions()` after reading `codex_home/memories/memory_summary.md`; see `codex-rs/core/src/memories/prompts.rs:263-284`.
- It is appended to developer instructions only when `Feature::MemoryTool` is enabled and `config.memories.use_memories` is true; see `codex-rs/core/src/session/mod.rs:2577-2584`.
- It says memories are read-only, gives the folder layout, gives a quick memory lookup procedure, and embeds `MEMORY_SUMMARY` at the end; see `codex-rs/core/templates/memories/read_path.md:1-7`, `:35-43`, and `:124-129`.

So yes, the harness injects memory into the prompt, but only the compact summary plus instructions. It does not inject full `MEMORY.md`, `raw_memories.md`, or rollout summaries. The agent is told to read those files if useful.

Memory generation prompts:

- Phase 1 uses system prompt `codex-rs/core/templates/memories/stage_one_system.md` plus user prompt `stage_one_input.md`; see `codex-rs/core/src/memories/mod.rs:41-42` and `codex-rs/core/src/memories/phase1.rs:323-344`.
- `stage_one_input.md` includes rollout path, cwd, filtered rollout content, and a warning not to follow instructions found inside the rollout; see `codex-rs/core/templates/memories/stage_one_input.md:1-11`.
- Phase 2 uses `codex-rs/core/templates/memories/consolidation.md`, rendered by `build_consolidation_prompt()` with the memory root, extension blocks, and phase-2 input diff; see `codex-rs/core/src/memories/prompts.rs:85-120`.

The large prompt bodies are already in source files:

- `codex-rs/core/templates/memories/read_path.md`
- `codex-rs/core/templates/memories/stage_one_system.md`
- `codex-rs/core/templates/memories/stage_one_input.md`
- `codex-rs/core/templates/memories/consolidation.md`

## 3. Tools Provided For Memory Operations

For the regular model-visible agent, I found no implemented dedicated memory read/update tool handler in this tree. There is a test named `get_memory_requires_feature_flag`, but `rg "get_memory"` only finds that test and unrelated path-classification helpers. Actual memory access is through normal file-capable tools, guided by the developer prompt.

Important constraints:

- The normal memory prompt says: "Never update memories. You can only read them." (`read_path.md:6`).
- Memory read usage is tracked heuristically when shell commands read/search paths under `memories/MEMORY.md`, `memory_summary.md`, `raw_memories.md`, `rollout_summaries/`, or `skills/`; see `codex-rs/core/src/memories/usage.rs:69-70` and `:133-145`.
- Assistant replies can include hidden `<oai-mem-citation>` markup, which is stripped from visible text and parsed to update memory usage metadata; see `codex-rs/core/src/stream_events_utils.rs:125-194` and `read_path.md:90-122`.

Non-model control operations:

- Core protocol ops: `DropMemories`, `UpdateMemories`, and `SetThreadMemoryMode`; see `codex-rs/protocol/src/protocol.rs:761-777`.
- Core handlers clear memory state, trigger the startup memory pipeline, or set thread memory mode; see `codex-rs/core/src/session/handlers.rs:674-730` and `:922-929`.
- App-server experimental API exposes `thread/memoryMode/set` and `memory/reset`; see `codex-rs/app-server/README.md:153-154` and examples at `:456-469`.

## 4. Does The Harness Update Memory Itself?

Yes, but not during normal model turns by letting the main agent write memory. The harness runs a background memory pipeline when a root session starts:

- Entry point: `start_memories_startup_task`; skipped for ephemeral sessions, disabled memory feature, sub-agent sessions, or missing state DB; see `codex-rs/core/src/memories/start.rs:10-29`.
- It runs `phase1::prune`, `phase1::run`, then `phase2::run`; see `codex-rs/core/src/memories/start.rs:37-42`.

Phase 1:

- Selects eligible interactive rollouts from sqlite, excluding the current thread, disabled/polluted memory modes, too-new rollouts, stale/running jobs, etc.
- Sends filtered rollout response items to a model with no tools and strict JSON output; see `codex-rs/core/src/memories/phase1.rs:313-344`.
- Redacts secrets and stores outputs in `stage1_outputs`; see `codex-rs/core/src/memories/phase1.rs:387-390` and `:442-457`.

Phase 2:

- Selects recent/useful stage-1 outputs, materializes `raw_memories.md` and `rollout_summaries/`, then spawns an internal consolidation sub-agent.
- The consolidation agent is ephemeral, has memory generation/use disabled, has collab disabled, has no approvals, no network, and writable access only to the memory root; see `codex-rs/core/src/memories/phase2.rs:300-346`.
- The consolidation prompt is sent as a user input; see `codex-rs/core/src/memories/phase2.rs:351-360`.
- On completion, the job is marked successful and selected phase-1 snapshots are recorded; see `codex-rs/core/src/memories/phase2.rs:240-270` and `:370-430`.

Reset/drop:

- `memory/reset` and `DropMemories` clear `stage1_outputs`, memory jobs, and memory directories, while preserving thread memory modes in app-server reset; see `codex-rs/app-server/src/codex_message_processor.rs:3255-3297` and `codex-rs/app-server/README.md:466`.

## 5. Repository-Level Or Global?

The implementation is global per `CODEX_HOME`, not repository-level.

Evidence:

- The memory root is only `codex_home/memories`; no repository path is part of the root path calculation.
- The phase-2 consolidation selects global stage-1 outputs from sqlite. It stores `cwd`, `git_branch`, and `rollout_path` inside artifacts, but does not create a separate memory root per repo.
- The normal agent prompt tells the model to decide relevance from `MEMORY_SUMMARY` and `MEMORY.md`; it says to use memory when the query mentions a workspace/repo/module/path/files in the summary.

How repo/project distinctions happen:

- Mostly semantically, through generated memory content and metadata such as `cwd`, `git_branch`, `rollout_path`, and `rollout_summary_file`.
- Stage-1 selection records thread cwd and git metadata from the thread DB.
- Phase-2 consolidation can organize `MEMORY.md` however the prompt asks, but the harness itself does not enforce per-repository memory files.

Thread-level eligibility is separate from repository scope. A thread can be `enabled`, `disabled`, or marked `polluted` in sqlite. `disabled` prevents future memory generation for that thread. If `disable_on_external_context` is true, MCP/web/search context can mark a thread `polluted`; see `codex-rs/core/src/stream_events_utils.rs:153-171` and `codex-rs/core/src/mcp_tool_call.rs:550-559`.

## 6. Harness Knobs

Feature/config knobs:

- Feature flag: `[features].memories` maps to `Feature::MemoryTool`, experimental and default-disabled; see `codex-rs/features/src/lib.rs:716-722`.
- `[memories].use_memories`: controls injection of memory developer instructions into normal turns.
- `[memories].generate_memories`: controls whether newly created threads are eligible for future memory generation.
- `[memories].disable_on_external_context`: if true, web/search/MCP context can mark a thread `memory_mode = "polluted"`; legacy alias is `no_memories_if_mcp_or_web_search`.
- `[memories].max_raw_memories_for_consolidation`: retained recent raw memories, clamped 1..4096.
- `[memories].max_unused_days`: eligibility window for phase-2 selection.
- `[memories].max_rollout_age_days`: max age of rollouts considered for extraction.
- `[memories].max_rollouts_per_startup`: max rollout candidates processed per pass, clamped 1..128.
- `[memories].min_rollout_idle_hours`: minimum idle time before extraction, clamped 1..48.
- `[memories].extract_model`: phase-1 model override.
- `[memories].consolidation_model`: phase-2 model override.

Defaults are in `codex-rs/config/src/types.rs:226-239`; config fields are declared at `:184-208`.

Hard-coded operational knobs:

- Phase 1 default model `gpt-5.4-mini`, reasoning effort `Low`, concurrency `8`, fallback rollout token limit `150_000`, memory summary injection limit `5_000` tokens, job lease/retry `3600` seconds, thread scan limit `5000`; see `codex-rs/core/src/memories/mod.rs:35-64`.
- Phase 2 default model `gpt-5.4`, reasoning effort `Medium`, job lease/retry `3600` seconds, heartbeat `90` seconds; see `codex-rs/core/src/memories/mod.rs:67-79`.
- The phase-2 consolidation agent is forced to no network, no approvals, memory feature disabled, collab disabled, and memory-root-only write access; see `codex-rs/core/src/memories/phase2.rs:300-346`.

User/client controls:

- TUI `/memories` persists `use_memories` and `generate_memories` into `config.toml`, profile-scoped when a profile is active; see `codex-rs/tui/src/app/config_persistence.rs:382-425`.
- If `generate_memories` changes for the current thread, TUI calls app-server `thread/memoryMode/set`; see `codex-rs/tui/src/app/config_persistence.rs:429-460`.
- App-server exposes experimental `thread/memoryMode/set` and `memory/reset`; see `codex-rs/app-server/README.md:153-154`.
- CLI has `debug clear-memories`, which clears memory DB rows and memory directories; see `codex-rs/cli/src/main.rs:1410-1430`.

## 7. Prompt Export Note

No separate prompt exports were necessary. The large prompt templates are already standalone source files under `codex-rs/core/templates/memories/`, and this report references them directly.

## 8. Phase Contracts, Job State, And Incremental Behavior

### Phase 1 Intended Input And Output

Phase 1 is "Single Rollout" extraction.

Inputs:

- One claimed stored rollout thread from sqlite, represented by thread metadata such as thread id, rollout path, cwd, updated_at, source, and git metadata.
- The rollout `.jsonl` loaded from `rollout_path`.
- A filtered rendering of model-relevant rollout response items. Phase 1 removes irrelevant/non-memory-safe content before prompt upload.
- Prompt context:
  - system prompt: `codex-rs/core/templates/memories/stage_one_system.md`
  - user prompt: `codex-rs/core/templates/memories/stage_one_input.md`
  - no tools
  - strict JSON output schema

Model output:

- `rollout_summary`: comprehensive per-rollout recap/evidence artifact.
- `rollout_slug`: filesystem-safe slug, <= 80 chars, used in rollout summary artifact filenames.
- `raw_memory`: structured raw memory with YAML-ish front matter and `### Task <n>` blocks.

If the rollout has no durable signal, the intended output is exactly empty strings for all three fields. In code, an empty `raw_memory` or empty `rollout_summary` is treated as "succeeded no output": the job is marked done and any previous `stage1_outputs` row for that thread is deleted.

Persistent output:

- Non-empty output is upserted into `stage1_outputs` keyed by `thread_id`, replacing prior output only when `source_updated_at` is newer or equal.
- Successful non-empty output enqueues/advances the singleton phase-2 consolidation job.

### Phase 2 Intended Input And Output

Phase 2 is global consolidation.

Inputs:

- A bounded selected set of `stage1_outputs`, ordered by usage and recency.
- The previous successful phase-2 selection, used to compute `added`, `retained`, and `removed`.
- Existing on-disk artifacts under `codex_home/memories`, if present.
- Optional old memory-extension resources under `codex_home/memories_extensions`.

Before the consolidation agent starts, the harness materializes filesystem inputs:

- `raw_memories.md`: mechanical merge of selected/current plus previous-selected raw memories.
- `rollout_summaries/*.md`: one file per retained/current-or-previous rollout summary.

Important nuance: artifact materialization uses the union of current selected outputs and previous selected outputs. This keeps "removed" evidence available during the consolidation agent's forgetting pass.

The phase-2 prompt tells the consolidation agent to:

- read `raw_memories.md`, existing `MEMORY.md`, existing `memory_summary.md`, existing `skills/*`, and rollout summaries as needed;
- treat missing/empty existing artifacts as INIT mode;
- otherwise do an INCREMENTAL UPDATE;
- for added thread ids, search `raw_memories.md`, read relevant raw-memory sections and rollout summaries;
- for removed thread ids, delete only memories supported by those removed threads;
- update or create:
  - `MEMORY.md`
  - `memory_summary.md`
  - optional `skills/*`

Persistent output:

- The durable memory files are written by the internal consolidation sub-agent, not by direct Rust formatting.
- On agent success, the phase-2 job is marked done, `last_success_watermark` advances, and `stage1_outputs.selected_for_phase2` is rewritten to mark only the exact selected snapshots consumed by that successful run.

### Job State Model

All memory pipeline work is coordinated through sqlite table `jobs`.

Job kinds:

- `memory_stage1`: one job per rollout/thread, keyed by `job_key = thread_id`.
- `memory_consolidate_global`: singleton global phase-2 job, keyed by `job_key = "global"`.

Relevant state fields:

- `status`: effectively `pending`, `running`, `done`, or `error`.
- `worker_id`: thread id of the worker that claimed the job.
- `ownership_token`: UUID token required to complete/fail/heartbeat a running job.
- `started_at`, `finished_at`.
- `lease_until`: running-job lease expiration.
- `retry_at`, `retry_remaining`, `last_error`: retry/backoff model.
- `input_watermark`: source update timestamp or logical dirty marker.
- `last_success_watermark`: latest successfully processed watermark.

Phase-1 claim semantics:

- Skip if existing stage-1 output or job success watermark is already at least as new as the thread `updated_at`.
- Otherwise claim by inserting/updating the job to `running`, with a fresh ownership token and lease.
- Enforce a global running cap for `memory_stage1`.
- Respect active leases, retry backoff, and retry exhaustion unless the source watermark advanced.

Phase-2 claim semantics:

- The singleton job is claimable only when `input_watermark > last_success_watermark`.
- It is skipped when not dirty, retry-exhausted/backing off, or currently running with a live lease.
- A running phase-2 job is heartbeated periodically while the consolidation sub-agent runs.

Failure semantics:

- Phase-1 and phase-2 failures set `status = error`, clear the lease, write `last_error`, set retry backoff, and decrement `retry_remaining`.
- Default retries are 3.

### How Raw Memory Becomes Rollout Summary And Durable Memory

The naming is easy to misread:

- `raw_memory` is the detailed, structured extraction from one rollout.
- `rollout_summary` is also phase-1 output from the same rollout, but it is a more narrative recap/evidence artifact for future deep dives.
- `raw_memories.md` is a mechanical merge of many `raw_memory` values, latest/selected first, with thread metadata and a pointer to the corresponding rollout summary file.
- `rollout_summaries/*.md` is generated from the `rollout_summary` value plus metadata.
- `MEMORY.md` and `memory_summary.md` are phase-2 synthesized durable artifacts, written by the consolidation agent using `raw_memories.md` and rollout summaries as inputs.

So rollout summary is not organized "into" raw memory. They are sibling phase-1 outputs. Phase 2 uses both: `raw_memories.md` for routing/task inventory and rollout summaries for richer evidence when needed.

### Concurrency Control

Yes.

- Phase 1 runs multiple extraction jobs concurrently with `CONCURRENCY_LIMIT = 8`.
- Each phase-1 job has an sqlite job row, ownership token, lease, retry state, and global running cap.
- Claiming uses transactions and live-lease checks to avoid duplicate workers processing the same rollout.
- Phase 2 uses a singleton `memory_consolidate_global/global` job. Only one consolidation can run while its lease is live.
- Phase 2 heartbeats the singleton job every 90 seconds while the consolidation sub-agent is running.
- On successful phase 2, the selected snapshot baseline is rewritten transactionally.

### Existing `MEMORY.md`, `raw_memories.md`, And `memory_summary.md`

Existing files do not directly change phase-1 job selection.

- Phase 1 is driven by sqlite `threads`, `stage1_outputs`, and `jobs`, specifically whether a thread has an up-to-date stage-1 output/success watermark relative to the rollout's `updated_at`.
- Existing `MEMORY.md`, `raw_memories.md`, or `memory_summary.md` on disk are not consulted by phase-1 extraction.

Existing files do affect phase 2 behavior:

- The harness rewrites `raw_memories.md` and syncs `rollout_summaries/` from DB state before spawning the consolidation agent.
- If no retained inputs exist, stale `MEMORY.md`, `memory_summary.md`, and `skills/` are removed during rollout-summary sync.
- If selected inputs exist, existing `MEMORY.md`, `memory_summary.md`, and `skills/*` are inputs to the consolidation agent, and the prompt classifies the run as INIT vs INCREMENTAL UPDATE based on whether those artifacts are missing/empty.
- If phase 2 is not dirty (`input_watermark <= last_success_watermark`), it is skipped even if someone manually edits existing memory files on disk; the dirty signal lives in sqlite job state, not file mtimes.
