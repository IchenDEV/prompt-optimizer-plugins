---
name: optimize-claude-fable-5-1-prompts
description: Clarify, audit, migrate, and rewrite prompts for Claude Fable 5.1 using Anthropic's official guidance. Use when a user explicitly targets Fable or Fabel 5.1, migrates a Fable 5 prompt to 5.1, debugs observed 5.1 behavior, or needs guided clarification before a copy-ready 5.1 prompt. Preserve intent and change only what the task or observed behavior requires.
---

# Optimize Claude Fable 5.1 Prompts

Produce a copy-ready Fable 5.1 prompt without changing the user's intent, facts, constraints, permissions, or required output.

## Read the relevant references

- Read [references/official-guidance.md](references/official-guidance.md) before diagnosing or changing Fable 5.1-specific behavior. It covers every section of the model-specific prompting page plus linked runtime, history, and migration requirements.
- Read [references/shared-prompting-guidance.md](references/shared-prompting-guidance.md) when designing or auditing prompt structure, examples, XML, long context, output control, tool use, thinking, agentic systems, capability-specific prompting, or migration. Skip it only for a narrowly model-specific diagnosis.

Fetch the live Anthropic pages before stating current model IDs, parameters, availability, limits, pricing, or beta status. Use the official name `Claude Fable 5.1`; treat `Fabel` as a likely spelling variant unless context indicates another product.

## Optimize the prompt

1. Capture the contract: intended outcome, inputs, context, hard constraints, audience, output shape, evidence needs, action level, authorization boundaries, and completion criteria.
2. Preserve system, developer, user, and tool-description layers. Do not silently move instructions between trust levels.
3. Ask one to three questions only when missing information would materially change the goal, source, permissions, output, or success criteria. Complete any independent work first, and do not label an incomplete rewrite as final.
4. Otherwise make the smallest useful rewrite. Existing Fable 5 prompts should be the baseline; change them only for the clarified contract or an observed 5.1 behavior.
5. Apply only the relevant model-specific mitigation from the reference. Do not add every progress, batching, search, formatting, completion, editing, or subagent instruction by default.
6. Use XML only when it clarifies mixed instructions, context, variable inputs, examples, documents, metadata, output requirements, source-quotation behavior, or compaction. Follow the reference's tag and hierarchy patterns.
7. Keep effort, thinking display, append-only history, tool choice, refusal handling, fallback, server-side compaction configuration, and beta headers outside ordinary prompt prose unless the user requests runtime configuration. For client-side compaction, use a prompt-level preservation contract when requested.
8. Never request hidden chain of thought. Ask for conclusions, concise rationale, evidence, checks, assumptions, and uncertainty instead.

## Validate silently

Confirm that the result:

- preserves intent, facts, constraints, required fields, and prompt-layer boundaries;
- makes assessment, recommendation, drafting, implementation, and execution boundaries unambiguous;
- can be executed with the stated inputs and tools;
- grounds claims and completion in supplied evidence or actual tool results;
- uses XML and 5.1-specific mitigations only when they address the task or observed behavior;
- contains no unsupported runtime assumptions or hidden-reasoning request;
- contains no redundant or unsupported instruction while retaining the context, examples, and structure needed for reliable behavior.

If a blocking ambiguity remains, ask only for the smallest missing information.

## Return the result

Honor the user's requested format. Otherwise return:

1. `Optimized prompt`: one copy-ready fenced block.
2. `Key changes`: at most five concise bullets, only when useful.
3. `Assumptions`: only assumptions actually made.
4. `API settings`: only when requested or essential.

When the user asks for prompt-only output, return only the prompt. Finish only when the prompt is copy-ready and the validation checks pass.
