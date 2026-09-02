---
name: optimize-gpt-5-6-prompts
description: Clarify, audit, and rewrite rough or existing prompts for GPT-5.6 Sol and the GPT-5.6 family using OpenAI's official prompting guidance. Use when a user asks to optimize, improve, rewrite, debug, migrate, or design a GPT-5.6 prompt, including requests explicitly phrased as GPT-5.6 prompt optimization, or when an underspecified prompt needs guided clarification before a copy-ready rewrite. Ask only for missing information that would materially change the result; otherwise preserve intent and produce a lean, outcome-first prompt with explicit constraints, output requirements, and completion criteria.
---

# Optimize GPT-5.6 Prompts

Turn rough ideas and existing prompt stacks into copy-ready GPT-5.6 prompts. Preserve the user's intent and hard constraints, ask for genuinely blocking information, and keep the result no more elaborate than the task requires.

## Use the official basis

Read [references/official-guidance.md](references/official-guidance.md) when model-specific rationale, API-setting guidance, or the longer checklist is needed. Treat its URLs as canonical sources and its prose as a dated summary. Fetch the live OpenAI pages before making claims about the current model alias, parameters, availability, or other facts that may change.

## Follow the workflow

### 1. Capture the prompt contract

Identify the following from the user's message, pasted prompt, or referenced file:

- intended user-visible outcome;
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
- a strict output contract whose required fields or semantics cannot be inferred safely.

Do not turn these gaps into placeholders, invented policies, assumed access, or a provisional workflow. A conservative execution policy does not cure an undefined goal or missing authorization.

Proceed without asking when the core outcome and contract are already clear and the missing detail is cosmetic or safely local, such as a minor heading choice. For a new prompt, require enough information to distinguish the intended use from other plausible uses; for an existing concrete prompt, preserve its contract and avoid demanding optional context.

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

### 3. Rewrite for GPT-5.6

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

For an existing production prompt, prefer a surgical rewrite that preserves working behavior. Do not redesign the entire prompt stack without evidence of a broader problem or an explicit request.

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

If a check exposes blocking ambiguity, ask the user instead of guessing. Otherwise revise until every applicable check passes.

### 5. Return the result

Honor any output format the user requested. Otherwise use the smallest suitable form.

For a completed optimization, return:

1. `Optimized prompt`: one copy-ready fenced block.
2. `Key changes`: at most five concise bullets, only when useful.
3. `Assumptions`: include only assumptions actually made.
4. `API settings`: include only when the user asks for runtime configuration or when a setting is essential to the stated use case.

When the user requests prompt-only output, return only the copy-ready prompt. Do not add analysis inside the prompt unless the user wants it there.

For blocking clarification, return only:

- a short statement that the prompt cannot yet be optimized reliably;
- the one to three questions;
- an optional one-line answer template.

Do not bury the questions beneath a provisional rewrite.

## Avoid common regressions

- Do not add role, personality, tools, or stop-rule sections when they do not change behavior.
- Do not replace specific requirements with generic instructions such as "be concise," "be thorough," or "use tools efficiently."
- Do not use prompt length or section count as a proxy for quality.
- Do not ask for information merely to make the prompt perfect; ask only when it changes the contract materially.
- Do not recommend maximum reasoning effort globally. Fix missing success criteria, routing, dependencies, or validation before increasing effort.
- Do not claim that a rewrite is better solely because it is shorter. Preserve product requirements and measured fixes.

## Completion bar

Finish only when the prompt is copy-ready, blocking ambiguity is resolved, explicit constraints are preserved, the rewrite is lean, and the validation checklist passes. If those conditions cannot be met, state the smallest missing information instead of fabricating a complete prompt.
