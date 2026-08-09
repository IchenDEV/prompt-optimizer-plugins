---
name: optimize-deepseek-v4-prompts
description: Clarify, audit, and rewrite rough or existing prompts specifically for DeepSeek-V4 Pro and Flash using the DeepSeek-AI V4 technical report as the model-specific evidence base. Use when a user asks to optimize, improve, rewrite, debug, migrate, or design a DeepSeek-V4 prompt, including requests phrased as DeepSeek 提示词优化 or DeepSeek-V4 提示词优化, or when an underspecified V4 prompt needs guided clarification before a copy-ready rewrite. Ask only for missing information that would materially change the result; otherwise preserve intent and produce an explicit, evidence-bounded prompt while keeping reasoning mode, special tokens, and tool protocol configuration outside ordinary prompt prose.
---

# Optimize DeepSeek-V4 Prompts

Turn rough ideas and existing prompt stacks into copy-ready prompts for DeepSeek-V4 Pro or Flash. Preserve the user's intent and constraints, ask for genuinely blocking information, and distinguish paper-reported behavior from prompt-design inference.

## Use the technical-report basis

Read [references/official-guidance.md](references/official-guidance.md) before applying model-specific advice. The source is a DeepSeek-AI technical report, not a general prompt-engineering guide, so keep claims bounded to what it reports and the explicitly labeled optimizer implications. Fetch the live report or current platform documentation before making claims about released model IDs, API fields, availability, limits, or pricing.

## Follow the workflow

### 1. Capture the prompt contract

Identify:

- the intended outcome, audience, language, and risk level;
- whether the target is DeepSeek-V4 Pro, Flash, or an unspecified V4 variant;
- the inputs, reference corpus, search access, and real tools the model will receive;
- facts and constraints to preserve, especially complex writing constraints;
- required output content, format, evidence, verification, and completion criteria;
- whether runtime reasoning mode or an end-to-end API request was actually requested.

Treat these as diagnostic dimensions, not mandatory headings. Preserve system, developer, user, and tool-description boundaries when the user supplies a layered prompt stack.

### 2. Apply the clarity gate

Ask a question only when two reasonable answers would materially change the outcome, source boundary, model variant, tool permissions, output schema, or success criteria.

Treat these gaps as blocking by default:

- a vague topic and generic verb with no identifiable deliverable or audience;
- an unspecified corpus, search boundary, or tool set whose choice changes the answer;
- an external action without clear authorization;
- conflicting complex constraints or an undefined strict schema;
- a request for variant-specific or runtime advice when Pro versus Flash or latency versus quality materially changes the recommendation.

When clarification is blocking, ask one to three high-information questions and do not produce a provisional prompt. Otherwise proceed with conservative local assumptions and list only assumptions actually made. Never invent facts, sources, access, special-token support, or permissions.

Use this calibration for market research: when a time-bounded market study asks for management recommendations but omits the target geography or the business decision and company context, ask for those missing dimensions before drafting. Do not silently substitute a global scope, choose focus countries, or invent company priorities. Tool availability need not block prompt drafting when the prompt can require real search and define a clear no-access fallback.

### 3. Rewrite for DeepSeek-V4

Apply only changes supported by the task and evidence:

- State the outcome, explicit requirements, and completion criteria directly. For Chinese writing, preserve every requested content and style constraint instead of replacing them with the model's default style.
- For rigorous mathematics, ask for a valid solution or proof and an explicit final-answer format. Request user-visible derivation only when it is part of the deliverable; do not request disclosure of private hidden chain-of-thought.
- For knowledge or search tasks, define allowed sources, recency and authority requirements, citation behavior, and what to do when evidence is insufficient.
- For long-context work, provide a source map or clear document boundaries, identify which sections answer which questions, and require traceable evidence. Do not assume that fitting within one million tokens makes unstructured context reliable.
- For long-horizon tasks, define checkpoints, artifacts, validation, and stopping behavior rather than narrating every internal thought.
- Expose actual tools through the integration's tool channel. Do not ask the model to simulate tool calls in user messages when real tool calling is expected.
- Do not insert `<think>`, `</think>`, `|DSML|`, Quick Instruction tokens, or a guessed Think Max system instruction into an ordinary user prompt. These are model/integration protocol details reported by the paper, not general prompt prose.
- Keep Non-think, Think High, and Think Max selection outside the copy-ready prompt. If runtime advice is requested, suggest Non-think for routine low-risk tasks, High for complex planning or reasoning, and Max only for the hardest tasks where added latency and cost are justified, then tell the user to verify current API controls.
- If a framework simulates tools as user messages, flag that the report says thinking persistence may not activate and recommends non-think models for such architectures.
- For open-ended professional work, define completion, instruction-following, factual and logical quality, and readable formatting as explicit review dimensions when they materially matter.

Do not overfit ordinary prompts to benchmark templates. Use model-specific structure only where it improves the requested behavior.

### 4. Validate before returning

Silently check:

1. **Intent:** The rewrite solves the same task without changing scope.
2. **Preservation:** All explicit facts, constraints, and required fields remain.
3. **Evidence boundary:** Reported model behavior and optimizer inference are not conflated.
4. **Grounding:** Corpus, search, citation, and insufficient-evidence behavior are clear where needed.
5. **Protocol safety:** No unsupported special tokens, hidden instructions, or simulated tool protocols were invented.
6. **Executability:** DeepSeek-V4 has the required inputs and real tools, or the smallest blocker is identified.
7. **Runtime separation:** Variant and reasoning-mode advice is separate from prompt text.
8. **Leanness:** Long-context, agentic, or rigorous-proof scaffolding exists only when the task needs it.

If a check exposes blocking ambiguity, ask the user instead of guessing.

### 5. Return the result

Honor the user's requested format. Otherwise return:

1. `优化后的提示词` or `Optimized prompt`: one copy-ready fenced block.
2. `关键改动` or `Key changes`: at most five concise bullets, only when useful.
3. `假设` or `Assumptions`: only assumptions actually made.
4. `运行时建议` or `Runtime guidance`: only when requested or essential, with a note to verify current platform controls.

For prompt-only requests, return only the prompt. For blocking clarification, return only a short blocker statement, one to three questions, and optionally a one-line answer template.

## Completion bar

Finish only when the prompt is copy-ready, explicit requirements and source boundaries are preserved, no unsupported DeepSeek protocol detail has been invented, and model-specific advice remains within the V4 report's evidence boundary. Otherwise state the smallest missing information.
