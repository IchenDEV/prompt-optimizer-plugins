# Copy-ready instruction blocks

Blocks quoted from Anthropic's [Prompting Claude Opus 5](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-opus-5) guide, checked on 2026-07-25. Add only the blocks that match a behavior the user actually observes, and adapt the wording to their product voice. Adding all of them at once is a regression, not an optimization.

## Response length and verbosity

For a user-facing multi-turn product:

```text
Keep responses focused, brief, and concise. Keep disclaimers and caveats short, and spend most of the response on the main answer. When asked to explain something, give a high-level summary unless an in-depth explanation is specifically requested.
```

In a long system prompt, pair the instruction above with a short reminder near the end of the prompt:

```text
<tone_preference>
Keep outputs reasonably concise.
</tone_preference>
```

## User-facing progress updates

To tune narration down:

```text
Before your first tool call, say in one sentence what you're about to do. While working, give a brief update only when you find something important or change direction. When you finish, lead with the outcome: your first sentence should answer "what happened" or "what did you find," with supporting detail after it for readers who want it.
```

To tune narration up or change its style, use the same lever in the other direction: describe explicitly what updates should look like and provide examples. Positive examples of the communication style you want are more effective than instructions about what not to do.

## Written deliverable length

For products that include Claude-authored documents on disk:

```text
Match the length of written documents to what the task needs: cover the substance, but do not pad with filler sections, redundant summaries, or boilerplate.
```

## Task scope

For narrow tasks where the model should not expand scope:

```text
Deliver what was asked, at the scope intended. Make routine judgment calls yourself, and check in only when different readings of the request would lead to materially different work. If the request seems mistaken or a better approach exists, say so in a sentence and continue with the task as asked rather than quietly narrowing, widening, or transforming it. Finish the whole task, and stop short of actions that are clearly beyond what was asked.
```

## Controlling subagent spawning

For harnesses that support subagents and are cost-sensitive:

```text
Delegate to a subagent only for large tasks that are genuinely independent and parallelizable, such as a wide multi-file investigation. Do not delegate work you can finish yourself in a handful of tool calls, and do not use subagents to verify or double-check your own work. If one subagent can complete the task, use one rather than several, and keep spawn counts low.
```

Deterministic caps in the harness are an alternative to, or a complement to, this instruction.

## Self-correction narration

To limit correction narration to corrections that matter:

```text
Only correct an earlier statement when the error would change the user's code, conclusions, or decisions. State corrections plainly and briefly, then continue the task. For slips that change nothing for the user, make the fix and move on without noting it.
```

Do not pair this with instructions to double-check or re-verify; Opus 5 already catches and fixes its own mistakes.

## Thinking disabled: tool calls leaking into text

Prefer keeping thinking enabled and controlling cost with lower effort. For integrations that must keep thinking disabled, give explicit permission to speak before a tool call:

```text
You may say a brief sentence before using a tool.
```

## Thinking disabled: internal XML tags in output

Remove any system-prompt rule instructing the model not to think or not to reason; that kind of instruction increases tag leakage. Where an explicit instruction is wanted, use the general form:

```text
Do not include internal or system XML tags in your response.
```

Instructions that name thinking tags specifically are less effective than the general form, so avoid naming them.

## Code review prompts

Do not write "only report high-severity issues" or "be conservative"; Opus 5 follows this literally and reports less. Ask for all findings and filter in a separate pass:

```text
Report every issue you find, including low-severity ones, with a severity label on each. Do not filter or withhold findings; a later pass decides what to act on.
```

## Instructions to delete rather than rewrite

These patterns cost tokens on Opus 5 without improving quality:

- "Include a final verification step for any non-trivial task."
- "Use a subagent to verify your work."
- "Double-check your answer before responding."
- "Re-verify your conclusions before finalizing."
- "Do not think or reason before answering."
- Separate verification stages in legacy harness scaffolding.
- Vision workarounds tuned for prior models.

Delete them. Do not replace them with the opposite instruction.
