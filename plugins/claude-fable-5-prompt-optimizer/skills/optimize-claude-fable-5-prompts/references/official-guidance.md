# Official Claude Fable 5 prompting guidance

This is a concise working summary of Anthropic's official documentation, checked on 2026-07-15. Use the live pages for current model IDs, parameters, availability, limits, or pricing.

## Canonical sources

- [Prompting Claude Fable 5](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-fable-5)
- [Prompting best practices](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices)
- [Introducing Claude Fable 5 and Claude Mythos 5](https://platform.claude.com/docs/en/about-claude/models/introducing-claude-fable-5-and-claude-mythos-5)

## Model-specific principles

1. Claude Fable 5 is designed for demanding reasoning and long-horizon agentic work. Its API model ID was `claude-fable-5` when this summary was checked.
2. Fable 5 follows instructions strongly. Start by simplifying prompts and removing scaffolding that compensates for weaker instruction following in older models.
3. At higher effort, the model can over-explore, over-engineer, or add unrequested refactors, features, abstractions, and fallback systems. State the intended scope and forbid likely scope expansion when relevant.
4. Give the task's purpose and context, not only commands. Explaining why helps the model generalize across edge cases.
5. State whether the task is assessment, recommendation, drafting, implementation, or execution. Do not let a request for information silently authorize state changes.
6. Ground progress and final claims in actual tool results. If a test fails, a step is skipped, or validation is unavailable, report that accurately.
7. Pause for destructive or irreversible operations, genuine scope expansion, or input only the user can provide. Continue through safe, reversible, authorized work.
8. For long tasks, tell the model to act when it has enough information and avoid repeatedly re-deriving facts, revisiting settled decisions, or narrating unused options.
9. Lead final summaries with the outcome. Use complete sentences and readable prose rather than compressed shorthand.
10. Add subagents, durable memory, or an asynchronous user-update tool only when the workflow genuinely needs them.

## Prompt construction

- Be clear and direct. Specify the desired output, constraints, and relevant context.
- Prefer positive instructions that say what to do. Use negative constraints for real boundaries and known failure modes.
- Put a role in the system prompt only when the role changes behavior materially.
- Use three to five relevant and varied examples when examples are the clearest way to specify format, tone, classifications, or edge cases.
- Use descriptive XML tags to separate instructions, context, examples, variable inputs, or documents. Do not add XML that does not clarify structure.
- Match the prompt's formatting style to the desired output style.
- State implement-versus-suggest behavior explicitly for tool-using agents. Run independent tool calls in parallel only when their parameters do not depend on one another, and never guess missing parameters.

## Long-context pattern

For prompts containing large documents or multiple sources:

1. Place the source material near the top.
2. Wrap sources in descriptive tags such as `<documents>`, `<document>`, `<source>`, and `<document_content>`.
3. Put the query and task instructions after the documents.
4. When the answer must be tightly grounded, ask for relevant excerpts first, followed by the synthesis. Ask for evidence, not hidden reasoning.

## Runtime settings versus prompt text

- Anthropic's Fable 5 guide recommends `high` effort for most tasks, `xhigh` for the most capability-sensitive work, and `medium` or `low` for more routine work. Treat this as a runtime starting point to evaluate, not text to insert into the prompt.
- Adaptive thinking is always enabled for Fable 5. Thinking-token budgets such as `budget_tokens` are not supported.
- Raw chain of thought is not returned. Asking the model to reproduce hidden reasoning can cause a reasoning-extraction refusal. Request concise rationale, evidence, checks, and uncertainty instead.
- Assistant-response prefills are unsupported on current Claude 4.6 and later models, including Fable 5. Redesign older prefill-dependent integrations.
- A model refusal can arrive as a successful HTTP response with a refusal stop reason. Detect and handle it in application code.
- Keep model choice, effort, retries, refusal handling, tool schemas, and fallback routing outside the prompt unless the user asks for a complete request configuration.

## Evaluation and migration pattern

1. Establish a representative baseline with the current model, prompt, and settings.
2. Run Fable 5 with the same prompt and appropriate effort.
3. Inspect observed failures before editing the prompt.
4. Remove obsolete scaffolding and make the smallest change tied to an observed failure.
5. Test high-effort tasks for unwanted exploration, extra features, refactors, abstractions, and repeated analysis.
6. Verify output-contract adherence, evidence quality, tool behavior, completion accuracy, latency, token use, and cost.
7. Change one prompt group or runtime setting at a time so regressions remain attributable.

Use representative evaluations rather than declaring a prompt better because it is shorter, more structured, or more detailed.
