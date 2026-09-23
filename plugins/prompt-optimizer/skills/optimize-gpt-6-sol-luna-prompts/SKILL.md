---
name: optimize-gpt-6-sol-luna-prompts
description: Clarify and rewrite prompts for GPT-6 Sol and GPT-6 Luna. Use when optimizing, debugging, migrating, or designing a gpt-6-sol / gpt-6-luna / GPT-6 Sol / GPT-6 Luna prompt—not for GPT-6 Astra (use optimize-gpt-6-astra-prompts or audit-gpt-6-astra-skills) or GPT-5.6 (use optimize-gpt-5-6-prompts).
---

# Optimize GPT-6 Sol and Luna Prompts

Turn rough ideas and existing prompt stacks into copy-ready GPT-6 Sol or GPT-6 Luna prompts. Preserve the user's intent and hard constraints, ask for genuinely blocking information, and keep the result no more elaborate than the task requires.

## Use the official basis

Read [references/official-guidance.md](references/official-guidance.md) when model-specific rationale, API-setting guidance, Sol vs Luna routing, or the longer checklist is needed. Treat its URLs as canonical sources and its prose as a dated summary. Fetch the live OpenAI pages before making claims about the current model alias, parameters, availability, or other facts that may change.

For GPT-6 Astra product prompts or Skills/`AGENTS.md` harness cleanup, use `$optimize-gpt-6-astra-prompts` or `$audit-gpt-6-astra-skills`. For GPT-5.6, keep using `$optimize-gpt-5-6-prompts`.

## Follow the workflow

### 1. Capture the prompt contract

Identify the following from the user's message, pasted prompt, or referenced file:

- intended user-visible outcome;
- Sol vs Luna fit when the user has not named a model: demanding coding/agentic work → Sol; focused high-volume or cost/latency-sensitive work → Luna;
- inputs and context the model will receive;
- hard constraints and facts to preserve;
- required output shape, audience, and language;
- evidence, tool use, or validation needed for success;
- side effects the model may take and actions that require approval;
- fallback or stopping behavior when required information is unavailable.

Treat these as diagnostic dimensions, not mandatory sections. Keep a simple rewriting prompt simple.

If the user supplies a layered prompt stack, preserve the separation between system, developer, user, and tool-description content. Do not silently move instructions between trust levels.

### 2. Apply the clarity gate

Ask a question only when the missing or conflicting information is blocking: two reasonable answers would lead to materially different prompts, outputs, permissions, or success criteria.

Treat the following as blocking by default:

- a vague new-prompt request that names only a topic and generic verb, such as "analyze this data," "write content," or "build an agent," while the intended decision, audience, or deliverable could vary materially;
- an action prompt that can send, publish, purchase, delete, change records, modify terms, or affect another person when the authorization and approval boundary is not explicit;
- a task that depends on an unspecified source, system, policy, tool, or evidence set whose choice changes what the model may conclude or do;
- conflicting instructions about scope, output, permissions, evidence, or completion;
- a strict output contract whose required fields or semantics cannot be inferred safely;
- a request that could reasonably target either Sol or Luna when the choice would change the recommended API settings or prompt density, and the user did not name a model or workload role.

Do not turn these gaps into placeholders, invented policies, assumed access, or a provisional workflow. A conservative execution policy does not cure an undefined goal or missing authorization.

Proceed without asking when the core outcome and contract are already clear and the missing detail is cosmetic or safely local, such as a minor heading choice. For a new prompt, require enough information to distinguish the intended use from other plausible uses; for an existing concrete prompt, preserve its contract and avoid demanding optional context. If Sol vs Luna is the only open choice and the workload role is obvious from context, pick it, label the assumption, and continue.

When clarification is blocking:

1. Ask one to three high-information questions in a single turn.
2. Explain choices briefly when the user may not know the terminology.
3. Offer concrete options or a short fill-in reply when that makes answering easier.
4. Do not present a supposedly final optimized prompt yet.
5. Repeat the gate after the reply and ask again only if a material ambiguity remains.

Ask for the smallest missing fact. Do not interrogate the user for optional tone preferences, minor formatting choices, or details that can be handled with a conservative assumption.

If the user explicitly delegates a choice, make a reasonable assumption, label it after the prompt, and continue. Never invent source facts, policies, permissions, metrics, or product capabilities.

Use these examples as calibration:

- For "analyze sales data and give recommendations," ask what decision the analysis should support, what data and period are available, and who will use the output. Do not immediately manufacture a generic analytics prompt.
- For "find overdue invoices, send reminders, and adjust payment terms," ask which systems and invoice scope apply, whether the agent may send or only draft, and what approval or policy governs term changes. Do not assume access or authority.
- For "rewrite this paragraph professionally" with the paragraph supplied, proceed unless "professional" conflicts with a material audience or compliance need.
- For "migrate this GPT-5.6 agent prompt," proceed with Sol unless the workload is clearly high-volume/latency-sensitive (then Luna), and preserve effective reasoning effort.

### 3. Rewrite for GPT-6 Sol or Luna

Apply only changes that improve the prompt contract:

- Lead with the outcome and define what completion means.
- State each rule once; remove repetition, obsolete scaffolding, and irrelevant examples.
- Resolve contradictions. If a conflict cannot be resolved from context, return to the clarity gate.
- Preserve explicit user values and factual claims exactly unless correction was requested.
- Prefer decision rules over blanket `always`, `never`, or keyword-trigger rules. Reserve absolutes for true invariants.
- Specify required evidence, validation, and stop conditions when correctness depends on them.
- Define autonomy and approval boundaries once for prompts that can read, change, purchase, publish, message, delete, or otherwise act.
- Expose or describe only relevant tools. State prerequisites, important result fields, fallback behavior, and direct-versus-programmatic routing only when they affect the task.
- Specify output content and structure. For short answers, name the facts, caveats, and next actions that must survive trimming.
- Describe tone with observable writing choices instead of vague labels when tone matters.
- Preserve the source language unless the user requests another language. Define language switching only when it is a real product rule.
- For editing, summarizing, or drafting, state what must be preserved and prohibit unsupported new claims.
- Replace requests to "think step by step" or "think harder" with outcome, evidence, and verification requirements. Keep API reasoning settings outside the prompt.
- Keep optional sections out. Do not force every prompt into a large template.

GPT-6 family mitigations — add only when traces or the product show the same failure mode (official snippets are Astra-observed; evaluate on Sol/Luna):

- **Initiative and follow-through:** If the model pauses too early, authorize action-shaped requests, bias toward completion for reversible work, and ask for approval only after a concrete reviewable result.
- **Instruction priority:** When skills or `AGENTS.md` can conflict with the user, make user instructions take precedence.
- **Writing style / verification / delegation:** Specify prose vs lists, test calibration, or subagent parallelization only when the product needs a different default.

For an existing production prompt, prefer a surgical rewrite that preserves working behavior. Do not redesign the entire prompt stack without evidence of a broader problem or an explicit request. Prefer a surgical migrate from GPT-5.6 Sol/Terra/Luna prompts unless the user asks for a broader redesign.

### 4. Validate before returning

Silently run all applicable checks:

1. **Intent:** Does the rewrite solve the same task without widening or narrowing scope?
2. **Preservation:** Are all user facts, explicit values, hard constraints, and required fields retained?
3. **Clarity:** Could two reasonable agents still disagree about the core outcome, permission boundary, or required output?
4. **Completeness:** Are success, evidence, validation, and stopping conditions defined where they materially matter?
5. **Consistency:** Are any instructions duplicated, contradictory, or placed in the wrong prompt layer?
6. **Leanness:** Can any clause be removed without changing behavior?
7. **Executability:** Can the model act using only the stated inputs and tools, or does a blocking dependency remain?
8. **Non-invention:** Did the rewrite add facts, access, permissions, or capabilities the user never supplied?
9. **Model fit:** Is the recommended model Sol or Luna aligned with the workload, and were family mitigations added only when needed?

If a check exposes blocking ambiguity, ask the user instead of guessing. Otherwise revise until every applicable check passes.

### 5. Return the result

Honor any output format the user requested. Otherwise use the smallest suitable form.

For a completed optimization, return:

1. `优化后的提示词` or `Optimized prompt`: one copy-ready fenced block.
2. `关键改动` or `Key changes`: at most five concise bullets, only when useful.
3. `假设` or `Assumptions`: include only assumptions actually made (including Sol vs Luna if chosen).
4. `API 设置建议` or `API settings`: include only when the user asks for runtime configuration or when a setting is essential to the stated use case. Prefer `model: gpt-6-sol` or `model: gpt-6-luna`, Responses API when tools or reasoning-with-tools are needed, and keep `reasoning.effort` outside prompt prose. Sol and Luna support `none`; Chat Completions function calling requires `reasoning_effort: "none"`.

When the user requests prompt-only output, return only the copy-ready prompt. Do not add analysis inside the prompt unless the user wants it there.

For blocking clarification, return only:

- a short statement that the prompt cannot yet be optimized reliably;
- the one to three questions;
- an optional one-line answer template.

Do not bury the questions beneath a provisional rewrite.

## Avoid common regressions

- Do not add role, personality, tools, or stop-rule sections when they do not change behavior.
- Do not paste every GPT-6 family snippet into every Sol/Luna prompt; apply only the relevant mitigation.
- Do not route Astra workloads here; send those to `$optimize-gpt-6-astra-prompts`.
- Do not replace specific requirements with generic instructions such as "be concise," "be thorough," or "use tools efficiently."
- Do not use prompt length or section count as a proxy for quality.
- Do not ask for information merely to make the prompt perfect; ask only when it changes the contract materially.
- Do not recommend maximum reasoning effort globally. Fix missing success criteria, routing, dependencies, or validation before increasing effort.
- Do not claim that a rewrite is better solely because it is shorter. Preserve product requirements and measured fixes.

## Completion bar

Finish only when the prompt is copy-ready, blocking ambiguity is resolved, explicit constraints are preserved, the rewrite is lean, Sol vs Luna fit is clear or labeled, and the validation checklist passes. If those conditions cannot be met, state the smallest missing information instead of fabricating a complete prompt.
