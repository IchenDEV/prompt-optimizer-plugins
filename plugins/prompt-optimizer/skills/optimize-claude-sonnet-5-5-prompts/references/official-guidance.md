# Official Claude Sonnet 5.5 prompting guidance

A concise working summary of Anthropic's official documentation, checked on 2026-09-29. Use the live pages for current model IDs, parameters, availability, limits, or pricing.

## Canonical sources

- [Prompting Claude Sonnet 5.5](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-sonnet-5-5)
- [Building with Claude Sonnet 5.5](https://claude.dev/blog/building-with-claude-sonnet-5-5/)
- [Sonnet 5.5 migration guide](https://platform.claude.com/docs/en/models/sonnet-5-5/migration-guide)
- [Choosing the right model](https://platform.claude.com/docs/en/about-claude/models/choosing-a-model)
- [Prompting best practices](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices)
- [Effort](https://platform.claude.com/docs/en/build-with-claude/effort)

## Model facts

- API model ID `claude-sonnet-5-5`. Also available as `anthropic.claude-sonnet-5-5` on Amazon Bedrock, and as `claude-sonnet-5-5` on Claude Platform on AWS, Google Cloud, and Microsoft Foundry (Global Standard deployments).
- 1M-token context window, native, no beta header. 128k max output tokens; up to 300k on Message Batches with the documented beta header.
- Knowledge cutoff: June 2026.
- Thinking is on by default (adaptive thinking). `thinking: {"type": "disabled"}` returns a 400 error; use `between_tools` to turn off up-front thinking.
- Effort levels: `low`, `medium`, `high`, `xhigh`, `max`. Default is `high` on the Claude API and `medium` in Claude Code.
- Same per-token price as Sonnet 5; typically fewer tokens per task, so overall cost often drops. Minimum cacheable prompt length is 512 tokens.
- Built for well-scoped everyday coding, high-volume development, polished documents/slides/spreadsheets, and repeated well-defined agent tasks. For the hardest long-horizon work, prefer an Opus model.

## Choosing Sonnet 5.5 versus Opus 5.5

| Workload | Start with |
| --- | --- |
| Well-scoped everyday coding: bugs, feature iteration, verifying against requirements | Sonnet 5.5 |
| High-volume everyday development | Sonnet 5.5 |
| Polished documents, slides, spreadsheets with an eye for design | Sonnet 5.5 |
| Well-defined agent tasks run repeatedly: investigation, review, drafting | Sonnet 5.5 |
| Complex work requiring careful judgment, long-horizon agentic coding and knowledge work | Opus 5.5 |
| The hardest problems, where you need the most intelligence | Opus 5.5 |

Sonnet fits best when the task has a clear spec and a way to check the result. If you are tempted to run Sonnet at `xhigh` or `max` by default, consider Opus 5.5 instead. For multi-agent Opus-plans / Sonnet-executes patterns, see the `select-claude-models` skill.

## Behavior differences that drive prompting

1. **Effort is recalibrated.** A level does not produce the same amount of thinking as on Sonnet 5. Re-run an effort sweep. Asking the model in the system prompt to think less does not reliably reduce thinking; lower the effort level instead.
2. **Initiative depends on effort.** At `low`/`medium`, the model may check in before coding work is finished. At higher effort or on open-ended requests, it may do more than asked. Steer with effort and explicit carry-through / stop-when-done instructions.
3. **Helpful extras when coding.** The model tends to add tests, docs, and small supporting files that fit repo conventions. Most teams welcome this; constrain it only when the user wants changes limited to what was explicitly requested.
4. **Thoroughness at `xhigh`/`max`.** Extra self-started review rounds and related fixes are common; reserve those levels for measured gains, or cap self-started review in the prompt.
5. **Verification at `low` effort.** The model generally checks its work, but at `low` it sometimes reports done without a real test/build. Add the real-check paragraph when you see that.
6. **Progress notes between tools.** Longer notes return as progress-update `thinking` blocks that are empty at the default display, so UIs that only render text can look silent.
7. **Search under-calling in chat/knowledge work.** Training knowledge can win over search for changeable facts unless anti-tool language is removed and current-check instructions are added.
8. **Injection resistance.** Genuine mid-turn user messages placed inside `tool_result` or as system text right after tool results can be misread as injections.

## Running without up-front thinking

Use `thinking: {"type": "between_tools"}` only when the integration previously ran with thinking off and tools are present:

- Accepted at `low`, `medium`, and `high` only. `xhigh`/`max` return 400.
- Takes no other field (`display`, `budget_tokens`, `block_binding` return 400).
- Effort cannot change mid-conversation with `between_tools`.
- Progress notes between tools still return as `thinking` blocks; pass them back unchanged.
- Without tools, the model answers without thinking first. For multi-step reasoning or JSON accuracy tasks, use adaptive thinking instead.
- Remove any "do not think" instructions; they increase internal XML tag leakage.

## Runtime settings versus prompt text

- Start at `high` on the Claude API unless the workload is agentic or latency-sensitive.
- Agentic coding / multistep tools: start `medium` for well-specified tasks; move to `high` for harder or longer ones.
- Chat / latency-sensitive: start `medium` or `low`.
- For agentic coding, set `max_tokens` to 128,000 and stream. Thinking counts toward `max_tokens` even when thinking content is omitted.
- Changing top-level effort between requests invalidates the prompt cache; use per-message effort (beta) with adaptive thinking to vary a turn without breaking the cache.
- Forced `tool_choice` of type `any` or `tool` returns 400 on Sonnet 5.5; use `auto` with `strict: true` tools and prompt the model when to call them.
- Keep model choice, effort, retries, refusal handling, advisor pairing, tool schemas, and fallback routing outside the prompt unless the user asks for a complete request configuration.
- Raw chain of thought is not returned. Asking the model to reproduce hidden reasoning can trigger a `reasoning_extraction` refusal.

## Migration pattern from Sonnet 5

1. Change the model ID to `claude-sonnet-5-5`.
2. Replace `thinking: {"type": "disabled"}` with `between_tools` if you need thinking off up front.
3. Replace forced `tool_choice` with `auto` + strict tools + prompt guidance.
4. Keep conversations append-only; pass thinking blocks back unchanged. Sonnet 5.5 can read Sonnet 5 thinking blocks; other models cannot read Sonnet 5.5's.
5. Move computer use to `computer_toolset_20260801` where required by the platform.
6. Check advisor pairing: Sonnet 5.5 executor accepts Opus 5.5, Opus 5, and Sonnet 5.5 itself; rejects Opus 4.8, Opus 4.7, and Sonnet 5 as advisors. Advice from accepted advisors returns encrypted.
7. Read content blocks by type; do not assume `content[0]` is text.
8. Remove Sonnet 5 workarounds, re-run an effort sweep, then add at most one targeted instruction per observed behavior.

## Evaluation checklist

Verify output-contract adherence, evidence quality, tool behavior, completion accuracy, progress visibility, latency, token use, and cost. Change one prompt group or runtime setting at a time so regressions remain attributable.
