---
name: optimize-glm-prompts
description: Clarify, audit, and rewrite rough or existing prompts for the GLM language-model family using Zhipu AI's official prompt-engineering guidance. Use when a user asks to optimize, improve, rewrite, debug, migrate, or design a GLM prompt, including requests phrased as GLM 提示词优化 or 智谱提示词优化, or when an underspecified GLM prompt needs guided clarification before a copy-ready rewrite. Ask only for missing information that would materially change the result; otherwise preserve intent and produce a clear prompt with appropriate system behavior, input boundaries, grounding, structured-output rules, and completion criteria.
---

# Optimize GLM Prompts

Turn rough ideas and existing prompt stacks into copy-ready GLM prompts. Preserve the user's intent and hard constraints, ask for genuinely blocking information, and add structure only when it changes model behavior.

## Use the official basis

Read [references/official-guidance.md](references/official-guidance.md) when model-specific rationale or the longer checklist is needed. Treat its prose as a dated summary and its URL as the canonical source. Fetch the live Zhipu AI page before making claims about current model IDs, API features, parameters, context limits, availability, or pricing.

## Follow the workflow

### 1. Capture the prompt contract

Identify:

- the intended user-visible outcome, audience, and language;
- the inputs, background, references, or retrieval results GLM will receive;
- standing behavior that belongs in a system prompt, if the integration supports one;
- facts and hard constraints to preserve;
- the output content, format, schema, and length;
- evidence, reasoning visibility, validation, and fallback behavior needed for success.

Treat these as diagnostic dimensions, not mandatory headings. Preserve system, developer, user, and tool-description boundaries when the user supplies a layered prompt stack.

### 2. Apply the clarity gate

Ask a question only when two reasonable answers would materially change the outcome, source boundary, prompt layer, schema, permissions, or success criteria.

Treat these gaps as blocking by default:

- a vague topic and generic verb with no identifiable deliverable or audience;
- a required backend schema with undefined fields or semantics;
- an unspecified source set whose choice changes the answer;
- an external action without clear authorization;
- conflicting role, scope, evidence, or output requirements.

When clarification is blocking, ask one to three high-information questions and do not produce a provisional prompt. Otherwise proceed with conservative local assumptions and list only assumptions actually made. Never invent facts, sources, policies, access, or permissions.

### 3. Rewrite for GLM

Apply only techniques that improve the contract:

- Define system behavior when stable role, language style, task mode, or domain-specific rules need to apply across turns. Do not flatten user data or one-off requests into the system layer.
- Supply concrete details and background GLM would otherwise have to guess.
- Add a role only when its expertise or perspective changes the result.
- Separate instructions, source material, examples, and user data with clear headings, triple quotes, or XML tags.
- Add few-shot examples when a style, classification boundary, or input-to-output mapping is difficult to specify directly.
- For reference-grounded work, name the allowed material, require answers to follow it, and define behavior when it is insufficient. Keep retrieval implementation outside the prompt unless requested.
- Decompose complex work into simple, coherent subtasks whose outputs feed the next stage.
- For backend-consumed output, define an explicit JSON or other schema, field meanings, allowed values, missing-value behavior, and whether any prose outside the structure is forbidden.
- Request a concise rationale, derivation, or verification when the user needs an auditable result. Ask for a full visible step-by-step solution only when those steps are themselves part of the deliverable; do not demand private hidden chain-of-thought.
- For long conversations, preserve decisions, constraints, and unresolved items in a compact summary. For long documents, define chunked summaries and recursive synthesis when needed.
- Treat exact word counts as approximate unless downstream validation will enforce them.
- Keep model selection, thinking mode, retrieval, structured-output API settings, and other runtime configuration outside the copy-ready prompt unless the user asks for a full request.

Do not add every technique to every prompt. Prefer a surgical rewrite for an existing prompt with a clear contract.

### 4. Validate before returning

Silently check:

1. **Intent:** The rewrite solves the same task without changing scope.
2. **Preservation:** All explicit facts, constraints, and required fields remain.
3. **Layering:** Standing behavior and user-specific content are placed appropriately.
4. **Grounding:** Source boundaries and missing-evidence behavior are explicit where needed.
5. **Schema:** Structured output is unambiguous and parseable where required.
6. **Executability:** GLM has the necessary inputs, or the smallest blocker is identified.
7. **Leanness:** Roles, examples, decomposed stages, and reasoning requests exist only when useful.
8. **Non-invention:** No new facts, access, sources, or permissions were assumed.

If a check exposes blocking ambiguity, ask the user instead of guessing.

### 5. Return the result

Honor the user's requested format. Otherwise return:

1. `优化后的提示词` or `Optimized prompt`: one copy-ready fenced block, or separate system and user blocks when both are needed.
2. `关键改动` or `Key changes`: at most five concise bullets, only when useful.
3. `假设` or `Assumptions`: only assumptions actually made.
4. `API 设置建议` or `API settings`: only when requested or essential.

For prompt-only requests, return only the prompt. For blocking clarification, return only a short blocker statement, one to three questions, and optionally a one-line answer template.

## Completion bar

Finish only when the prompt is copy-ready, explicit requirements are preserved, prompt layers and schemas are clear where relevant, and GLM does not need to guess a material part of the contract. Otherwise state the smallest missing information.
