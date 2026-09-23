# Official GPT-6 Sol and Luna prompting guidance

This is a concise, dated working summary of OpenAI's official guidance, checked on 2026-09-23. Use the live pages for current model IDs, parameters, availability, limits, or pricing. This file is a paraphrase for optimizer use, not a verbatim copy of the source. For GPT-6 Astra, use the `optimize-gpt-6-astra-prompts` skill instead.

## Canonical sources

- [Using GPT-6](https://developers.openai.com/api/docs/guides/latest-model) — family guide (Astra, Sol, Luna)
- [GPT-6 Sol model](https://developers.openai.com/api/docs/models/gpt-6-sol)
- [GPT-6 Luna model](https://developers.openai.com/api/docs/models/gpt-6-luna)
- [Prompt engineering](https://developers.openai.com/api/docs/guides/prompt-engineering)
- Related capability pages linked from the model guide: [Async tool calling](https://developers.openai.com/api/docs/guides/async-tool-calling), [Mid-turn steering](https://developers.openai.com/api/docs/guides/steering), [Reasoning](https://developers.openai.com/api/docs/guides/reasoning), [Prompt caching](https://developers.openai.com/api/docs/guides/prompt-caching)

## Model identity and routing

| Model | API ID | Role |
| --- | --- | --- |
| GPT-6 Sol | `gpt-6-sol` | Strong reasoning for complex coding and agentic workflows |
| GPT-6 Luna | `gpt-6-luna` | Most efficient tier for focused, high-volume, or cost/latency-sensitive work |
| GPT-6 Astra | `gpt-6-astra` | Highest capability / alignment — use the Astra skill |

There is no GPT-6 Terra. When migrating GPT-5.6 Terra workloads, choose Sol or Luna by role: keep demanding agentic/coding work on Sol; move high-volume or strict-latency routes to Luna.

## Runtime settings versus prompt text

- Prefer the Responses API for tool calling and for reasoning with tools.
- GPT-6 Sol and Luna support reasoning effort `none`, `low`, `medium` (default), `high`, `xhigh`, and `max`. Preserve the current effective effort on migrate; if the prior setting was `minimal`, start with `low` and compare.
- Chat Completions function calling works for Sol/Luna only with `reasoning_effort: "none"`. Use Responses when tools run with non-`none` reasoning.
- When reasoning effort is not `none`, remove unsupported sampling parameters such as `temperature`, `top_p`, and `top_logprobs`. Keep reasoning and verbosity controls in API settings, not prompt prose.
- EU data residency for Sol and Luna is available only with Standard processing; verify live docs before recommending Fast mode or residency options.
- Sol and Luna inherit GPT-6 family capabilities also available with GPT-5.6, including computer use, Structured Outputs, streaming, Programmatic Tool Calling, multi-agent orchestration, prompt caching, persisted reasoning, compaction, and pro mode.
- When changing effort mid-conversation, prefer `configuration_update` items while keeping request-level `reasoning.effort` stable for cache-friendly prefixes when compatible.
- When migrating from GPT-5.5 or earlier, replace `prompt_cache_retention` with `prompt_cache_options.ttl` (for example `"30m"`).

## Prompting for the GPT-6 family on Sol/Luna

OpenAI's latest-model prompting snippets are published as a starting point across the GPT-6 family and address behaviors observed with Astra. Evaluate them on Sol or Luna; do not paste every Astra mitigation into every Sol/Luna prompt.

Shared principles that still apply:

1. Define the outcome, important context, hard constraints, available evidence, and completion bar; leave room for the model to choose an efficient path.
2. Simplify first. Remove repeated rules, ineffective examples, obsolete process scaffolding, and irrelevant tools. Keep product requirements and measured fixes.
3. Use decision rules for judgment calls. Reserve absolute terms for real invariants.
4. Preserve explicit user values. Do not replace them with universal defaults, keyword maps, or guessed facts.
5. State autonomy and approval boundaries once. Name safe in-scope actions and stop before destructive, external, costly, or scope-expanding actions unless authorized.
6. Expose only relevant tools. State prerequisites, return shapes, error behavior, retries, and stop conditions only when they affect execution.
7. Define required evidence and citation behavior. Treat missing evidence as uncertainty, not automatically as proof of absence.
8. For long work, request a short initial preamble and sparse outcome-based updates rather than narration of routine calls.
9. Validate outputs with the most relevant tests, render checks, or smoke checks. If validation is unavailable, state the gap and next-best check.
10. Iterate with representative evaluations. Change one prompt group or setting at a time so regressions remain attributable.

Optional family mitigations (apply when Sol/Luna traces show the same issue):

- Initiative and follow-through when the model asks too early or stops after a partial result.
- Explicit user-over-skill instruction priority when skills/`AGENTS.md` divert work.
- Writing-style controls when default formatting or stock phrases hurt the product.
- Subagent delegation and verification calibration for multi-agent or coding harnesses.

## Clarification decision rule

Treat an ambiguity as blocking when it can change one or more of:

- the core outcome or audience;
- Sol vs Luna workload role when that choice changes settings or prompt density;
- the input source or facts the model may use;
- a required schema, artifact, length limit, or language;
- a safety, business, evidence, or permission boundary;
- whether the model may take an external or irreversible action;
- the definition of success or when to stop.

Ask for the smallest missing field. If a safe default does not change the essential contract, proceed and label the assumption instead of asking.

Never default an unprovided permission, external side effect, evidence source, policy, or business decision. If a rough request contains only a generic verb and topic while several materially different prompt contracts are plausible, clarify the intended use before drafting.

## Prompt modules for complex tasks

Use only the modules that change behavior:

```text
Role: [function and relevant operating context]

Personality: [observable tone and collaboration choices]

Goal: [user-visible outcome]

Success criteria: [conditions that must be true before finishing]

Constraints: [hard facts, policy, evidence, and side-effect limits]

Tools: [relevant tools, prerequisites, routing, and fallback behavior]

Output: [required content, structure, length, language, and tone]

Stop rules: [when to retry, ask, abstain, hand off, or stop]
```

Do not use this full scaffold for a simple prompt.

## Migration checklist

When moving an application to GPT-6 Sol or Luna:

1. Set `model` to `gpt-6-sol` or `gpt-6-luna` by workload role.
2. Preserve effective reasoning effort (`none` is valid for Sol/Luna).
3. Use Responses for tools with reasoning; Chat Completions tools only with `none`.
4. Remove unsupported sampling / logprobs parameters when effort is not `none`.
5. Review prompt-caching option names when migrating from GPT-5.5 or earlier.
6. Re-run representative evals after each prompt or setting change; add family mitigations only for measured failures.

## Evaluation pattern

For an existing application, compare representative tasks in controlled stages:

1. current model, prompt, and settings;
2. GPT-6 Sol or Luna with the same prompt and preserved effective reasoning;
3. the same GPT-6 model with one lower reasoning setting when latency or cost matters;
4. the smallest prompt edit tied to an observed failure;
5. optional API features isolated from the baseline.

Measure task success, output-contract validity, evidence completeness, tool behavior, latency, tokens, cost, and preserved user-visible behavior. Do not call a prompt improved merely because it is shorter or uses fewer tool calls.
