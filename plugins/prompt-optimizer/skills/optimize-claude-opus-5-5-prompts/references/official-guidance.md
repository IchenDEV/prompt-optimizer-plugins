# Official Claude Opus 5.5 prompting guidance

A concise working summary of Anthropic's official documentation, checked on 2026-09-23. Use the live pages for current model IDs, parameters, availability, limits, or pricing.

## Canonical sources

- [Prompting Claude Opus 5.5](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-opus-5-5)
- [What's new in Claude Opus 5.5](https://platform.claude.com/docs/en/models/opus-5-5/whats-new-opus-5-5)
- [Migrating to Claude Opus 5.5](https://platform.claude.com/docs/en/models/opus-5-5/migration-guide)
- [Prompting Claude Opus 5](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-opus-5) (still a reasonable starting point for symptoms that match that guide)
- [Prompting best practices](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices)
- [Effort](https://platform.claude.com/docs/en/build-with-claude/effort)

## Model facts

- API model ID `claude-opus-5-5`. Also available as `anthropic.claude-opus-5-5` on Amazon Bedrock, `claude-opus-5-5` on Google Cloud / Claude Platform on AWS, and through Microsoft Foundry.
- Built for long-running agentic coding and knowledge work. Generates output tokens more than 30% faster than Claude Opus 5 and tends to finish the same task with fewer tokens.
- Existing Claude Opus 5 prompts should perform well without changes.
- Adaptive thinking is always on. Omit `thinking`, or send `thinking: {"type": "adaptive"}`. `thinking: {"type": "disabled"}` and manual `budget_tokens` return 400.
- Default effort is `medium` (Claude Opus 5 defaults to `high`). Effort is the only thinking-depth control.
- 1M-token context window and 128k max output tokens (confirm on the live model page).
- Forced tool use is unsupported: `tool_choice` types `any` and `tool` return 400. Use `auto` with strict tools or structured outputs; say in the prompt when a tool applies.
- On the Claude API and Google Cloud, computer use requires `computer_toolset_20260801` (not `computer_20251124`). Amazon Bedrock still accepts `computer_20251124`.

## Behavior differences that drive prompting

1. **Effort calibration.** At a given effort level the model tends to think more per turn than Opus 5, especially at `xhigh` and `max`. Start at `medium`, set effort explicitly, and re-run sweeps rather than carrying Opus 5 settings. In Anthropic's testing, Opus 5.5 at `medium` matched or exceeded Opus 5 at `high` on coding and knowledge-work evals; on several coding evals `low` came close at much lower cost.
2. **Always-on thinking.** Integrations that disabled thinking on Opus 5 must migrate: start at `low`, remove response-text reasoning substitutes, drop anti-thinking rules, and select content blocks by `type`.
3. **Unattended early stops.** On long multi-part tasks the model keeps the user updated, and some updates end the turn with text (`stop_reason: "end_turn"`) rather than a tool call. Unattended loops that treat that as completion stop early. Keep a checklist, continue open items, and optionally add a system instruction naming unwanted early-stop patterns.
4. **Progress updates in thinking blocks.** Short notes between tool calls arrive as progress-update `thinking` blocks. At default `display: "omitted"` their text is empty, so UIs that only render `text` look silent. Set `display: "updates"` (beta) or `"summarized"` and render non-empty thinking blocks.
5. **Safeguard refusals.** Classifiers include biology (aligned with Fable 5.1), cybersecurity, and `reasoning_extraction`. Declines arrive as HTTP 200 with `stop_reason: "refusal"` and `stop_details.category`.
6. **Multi-app exploration.** The model tends to act quickly; for loosely specified multi-app workflows, tell it to explore relevant sources before changing anything.
7. **Time signals.** The model pays close attention to elapsed time. Passing `elapsed Ns / budget Ns` (or elapsed alone plus a time-matters sentence) helps multi-agent teams finish sooner via better parallelization.
8. **Chat thinking latency.** Remove "think carefully" lines. For multi-turn chat, optionally tell the model to treat earlier answers as settled unless the user revisits them.
9. **Pasted-content injection resistance.** Mark pasted user text with tagged `<pasted_content>` blocks and matching random IDs so instructions inside pasted material are followed only when the user's own message asks.
10. **Stronger vision without tools.** Dense charts, position-dependent diagrams, and screenshots read more accurately; re-test older vision scaffolding. Crop/zoom tools and higher resolution still help on densest inputs.
11. **Frontend defaults.** Vague "avoid generic AI look" swaps one default for another; name specific patterns to avoid and iterate.

## Capability notes

- **Agentic coding and code review:** strongest on multistep real-repo work through tests. Sustains long autonomous runs (multi-hour audits/migrations with parallel subagents) better than Opus 5. Stronger code review with more real bugs and fewer false alarms in early tester reports.
- **Knowledge work:** less likely to state incorrect figures or cite wrong sources; stronger financial modeling and detail catching in large inputs; office docs need less editing.
- **Communication:** progress updates and final summaries state plainly what was done, found, and needed from the user.
- **Computer use:** more reliable; at default effort matched success rates Opus 5 reached only at much higher effort.

## Runtime settings versus prompt text

- Set `output_config.effort` explicitly. Default without it is `medium`.
- Lower effort to cut thinking, cost, and latency more reliably than prompt instructions. Reserve `xhigh` and `max` for measured quality gains.
- Changing top-level `effort` between requests invalidates the prompt cache; use per-message effort (beta) for single-turn level changes while keeping the cache.
- Size `max_tokens` for thinking plus reply. Thinking counts toward `max_tokens` even when content is omitted. For long agentic coding turns, 128,000 has worked well in Anthropic's testing.
- Pass `thinking` blocks back unmodified in tool-use loops. Select blocks by `type`, not position.
- Keep conversations append-only when relying on preserved thinking. Mid-conversation instruction or tool changes should use mid-conversation system messages rather than editing earlier prefixes.
- Claude Opus 5.5 reads thinking from Opus 5 and earlier Opus/Sonnet/Haiku models, but not from Fable or Mythos. On the Claude API, Fable 5.1 and Mythos 5.1 read Opus 5.5 thinking; other models do not.
- Handle `stop_reason: "refusal"` in application code; configure fallback except that server-side fallback returns `reasoning_extraction` declines to you.
- Fast mode (`speed: "fast"`, research preview) is Claude API only.
- Keep model choice, effort, display, retries, refusal handling, tool schemas, computer-use toolset choice, and fallback routing outside the prompt unless the user asks for a complete request configuration.
- Raw chain of thought is not returned for free inspection under default display. Asking the model to reproduce hidden reasoning can trigger `reasoning_extraction`. Request concise rationale, evidence, checks, and uncertainty instead—or read summarized thinking blocks.

## Migration and evaluation pattern

1. Update the model ID to `claude-opus-5-5` and run the existing Opus 5 prompt unchanged.
2. Apply breaking API changes: remove disabled/manual thinking; replace forced `tool_choice`; migrate computer use to the toolset on Claude API / Google Cloud; set `thinking.display` if the UI streams inter-tool narration.
3. Set effort explicitly and re-run a sweep starting from `medium`.
4. Inspect observed failures before editing prompt text. Most fixes are removals (think-hard lines, response-text reasoning, obsolete thinking-disabled mitigations).
5. Add at most one targeted instruction per observed behavior: unattended continuation, progress cadence, multi-app explore, time signals, settle-earlier-answers, pasted-content marking, frontend avoid-list.
6. Verify output-contract adherence, evidence quality, tool behavior, completion accuracy, silence between tool calls, latency, token use, and cost. Change one prompt group or runtime setting at a time so regressions remain attributable.
