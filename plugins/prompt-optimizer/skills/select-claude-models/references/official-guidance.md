# Official Claude model selection and multi-agent guidance

A concise working summary of Anthropic's official documentation, checked on 2026-09-29. Use the live pages for current model IDs, parameters, availability, limits, compatibility, or pricing.

## Canonical sources

- [Choosing the right model](https://platform.claude.com/docs/en/about-claude/models/choosing-a-model)
- [Building with Claude Sonnet 5.5](https://claude.dev/blog/building-with-claude-sonnet-5-5/)
- [Optimizing for cost and intelligence](https://platform.claude.com/docs/en/about-claude/models/optimizing-for-cost-and-intelligence)
- [Advisor tool](https://platform.claude.com/docs/en/agents-and-tools/tool-use/advisor-tool)
- [The advisor strategy](https://claude.com/blog/the-advisor-strategy)
- [Prompting Claude Sonnet 5.5](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-sonnet-5-5)
- [Sonnet 5.5 migration guide](https://platform.claude.com/docs/en/models/sonnet-5-5/migration-guide)

## Single-model starting matrix

Most production Claude 5.5 workloads start with Opus 5.5 for complex work and Sonnet 5.5 for everyday work. Tuning effort is often a better first lever than switching models.

| When you need... | Start with | Example use cases |
| --- | --- | --- |
| Highest available capability | Claude Fable 5.1 | Multi-hour agents, deep research, analysis carried through to finished docs/decks |
| Complex agentic coding and enterprise work | Claude Opus 5.5 | Multihour autonomous coding, large refactors, complex systems engineering, vision-heavy workflows, computer use |
| Speed and capability for everyday coding, agent, and enterprise workloads | Claude Sonnet 5.5 | Bug fixes, feature iteration, data analysis, polished documents/slides, repeated investigation/review/drafting |
| Lowest latency and price with strong reasoning | Claude Haiku 4.5 | Real-time apps, high-volume processing, cost-sensitive sub-agent tasks |

Sonnet 5.5 fits best when the task has a clear spec and a way to check the result. Opus 5.5 fits complex work requiring careful judgment. If Sonnet needs `xhigh` or `max` effort by default to meet the bar, consider Opus instead.

### Effort defaults worth remembering

- Claude Sonnet 5.5: API default `high`; Claude Code default `medium`. Agentic well-specified tasks often start at `medium`; chat/latency-sensitive at `medium` or `low`.
- Claude Opus 5.5: default `medium`; raise or lower from evals.
- Claude Opus 5 / Fable 5.1: often start from documented defaults (`high` on several of these surfaces) and sweep.
- Re-run effort sweeps after model upgrades; levels are recalibrated across 5.5 models.

## Multi-model patterns

Anthropic documents two patterns that bill most tokens at a lower rate while keeping frontier judgment where it matters.

### Advisor strategy — Opus 想，Sonnet 干活

Sonnet (or Haiku) is the **executor**: it runs the task end-to-end, calls tools, reads results, and produces user-facing output. Opus (or Fable) is the **advisor**: consulted mid-generation for a plan, correction, or stop signal. The advisor never calls tools and never produces user-facing output.

```text
User / harness
    → Sonnet 5.5 executor (tools, iteration, deliverables)
         ⇄ Opus 5.5 advisor (plan / course-correct / review)
```

**When it fits**

- Coding agents, computer use, multistep research pipelines.
- Most turns are mechanical, but an excellent plan matters.
- You currently run Sonnet on complex tasks and want near-Opus quality without Opus-solo cost.
- You currently run Haiku and want a step up without promoting every turn.

**When it fits poorly**

- Single-turn Q&A with nothing to plan.
- Every turn genuinely needs frontier capability.
- The executor is already close to the advisor's capability, so consults rarely help.

**Recommended 5.5 pairing**

| Role | Model ID |
| --- | --- |
| Executor | `claude-sonnet-5-5` |
| Advisor | `claude-opus-5-5` |

Sonnet 5.5 as executor also accepts Opus 5 and Sonnet 5.5 itself as advisors. It rejects Opus 4.8, Opus 4.7, and Sonnet 5. Advice from Sonnet 5.5 / Opus 5 / Opus 5.5 advisors returns as encrypted `advisor_redacted_result` blocks.

**API shape (conceptual)**

- Beta header: `advisor-tool-2026-03-01`
- Tool entry: `{"type": "advisor_20260301", "name": "advisor", "model": "claude-opus-5-5", "max_uses": <cap>}`
- Sonnet 5.5 rejects forced `tool_choice` of type `tool` or `any`; steer consult timing with the system prompt or a rare harness nudge, not forced choice.
- Advisor tokens bill at advisor rates; executor tokens at executor rates. Cap with `max_uses`.

### Orchestrator strategy — frontier plans，workers execute

A frontier **orchestrator** (Opus or Fable) holds the loop: decompose, dispatch, synthesize. Lower-cost **workers** (Sonnet or Haiku) absorb token-heavy exploration on independent partitions.

**When it pays**

- Work larger than one context window (partitioned corpus / many independent files or cases).
- Routine work with a long cost tail, where a frontier solo agent occasionally spirals.

**When it does not pay**

- One dependent chain that fits in a single context.
- Work a single model at lower effort already solves well.
- In those cases, Anthropic's measurements favor one model at lower effort over building an orchestrator.

**Typical 5.5 map**

| Role | Model ID |
| --- | --- |
| Orchestrator / coordinator | `claude-opus-5-5` or `claude-fable-5-1` |
| Workers | `claude-sonnet-5-5` (or Haiku for cheaper bulk) |

Prefer the advisor strategy for "Opus 想，Sonnet 干活" coding agents unless the user explicitly needs partition parallelism or Managed Agents multiagent orchestration.

## Decision tree

1. Is the task well-scoped with a checkable result? → start Sonnet 5.5.
2. Is it long-horizon judgment / complex agentic coding? → start Opus 5.5.
3. Do evals at high Opus effort still fail? → consider Fable 5.1.
4. Is cost too high while quality is fine? → lower effort before switching down.
5. Does a lower-cost executor stall only on hard decisions? → advisor strategy (Sonnet executor + Opus advisor).
6. Does the work exceed one context window or show a routine cost tail? → orchestrator strategy.
7. Otherwise stay on one model.

## Suggested executor system prompt for coding + advisor

Without steering, coding executors tend to under-call the advisor. For consistent timing (roughly two to three consults per task), prepend these blocks before any other advisor-related sentences.

### Timing

```text
You have access to an `advisor` tool backed by a stronger reviewer model. It takes NO parameters — when you call advisor(), your entire conversation history is automatically forwarded. They see the task, every tool call you've made, every result you've seen.

Call advisor BEFORE substantive work — before writing, before committing to an interpretation, before building on an assumption. If the task requires orientation first (finding files, fetching a source, seeing what's there), do that, then call advisor. Orientation is not substantive work. Writing, editing, and declaring an answer are.

Also call advisor:
- When you believe the task is complete. BEFORE this call, make your deliverable durable: write the file, save the result, commit the change. The advisor call takes time; if the session ends during it, a durable result persists and an unwritten one doesn't.
- When stuck — errors recurring, approach not converging, results that don't fit.
- When considering a change of approach.

On tasks longer than a few steps, call advisor at least once before committing to an approach and once before declaring done. On short reactive tasks where the next action is dictated by tool output you just read, you don't need to keep calling — the advisor adds most of its value on the first call, before the approach crystallizes.
```

### How to treat advice

```text
Give the advice serious weight. If you follow a step and it fails empirically, or you have primary-source evidence that contradicts a specific claim (the file says X, the paper states Y), adapt. A passing self-test is not evidence the advice is wrong — it's evidence your test doesn't check what the advice is checking.

If you've already retrieved data pointing one way and the advisor points another: don't silently switch. Surface the conflict in one more advisor call — "I found X, you suggest Y, which constraint breaks the tie?" The advisor saw your evidence but may have underweighted it; a reconcile call is cheaper than committing to the wrong branch.
```

### Soft-cap advisor length (place in the user message)

```text
(Advisor: please keep your guidance under 80 words — I need a focused starting point, not a comprehensive plan.)
```

Ask for roughly 80% of your true ceiling; the limit is soft.

If the system prompt already contains restraint language ("reserve the advisor for genuine uncertainty"), do not also fire a frequent harness nudge — the two instructions conflict.

## Free cost wins before multi-model

Before adding a second model, Anthropic recommends prompt caching, token hygiene, and an effort sweep on the current model. Multi-model is a narrower lever: it pays when the capability gap is real and the consult or delegation rate is healthy.

## Evaluation pattern

1. Run the same eval suite on executor-solo, advisor pairing, and advisor-solo (or orchestrator vs solo).
2. Compare cost **per completed task**, not only per-token list prices.
3. Measure advisor consult rate; a pairing that almost never consults is usually the wrong pattern for that workload.
4. Change one variable at a time: model, effort, or prompt steering.
