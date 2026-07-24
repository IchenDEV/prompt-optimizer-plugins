# Official Claude Opus 5 prompting guidance

A concise working summary of Anthropic's official documentation, checked on 2026-07-25. Use the live pages for current model IDs, parameters, availability, limits, or pricing.

## Canonical sources

- [Prompting Claude Opus 5](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-opus-5)
- [What's new in Claude Opus 5](https://platform.claude.com/docs/en/about-claude/models/whats-new-opus-5)
- [Prompting best practices](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices)
- [Effort](https://platform.claude.com/docs/en/build-with-claude/effort)
- [Migration guide](https://platform.claude.com/docs/en/about-claude/models/migration-guide)

## Model facts

- API model ID `claude-opus-5`. Also available as `anthropic.claude-opus-5` on Amazon Bedrock, `claude-opus-5` on Google Cloud, and through Microsoft Foundry.
- 1M-token context window as both the default and the maximum; there is no smaller context variant. 128k max output tokens.
- Thinking is on by default. `thinking: {"type": "adaptive"}` remains valid and equivalent to the default.
- Built for complex agentic coding and enterprise work, with the largest gains in deep reasoning, long-horizon agentic tasks, and test-time compute scaling.
- Performs well out of the box on existing Claude Opus 4.8 prompts.

## Behavior differences that drive prompting

1. **Longer visible responses.** Default user-facing responses run longer than prior Opus models'. Effort controls how much the model thinks, not how much it says; lowering effort does not reliably shorten the visible response. Prompt for length explicitly.
2. **More narration during agentic work.** The model announces what it is about to do, and per-message output in agentic sessions is longer. It responds well to an explicit description of update cadence and shape.
3. **Longer written deliverables.** Files written to disk (reports, Markdown documents, summaries) run longer than on prior models. Calibrate document length separately from conversational verbosity.
4. **Self-verification without being told.** Explicit verification instructions ("include a final verification step for any non-trivial task," "use a subagent to verify," "double-check your answer," "re-verify before responding") cause over-verification. Removing them reduces wasted tokens with no loss in quality. The same applies to legacy harness scaffolding that adds separate verification steps.
5. **Scope expansion.** The model can add steps that were not requested or apply its own judgment about what the task should be. Constrain scope explicitly for narrow tasks.
6. **Eager delegation.** The model delegates to subagents more readily. Delegation pays off on genuinely independent, sizeable tracks of work but multiplies cost and time on small tasks. Give explicit criteria or deterministic caps.
7. **More correction narration.** The model narrates corrections to its earlier statements more than prior models, which can be undesirable in user-facing products. Set a threshold for which corrections are worth stating.
8. **Literal review filters.** "Only report high-severity issues" or "be conservative" is followed literally and yields fewer findings. Ask for everything and filter in a separate pass. Review precision and recall stay high even at lower effort, which supports a fast pass at review time and a thorough pass later.

## Capability notes

- **Agentic coding:** strongest on multi-file features, larger refactors, and end-to-end work. It completes tasks rather than leaving stubs or placeholders, and performs best given the complete task specification up front and left to run.
- **Vision:** strong on charts, documents, diagrams, and UI/frontend visual replication. Re-validate prompt-side vision workarounds tuned for prior models. Performance is strongest with tools to iteratively analyze, crop, and visually verify; tool use is a more cost-effective lever than thinking alone.
- **Long context:** instruction following, tool calling, and reasoning stay consistent throughout the 1M-token window.
- **Office and documents:** generates complex multi-sheet spreadsheets with non-trivial formulas and well-structured slide decks. Supply the specific styles or templates to follow.
- **Multi-agent coordination:** effective writer-verifier patterns with few cases of agents overwriting each other's work. Cap delegation for cost-sensitive workloads.

## Running with thinking disabled

Thinking can be disabled only at effort `high` or below; `thinking: {"type": "disabled"}` with `xhigh` or `max` returns a 400 error. This is a breaking change from Claude Opus 4.8, where the two settings were independent.

With thinking disabled, two artifacts can appear:

- **Tool calls as text.** The model occasionally writes a tool call into user-facing text instead of emitting a structured `tool_use` block. The turn completes normally, the call never runs, and in agentic loops the leaked text stays in conversation history and affects later turns. Most common on tool-heavy workloads such as search.
- **Internal XML tags in output.** The model can emit `<thinking>` or other internal tags into its visible response. A system-prompt rule instructing the model not to think or not to reason increases leakage; remove it. A general instruction against internal or system XML tags works better than one naming the tags.

The primary mitigation for both is to keep thinking enabled and control token cost with lower effort instead of disabling thinking.

## Runtime settings versus prompt text

- The API default effort is `high`. Set effort explicitly to use another level.
- Start at `xhigh` for coding and agentic work; use `high` for most other intelligence-sensitive workloads; use `low` and `medium` liberally as the primary control for token cost and latency wherever evals show quality holds; step up to `max` when a task justifies unconstrained token spending.
- `low` and `medium` on Opus 5 are stronger than the same settings on earlier Opus models. Re-run an effort sweep rather than carrying settings over from a prior model.
- At `xhigh` or `max`, set a large `max_tokens` so the model has room to think and act across subagents and tool calls; 64k is a reasonable starting point. `max_tokens` is a hard limit on total output, thinking plus response text.
- Effort shapes the rendered prompt, so changing it between requests invalidates cached prefixes. Pick a level at the start of a cached conversation and keep it constant.
- The minimum cacheable prompt length is 512 tokens, down from 1,024 on Claude Opus 4.8.
- Mid-conversation tool changes and the `"default"` server-side fallback mode are beta features requiring their own headers.
- Keep model choice, effort, retries, refusal handling, tool schemas, and fallback routing outside the prompt unless the user asks for a complete request configuration.
- Raw chain of thought is not returned. Asking the model to reproduce hidden reasoning can trigger a reasoning-extraction refusal. Request concise rationale, evidence, checks, and uncertainty instead.

## Migration and evaluation pattern

1. Update the model ID to `claude-opus-5` and run the existing prompt unchanged; it usually ports well from Claude Opus 4.8.
2. Review the two API behavior changes: thinking on by default, and disabling thinking rejected at `xhigh` or `max`. Revisit `max_tokens` for workloads that previously ran without thinking.
3. Inspect observed failures before editing prompt text. Most fixes are removals.
4. Delete verification, re-check, anti-thinking, and conservatism instructions carried over from earlier models.
5. Add at most one targeted instruction per observed behavior: length, narration, document length, scope, delegation, correction narration.
6. Re-run an effort sweep on representative evals rather than reusing prior-model defaults.
7. Verify output-contract adherence, evidence quality, tool behavior, completion accuracy, latency, token use, and cost. Change one prompt group or runtime setting at a time so regressions remain attributable.
