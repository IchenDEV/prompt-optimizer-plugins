# Copy-ready instruction blocks

Blocks quoted or closely adapted from Anthropic's [Prompting Claude Sonnet 5.5](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-sonnet-5-5) guide, checked on 2026-09-29. Add only the blocks that match a behavior the user actually observes, and adapt the wording to their product voice. Adding all of them at once is a regression, not an optimization.

## Carry work through and stop when done

For agentic coding at `low` or `medium` effort when the model checks in early, or when it adds unrequested extras:

```text
Keep working until everything the user asked for is done, and only stop to ask when you can't go on without the user or before a risky step.

When the work the user asked for is done and checked, stop and report. Don't add features, tests, files, docs or refactors that weren't asked for. If you think one would help, mention it at the end instead of doing it.
```

If the user welcomes helpful extras but wants carry-through, keep only the first paragraph. If they want scope limited without changing check-in behavior, keep only the second paragraph. At `xhigh` and `max`, the second paragraph also reduces unrequested additions.

## Cap self-started review at xhigh/max

```text
When the work the user asked for is done and its checks pass, stop and report. Don't start extra rounds of review or hardening on your own, and don't launch reviewer sub-agents unless the user asked for a review. If you think a deeper review is worth doing, say so at the end.
```

## Ideas or plan first on open-ended requests

```text
When the user asks for ideas, options or a plan, give them that and stop. Don't start building or changing anything until they say to go ahead.
```

## Think before answering (structured JSON / multi-step reasoning)

With adaptive thinking, place at the end of the system prompt:

```text
Think the problem through before you answer.
```

Do not use this with `between_tools` on tool-free requests; switch to adaptive thinking instead. Prefer structured outputs when available. Treat `stop_reason: "max_tokens"` as a failed attempt even if the text looks like valid JSON.

## Search for changeable facts

Remove language that discourages tool use, then add:

```text
Use the search tool to check specifics that may have changed since your training, such as what is allowed, required or charged, even when you feel confident. For researched work such as a report or a comparison, gather current sources rather than writing from your training knowledge.
```

## Real verification on coding tasks (especially low effort)

```text
When you change code that can be run, built, or type-checked, run a real check that exercises the change before reporting it done: the project's tests, type-checker, or build, or the changed command itself. A syntax-only check, or a check command that failed to start, does not count; if all that is missing is the project's declared dependencies, install them with its own package manager and lockfile (e.g. npm install, pip install -r requirements.txt), never via sudo or the system package manager, unless told not to. Only if no real check can run here, say which one you did not run and why instead of reporting the change as done.
```

## Predictable progress updates

After enabling progress rendering (`display: "updates"` or `between_tools` notes), remove "hold all findings for the final response." If you want updates at set points, say so explicitly, for example:

```text
Before your first tool call, say in one sentence what you're about to do. While working, give a brief update when you find something important or change direction. When you finish, lead with the outcome in the first sentence.
```

Harness-side one-turn reminders after several silent tool steps are an alternative; keep them rare so they are not misread as injections.

## Between_tools: avoid anti-thinking rules

When using `thinking: {"type": "between_tools"}`, remove any instruction that tells the model not to think. If tag leakage still appears, prefer adaptive thinking with lower effort, or use a general anti-leakage line:

```text
Do not include internal or system XML tags in your response.
```

Do not name specific internal tags; the general form works better.

## Instructions to delete rather than rewrite

These patterns hurt Sonnet 5.5 more often than they help:

- Sonnet 5 refusal-steering or "do not be lazy" workarounds.
- "Only use tools when strictly necessary" / "minimize tool calls" when search or other tools are available for changeable facts.
- "Do not think or reason before answering."
- "Show your chain of thought" / "reproduce your internal reasoning."
- "Hold all findings for the final response" when the product needs mid-turn progress.
- Forced `tool_choice` of type `any` or `tool` (API-level breakage, not a prompt fix).

Delete them. Do not replace a removed anti-thinking rule with a stronger prohibition on thinking.
