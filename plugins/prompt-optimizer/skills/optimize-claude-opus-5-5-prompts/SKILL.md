---
name: optimize-claude-opus-5-5-prompts
description: Clarify and rewrite prompts or agent harnesses for Claude Opus 5.5 (claude-opus-5-5). Use when optimizing, migrating from Opus 5, or tuning Opus 5.5 behaviors such as effort, always-on thinking, unattended early stops, progress updates, pasted-content marking, or chat thinking latency (including Opus 5.5 提示词优化)—not for Claude Opus 5 or Claude Fable 5.
---

# Optimize Claude Opus 5.5 Prompts

Turn rough ideas and existing prompt stacks into copy-ready prompts for Claude Opus 5.5. Preserve the user's intent and hard constraints, clarify genuinely blocking gaps, and keep the result no more elaborate than the task requires.

Opus 5.5 usually runs existing Claude Opus 5 prompts well. Most real optimization here is **subtractive plus a few targeted behavior instructions**, not a rewrite into a larger template. Prefer the Opus 5.5-specific levers below; fall back to [Prompting Claude Opus 5](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-opus-5) patterns only when the observed symptom matches that older guide.

## Use the official basis

Read [references/official-guidance.md](references/official-guidance.md) for model-specific rationale, runtime settings, and migration details. Read [references/snippets.md](references/snippets.md) for the copy-ready instruction blocks from Anthropic's guide. Treat the listed URLs as canonical and the prose as a dated working summary; fetch the live pages before making claims about current model IDs, parameters, availability, limits, or pricing.

Use the official model name `Claude Opus 5.5` and the API model ID `claude-opus-5-5`. Treat `Opus5.5`, `opus-5-5`, `Opus 5.5`, and `O5.5` as spelling variants unless context indicates a different product. Do not route Claude Opus 5 (`claude-opus-5`) requests here; use `optimize-claude-opus-5-prompts` instead.

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
- **which Opus 5.5 behavior the user has actually observed as a problem**, and whether the prompt or harness was carried over from Opus 5, Opus 4.8, or an earlier model;
- whether the integration previously ran with thinking disabled, forced `tool_choice`, or streamed text between tool calls.

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

Opus 5.5 already does several things that older prompts had to force. Remove these before adding anything:

- **"Think carefully" / "think step by step" lines** in chat system prompts. Thinking is always on; effort controls depth. These lines add latency without clear quality gain.
- **Instructions that ask the model to write out its reasoning in the response** as a substitute for thinking. Prefer summarized thinking blocks (`display: "summarized"`) instead; response-text reasoning can trigger `reasoning_extraction` refusals.
- **Rules telling the model not to think or not to reason.** Thinking cannot be disabled; these rules are obsolete and harmful.
- **Thinking-disabled mitigations** carried from Opus 5 (permission to speak before a tool call, anti-tag leakage) unless re-testing shows they are still needed with always-on thinking.
- **Verification instructions** and verifier-subagent scaffolding carried from earlier Opus models, when the user is migrating through Opus 5 patterns that already removed them—or when over-verification appears.
- **Instruction repetition, emphasis stacking, and procedural micromanagement** that compensated for weaker instruction following.
- **Vision workarounds** tuned for earlier models. Re-validate them; Opus 5.5 reads dense charts and layout-dependent visuals more accurately without tools.

State plainly which removals are behavior-driven so the user can re-add anything their evals actually need.

### 4. Tune only the behaviors that need it

Map the observed symptom to one lever. Copy the matching block from [references/snippets.md](references/snippets.md) and adapt it to the user's product voice.

| Observed behavior | Lever |
| --- | --- |
| Turns longer or costlier than on Opus 5 at the same effort | Re-run an effort sweep starting at `medium`. Lower effort before adding prompt instructions that try to reduce thinking. |
| Integration previously ran with thinking disabled | Start at `low` effort; optionally add "Answer directly without deliberating." only after measuring; remove response-text reasoning asks; select blocks by `type`. |
| Unattended agent stops after a progress report while work remains | Treat text-only `end_turn` as a report, not completion; keep a checklist; continue with open items; optionally add the unattended early-stop system block. |
| Long agentic turns look silent to users | Set `thinking.display` to `"updates"` (or `"summarized"`); optionally describe update cadence; harness-nudge after several quiet tool steps. |
| Multi-app automation misses unmentioned context | Add the explore-before-acting instruction for connected apps. |
| Multi-agent teams finish slowly | Pass elapsed time (and optional budget) each turn; optionally add the time-matters sentence. |
| Chat follow-ups think too long over settled answers | Add the settle-earlier-answers instruction at the end of the system prompt. |
| Model follows instructions inside pasted user text | Wrap pasted blocks in tagged `<pasted_content>` with matching random IDs and add the pasted-content system note. |
| Dense charts/diagrams/screenshots miss detail | Prefer higher-resolution images and crop/zoom tools; re-test whether older vision scaffolding is still needed. |
| Frontend output looks generic | Name specific styles to avoid; iterate rather than saying "avoid a generic AI look." |
| Forced tool use / `tool_choice: any` or named tool | Move to `auto` plus strict tools or structured outputs; say in the prompt when the tool applies (runtime, not usually prompt-template work). |
| Opus 5 length, narration, scope, or delegation issues persist | Use the matching Opus 5 behavior block sparingly; Opus 5.5 often needs less of that scaffolding. |

Beyond these levers, apply ordinary prompt hygiene only where it materially improves the contract:

- State the intended outcome, relevant context, and why the task matters, so the model generalizes correctly to edge cases.
- Preserve explicit facts, values, required fields, and constraints exactly unless the user asks to change them.
- State whether Claude should assess, recommend, draft, implement, or execute. A request for review must not silently become authorization to modify.
- Define authorization once: what may proceed unattended, and what requires approval. Pause only for destructive or irreversible actions, genuine scope changes, or input only the user can supply.
- For unattended agents, name the early stops you want avoided and the stops you do want (blocker requiring user input, or deliberately protected actions).
- Require progress and completion claims to be grounded in actual tool results, and require an exact statement of any gap when a step is skipped or a check fails.
- Prefer positive instructions that describe desired behavior; reserve prohibitions for real boundaries and known failure modes.

Use a role only when a specialized perspective changes the result. Use descriptive XML tags when the prompt mixes instructions, context, examples, variable inputs, or multiple documents and tags make boundaries clearer; do not wrap a simple prompt in a large XML scaffold. Use three to five diverse examples only when they encode a format, tone, classification boundary, or edge case better than prose.

For long source material, place documents near the beginning, wrap them in clear tags such as `<documents>`, `<document>`, and `<document_content>`, and put the query and task instructions after them.

Never ask Claude to reveal, reproduce, or quote hidden chain of thought. Ask instead for conclusions, concise rationale, supporting evidence, assumptions, checks performed, and uncertainty.

### 5. Keep runtime configuration separate

Do not embed API settings in the copy-ready prompt unless the user explicitly asks for a complete API request or deployment configuration.

When runtime guidance is requested:

- confirm the current model ID against live documentation before quoting it (`claude-opus-5-5`);
- treat effort as a runtime and evaluation choice, not prose inside the prompt: default is `medium`; set it explicitly; re-run a sweep rather than carrying Opus 5 settings; reserve `xhigh` and `max` for measured gains;
- note that thinking is always on: omit `thinking`, or use `thinking: {"type": "adaptive"}`; `disabled` / manual `budget_tokens` return 400;
- note that `tool_choice` types `any` and `tool` are rejected; use `auto` with strict tools or structured outputs;
- for user-visible progress between tool calls, set `thinking.display` to `"updates"` (beta) or `"summarized"` and render non-empty thinking blocks;
- size `max_tokens` for thinking plus reply (up to 128k); agentic coding often wants the full maximum;
- keep retries, refusal handling (`stop_reason: "refusal"`), fallbacks, tool schemas, computer-use toolset migration, and routing in application code rather than prompt text;
- keep conversations append-only when preserving thinking blocks; change instructions or tools with mid-conversation system messages rather than edits.

### 6. Validate before returning

Silently run every applicable check:

1. **Intent:** Does the rewrite solve the same task without widening or narrowing scope?
2. **Preservation:** Are all facts, values, hard constraints, required fields, and prompt-layer boundaries retained?
3. **Subtraction:** Have think-hard lines, response-text reasoning asks, anti-thinking rules, and obsolete thinking-disabled mitigations been removed when appropriate?
4. **Targeting:** Does every added behavior block correspond to a behavior the user observed or plausibly faces?
5. **Clarity:** Could two reasonable agents still disagree about the outcome, source, permission boundary, or required output?
6. **Action boundary:** Is assess versus implement clear, and are approval boundaries explicit where needed?
7. **Evidence:** Are progress, completion, and failure reporting grounded in real results?
8. **Output:** Are audience, language, structure, length, and must-include content clear where they matter?
9. **Structure:** Are XML tags and examples used only where they improve behavior?
10. **Reasoning safety:** Does the prompt avoid requesting hidden chain of thought?
11. **Runtime separation:** Does the prompt avoid unsupported API assumptions (disabled thinking, forced tool choice, outdated computer-use tool)?
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

- Do not recommend disabling thinking; it is unsupported on Opus 5.5.
- Do not keep "think carefully," "think step by step," or similar chat-system lines by default.
- Do not ask the model to reproduce hidden reasoning in response text.
- Do not carry Opus 5 effort defaults unchanged; start sweeps at `medium`.
- Do not claim effort will shorten visible responses.
- Do not add the full unattended early-stop block to human-in-the-loop apps.
- Do not add the settle-earlier-answers instruction where re-examination of prior work is desired.
- Do not treat a text-only progress turn as task completion in an unattended loop.
- Do not recommend `tool_choice: any` or named forced tools.
- Do not turn every prompt into a giant XML template.
- Do not invent tools, access, permissions, policies, metrics, or product capabilities.
- Do not make a review prompt perform implementation work.
- Do not use prompt length or section count as a proxy for quality.
- Do not preserve assistant-prefill patterns or thinking-budget parameters from older integrations.

## Completion bar

Finish only when the prompt is copy-ready, blocking ambiguity is resolved, explicit constraints are preserved, action boundaries are clear where relevant, obsolete scaffolding is gone, each remaining behavior instruction is justified, and the validation checklist passes. If those conditions cannot be met, state the smallest missing information instead of fabricating a complete prompt.
