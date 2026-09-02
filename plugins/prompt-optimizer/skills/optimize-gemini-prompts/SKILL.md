---
name: optimize-gemini-prompts
description: Clarify, audit, and rewrite rough or existing prompts for the Gemini model family using Google AI's official prompt design strategies. Use when a user asks to optimize, improve, rewrite, debug, migrate, or design a Gemini prompt, including explicit Gemini prompt optimization requests, or when an underspecified Gemini prompt needs guided clarification before a copy-ready rewrite. Ask only for missing information that would materially change the result; otherwise preserve intent and produce a direct, consistently structured prompt with clear input boundaries, output requirements, grounding rules, multimodal references, and completion criteria where relevant.
---

# Optimize Gemini Prompts

Turn rough ideas and existing prompt stacks into copy-ready prompts for Gemini. Preserve the user's intent and hard constraints, ask for genuinely blocking information, and add model-specific structure only when it changes behavior.

## Use the official basis

Read [references/official-guidance.md](references/official-guidance.md) when model-specific rationale, runtime guidance, or the longer checklist is needed. Treat its prose as a dated summary and its URLs as canonical sources. Fetch the live Google AI pages before making claims about current model IDs, API fields, thinking controls, tool support, context limits, knowledge cutoffs, availability, or pricing.

## Follow the workflow

### 1. Capture the prompt contract

Identify:

- the intended outcome, audience, language, and desired level of detail;
- the target Gemini model or model family when version-specific behavior matters;
- the text, documents, images, audio, video, examples, or other inputs Gemini will receive;
- facts, definitions, and hard constraints to preserve;
- the required output content, structure, schema, and length;
- allowed sources, grounding, tools, calculations, citations, and validation needed for success;
- fallback behavior when supplied context or tools do not support an answer.

Treat these as diagnostic dimensions, not mandatory headings. Preserve system, developer, user, tool-description, and untrusted-data boundaries when the user supplies a layered prompt stack.

### 2. Apply the clarity gate

Ask a question only when two reasonable answers would materially change the outcome, source boundary, model-specific strategy, multimodal interpretation, schema, permissions, or success criteria.

Treat these gaps as blocking by default:

- a vague topic and generic verb with no identifiable deliverable or audience;
- an input or source set whose identity changes what Gemini may conclude;
- a multimodal request that does not identify which media or regions the instructions refer to;
- a strict output contract with undefined fields, allowed values, or missing-value behavior;
- an external or irreversible action without clear authorization;
- conflicting requirements or a request for version-specific advice without a target model when the answer differs materially by version.

When clarification is blocking, ask one to three high-information questions and do not produce a provisional optimized prompt. Otherwise proceed with conservative local assumptions and list only assumptions actually made. Never invent facts, sources, media contents, policies, access, model capabilities, or permissions.

### 3. Rewrite for Gemini

Apply only techniques that improve the contract:

- State the goal accurately and directly. Define ambiguous terms or parameters instead of relying on persuasive language or repeated emphasis.
- Put essential standing behavior, hard constraints, and output rules in the system instruction or at the beginning of the user prompt when the target integration supports that separation.
- Use one consistent structure. Separate instructions, examples, context, and user input with either clear Markdown headings or XML-style tags; do not mix delimiter styles without a reason.
- For large context, place the source material before the specific task, then anchor the final request with wording such as `Based on the context above`. Keep critical system-level rules outside the untrusted context.
- Refer to each image, audio clip, video, document, or relevant segment explicitly. Treat media as real inputs, not as decorations or facts to guess.
- Specify output detail, format, length, and allowed extra prose. Gemini 3 defaults to direct answers, so request a conversational or detailed response explicitly when needed.
- Add a small set of concrete, diverse, consistently formatted few-shot examples when the desired mapping, scope boundary, tone, or output pattern is difficult to express directly. Do not add redundant examples or examples that invent business rules.
- For simple output shapes, use an answer prefix or completion pattern when it clarifies the expected continuation. For complex machine-consumed JSON, recommend the API's structured-output feature separately rather than relying on prose alone.
- Ground factual work in the supplied sources and define what to do when the answer is absent. If current or obscure facts require Google Search grounding, or arithmetic requires code execution, describe the need only when those tools will actually be available and keep tool enablement in runtime guidance.
- Split or chain genuinely complex tasks when one prompt would combine conflicting jobs or when intermediate outputs need independent validation. Keep a single coherent task in one prompt.
- Do not ask Gemini 2.5 or 3 to reveal internal chain-of-thought. Request a concise rationale, derivation, plan, or validation result only when it is part of the user-visible deliverable.
- Keep model selection, temperature, `topP`, `topK`, maximum output tokens, stop sequences, thinking configuration, grounding, code execution, safety configuration, and structured-output settings outside the copy-ready prompt unless the user requests a full API configuration.
- For Gemini 3.x, preserve default sampling values unless representative evaluations justify a change. Never copy a hard-coded current year or knowledge-cutoff date from an example without verifying it for the target model and runtime.

Prefer a surgical rewrite for an existing prompt with a clear contract. Do not force the full XML template from the documentation onto a simple task.

### 4. Validate before returning

Silently check:

1. **Intent:** The rewrite solves the same task without changing scope.
2. **Preservation:** All explicit facts, definitions, constraints, and required fields remain.
3. **Structure:** Instructions, examples, context, and input are consistently delimited and correctly ordered.
4. **Grounding:** Allowed sources, citations, tools, and missing-evidence behavior are clear where needed.
5. **Multimodality:** Every required media input is referenced unambiguously.
6. **Output contract:** Detail, schema, allowed values, and extra-text behavior are defined to the degree the consumer needs.
7. **Executability:** Gemini has the required inputs and capabilities, or the smallest blocker is identified.
8. **Runtime separation:** API controls and tool enablement are not disguised as prompt prose.
9. **Leanness:** Roles, examples, delimiters, and task decomposition exist only when useful.
10. **Non-invention:** No facts, sources, media contents, capabilities, access, or permissions were assumed.

If a check exposes blocking ambiguity, ask the user instead of guessing.

### 5. Return the result

Honor the user's requested format. Otherwise return:

1. `Optimized prompt`: one copy-ready fenced block, or separate system and user blocks when both are needed.
2. `Key changes`: at most five concise bullets, only when useful.
3. `Assumptions`: only assumptions actually made.
4. `API settings`: only when requested or essential, and separate from the prompt.

For prompt-only requests, return only the prompt. For blocking clarification, return only a short blocker statement, one to three questions, and optionally a one-line answer template.

## Completion bar

Finish only when the prompt is copy-ready, explicit requirements are preserved, inputs and output are unambiguous, runtime controls remain separate, and Gemini does not need to guess a material part of the contract. Otherwise state the smallest missing information.
