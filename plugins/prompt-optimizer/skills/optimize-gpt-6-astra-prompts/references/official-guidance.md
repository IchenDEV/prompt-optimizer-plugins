# Official GPT-6 Astra prompting guidance

This is a concise, dated working summary of OpenAI's official guidance, checked on 2026-09-04. Use the live pages for current model IDs, parameters, availability, limits, or pricing. This file is a paraphrase for optimizer use, not a verbatim copy of the source.

## Canonical sources

- [Using GPT-6 Astra](https://developers.openai.com/api/docs/guides/latest-model)
- [Prompt engineering](https://developers.openai.com/api/docs/guides/prompt-engineering)
- Related capability pages linked from the model guide: [Async tool calling](https://developers.openai.com/api/docs/guides/async-tool-calling), [Mid-turn steering](https://developers.openai.com/api/docs/guides/steering), [Reasoning](https://developers.openai.com/api/docs/guides/reasoning), [Prompt caching](https://developers.openai.com/api/docs/guides/prompt-caching)

## Model identity

- Set `model` to `gpt-6-astra` in a Responses API request for tool calling and agentic workflows.
- GPT-6 Astra does not support reasoning effort `none`. If migrating from `none` or `minimal`, start with `low` and compare results; otherwise preserve the current effective effort.
- Chat Completions is supported for some use cases, but tool calling requires the Responses API.
- Remove unsupported sampling parameters such as `temperature`, `top_p`, and `top_logprobs`. Keep reasoning and verbosity controls in API settings, not prompt prose.
- Fast mode / priority service tiers have residency and SLA limits; verify live docs before recommending them.
- Astra also supports capabilities available with GPT-5.6, including computer use, Structured Outputs, streaming, Programmatic Tool Calling, multi-agent orchestration, prompt caching, persisted reasoning, compaction, and pro mode.

## Behavior patterns to optimize for

Astra is more likely than GPT-5.6 Sol to ask clarifying questions, follow long instructions closely (including skills and `AGENTS.md`), produce detailed formatted prose, under-delegate to subagents relative to some harnesses, and over-verify small coding changes. Tune prompts only for the behaviors that matter to the product.

### Initiative and follow-through

Use when the model pauses for clarification or approval too early:

- Infer intent and task scope from instructions and prior context; bias toward action and completion.
- Treat action-shaped user language ("can you…", "help me…", "I want to…") as authorization to do the work, not only to acknowledge capability or propose a plan.
- Persist on reversible, read-only, review, fix, and in-scope work until the intended outcome is complete.
- Ask for approval only after preparing a concrete, reviewable result for external writes, deploys, merges, publishes, or similarly consequential actions.
- Avoid unsolicited hypothetical risk checklists, disclaimer walls, or approval flows that block already-authorized work.
- Calibrate autonomy: the model may also ask non-blocking questions while working; dial that up or down for the product.

### Instruction following

Use when skills or instruction files conflict with the user, or cause early pauses:

- State that explicit user instructions take precedence over skill guidelines when they conflict.
- When a skill causes a permission ask, pause, unfinished work, or divergence from intent, require naming and linking the exact `SKILL.md`, quoting the relevant instruction, and distinguishing explicit requirements from interpretation.
- Audit loaded skills and files such as `AGENTS.md` for unclear or conflicting guidance; Astra is more sensitive to them than earlier models.

### Personality and writing style

Use when default Astra formatting or stock phrasing does not match the product:

- Astra tends toward lists, tables, and Markdown. Request clear concise paragraphs when prose is preferred; use lists only for genuinely parallel or sequential content.
- For technical audiences, prefer plain language and introduce jargon only when it helps the reader.
- Ban recurring slop phrases and contrastive filler ("X, not Y", "Bottom Line:", "In short:", etc.) when they appear in traces.
- State the main point early and develop it; avoid invented compound labels and canned transitions.

### Subagent delegation

Use in multi-agent harnesses:

- Tell the model to parallelize with collaboration tools whenever that can save time or improve quality, whether it is the root or a subagent.
- Require legible spacing and wording in inter-agent messages and final answers that humans may read.

### Testing and verification

Use for coding agents that over-test small changes:

- Do not write tests that merely mirror reversible, low-impact implementation.
- Run tests appropriate to the change; broaden or repeat testing only when new changes, failures, or unresolved concerns justify it.

## Shared prompt principles that still apply

1. Define the outcome, important context, hard constraints, available evidence, and completion bar; leave room for an efficient path.
2. Simplify first. Remove repeated rules, ineffective examples, obsolete process scaffolding, and irrelevant tools.
3. Use decision rules for judgment calls. Reserve absolute terms for real invariants.
4. Preserve explicit user values. Do not replace them with universal defaults or guessed facts.
5. State autonomy and approval boundaries once. Name safe in-scope actions and stop before destructive, external, costly, or scope-expanding actions unless authorized.
6. Expose only relevant tools. State prerequisites, return shapes, error behavior, retries, and stop conditions only when they affect execution.
7. Define required evidence and citation behavior. Treat missing evidence as uncertainty, not automatically as proof of absence.
8. Keep `reasoning.effort`, `text.verbosity`, caching, and other API configuration outside the copy-ready prompt unless the user asks for a full request configuration.
9. Iterate with representative evaluations. Change one prompt group or setting at a time.

## Clarification decision rule

Treat an ambiguity as blocking when it can change one or more of:

- the core outcome or audience;
- the input source or facts the model may use;
- a required schema, artifact, length limit, or language;
- a safety, business, evidence, or permission boundary;
- whether the model may take an external or irreversible action;
- the definition of success or when to stop;
- the intended autonomy level (ask vs assume vs prepare-then-approve).

Ask for the smallest missing field. If a safe default does not change the essential contract, proceed and label the assumption instead of asking.

## Prompt modules for complex tasks

Use only the modules that change behavior:

```text
Role: [function and relevant operating context]

Personality: [observable tone and collaboration choices]

Goal: [user-visible outcome]

Success criteria: [conditions that must be true before finishing]

Autonomy: [what may proceed, what needs a concrete reviewable draft first, what needs explicit approval]

Instruction priority: [user vs skill / AGENTS.md precedence, if relevant]

Constraints: [hard facts, policy, evidence, and side-effect limits]

Tools: [relevant tools, prerequisites, routing, and fallback behavior]

Delegation: [when to use subagents, if the harness supports them]

Output: [required content, structure, length, language, and tone]

Verification: [tests or checks calibrated to change impact]

Stop rules: [when to retry, ask, abstain, hand off, or stop]
```

Do not use this full scaffold for a simple prompt.

## Migration checklist

When moving an application to GPT-6 Astra:

1. Set `model` to `gpt-6-astra`.
2. Preserve effective reasoning effort, mapping `none` / `minimal` to at least `low`.
3. Move tool calling to the Responses API.
4. Remove unsupported sampling / logprobs parameters.
5. If changing effort between turns, prefer `configuration_update` items while keeping the request-level effort stable for cache-friendly prefixes when compatible.
6. Review prompt-caching option names when migrating from GPT-5.5 or earlier (`prompt_cache_options.ttl`).
7. If the model asks for approval too often, add initiative and follow-through guidance rather than raising reasoning effort first.
8. Re-run representative evals after each prompt or setting change.

## Evaluation pattern

Compare representative tasks in controlled stages:

1. current model, prompt, and settings;
2. GPT-6 Astra with the same prompt and preserved effective reasoning;
3. Astra with the smallest prompt edit tied to an observed failure (initiative, instruction priority, style, delegation, or verification);
4. optional API features isolated from the baseline.

Measure task success, output-contract validity, evidence completeness, tool behavior, unnecessary clarification rate, latency, tokens, cost, and preserved user-visible behavior.
