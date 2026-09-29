---
name: select-claude-models
description: Recommend Claude model choices and multi-agent pairings (Opus 想 / Sonnet 干活, advisor-executor, orchestrator-worker). Use when picking among Opus 5.5, Sonnet 5.5, Fable, or Haiku, or designing Claude cost-versus-intelligence routing—not for rewriting a single model's prompt text.
---

# Select Claude Models

Help the user pick Claude models for a workload, especially multi-agent setups where a stronger model plans or advises and a faster model executes. Preserve their cost, latency, and quality constraints. Prefer Anthropic's measured patterns over inventing a custom hierarchy.

Default multi-agent recommendation for most coding and agent products in the Claude 5.5 family: **Opus 想，Sonnet 干活** — Claude Opus 5.5 for judgment, planning, and hard decisions; Claude Sonnet 5.5 for execution, iteration, and bulk tool work.

## Use the official basis

Read [references/official-guidance.md](references/official-guidance.md) for the selection matrix, advisor versus orchestrator patterns, effort defaults, pairing rules, and copy-ready executor prompt blocks. Treat its URLs as canonical and its prose as a dated working summary. Fetch the live Anthropic pages before making claims about current model IDs, prices, compatibility, or beta headers.

When the user then needs prompt text for a chosen model, hand off to the matching optimizer skill (`optimize-claude-sonnet-5-5-prompts`, `optimize-claude-opus-5-prompts`, or `optimize-claude-fable-5-prompts`).

## Follow the workflow

### 1. Capture the workload contract

Identify:

- the task shape: everyday coding, polished docs, repeated agent jobs, long-horizon autonomy, deep research, high-volume low-latency, or mixed;
- whether success is checkable (tests, build, rubric) or judgment-heavy;
- latency sensitivity and budget sensitivity;
- whether the harness already supports tools, subagents, Managed Agents, or the advisor tool;
- whether the user wants a single model, an advisor-executor pair, or an orchestrator-worker team;
- any hard constraints on which models they can call.

Treat these as diagnostic dimensions. Do not invent product architecture the user did not ask for.

### 2. Apply the clarity gate

Ask only when the answer would change the model map. Blocking examples:

- "帮我选模型" with no task description and no cost/latency preference;
- a request that could mean either advisor-executor or orchestrator-worker, and the harness supports only one;
- a requirement to stay under a budget that cannot be inferred from "cheaper" alone.

Otherwise proceed with a conservative default and state the assumption.

### 3. Recommend with the official matrix

Use this starting map unless the user's evals or constraints override it:

| Need | Start with |
| --- | --- |
| Everyday coding, polished docs/slides, repeated well-scoped agent tasks, speed | `claude-sonnet-5-5` |
| Complex judgment, long-horizon agentic coding and knowledge work | `claude-opus-5-5` |
| Highest available capability for demanding long-horizon work | Claude Fable 5.1 (`claude-fable-5-1`) when Opus 5.5 at high effort still falls short |
| Lowest latency / high volume / cheap workers | Claude Haiku 4.5 until Haiku 5.5 is available |

Prefer tuning **effort on the current model** before switching models. Defaults to remember: Sonnet 5.5 API default `high`; Opus 5.5 default `medium`; Claude Code often uses `medium` for Sonnet.

### 4. Choose a multi-model pattern when needed

Prefer one of Anthropic's two documented patterns:

1. **Advisor strategy (recommended default for "Opus 想，Sonnet 干活")**
   - Executor: Sonnet 5.5 runs the loop, tools, and user-facing work.
   - Advisor: Opus 5.5 consulted for approach, stuck recovery, and final review.
   - Fits coding agents, computer use, and multistep research where most turns are mechanical but the plan matters.
   - Does not fit single-turn Q&A, or workloads where every turn needs frontier intelligence.

2. **Orchestrator strategy**
   - Orchestrator: Opus or Fable holds the plan and synthesis.
   - Workers: Sonnet (or Haiku) absorb token-heavy exploration on independent partitions.
   - Fits work larger than one context window, or routine work with a long cost tail.
   - Does not pay when the work is one dependent chain that fits in a single context; lower effort on one model is usually cheaper.

Do not invent a third pattern unless the user already has a harness that requires it. If they already run Opus end-to-end successfully and cost is acceptable, say so; multi-model is optional.

### 5. Attach runtime notes, not prompt clutter

Keep these outside the copy-ready prompt unless the user asks for a full request sketch:

- model IDs and which role each model plays;
- effort starting points;
- advisor tool beta header and `advisor_20260301` pairing rules for Sonnet 5.5 executors;
- that Sonnet 5.5 rejects forced `tool_choice` of type `tool`/`any`, so advisor timing is steered by prompt or nudge, not forced choice;
- that accepted Sonnet 5.5 advisors return encrypted advice (`advisor_redacted_result`);
- prompt-caching implications of changing effort mid-session.

When executor prompt steering is needed for the advisor pattern, copy only the relevant blocks from [references/official-guidance.md](references/official-guidance.md).

### 6. Return the result

Honor the user's requested format. Otherwise return:

1. `推荐方案` or `Recommendation`: one short paragraph naming the pattern and models.
2. `角色分工` or `Role map`: a compact list of who plans/advises vs who executes.
3. `运行时设置` or `Runtime settings`: model IDs, effort starts, advisor/orchestrator notes when relevant.
4. `提示词要点` or `Prompt notes`: only the blocks or deletions the chosen pattern needs.
5. `假设` or `Assumptions`: only assumptions actually made.

Answer in the user's language. Keep the response pointed; do not dump the whole official matrix unless asked.

## Avoid common regressions

- Do not default every task to Fable or to `max` effort.
- Do not put Opus on every token when Sonnet can execute a clear spec.
- Do not put Sonnet alone on the hardest long-horizon judgment work just to save money without measuring quality.
- Do not recommend Sonnet 5, Opus 4.8, or Opus 4.7 as advisors for a Sonnet 5.5 executor.
- Do not build an orchestrator for a single dependent chain that fits one context window.
- Do not bury model IDs and beta headers inside user-facing prompt prose.
- Do not invent prices; point to live pricing pages when cost comparisons matter.

## Completion bar

Finish only when the user has a concrete model map, the pattern matches their workload, blocking ambiguity is resolved or explicitly assumed, and runtime notes are separated from prompt text.
