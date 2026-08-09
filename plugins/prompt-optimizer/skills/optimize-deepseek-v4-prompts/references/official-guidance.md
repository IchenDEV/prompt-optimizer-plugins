# DeepSeek-V4 report evidence for prompt optimization

This is a concise working summary of the DeepSeek-AI technical report, checked on 2026-08-09. The report describes model design, post-training, evaluation, and product behavior; it is not a general prompting guide. Use live platform documentation for released model IDs, API fields, limits, availability, or pricing.

## Canonical source

- [DeepSeek-V4: Towards Highly Efficient Million-Token Context Intelligence](https://arxiv.org/abs/2606.19348)
- [Full HTML report](https://arxiv.org/html/2606.19348v1)

## Directly reported behavior

1. DeepSeek-V4 Pro and Flash support one-million-token contexts. The report says retrieval remains highly stable within 128K on its MRCR evaluation, with visible degradation beyond that point even though performance remains strong at one million tokens.
2. Both variants support Non-think, Think High, and Think Max reasoning modes. Non-think targets routine or low-risk work, High targets complex planning and problem solving, and Max targets the hardest reasoning tasks with greater compute.
3. The report uses `<think>` delimiters internally and applies a special system instruction for Think Max. It does not expose that instruction in the HTML text.
4. DeepSeek-V4 introduces an XML-based tool-call protocol with a `|DSML|` token. These are integration-level protocol details.
5. In actual tool-calling conversations, V4 can preserve reasoning state across tool-result and user-message rounds. Frameworks that simulate tool interactions through user messages may not trigger this behavior; the report recommends non-think models for those architectures.
6. Quick Instruction special tokens support internal auxiliary tasks such as search routing, query generation, authority classification, domain classification, and URL-reading decisions.
7. Evaluation prompts for mathematics ask for stepwise solving and a distinct final answer; the Max evaluation asks for a rigorous proof when an answer must be established.
8. Search behavior differs by product mode in the report: non-think uses retrieval-augmented search, while thinking uses agentic search.
9. Chinese writing evaluation emphasizes following explicit user requirements. The report also notes weaker results than Claude Opus 4.5 on its hardest high-constraint or multi-turn writing prompts.
10. The report evaluates professional work by task completion, instruction following, factual and logical content quality, and formatting readability.

## Bounded optimizer implications

- Do not inject unpublished Think Max instructions or special protocol tokens into normal prompts. Configure supported reasoning and tool modes through the current integration.
- Treat a large context window as capacity, not automatic grounding. Delimit sources, map document roles, request traceable evidence, and structure multi-document work.
- Preserve complex writing constraints explicitly and validate them before returning the optimized prompt.
- For search, state evidence authority, recency, citation, and insufficiency behavior in the task contract; let the product or API implement actual retrieval and agentic-search routing.
- For math and other auditable tasks, require a rigorous user-visible solution or verification when needed, but do not universally demand private internal reasoning traces.
- Treat Pro-versus-Flash and reasoning effort as runtime choices. Ask about them only when variant, latency, cost, or task difficulty materially changes the recommendation.
