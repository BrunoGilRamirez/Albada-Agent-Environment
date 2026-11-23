# Agent Model Routing

> Status: development orchestration policy
> Date: 2026-09-06
> Scope: OpenCode agents working on the implementation plan

This policy assigns an OpenCode model to each task group in `docs/todo/`. It
controls development-agent selection only. It does not add LLM functionality to
the JOI-ProjectLife product and does not change the MVP boundary in
`docs/masterPlan.md`.

## Levels

| Level | Primary model | Use for |
| --- | --- | --- |
| **L3** | `opencode-go/gpt-5.6-luna` | Architecture, planning, domain decisions, privacy-sensitive design, and final trade-offs |
| **L2** | `opencode-go/gpt-6-luna` | Rust/domain logic, database work, persistence, import/export, deep debugging, and technically complex implementation |
| **L1 (default)** | `opencode-go/qwen3.8-flash` via `l1-execute` | Normal paid route for sustained, cross-cutting, multi-concern, integration-heavy, or likely iterative work; also the default when free-tier work is uncertain |
| **L0** | No LLM | Search, file inventory, tests, lint, formatting, builds, fixtures, and deterministic scripts |

The model named in a phase routing table is the primary model for every
checkbox below that task-group heading. Apply the conservative L1 rules below
when the task's actual effort or iteration risk is clearer than the phase-wide
label. File count alone does not determine the implementation tier.

## Go Pricing Reference

All models below run on the **OpenCode Go** catalog, a $10/month subscription
with usage metered in dollars: a 5-hour limit of $12, a weekly limit of $30,
and a monthly limit of $60. Prices are USD per one million tokens from the
official [OpenCode Go pricing page](https://opencode.ai/docs/go/), checked on
2026-09-04. Actual request counts depend on input, output, cache hits, context
size, and retries.

| Model | Input | Output | Cached read | Cached write | Monthly usage |
| --- | ---: | ---: | ---: | ---: | ---: |
| `opencode-go/gpt-5.6-luna` | $0.20 | $1.20 | $0.02 | $0.25 | $15 |
| `opencode-go/gpt-6-luna` | $0.66 off-peak | $1.98 off-peak | $0.022 off-peak | - | $15 |
| `opencode-go/kimi-k2.7-code` | $0.95 | $4.00 | $0.19 | - | $60 |

Notes: `gpt-5.6-luna` above 272K input tokens is $0.40 / $1.80 / $0.04 / $0.50.

Kimi K2.7 remains in this table only as a cost reference for the exceptional
fallback. It is not a normal phase assignment or default implementation model.

A monthly spending limit should be configured before using paid models.

## Selection Rules

1. Use L3 only for decisions that can invalidate the model, architecture,
   privacy boundary, or phase strategy. Do not spend L3 on routine edits.
2. Prefer `opencode-go/gpt-6-luna` for long-context domain, SQL, Rust, import, and
   export work as the current L2 model.
3. **Paid L1 is the default for sustained implementation.** Use
   `opencode-go/qwen3.8-flash` through `l1-execute` for multi-concern,
   cross-cutting, integration-heavy, longer-context, or likely iterative work.
   This includes broad UI/screen migrations even when individual edits are
   mechanical. `GLM Flash` is not a valid default model ID unless a separately
   configured provider is explicitly approved.
4. **Use whole-task `l1-execute-free` sparingly.** It is appropriate only for an
   exact, trivial, low-risk code-only or routine docs/config task expected to
   finish in one pass with at most one obvious local correction. Keep it to very
   small scopes such as a single-file label, fixture, or mechanical config adjustment. Do not send it a
   cross-cutting migration, unclear task, integration change, or work likely to
   need repeated review. When uncertain, choose paid `l1-execute` immediately.
4a. **Docs and config are mandatory L1 work.** Routine documentation
    corrections or authoring, and configuration edits, always route to L1 —
    never to L2/L3 directly. L2/L3 retain domain and architecture decisions
    and supply the settled content or acceptance contract; L1 writes the file
    and returns it for domain/architecture review. Use paid `l1-execute` by
    default, especially for multi-file or iterative work; whole-task
    `l1-execute-free` only for an exact, trivial one-pass edit. In a
    quota-exhausted session, if the task exceeds free L1's one-pass scope,
    pause/report instead of charging a paid model or looping free agents.
    Never route domain, persistence, or Rust work to L1.
5. **After a free L1 failure, paid L1 owns repair.** Give the free whole-task
   executor one bounded attempt. If it blocks, introduces substantial errors, or
   cannot finish in one straightforward correction, stop that route and pass the
   same task/diff to paid `l1-execute` when Go quota is available. Do not keep
   retrying the free model, and do not make the principal the first repair loop.
   Paid L1 reviews and repairs the free result, then reports the completed diff.
   The principal fixes only a quick convention-level residue after paid L1 has
   finished, or handles the issue after both tiers fail. Route domain, privacy,
   persistence, or architecture issues to L2/L3 instead.
6. **L1 normal owns its free micro child.** Paid `l1-execute` may use
   `l1-micro-execute-free` only for one precise, independent, atomic code-only
   slice when the handoff saves effort. L1 is responsible for the plan, path
   pre-screen, child diff review, straightforward child repair, integration,
   and acceptance. It may run only one micro child at a time. The principal does
   not invoke the micro child directly.
7. **L2 free failures escalate to paid L2.** If `l2-domain-free` blocks or leaves
    substantial errors, route the domain repair to paid `l2-domain` when Go is
    available. Do not route persistence, SQL, transaction, import/export, or
    domain-invariant work to L1. Separate routine docs/config edits still go to
    L1 under rule 4a. If the paid L2 tier is unavailable, pause/report
   instead of retrying the free agent or silently asking the principal to run a
   long repair loop.
8. Use L0 for deterministic verification. Run the actual test, lint, search,
   formatter, and build commands directly; do not delegate command execution to
   an LLM.
8a. **L1 micro-task limits**: `l1-micro-execute-free` is a distinct child of
    paid `l1-execute`, not a whole-task fallback. Only paid L1 can invoke it.
    L1 plans before editing and may delegate one short, independent, code-only
    slice when the handoff saves effort. Do not manufacture slices. The child
    receives an exact outcome, acceptance criteria, file allowlist, phase/domain
    constraints, and exclusions; it cannot use shell, repository-wide search,
    documentation/fixtures, external paths, or further agents. L1 pre-screens
    paths, reviews the actual child diff, repairs straightforward child failures,
    integrates the change, and owns acceptance. Run at most one micro child at a
    time. If the child blocks on ambiguity, private/local data, or repeated
    errors, return control to the paid parent; the principal does not invoke the
    child directly. These are OpenCode boundaries, not an OS sandbox. Any
    `webfetch` is limited to one parent-supplied official URL and a precise
    current-version question; never send project or user content in a URL.

9. **Go quota below 2% or exhausted is not an automatic downgrade.** Record the
   affected window and percentage. Free whole-task agents remain available only
   for their simple, one-pass scopes. If sustained L1/L2 work requires a paid
   model that is unavailable, pause/report or defer until quota recovers.
10. Kimi K2.7 Code is an exceptional, explicitly authorized fallback only after
    paid `l1-execute` has failed or is clearly insufficient for implementation-
    heavy work. Never use it for domain, persistence, architecture, or privacy
    decisions. A free L3 exists separately as `l3-arch-free`
    (`opencode/longcat-2.5-preview-free`), usable only under `principal-free` or
    explicit user authorization; never auto-downgrade an L3 decision. When Go
    L3 is available, prefer it for final architecture/privacy decisions.
11. **Privacy.** Free-tier models may receive repository code, ordinary logs,
    and synthetic/approved dev-test data only. This is not permission to access
    AppData, Temp, local databases, backups, imported legacy files, journal or
    therapy content, secrets, or arbitrary machine-local paths. Never use real
    journal/therapy content as dev/test data without explicit approval.
12. If the free tier's own ~200 req/day pool is exhausted, stop and report rather
    than retry-looping or substituting an unauthorized provider.
13. Free models are rotating limited-time offers — confirm per-role picks via
    `/models` periodically. Do not assume availability or treat free L1 as the
    normal route for sustained implementation.

## Delegation tree

```text
principal
├── L2 domain/persistence
│   ├── L2 owns domain/persistence decisions
│   └── paid L1 handles routine implementation and docs/config slices
├── L3 architecture/privacy
│   ├── L3 owns architecture/privacy decisions
│   └── paid L1 implements the settled code or docs/config contract
└── L1 implementation
    ├── paid l1-execute (default for sustained/iterative work)
    │   └── optional l1-micro-execute-free (one atomic code-only slice)
    └── l1-execute-free (only a very small one-pass task or suitable quota fallback)
        └── on failure, principal routes the task to paid l1-execute
```

L2/L3 retain decisions in their domains. Paid `l1-execute` is the normal
implementation owner for sustained L1 work and owns its plan, integration,
child-diff review, repairs, and acceptance. A free whole-task attempt is not a
prerequisite for paid L1. The principal does not invoke the micro child directly.
L2/L3 file-edit permissions also deny Markdown edits, so their ordinary edit
tools require handing those changes to L1; this tool guard does not make Bash an
OS sandbox or replace the routing instructions.

## How To Apply It

Phase files contain a routing table immediately before their task groups. The
orchestrating agent must:

1. Read the phase routing table and identify the task-group row.
2. Launch the corresponding OpenCode subagent with the exact `provider/model`
   identifier, or configure that model on the selected subagent.
3. Include only the files and context needed by the task.
4. Keep tests, lint, formatting, search, and builds on the L0 path.
5. Report the selected model, fallback (if any), and verification result in the
   task completion note.

## Free-Tier Fallback

The free Zen catalog has a separate ~200 req/day pool shared across free models,
independent of Go budget. Free models are a conservative cost-saving/fallback
route, not the default for sustained implementation. `l1-execute-free` may own
only an exact, very small one-pass task; if that attempt fails, paid
`l1-execute` repairs it when Go is available. The micro child shares the L1
Free model pool but is callable only by paid `l1-execute` for an atomic slice.
For sustained tasks when Go is exhausted/low, pause and report rather than
starting a free route expected to need repeated iterations. Failed L2-free
domain/persistence work routes to paid `l2-domain` when Go returns, never to L1;
this does not change the separate L1 route for settled routine docs/config edits.

| Role | Agent | Model | Why this model |
| --- | --- | --- | --- |
| Supervisor (primary) | `principal-free` | `opencode/glm-5.3-flash-free` | GLM 5.3 Flash Free — Zero-Retention privacy. Free supervisor for quota-exhausted sessions. |
| L1 whole-task fallback | `l1-execute-free` | `opencode/minimax-m3.1-flash-free` | MiniMax M3.1 Flash Free — Zero-Retention privacy. Best free fit here for general implementation while the task remains bounded and one-pass. |
| L1 atomic child implementation | `l1-micro-execute-free` | `opencode/beam-free` | Beam Free — no training use, but 30-day retention. Restricted to tiny, exact code-only edits with no personal or confidential data. |
| L2 domain / persistence | `l2-domain-free` | `opencode/deepseek-v4.1-flash-free` | DeepSeek V4.1 Flash Free — Zero-Retention privacy. Best free fit for Rust, SQL, invariants, and debugging where reasoning quality matters more than latency. |
| L3 architecture (restricted) | `l3-arch-free` | `opencode/longcat-2.5-preview-free` | LongCat 2.5 Preview Free — Zero-Retention privacy. 1M context plus native multimodal image understanding, ideal for secure architecture docs and diagrams. |

Backup order per role (first available wins). Only Zero-Retention models
and the explicitly-authorized supervisor exception are listed. Models with
training-use or trial-logging privacy policies (nemotron, mimo, big-pickle,
ling) are excluded per 2026-10-05 Zen Privacy review.

- `principal-free`: `opencode/glm-5.3-flash-free` (primary) -> `opencode/longcat-2.5-preview-free`
- `l1-execute-free`: `opencode/minimax-m3.1-flash-free` (primary) -> `opencode/glm-5.3-flash-free`
- `l1-micro-execute-free`: `opencode/beam-free` (primary) -> `opencode/glm-5.3-flash-free`
- `l2-domain-free`: `opencode/deepseek-v4.1-flash-free` (primary) -> `opencode/longcat-2.5-preview-free`
- `l3-arch-free`: `opencode/longcat-2.5-preview-free` (primary) -> `opencode/glm-5.3-flash-free`

Verification note (2026-10-19): the active free IDs were checked against the
[OpenCode Console models page](https://opencode.ai/v2/docs/console/models/).
The current zero-retention assignments are `glm-5.3-flash-free`,
`deepseek-v4.1-flash-free`, `minimax-m3.1-flash-free`, and
`longcat-2.5-preview-free`. `beam-free` is limited to the micro-L1 role
because OpenCode documents 30-day retention for safety, despite no training
use. Considered and rejected for other roles:
`opencode/ling-3.0-flash-fin-free`
(finance-specialized), `opencode/jev-1.13-free` (decision-only model, not a
coding agent), `opencode/mimo-v2.5-free` (in Zen docs but absent from local
`/models` output — unverified).

**Privacy verification (2026-10-19):** the OpenCode Console privacy page
documents GLM 5.3 Flash Free, DeepSeek V4.1 Flash Free, MiniMax M3.1 Flash
Free, and LongCat 2.5 Preview Free as zero-retention/no-training-use models.
Beam Free does not use prompts for training but retains them for 30 days, so it
is not assigned to repository-facing roles. Source: [OpenCode Console privacy](https://opencode.ai/v2/docs/console/models/#privacy).

Quota-exhausted sessions are supervised by `principal-free`
(`.opencode/agent/principal-free.md`), whose `permission.task` admits only
the free-tier subagents (`l1-execute-free`, `l2-domain-free`,
`l3-arch-free`) plus `explore` and `general`. The free fallback agents carry
matching deny-first allowlists so no free role can launch a paid Go agent:
`l2-domain-free` may invoke only `l1-execute-free`, `l3-arch-free`, `explore`,
and `general`; `l3-arch-free` may invoke only `l1-execute-free`,
`l2-domain-free`, `explore`, and `general`. Reasoning quality cannot be
benchmarked from here — assignments above are made on context/output window,
attachment support, and stated stability, and should be revisited after real
free-tier usage.

## Registered Agents

The tiers below are registered as named subagents in `.opencode/agent/`, so the
orchestrating agent can invoke them directly with the Task tool. OpenCode also
supports Markdown agent files as configuration: files under `.opencode/agent/`
are auto-discovered, so these agents do not need redundant entries under
`opencode.json`'s `agent` block. This project keeps role prompts and most role
permissions in those files; `opencode.json` sets `default_agent` and
`subagent_depth` and retains the principal's task allowlist:

| Agent file | Model | Use for |
| --- | --- | --- |
| `.opencode/agent/l3-arch.md` | `opencode-go/gpt-5.6-luna` | Architecture, domain decisions, privacy review, migration strategy |
| `.opencode/agent/l2-domain.md` | `opencode-go/gpt-6-luna` | Rust domain logic, SQL migrations, repositories, deep debugging, import/export |
| `.opencode/agent/l1-execute.md` | `opencode-go/qwen3.8-flash` | **Default paid L1** for sustained, cross-cutting, multi-concern, or likely iterative UI/CRUD/tests/configuration/documentation work; may use its micro child for atomic code-only slices |
| `.opencode/agent/fb-kimi.md` | `opencode-go/kimi-k2.7-code` | **Fallback only:** implementation-heavy tool-driven work after L1 is insufficient |
| `.opencode/agent/l1-execute-free.md` | `opencode/minimax-m3.1-flash-free` (backup order above) | Conservative free whole-task route for very small, exact, one-pass code-only or docs/config tasks; on failure, paid L1 takes over when available |
| `.opencode/agent/l1-micro-execute-free.md` | `opencode/beam-free` (backup order above) | **L1 child only:** short, atomic code-only edits; one task at a time; no shell, search, external paths, or private data |
| `.opencode/agent/l2-domain-free.md` | `opencode/deepseek-v4.1-flash-free` (backup order above) | Conservative L2 fallback when Go quota is unavailable; failures escalate to paid L2, never to L1 for domain/persistence repair |
| `.opencode/agent/l3-arch-free.md` | `opencode/longcat-2.5-preview-free` (backup order above) | **Restricted:** free-tier architecture decisions — only in `principal-free` sessions or with explicit user authorization |
| `.opencode/agent/principal-free.md` | `opencode/glm-5.3-flash-free` (backup order above) | **Primary** free-tier supervisor for quota-exhausted sessions; delegates only to free-tier subagents |

> **L2 model mapping resolution (2026-10-07):** the registered `l2-domain` role
> uses `opencode-go/gpt-6-luna`, matching its phase assignments and the
> Rust/persistence routing policy. `opencode-go/gpt-6-luna` remains the
> principal/supervisor model.

## Default supervisor

`.opencode/agent/principal.md` registers the **principal** agent (primary mode,
`opencode-go/gpt-6-luna`). `opencode.json` sets `default_agent: "principal"`,
so every session starts with the supervisor that delegates to the L1/L2/L3
subagents above and reviews their work. `permission.task` limits the principal to
invoking `l3-arch`, `l2-domain`, `l2-domain-free` (free-tier fallback),
`fb-kimi` (exceptional fallback), `l1-execute`, `l1-execute-free` (free-tier
fallback), `explore`, and `general`; L0 work stays on the L0 path and is never
delegated to an LLM. The principal cannot invoke `l1-micro-execute-free`;
paid `l1-execute` alone can call that child. The registered `l2-domain` and
`l3-arch` frontmatter each permit both `l1-execute` and `l1-execute-free`
(verified against `opencode agent list` on OpenCode 1.18.35), so L2/L3 can
route a settled implementation or docs/config contract directly to L1;
whole-task free L1 stays reserved for trivial one-pass edits. `l1-execute` may
use its child for one atomic slice. The Kimi agent is hidden from normal agent
selection.
`opencode.json` sets `subagent_depth: 4` to permit the bounded chain
principal -> L2/L3 -> optional L2/L3 handoff -> L1 -> L1 micro-child;
per-agent task permissions limit which links in that chain are available.

For quota-exhausted sessions, `.opencode/agent/principal-free.md` registers a
free-tier primary supervisor (GLM 5.3 Flash Free — `opencode/glm-5.3-flash-free`; fallback: `opencode/longcat-2.5-preview-free`) whose `permission.task`
admits only `l3-arch-free`, `l2-domain-free`, `l1-execute-free`, `explore`,
and `general`. Switch to it explicitly when Go quota is exhausted and hand
supervision back to `principal` when quota recovers. In this free-only session,
do not start sustained L1 work expected to need repeated iteration; pause/report
until paid `l1-execute` is available.

## Installed skills

Curated agent skills live in `.agents/skills/` (hash-verified in
`skills-lock.json`). `opencode.json` registers the folder via `skills.paths`.
Installed skills (11):

- Autoskills registry (`npx autoskills`): `react-best-practices`,
  `composition-patterns`, `tailwind-css-patterns`,
  `typescript-advanced-types`, `zod`, `vite`, `vitest`, `tauri-v2`.
- Ponytail family (`npx skills add DietrichGebert/ponytail`, MIT):
  `ponytail` (lazy minimal-diff mode), `ponytail-review` (diff review for
  over-engineering only, lists findings, applies nothing), `ponytail-audit`
  (repo-wide over-engineering audit, one-shot ranked report, applies
  nothing).

Irrelevant autoskills candidates (`nodejs-*`, `frontend-design`, `seo`,
`accessibility`) were removed because the project backend is Rust/Tauri and the
frontend is a local desktop app. The detector does not scan `Cargo.toml`, so no
Rust-specific skill is installed. Review any new skill's content before
adopting it; skills become agent instructions and must not conflict with
`AGENTS.md` or the domain/privacy rules. Ponytail findings are advisory only:
`AGENTS.md` invariants (versioned migrations, validation below the command
boundary, mandatory domain tests) prevail over brevity.

L1 work uses the configured L1 model directly; L0 work is never delegated to an
LLM. Config changes require restarting opencode to take effect.

The policy follows OpenCode's agent model configuration documented in the
[Agents guide](https://opencode.ai/docs/agents/) and model identifiers documented
in the [Models guide](https://opencode.ai/docs/models/).
