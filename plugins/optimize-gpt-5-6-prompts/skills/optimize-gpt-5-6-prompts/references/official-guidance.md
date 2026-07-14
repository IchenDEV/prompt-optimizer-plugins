# Official GPT-5.6 prompting guidance

This is a concise, dated working summary of OpenAI's official guidance, checked on 2026-07-15. Use the live pages for current model IDs, parameters, availability, limits, or pricing.

## Canonical sources

- [Using GPT-5.6](https://developers.openai.com/api/docs/guides/latest-model)
- [Prompting guidance for GPT-5.6 Sol](https://developers.openai.com/api/docs/guides/prompt-guidance-gpt-5p6)
- [Upgrading to GPT-5.6 Sol](https://developers.openai.com/api/docs/guides/upgrading-to-gpt-5p6-sol)
- [Prompt engineering](https://developers.openai.com/api/docs/guides/prompt-engineering)

## Model-specific principles

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

## Clarification decision rule

Treat an ambiguity as blocking when it can change one or more of:

- the core outcome or audience;
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

## Runtime settings versus prompt text

- Control default response detail with the API's `text.verbosity` value (`low`, `medium`, or `high`) when available; keep task-specific must-include content in the prompt.
- Treat `reasoning.effort` as an evaluation variable, not prose to embed in the prompt. GPT-5.6 supports multiple effort levels, and higher is not automatically better.
- Replace "think step by step" with explicit success criteria, evidence, validation, and failure behavior.
- Keep model choice, reasoning mode, prompt caching, persisted reasoning, and other API configuration separate from the copy-ready prompt unless the user explicitly asks for a full request configuration.

## Evaluation pattern

For an existing application, compare representative tasks in controlled stages:

1. current model, prompt, and settings;
2. GPT-5.6 with the same prompt and preserved effective reasoning;
3. GPT-5.6 with one lower reasoning setting when latency or cost matters;
4. GPT-5.6 with the smallest prompt edit tied to an observed failure;
5. optional features isolated from the baseline.

Measure task success, output-contract validity, evidence completeness, tool behavior, latency, tokens, cost, and preserved user-visible behavior. Do not call a prompt improved merely because it is shorter or uses fewer tool calls.
