---
name: optimize-kimi-prompts
description: Clarify, audit, and rewrite rough or existing prompts for the Kimi model family using Moonshot AI's official prompt best practices. Use when a user asks to optimize, improve, rewrite, debug, migrate, or design a Kimi prompt, including requests phrased as Kimi 提示词优化, or when an underspecified Kimi prompt needs guided clarification before a copy-ready rewrite. Ask only for missing information that would materially change the result; otherwise preserve intent and produce a clear prompt with explicit context, input boundaries, output requirements, reference-grounding rules, and completion criteria.
---

# Optimize Kimi Prompts

Turn rough ideas and existing prompt stacks into copy-ready Kimi prompts. Preserve the user's intent and hard constraints, ask for genuinely blocking information, and keep simple tasks simple.

## Use the official basis

Read [references/official-guidance.md](references/official-guidance.md) when model-specific rationale or the longer checklist is needed. Treat its prose as a dated summary and its URL as the canonical source. Fetch the live Moonshot AI page before making claims about current models, API fields, context limits, availability, or pricing.

## Follow the workflow

### 1. Capture the prompt contract

Identify:

- the intended user-visible outcome and audience;
- the inputs, background, and reference text Kimi will receive;
- facts and constraints that must be preserved;
- the required output content, structure, language, and approximate length;
- evidence, tools, or validation needed for success;
- fallback behavior when an answer is absent from the supplied material.

Treat these as diagnostic dimensions, not mandatory headings. Preserve system, developer, user, and tool-description boundaries when the user supplies a layered prompt stack.

### 2. Apply the clarity gate

Ask a question only when two reasonable answers would materially change the outcome, source boundary, permissions, schema, or success criteria.

Treat these gaps as blocking by default:

- a vague topic and generic verb with no identifiable deliverable or audience;
- an unspecified source or reference set whose choice changes the answer;
- an external or irreversible action without an authorization boundary;
- conflicting requirements or an undefined strict output schema.

When clarification is blocking, ask one to three high-information questions and do not produce a provisional optimized prompt. Otherwise, proceed with conservative local assumptions and list only assumptions actually made. Never invent facts, sources, policies, access, or permissions.

### 3. Rewrite for Kimi

Apply only techniques that improve the contract:

- Give the important background and concrete details Kimi would otherwise have to guess.
- Add a role only when domain perspective, behavior, or tone materially changes the result.
- Separate instructions, reference text, examples, and user data with unambiguous headings, XML tags, or triple quotes.
- State an ordered sequence when the task genuinely has dependent stages.
- Add a small number of representative examples when the desired style, classification boundary, or output mapping is difficult to describe directly.
- Specify approximate length with paragraphs, sentences, or bullets when practical; do not promise exact word or character counts.
- For grounded answers, identify the allowed reference text and state what to do when the answer is not present. Do not treat model memory as supplied evidence.
- Split complex workflows into coherent subtasks. For long documents, define chunking, per-chunk summaries, recursive synthesis, and preservation of necessary cross-chunk context when needed.
- For long conversations, summarize or filter history while preserving decisions, constraints, unresolved questions, and facts needed later.
- For routing workflows, classify the query first only when different classes truly require different instructions.
- Keep API settings, context-window management, retrieval code, and orchestration outside the copy-ready prompt unless the user requests a full implementation.

Do not add every technique to every prompt. A short, well-specified request may need only a surgical rewrite.

### 4. Validate before returning

Silently check:

1. **Intent:** The rewrite solves the same task without changing scope.
2. **Preservation:** All explicit facts, constraints, and required fields remain.
3. **Grounding:** Reference boundaries and missing-answer behavior are clear where needed.
4. **Structure:** Inputs cannot be mistaken for instructions, and dependent steps are ordered.
5. **Executability:** Kimi has the required inputs, or the smallest blocker is identified.
6. **Leanness:** Roles, examples, delimiters, and stages exist only when useful.
7. **Non-invention:** No new facts, access, sources, or permissions were assumed.

If a check exposes blocking ambiguity, ask the user instead of guessing.

### 5. Return the result

Honor the user's requested format. Otherwise return:

1. `优化后的提示词` or `Optimized prompt`: one copy-ready fenced block.
2. `关键改动` or `Key changes`: at most five concise bullets, only when useful.
3. `假设` or `Assumptions`: only assumptions actually made.
4. `API 设置建议` or `API settings`: only when requested or essential.

For prompt-only requests, return only the prompt. For blocking clarification, return only a short blocker statement, one to three questions, and optionally a one-line answer template.

## Completion bar

Finish only when the prompt is copy-ready, explicit requirements are preserved, source boundaries are clear where relevant, and Kimi does not need to guess a material part of the contract. Otherwise state the smallest missing information.
