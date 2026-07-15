---
name: optimize-claude-fable-5-prompts
description: Clarify, audit, and rewrite rough or existing prompts for Claude Fable 5 using Anthropic's official prompting guidance. Use when a user asks to optimize, improve, migrate, debug, or design a prompt for Claude Fable 5, including requests phrased as Fable or Fabel prompt optimization, Fable/Fabel 提示词优化, or when an underspecified idea needs guided clarification before a copy-ready prompt. Ask only for missing information that would materially change the result; otherwise preserve intent and produce a lean, context-rich prompt with explicit outcomes, boundaries, evidence, output requirements, and completion criteria.
---

# Optimize Claude Fable 5 Prompts

Turn rough ideas and existing prompt stacks into copy-ready prompts for Claude Fable 5. Preserve the user's intent and hard constraints, clarify genuinely blocking gaps, and keep the result no more elaborate than the task requires.

## Use the official basis

Read [references/official-guidance.md](references/official-guidance.md) when model-specific rationale, API-setting guidance, migration details, or long-context patterns are relevant. Treat its URLs as canonical sources and its prose as a dated working summary. Fetch the live Anthropic pages before making claims about current model IDs, parameters, availability, limits, or pricing.

Use the official model name `Claude Fable 5`. Treat `Fabel` as a likely spelling variant when it appears in a prompt-optimization request, unless context indicates a different product.

## Follow the workflow

### 1. Capture the prompt contract

Identify the following from the user's message, pasted prompt, or referenced file:

- the user-visible outcome and why it matters;
- the inputs, source material, and context Claude will receive;
- hard constraints, facts, policies, and values to preserve;
- the required output, audience, language, and level of detail;
- tools, evidence, or verification needed for success;
- whether Claude should assess, recommend, draft, implement, or take external action;
- authorized actions and actions that require approval;
- stopping and fallback behavior when a dependency is unavailable.

Treat these as diagnostic dimensions, not mandatory prompt sections. Keep a simple rewriting prompt simple.

If the user supplies a layered prompt stack, preserve the separation between system, developer, user, and tool-description content. Do not silently move instructions between trust levels.

### 2. Apply the clarity gate

Ask a question only when the missing or conflicting information is blocking: two reasonable answers would produce materially different prompts, outputs, permissions, evidence, or success criteria.

Treat these gaps as blocking by default:

- a new-prompt request that contains only a broad topic and a generic verb such as "analyze," "write," or "build," while the intended decision, audience, or deliverable could vary materially;
- a prompt that can send, publish, purchase, delete, change records, modify terms, or affect another person when authorization and approval boundaries are unclear;
- a task that depends on an unspecified source, system, policy, tool, or evidence set whose choice changes what Claude may conclude or do;
- conflicting instructions about scope, output, permissions, evidence, or completion;
- a strict schema whose required fields or field semantics cannot be inferred safely.

Do not turn blocking gaps into placeholders, invented policies, assumed access, or a provisional workflow. A conservative execution policy does not cure an undefined goal or missing authorization.

Proceed when the core contract is clear and the missing detail is cosmetic or safely local. For an existing concrete prompt, preserve its contract and do not demand optional context. If the user explicitly delegates a nonblocking choice, make a conservative assumption, state it after the prompt, and continue.

When clarification is blocking:

1. Ask one to three high-information questions in one turn.
2. Briefly explain choices when the user may not know the terminology.
3. Offer concrete options or a short fill-in reply when that reduces effort.
4. Do not present a final or provisional optimized prompt yet.
5. Reapply the clarity gate after the reply. Ask again only if material ambiguity remains.

Ask for the smallest missing fact. Do not interrogate the user about optional tone, minor headings, or preferences that can be handled with a conservative assumption.

Use these examples for calibration:

- For "analyze customer-service data and find problems," ask what decision the analysis should support, which data and period are available, and who will use the result. Do not manufacture a generic analytics prompt.
- For "find overdue accounts, message customers, and adjust terms," ask which systems and records are in scope, whether Claude may send or only draft, and what approval or policy governs term changes.
- For "rewrite this paragraph professionally" with the paragraph supplied, proceed unless the audience or compliance context is materially ambiguous.

### 3. Rewrite for Claude Fable 5

Apply only changes that materially improve the prompt contract:

- State the intended outcome, relevant context, and reason for the task. Explaining why helps Claude generalize correctly.
- Keep instructions direct and relatively lean. Fable 5 follows instructions strongly, so remove duplicated rules, obsolete scaffolding, excessive emphasis, and unnecessary procedural detail.
- Preserve explicit facts, values, required fields, and constraints exactly unless the user asks to change them.
- State whether Claude should assess, recommend, draft, implement, or execute. A request for review or explanation should not silently become authorization to modify anything.
- Define scope and authorization once. For high-effort or agentic work, explicitly exclude unrequested features, refactors, abstractions, cleanup, and speculative fallback systems when they are plausible failure modes.
- Require progress and completion claims to be grounded in actual tool results or supplied evidence. If work is skipped or verification fails, require an exact statement of the gap.
- Tell Claude what to verify and what constitutes completion. Use fresh-context verification or independent reviewers only when the task genuinely benefits from them.
- For long-running work, say to act once enough information is available, avoid re-deriving established facts or relitigating decisions, and report outcome-based progress rather than narrating every step.
- Pause only for destructive or irreversible actions, real scope changes, or information only the user can supply. Continue through safe, reversible, authorized work.
- Make final summaries readable: lead with the outcome, use complete sentences, and name failed or skipped checks plainly.
- Prefer positive instructions that describe desired behavior. Use prohibitions for real boundaries and known failure modes.

Use a role only when specialized perspective changes the result. Do not add personality, memory, subagents, tools, or status-update machinery by default.

Use descriptive XML tags when the prompt mixes instructions, context, examples, variable inputs, or multiple documents and the tags make boundaries clearer. Do not wrap a simple prompt in a large XML scaffold.

When long source material is included:

1. Put the documents or data near the beginning.
2. Wrap multiple sources in clear tags such as `<documents>`, `<document>`, `<source>`, and `<document_content>`.
3. Place the actual query and task instructions after the source material.
4. When grounding is critical, ask Claude to identify relevant excerpts before synthesizing, without requesting hidden reasoning.

Use three to five diverse examples only when they encode a difficult output format, tone, classification boundary, or edge case better than prose. Keep examples relevant and separate them with descriptive tags.

Never ask Claude to reveal, reproduce, or quote its hidden chain of thought. Replace "show your reasoning" or "think step by step" with requests for conclusions, concise rationale, supporting evidence, assumptions, checks performed, and uncertainty.

### 4. Keep runtime configuration separate

Do not embed API settings in the copy-ready prompt unless the user explicitly asks for a complete API request or deployment configuration.

When runtime guidance is requested:

- use the current official model ID only after checking the live documentation;
- treat effort as a runtime and evaluation choice, not prose inside the prompt;
- do not recommend maximum effort globally;
- do not use unsupported thinking-token budgets;
- handle refusal detection, retries, fallbacks, and response-prefill compatibility in application code rather than prompt text.

### 5. Validate before returning

Silently run every applicable check:

1. **Intent:** Does the rewrite solve the same task without widening or narrowing scope?
2. **Preservation:** Are all facts, values, hard constraints, required fields, and prompt-layer boundaries retained?
3. **Clarity:** Could two reasonable agents still disagree about the outcome, source, permission boundary, or required output?
4. **Fable fit:** Is the prompt direct and context-rich without over-prescribing the method?
5. **Action boundary:** Is assess versus implement clear, and are approval boundaries explicit where needed?
6. **Evidence:** Are claims, progress, verification, and failure reporting grounded appropriately?
7. **Output:** Are audience, language, structure, and must-include content clear where they matter?
8. **Structure:** Are XML tags or examples used only when they improve behavior?
9. **Reasoning safety:** Does the prompt avoid requests for hidden chain of thought?
10. **Runtime separation:** Does the prompt avoid unsupported or unnecessary API assumptions?
11. **Leanness:** Can any clause be removed without changing behavior?
12. **Executability:** Can Claude act with the stated inputs and tools, or does a blocking dependency remain?

If a check exposes blocking ambiguity, return to the clarity gate. Otherwise revise until every applicable check passes.

### 6. Return the result

Honor the user's requested format. Otherwise use the smallest suitable response.

For a completed optimization, return:

1. `优化后的提示词` or `Optimized prompt`: one copy-ready fenced block.
2. `关键改动` or `Key changes`: at most five concise bullets, only when useful.
3. `假设` or `Assumptions`: only assumptions actually made.
4. `API 设置建议` or `API settings`: only when requested or essential to the stated use case.

When the user asks for prompt-only output, return only the copy-ready prompt. Omit headings, commentary, change notes, and Markdown fences unless the user explicitly requests a fenced block.

For blocking clarification, return only:

- one short sentence explaining that reliable optimization needs more information;
- one to three questions;
- an optional one-line answer template.

Do not bury the questions beneath a provisional rewrite.

## Avoid common regressions

- Do not turn every prompt into a giant XML template.
- Do not add memory, subagents, an asynchronous update tool, or autonomous behavior unless the use case requires it.
- Do not recommend `xhigh` effort as a universal default.
- Do not ask for hidden reasoning or chain-of-thought disclosure.
- Do not invent tools, access, permissions, policies, metrics, source facts, or product capabilities.
- Do not add unrelated features, cleanup, refactors, abstractions, or speculative fallbacks.
- Do not make a review prompt perform implementation work.
- Do not use prompt length or section count as a proxy for quality.
- Do not claim improvement merely because the rewrite is shorter.
- Do not preserve unsupported assistant-prefill patterns when migrating older prompts.

## Completion bar

Finish only when the prompt is copy-ready, blocking ambiguity is resolved, explicit constraints are preserved, action boundaries are clear where relevant, the rewrite is lean, and the validation checklist passes. If those conditions cannot be met, state the smallest missing information instead of fabricating a complete prompt.
