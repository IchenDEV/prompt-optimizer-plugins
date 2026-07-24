---
name: optimize-claude-opus-5-prompts
description: Clarify, audit, and rewrite rough or existing prompts for Claude Opus 5 using Anthropic's official prompting guidance. Use when a user asks to optimize, improve, migrate, debug, or design a prompt, system prompt, or agent harness for Claude Opus 5 (claude-opus-5), including requests phrased as Opus 5 prompt optimization, Opus 5 提示词优化, or migrating a Claude Opus 4.8 prompt. Also use when tuning observed Opus 5 behavior such as long responses, heavy progress narration, long written deliverables, scope expansion, eager subagent delegation, correction narration, over-verification, conservative code review, or thinking-disabled output artifacts. Ask only for missing information that would materially change the result; otherwise preserve intent and return a lean, copy-ready prompt.
---

# Optimize Claude Opus 5 Prompts

Turn rough ideas and existing prompt stacks into copy-ready prompts for Claude Opus 5. Preserve the user's intent and hard constraints, clarify genuinely blocking gaps, and keep the result no more elaborate than the task requires.

Opus 5 performs well out of the box on prompts written for Claude Opus 4.8. Most real optimization here is **subtractive plus a few targeted behavior instructions**, not a rewrite into a larger template.

## Use the official basis

Read [references/official-guidance.md](references/official-guidance.md) for model-specific rationale, runtime settings, and migration details. Read [references/snippets.md](references/snippets.md) for the copy-ready instruction blocks from Anthropic's guide. Treat the listed URLs as canonical and the prose as a dated working summary; fetch the live pages before making claims about current model IDs, parameters, availability, limits, or pricing.

Use the official model name `Claude Opus 5` and the API model ID `claude-opus-5`. Treat `Opus5`, `opus-5`, and `O5` as spelling variants unless context indicates a different product.

## Follow the workflow

### 1. Capture the prompt contract

Identify the following from the user's message, pasted prompt, or referenced file:

- the user-visible outcome and why it matters;
- the inputs, source material, and context Claude will receive;
- hard constraints, facts, policies, and values to preserve;
- the required output, audience, language, and level of detail;
- tools, evidence, or verification the task genuinely depends on;
- whether Claude should assess, recommend, draft, implement, or take external action;
- authorized actions and actions that require approval;
- stopping and fallback behavior when a dependency is unavailable;
- **which Opus 5 behavior the user has actually observed as a problem**, and whether the prompt was carried over from Opus 4.8 or an earlier model.

Treat these as diagnostic dimensions, not mandatory prompt sections. Keep a simple prompt simple.

If the user supplies a layered prompt stack, preserve the separation between system, developer, user, and tool-description content. Do not silently move instructions between trust levels.

### 2. Apply the clarity gate

Ask a question only when the missing or conflicting information is blocking: two reasonable answers would produce materially different prompts, outputs, permissions, evidence, or success criteria.

Treat these gaps as blocking by default:

- a new-prompt request that contains only a broad topic and a generic verb such as "analyze," "write," or "build," while the intended decision, audience, or deliverable could vary materially;
- a prompt that can send, publish, purchase, delete, change records, modify terms, or affect another person when authorization and approval boundaries are unclear;
- a task that depends on an unspecified source, system, policy, tool, or evidence set whose choice changes what Claude may conclude or do;
- conflicting instructions about scope, output, permissions, evidence, or completion;
- a strict schema whose required fields or field semantics cannot be inferred safely.

Do not turn blocking gaps into placeholders, invented policies, assumed access, or a provisional workflow.

Proceed when the core contract is clear and the missing detail is cosmetic or safely local. For an existing concrete prompt, preserve its contract and do not demand optional context. If the user explicitly delegates a nonblocking choice, make a conservative assumption, state it after the prompt, and continue.

When clarification is blocking:

1. Ask one to three high-information questions in one turn.
2. Briefly explain choices when the user may not know the terminology.
3. Offer concrete options or a short fill-in reply when that reduces effort.
4. Do not present a final or provisional optimized prompt yet.
5. Reapply the clarity gate after the reply. Ask again only if material ambiguity remains.

For a prompt-tuning request, one question is usually enough: which behavior is wrong in the observed output. Do not interrogate the user about optional tone, headings, or preferences that a conservative assumption covers.

### 3. Subtract first

Opus 5 already does several things that older prompts had to force. Remove these before adding anything:

- **Verification instructions.** "Include a final verification step for any non-trivial task," "use a subagent to verify," "double-check your answer," "re-verify before responding." Opus 5 verifies and self-corrects unprompted; these compound and waste tokens with no quality gain. Delete them rather than replacing them with a prohibition on verifying.
- **Legacy harness scaffolding** that inserts separate verification or re-check stages.
- **Conservative review filters** such as "only report high-severity issues" or "be conservative." Opus 5 follows them literally and reports less. Ask for everything and filter in a separate pass.
- **Rules telling the model not to think or not to reason.** They increase internal-tag leakage when thinking is disabled.
- **Vision workarounds** tuned for older models. Re-validate them; prefer giving the model tools to crop, zoom, and visually verify.
- **Instruction repetition, emphasis stacking, and procedural micromanagement** that compensated for weaker instruction following.

State plainly which removals are behavior-driven so the user can re-add anything their evals actually need.

### 4. Tune only the behaviors that need it

Map the observed symptom to one lever. Copy the matching block from [references/snippets.md](references/snippets.md) and adapt it to the user's product voice.

| Observed behavior | Lever |
| --- | --- |
| Visible responses too long or over-caveated | Explicit conciseness instruction. Effort controls thinking, not visible length; lowering effort will not reliably shorten output. In a long system prompt, pair it with a short reminder near the end. |
| Too much narration during agentic work | Describe the cadence and shape of updates you want. Positive examples of the desired style beat prohibitions. |
| Files written to disk are padded or bloated | Length-calibration line for written deliverables, separate from conversational verbosity. |
| Unrequested steps, refactors, or reinterpreted tasks | Explicit scope constraint: deliver what was asked, flag a better approach in a sentence and continue, finish the whole task. |
| Too many subagents, or subagents used for trivial work | Explicit delegation criteria and a low or deterministic spawn cap; forbid delegating verification. |
| Corrections narrated for slips that change nothing | Correction threshold: correct only when the error changes the user's code, conclusions, or decisions. |
| Code review returns too few findings | Remove conservatism instructions; ask for all findings and filter in a second pass. |
| Multi-file feature work stops short or leaves stubs | Give the complete task specification up front and let the model run, instead of drip-feeding steps. |
| Charts, documents, diagrams, or UI replication underperform | Provide tools for iterative analysis, cropping, and visual verification rather than more thinking. |
| Spreadsheets or slide decks miss house style | State the specific styles, templates, and structure to follow. |
| Tool call appears as plain text (thinking disabled) | Prefer re-enabling thinking and controlling cost with lower effort. If thinking must stay off, explicitly permit a brief sentence before a tool call. |
| Internal XML tags leak into output (thinking disabled) | Prefer re-enabling thinking. Otherwise use a general instruction against internal or system XML tags; naming the tags specifically is less effective. |

Beyond these levers, apply ordinary prompt hygiene only where it materially improves the contract:

- State the intended outcome, relevant context, and why the task matters, so the model generalizes correctly to edge cases.
- Preserve explicit facts, values, required fields, and constraints exactly unless the user asks to change them.
- State whether Claude should assess, recommend, draft, implement, or execute. A request for review must not silently become authorization to modify.
- Define authorization once: what may proceed unattended, and what requires approval. Pause only for destructive or irreversible actions, genuine scope changes, or input only the user can supply.
- Require progress and completion claims to be grounded in actual tool results, and require an exact statement of any gap when a step is skipped or a check fails.
- Prefer positive instructions that describe desired behavior; reserve prohibitions for real boundaries and known failure modes.

Use a role only when a specialized perspective changes the result. Use descriptive XML tags when the prompt mixes instructions, context, examples, variable inputs, or multiple documents and tags make boundaries clearer; do not wrap a simple prompt in a large XML scaffold. Use three to five diverse examples only when they encode a format, tone, classification boundary, or edge case better than prose.

For long source material, place documents near the beginning, wrap them in clear tags such as `<documents>`, `<document>`, and `<document_content>`, and put the query and task instructions after them. Opus 5 holds instruction following and reasoning across its 1M-token window, so position matters less than boundary clarity, but keep instructions after the data.

Never ask Claude to reveal, reproduce, or quote hidden chain of thought. Ask instead for conclusions, concise rationale, supporting evidence, assumptions, checks performed, and uncertainty.

### 5. Keep runtime configuration separate

Do not embed API settings in the copy-ready prompt unless the user explicitly asks for a complete API request or deployment configuration.

When runtime guidance is requested:

- confirm the current model ID against live documentation before quoting it;
- treat effort as a runtime and evaluation choice, not prose inside the prompt: `xhigh` is the starting point for coding and agentic work, `high` for other intelligence-sensitive workloads, and `low` or `medium` are strong on Opus 5 wherever evals show quality holds;
- tell the user to re-run an effort sweep rather than reusing settings carried over from an earlier model;
- note that thinking is on by default and cannot be disabled at `xhigh` or `max` effort, and that `max_tokens` is a hard cap covering thinking plus response text;
- keep retries, refusal handling, fallbacks, tool schemas, and routing in application code rather than prompt text.

### 6. Validate before returning

Silently run every applicable check:

1. **Intent:** Does the rewrite solve the same task without widening or narrowing scope?
2. **Preservation:** Are all facts, values, hard constraints, required fields, and prompt-layer boundaries retained?
3. **Subtraction:** Have redundant verification, re-check, anti-thinking, and conservatism instructions been removed?
4. **Targeting:** Does every added behavior block correspond to a behavior the user observed or plausibly faces?
5. **Clarity:** Could two reasonable agents still disagree about the outcome, source, permission boundary, or required output?
6. **Action boundary:** Is assess versus implement clear, and are approval boundaries explicit where needed?
7. **Evidence:** Are progress, completion, and failure reporting grounded in real results?
8. **Output:** Are audience, language, structure, length, and must-include content clear where they matter?
9. **Structure:** Are XML tags and examples used only where they improve behavior?
10. **Reasoning safety:** Does the prompt avoid requesting hidden chain of thought?
11. **Runtime separation:** Does the prompt avoid unsupported or unnecessary API assumptions?
12. **Leanness:** Can any clause be removed without changing behavior?
13. **Executability:** Can Claude act with the stated inputs and tools, or does a blocking dependency remain?

If a check exposes blocking ambiguity, return to the clarity gate. Otherwise revise until every applicable check passes.

### 7. Return the result

Honor the user's requested format. Otherwise use the smallest suitable response.

For a completed optimization, return:

1. `优化后的提示词` or `Optimized prompt`: one copy-ready fenced block.
2. `关键改动` or `Key changes`: at most five concise bullets, only when useful. Name removals explicitly, with the behavior each removal targets.
3. `假设` or `Assumptions`: only assumptions actually made.
4. `运行时设置` or `Runtime settings`: only when requested or essential to the stated use case.

When the user asks for prompt-only output, return only the copy-ready prompt: no headings, commentary, change notes, or Markdown fences unless a fenced block was requested.

For blocking clarification, return only a one-sentence reason, one to three questions, and an optional one-line answer template. Do not bury the questions beneath a provisional rewrite.

Answer in the user's language.

## Avoid common regressions

- Do not add verification, double-checking, or verifier subagents to an Opus 5 prompt.
- Do not replace a removed verification rule with an instruction not to verify.
- Do not add "do not think" or "do not reason" rules; they worsen tag leakage.
- Do not name `<thinking>` or other internal tags in an anti-leakage instruction; use the general form.
- Do not recommend disabling thinking as a cost control; lower effort instead.
- Do not claim effort will shorten visible responses.
- Do not tell a reviewer prompt to report only high-severity issues.
- Do not encourage broad subagent delegation for small tasks.
- Do not turn every prompt into a giant XML template.
- Do not invent tools, access, permissions, policies, metrics, or product capabilities.
- Do not make a review prompt perform implementation work.
- Do not use prompt length or section count as a proxy for quality, and do not claim improvement merely because the rewrite is shorter.
- Do not preserve assistant-prefill patterns or thinking-budget parameters from older integrations.

## Completion bar

Finish only when the prompt is copy-ready, blocking ambiguity is resolved, explicit constraints are preserved, action boundaries are clear where relevant, obsolete scaffolding is gone, each remaining behavior instruction is justified, and the validation checklist passes. If those conditions cannot be met, state the smallest missing information instead of fabricating a complete prompt.
