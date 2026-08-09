# Official Kimi prompting guidance

This is a concise working summary of Moonshot AI's official guidance, checked on 2026-08-09. Use the live page for current models, API fields, context limits, availability, or pricing.

## Canonical source

- [Kimi API: Prompt best practices](https://platform.kimi.com/docs/guide/prompt-best-practice)

## Reported principles

1. Give clear instructions and all important details and background. The less the model must infer about the request, the more likely the output will match the user's intent.
2. Assign a role when a particular perspective or behavior will improve accuracy.
3. Use delimiters such as triple quotes, XML tags, or section headings to separate different input parts.
4. State the steps required to complete a multi-stage task.
5. Provide examples when they communicate a desired style or behavior more effectively than general prose.
6. Specify output length. Counts expressed as paragraphs, sentences, or bullets are more reliable than exact word counts.
7. Supply trustworthy reference text and direct the model to answer from it. Define a fallback such as saying the answer cannot be found when the material does not support an answer.
8. Split complex tasks into simpler stages. Classification can route queries to the relevant instruction set.
9. Summarize or filter earlier turns in long conversations as context grows.
10. Summarize long documents in chunks and recursively combine partial summaries, retaining prior context when later sections depend on it.

## Bounded optimizer interpretation

- Use roles, examples, and multi-stage scaffolds conditionally; the source presents them as techniques, not mandatory prompt sections.
- Treat reference-grounded fallback behavior as part of the prompt contract, not as permission to invent missing facts.
- Keep retrieval, chunking infrastructure, model selection, and API parameters outside the prompt unless the user asks for an end-to-end request or application design.
- Preserve prompt-layer boundaries. A role or standing behavior belongs in a system message only when the target integration actually supports that message structure.
