# Official Gemini prompt design guidance

This is a concise working summary of Google AI's official guidance, checked on 2026-08-09. The source page reports a last update of 2026-06-10 UTC. Use the live pages for current model IDs, API fields, thinking controls, tool support, context limits, knowledge cutoffs, availability, or pricing.

## Canonical sources

- [Prompt design strategies](https://ai.google.dev/gemini-api/docs/prompting-strategies)
- [Structured outputs](https://ai.google.dev/gemini-api/docs/structured-output)
- [Thinking](https://ai.google.dev/gemini-api/docs/thinking)

## Reported principles

1. Give clear, specific instructions. Define the input, constraints, and response format; use partial completion or an answer prefix when it helps establish a simple output pattern.
2. Use the API's structured-output feature for complex JSON schemas instead of depending only on a natural-language formatting request.
3. The guide recommends few-shot examples to demonstrate format, wording, scope, or mappings. Use concrete and diverse examples, keep their structure consistent, and avoid so many that the model overfits.
4. Supply the context Gemini needs rather than assuming it has every relevant fact.
5. Decompose complex work by separating instructions, chaining dependent prompts, or aggregating parallel results when those structures match the task.
6. Iterate against observed outputs: try different wording, a closely related task formulation, or a different ordering of examples, context, and input.
7. Runtime parameters include maximum output tokens, temperature, `topK`, `topP`, and stop sequences. The guide strongly recommends leaving Gemini 3.x sampling parameters at their defaults because changes, including temperature below 1.0, can cause unexpected behavior on complex math or reasoning tasks.
8. Use Google Search grounding for obscure or current facts and code execution for arithmetic, counting, or calculations when the integration supports those tools.
9. Gemini 3 responds best to direct, well-structured prompts with defined terms, an explicit detail level, consistent delimiters, and clear references to every text or media input.
10. Put critical behavior, role, and output constraints in the system instruction or at the start. For large context, provide the context first, put the specific question at the end, and explicitly anchor it to the preceding material.
11. Gemini 2.5 and 3 use internal thinking. The guide says visible planning or step-by-step reasoning is usually unnecessary; a request to think carefully can help difficult tasks but consumes thinking tokens.

## Bounded optimizer interpretation

- Apply Gemini 3-specific advice only when the target is Gemini 3 or the user accepts that assumption. Keep family-wide prompts compatible when no version-specific behavior is needed.
- Use either Markdown headings or XML-style tags consistently. Do not add a large template to a simple task merely because the guide contains one.
- Preserve prompt-layer boundaries: system-level rules stay outside user-supplied context, and untrusted source text remains clearly marked as data.
- Make multimodal references explicit enough that Gemini knows which image, audio, video, document, or segment each instruction concerns.
- Add few-shot examples when they materially communicate behavior and can be made representative. Do not invent domain policy or label semantics solely to satisfy an example count.
- Keep sampling, thinking, grounding, code execution, safety, structured output, and other API controls outside ordinary prompt prose unless the user asks for an end-to-end request.
- Do not copy the guide's year-specific or model-cutoff example strings into a reusable prompt. Verify the actual current date, target model, and runtime-provided metadata first.
- Ask for user-visible rationale or validation only when the deliverable needs it; do not require disclosure of private internal chain-of-thought.

## Evaluation pattern

Test representative inputs rather than judging one polished example. Hold the model and runtime settings constant while changing one prompt element at a time, then compare task success, output-format validity, grounding, multimodal interpretation, latency, token use, and failure behavior. Promote a runtime-parameter change only when the evaluation demonstrates a repeatable gain.
