---
name: optimize-claude-sonnet-5-5-prompts
description: Clarify and rewrite prompts or agent harnesses for Claude Sonnet 5.5 (claude-sonnet-5-5). Use when optimizing, migrating from Sonnet 5, or tuning Sonnet 5.5 behaviors such as effort, initiative/scope, between_tools, JSON accuracy, progress updates, or low-effort verification (including Sonnet 5.5 提示词优化)—not for Opus/Fable prompt rewrites or bare model picking (use select-claude-models).
---

# Optimize Claude Sonnet 5.5 Prompts

Turn rough ideas and existing prompt stacks into copy-ready prompts for Claude Sonnet 5.5. Preserve the user's intent and hard constraints, clarify genuinely blocking gaps, and keep the result no more elaborate than the task requires.

Existing Claude Sonnet 5 prompts should perform well without changes. Most real optimization here is **targeted steering for observed behavior plus removal of obsolete workarounds**, not a rewrite into a larger template. For the hardest long-horizon work, prefer an Opus model or the `select-claude-models` skill rather than over-prompting Sonnet.

## Use the official basis

Read [references/official-guidance.md](references/official-guidance.md) for model-specific rationale, runtime settings, and migration details. Read [references/snippets.md](references/snippets.md) for the copy-ready instruction blocks from Anthropic's guide. Treat the listed URLs as canonical and the prose as a dated working summary; fetch the live pages before making claims about current model IDs, parameters, availability, limits, or pricing.

Use the official model name `Claude Sonnet 5.5` and the API model ID `claude-sonnet-5-5`. Treat `Sonnet 5.5`, `sonnet-5-5`, and `S5.5` as spelling variants unless context indicates a different product.

When the user is choosing between Sonnet and Opus, or designing a multi-agent Opus-plans / Sonnet-executes setup, use or point to `select-claude-models`.

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
- **which Sonnet 5.5 behavior the user has actually observed as a problem**, and whether the prompt was carried over from Sonnet 5 or an earlier model;
- whether the workload is everyday coding, polished documents, repeated agent tasks, latency-sensitive chat, or something that may belong on Opus instead.

Treat these as diagnostic dimensions, not mandatory prompt sections. Keep a simple prompt simple.

If the user supplies a layered prompt stack, preserve the separation between system, developer, user, and tool-description content. Do not silently move instructions between trust levels.

### 2. Apply the clarity gate

Ask a question only when the missing or conflicting information is blocking: two reasonable answers would produce materially different prompts, outputs, permissions, evidence, or success criteria.

Treat these gaps as blocking by default:

- a new-prompt request that contains only a broad topic and a generic verb such as "analyze," "write," or "build," while the intended decision, audience, or deliverable could vary materially;
- a prompt that can send, publish, purchase, delete, change records, modify terms, or affect another person when authorization and approval boundaries are unclear;
- a task that depends on an unspecified source, system, policy, tool, or evidence set whose choice changes what Claude may conclude or do;
- conflicting instructions about scope, output, permissions, evidence, or completion;
- a strict schema whose required fields or field semantics cannot be inferred safely;
- a request that mixes "optimize this prompt for Sonnet" with an obviously long-horizon Opus-class problem, when the user has not chosen whether to stay on Sonnet or switch models.

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

Sonnet 5.5 performs well on existing Sonnet 5 prompts. Remove these before adding anything:

- **Sonnet 5 workarounds** such as refusal steering, tool-call retry shims, or "do not be lazy."
- **Language that discourages tool use** such as "only use tools when strictly necessary" or "minimize tool calls," when the product has search or other tools the model should use for changeable facts.
- **Instructions that tell the model not to think or not to reason.** They increase internal-tag leakage, especially with `between_tools`.
- **Requests to write out hidden reasoning** in the visible response. They invite `reasoning_extraction` refusals; use summarized thinking or progress updates instead.
- **Hold-all-findings-for-the-final-response** instructions that suppress useful mid-turn progress notes.
- **Instruction repetition, emphasis stacking, and procedural micromanagement** that compensated for weaker instruction following.

State plainly which removals are behavior-driven so the user can re-add anything their evals actually need.

### 4. Tune only the behaviors that need it

Map the observed symptom to one lever. Copy the matching block from [references/snippets.md](references/snippets.md) and adapt it to the user's product voice.

| Observed behavior | Lever |
| --- | --- |
| Turns longer/shorter or quality off after migrating from Sonnet 5 | Re-sweep effort; do not reuse the old level. Start `high` on the API unless the workload is agentic or latency-sensitive. |
| Stops to check in before coding work is done at `low`/`medium` | Raise effort first, or add carry-through + stop-when-done instructions. |
| Adds unrequested tests, docs, or supporting files | Keep only the "stop when done; don't add extras" paragraph. |
| Extra review rounds or reviewer subagents at `xhigh`/`max` | Cap self-started review; reserve those effort levels for measured gains. |
| Open-ended asks become builds instead of ideas | Ask for ideas/plan first, or add the plan-then-stop block. |
| Integration previously ran with thinking off | Switch to `between_tools` at `high` or below; keep adaptive thinking for tool-free reasoning tasks. |
| JSON answers to multi-step reasoning tasks are wrong or unparsable | Prefer structured outputs; add "think before you answer"; use adaptive thinking, not `between_tools`. |
| Long agentic turns look silent in the UI | Surface progress via `thinking.display` / `between_tools` notes; optionally schedule update points in the prompt. |
| Answers from training knowledge when search would catch changes | Remove anti-tool language; add the search-check instruction. |
| Mid-turn user messages ignored or treated as injections | Fix harness placement: user text as a user turn after tool results, never inside `tool_result`. |
| Code changes reported done without a real test/build at `low` effort | Add the real-check verification paragraph. |
| Tool names mismatch only by case or near-miss parameter names | Handle in the harness with tolerant matching or a precise `is_error` result; do not bloat the prompt. |
| Dense charts or technical drawings miss detail | Provide crop/zoom/code tools; tools beat raising effort for charts. |
| `stop_reason: "refusal"` on legitimate work | Remove reasoning-extraction asks; handle fallbacks in app code; note Cyber / Life Sciences verification programs where relevant. |

Beyond these levers, apply ordinary prompt hygiene only where it materially improves the contract:

- State the intended outcome, relevant context, and why the task matters.
- Preserve explicit facts, values, required fields, and constraints exactly unless the user asks to change them.
- State whether Claude should assess, recommend, draft, implement, or execute.
- Define authorization once: what may proceed unattended, and what requires approval.
- Require progress and completion claims to be grounded in actual tool results.
- Prefer positive instructions that describe desired behavior; reserve prohibitions for real boundaries and known failure modes.

Use a role only when a specialized perspective changes the result. Use descriptive XML tags when the prompt mixes instructions, context, examples, variable inputs, or multiple documents and tags make boundaries clearer; do not wrap a simple prompt in a large XML scaffold. Use three to five diverse examples only when they encode a format, tone, classification boundary, or edge case better than prose.

For long source material, place documents near the beginning, wrap them in clear tags, and put the query and task instructions after them.

Never ask Claude to reveal, reproduce, or quote hidden chain of thought. Ask instead for conclusions, concise rationale, supporting evidence, assumptions, checks performed, and uncertainty.

### 5. Keep runtime configuration separate

Do not embed API settings in the copy-ready prompt unless the user explicitly asks for a complete API request or deployment configuration.

When runtime guidance is requested:

- confirm the current model ID against live documentation before quoting it (`claude-sonnet-5-5`);
- treat effort as a runtime and evaluation choice, not prose inside the prompt: API default is `high`; for agentic coding start at `medium` for well-specified tasks and move to `high` for harder ones; for chat/latency-sensitive work start at `medium` or `low`; reserve `xhigh`/`max` for measured gains, and consider Opus instead if those levels become the default;
- note that thinking is on by default (adaptive); `thinking: {"type": "disabled"}` returns 400 — use `between_tools` to turn off up-front thinking;
- note that thinking counts toward `max_tokens`; for agentic coding, 128k with streaming is the documented starting point;
- keep retries, refusal handling, fallbacks, tool schemas, advisor pairing, and routing in application code rather than prompt text;
- never claim that asking the model in the system prompt to "think less" reliably reduces thinking — lower effort instead.

### 6. Validate before returning

Silently run every applicable check:

1. **Intent:** Does the rewrite solve the same task without widening or narrowing scope?
2. **Preservation:** Are all facts, values, hard constraints, required fields, and prompt-layer boundaries retained?
3. **Subtraction:** Have Sonnet 5 workarounds, anti-tool language, anti-thinking rules, and reasoning-extraction asks been removed?
4. **Targeting:** Does every added behavior block correspond to a behavior the user observed or plausibly faces?
5. **Model fit:** Is Sonnet still the right model, or should the user consider Opus / multi-model selection?
6. **Clarity:** Could two reasonable agents still disagree about the outcome, source, permission boundary, or required output?
7. **Action boundary:** Is assess versus implement clear, and are approval boundaries explicit where needed?
8. **Evidence:** Are progress, completion, and failure reporting grounded in real results?
9. **Output:** Are audience, language, structure, length, and must-include content clear where they matter?
10. **Structure:** Are XML tags and examples used only where they improve behavior?
11. **Reasoning safety:** Does the prompt avoid requesting hidden chain of thought?
12. **Runtime separation:** Does the prompt avoid unsupported or unnecessary API assumptions?
13. **Leanness:** Can any clause be removed without changing behavior?
14. **Executability:** Can Claude act with the stated inputs and tools, or does a blocking dependency remain?

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

- Do not add Sonnet 5 workarounds back into a Sonnet 5.5 prompt.
- Do not recommend `thinking: {"type": "disabled"}`; use `between_tools` or lower effort.
- Do not use `between_tools` for tool-free multi-step reasoning or JSON tasks.
- Do not ask the model to think less in prose as a substitute for lowering effort.
- Do not ask for hidden reasoning or chain-of-thought disclosure.
- Do not recommend `xhigh` or `max` as a universal default on Sonnet; measure first, and consider Opus for hard long-horizon work.
- Do not put mid-turn user text inside `tool_result` blocks.
- Do not invent tools, access, permissions, policies, metrics, or product capabilities.
- Do not turn every prompt into a giant XML template.
- Do not use prompt length or section count as a proxy for quality.

## Completion bar

Finish only when the prompt is copy-ready, blocking ambiguity is resolved, explicit constraints are preserved, action boundaries are clear where relevant, obsolete scaffolding is gone, each remaining behavior instruction is justified, and the validation checklist passes. If those conditions cannot be met, state the smallest missing information instead of fabricating a complete prompt.
