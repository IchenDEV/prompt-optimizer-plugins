# Official GLM prompting guidance

This is a concise working summary of Zhipu AI's official guidance, checked on 2026-08-09. Use the live page for current model IDs, API features, parameters, context limits, availability, or pricing.

## Canonical source

- [Zhipu AI: Prompt engineering](https://docs.bigmodel.cn/cn/guide/platform/prompt)

## Reported principles

1. Give GLM clear, specific instructions. Use a system prompt to define assistant behavior such as role, language style, task mode, and problem-specific rules.
2. Add concrete details and background, and use role-playing when a domain perspective improves the response.
3. Use delimiters to distinguish input sections.
4. For reasoning problems, the guide recommends stepwise solutions so accuracy can be assessed.
5. Use few-shot examples to demonstrate a desired behavior or style.
6. Specify output length, while recognizing that exact word counts are difficult to guarantee.
7. Provide external reference material to improve accuracy and recency and reduce unsupported output. Use retrieval when relevant material cannot fit directly in context.
8. Decompose complex work into smaller sequential tasks.
9. When output feeds a backend, require a fixed parseable format such as JSON.
10. Summarize important earlier context in long conversations and recursively combine section summaries for long documents.

## Bounded optimizer interpretation

- Preserve system and user message boundaries rather than turning the entire request into one undifferentiated prompt.
- Translate the guide's stepwise-reasoning advice into the smallest user-visible rationale or derivation the deliverable needs. Do not request hidden internal chain-of-thought as a universal technique.
- Define JSON field semantics, missing values, allowed values, and extra-text rules when parseability matters; merely saying "output JSON" is not a complete contract.
- Treat retrieval and runtime structured-output controls as application configuration unless the user asks for an end-to-end request.
- Use roles, examples, and decomposition conditionally; the source presents techniques that can be combined, not mandatory sections for every prompt.
