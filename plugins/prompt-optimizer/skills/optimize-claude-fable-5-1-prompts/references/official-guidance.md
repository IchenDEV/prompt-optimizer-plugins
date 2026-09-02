# Model-specific official guidance for Claude Fable 5.1

Checked against the live Anthropic documentation on 2026-09-02. This file covers every section of the model-specific Fable 5.1 prompting page and its linked runtime, history, and migration requirements. Read [shared-prompting-guidance.md](shared-prompting-guidance.md) for the complete shared prompting methodology. This file is a comprehensive paraphrase, not a copy of the copyrighted source. Short excerpts are marked and attributed.

## Canonical sources

- [Prompting Claude Fable 5.1](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-fable-5-1)
- [What's new in Claude Fable 5.1](https://platform.claude.com/docs/en/models/fable-5-1/whats-new-fable-5-1)

Use the live pages for current model IDs, parameters, availability, limits, pricing, beta headers, safety behavior, retention, and fallback support.

Unless a paragraph is labeled `Local application note` or `Derived`, it paraphrases the official sources above. Local notes adapt the source to this optimizer and should not be presented as Anthropic quotations.

## Migration principle and symptom index

Anthropic says existing Fable 5 prompts should generally work on Fable 5.1 unchanged. Start from the existing prompt, re-run representative evaluations, and change only what an observed behavior or clarified contract requires.

The model-specific page routes by symptom:

- effort, latency, or cost mismatch;
- too few user-facing updates;
- one independent tool call per turn;
- conversation-binding failures caused by edited history;
- dense prose;
- insufficient chat structure;
- unmarked source wording;
- early stopping or repeated permission requests;
- incomplete compaction summaries;
- unrequested changes or excessive committed tests;
- memory-based answers at low effort;
- benign coding refusals;
- whole-file rewrites for small edits;
- long outputs exhausting the token limit at `xhigh` or `max`;
- lead-agent idling while subagents run;
- insufficient detail on dense visual inputs.

Apply only the section that matches the observed problem.

## Consider all effort levels

- Start evaluation at the default `high` effort.
- Re-test `low`, `medium`, `xhigh`, and `max` even if the same names were evaluated on Fable 5; the levels do not represent identical thinking amounts across models.
- Capability gains are largest at higher effort, but `medium` can approximately match Fable 5 at lower cost on suitable tasks.
- Include `low` in comparisons where a smaller model at higher effort would otherwise be considered.
- At `low`, watch for reduced search and retrieval.
- At `xhigh` and `max`, watch for long pre-answer deliberation and token pressure on large deliverables.

## Ask for user-facing progress updates

Fable 5.1 writes fewer visible updates during long tool runs than Fable 5, especially at higher effort.

1. First verify the client receives progress-update thinking blocks.
2. The default thinking display omits their text; expose updates or summarized thinking through the supported runtime setting and required beta header when appropriate.
3. Remove inherited prompt instructions that suppress narration before adding a new prompt rule.
4. If the product needs visible collaboration, ask for one opening line, useful updates while working, and a self-contained closing recap.
5. If tool output is hidden or collapsed, tell the model what the user can actually see so necessary content is repeated in the response.
6. Prefer a turn-scoped system message for temporary visibility reminders rather than mutating earlier history.

## Batch independent tool calls in agent loops

Fable 5.1 usually batches requests when multiple items are explicitly named, but coding and computer-use loops may serialize independent work that is only implied.

- Tell the model to identify the next needed items privately and request every independent item together.
- Keep calls sequential when an input depends on a prior result.
- Re-send the nudge after each tool-result turn when the loop needs it.
- Prefer a turn-scoped system message with `clear_at: "next_user_message"`; without that beta feature, place the nudge after tool results in the same user message.
- Append each fresh reminder rather than deleting or rewriting earlier copies.
- The harness example on the official page preserves assistant turns exactly, sends tool results as new user turns, and appends a fresh scoped batching reminder.

## Keep conversation history append-only

Append every assistant turn exactly as returned, including thinking blocks. Do not edit the system prompt, tool list, or earlier messages after a Fable 5.1 thinking block.

- New-account enforcement can reject a mismatched replay as bound to a different conversation.
- The beta binding control can drop invalid blocks instead, with the transformation reported to the application.
- Injecting and later removing reminders, editing old messages, changing earlier tools, or serving different file bytes through a stable URL can invalidate later blocks.
- Use mid-conversation system messages and tool changes for new instructions or tools.
- Use turn-scoped system messages for one-turn reminders.
- Prefer server-side compaction or context editing for trimming.
- Client-side compaction should replace the old history with one summary and the new user turn, without replaying old thinking blocks.
- To audit a harness, enable drop behavior temporarily and inspect input transformations, or compare consecutive requests byte-for-byte through the shared prefix.

## Writing density

Fable 5.1 generally improves prose quality but can produce longer sentences and fewer paragraph breaks than Fable 5. If that failure appears, ask for direct literal prose, remove performative metaphor, and use paragraphing that reduces reader effort. A short instruction can be enough; do not add a long style manifesto without evidence that it helps.

## Formatting in chat

Fable 5.1 is less likely than earlier models to use headings, lists, bold, and quotation marks. Remove blanket anti-formatting rules inherited from models that overformatted. Ask for lists or structure when the content is multifaceted, honor explicit minimal-format requests, and keep conversational or emotional exchanges in plain prose when appropriate.

## Quoting retrieved sources

Fable 5.1 can reproduce source wording without marking it as quotation. If this occurs:

1. add one complete correct example to the system prompt;
2. include the request, tool-use representation, response, and a rationale explaining the correct quotation/paraphrase boundary;
3. organize the answer around the requested comparison rather than walking through sources mechanically;
4. mark direct wording as quotation and otherwise use faithful indirect speech;
5. replace the example's placeholder search tool with the application's actual tool name.

The official example uses this tag structure:

```xml
<example>
  <user>{{retrieval_and_comparison_request}}</user>
  <response>{{tool_calls_and_correctly_marked_comparison}}</response>
  <rationale>{{why_the_quotation_boundary_is_correct}}</rationale>
</example>
```

The rationale teaches the output boundary; it is not a request for hidden chain of thought.

## Finish the whole task

For complex autonomous workloads, Fable 5.1 may state a next step without doing it or ask for permission already granted.

- Tell autonomous agents that reversible actions implied by the original request should proceed without another permission round.
- Preserve explicit confirmation requirements for destructive actions, irreversible actions, and genuine scope changes.
- Distinguish assessment from implementation: a question or problem description does not authorize a fix.
- Before ending, inspect whether the final paragraph promises unfinished work; if so, perform that work first.
- Retry recoverable failures and gather discoverable information instead of stopping solely because the session is long.
- Verify evidence before state-changing commands.
- Treat the user's request or approved plan as the deliverable; do not quietly narrow, widen, or replace it.
- Make routine judgment calls, complete every independent part, and report any blocked remainder precisely.
- Keep unrelated cleanup or documentation as a follow-up suggestion rather than an unrequested change.

Anthropic notes that the strongest autonomy wording can reduce clarification, so evaluate that trade-off on ambiguous tasks.

## Preserve the right information in compaction summaries

Server-side compaction already preserves continuity. For client-side compaction, require a bounded summary that retains:

1. problems encountered and their resolutions;
2. approaches considered, attempted, or rejected and why;
3. requests, decisions, agreements, preferences, constraints, and boundaries;
4. the exact current state of completed and settled work;
5. open issues, promises, and expected next actions;
6. hard-to-reconstruct names, numbers, dates, exact wording, links, and references.

Preserve user-established content more closely than the assistant's intermediate reasoning.

Short excerpt from the Fable 5.1 page:

> “Summarize the transcript inside `<summary></summary>` tags.”

Derived structure:

```xml
<summary>
{{problems_and_resolutions}}
{{approaches_and_reasons}}
{{decisions_preferences_constraints_and_boundaries}}
{{current_state}}
{{open_items_and_next_actions}}
{{hard_to_reconstruct_details}}
</summary>
```

## Keep changes and tests to the requested scope

- Do not fix, optimize, or extend unrelated behavior unless the requested behavior cannot work otherwise.
- When wording is ambiguous, implement the reading most directly supported by the request and surrounding code, then state the assumption.
- Do not implement all plausible readings.
- Scratch verification does not need to become permanent repository content.
- Commit tests when requested or when the repository normally keeps focused tests for that kind of behavior.
- Size tests like neighboring tests, roughly one focused test per stated behavior.
- This constraint applies to extras only; every requested behavior still needs complete implementation.

## Search triggering at low effort

At `low`, the model is more likely to answer from memory and less likely to search or retrieve.

- Raise effort for the affected turn when freshness matters.
- Alternatively, tell the model that partial familiarity with a fast-moving name is not enough, and require searching the name as written plus any useful reformulation.
- Apply this to unfamiliar or rapidly changing subjects, not to stable self-contained work.

## Reduce safeguard false positives

Fable 5.1 improved false-positive behavior, and defensive source-code vulnerability analysis is allowed, but refusals can still occur.

- Ask whether code contains bugs rather than phrasing the request only as a compile-success check.
- For lesser-known languages, supply or expose documentation explaining the language.
- Remove base64 data from tool output when possible because it can trigger classifiers.
- Detect `stop_reason: "refusal"` in application code and use supported fallback handling.

## Prefer targeted edits over whole-file rewrites

Fable 5.1 may rewrite an entire file for a small change. When observed, instruct it to minimize edit tokens and modify only the relevant region when doing so preserves the result. A whole-file rewrite remains appropriate when the file is short or most of it is changing.

## Leave room for long outputs at xhigh and max

At `xhigh` and especially `max`, the model can spend substantial tokens reasoning before emitting a long deliverable.

- Prefer `high` unless evaluations show a quality gain from higher effort.
- Set `max_tokens` for both reasoning and visible output, not only the expected deliverable length.
- For long documents, tables, datasets, or code files, tell the model to use reasoning for understanding, verification, structure, and difficult decisions rather than drafting the complete deliverable twice.
- Substitute the real token limit in any limit reminder.

## Let the lead agent continue while subagents run

When the harness supports delegation:

- make the spawn operation return immediately;
- deliver results to the lead in later user messages;
- provide a separate wait operation;
- allow the lead to continue independent work.

The model may still choose to wait. The benefit comes from the runs where useful lead work overlaps with subagent execution.

Local application note: Do not add subagents without a task that benefits from them.

## Give vision work crop-and-zoom tools

For dense charts, filings, tables, screenshots, images, or video:

- expose the original media in a container with basic image-processing libraries when practical;
- let the agent inspect, crop, enlarge, and visually verify iteratively;
- if a full container is excessive, a crop-and-enlarge tool captures most of the benefit.

## Runtime and API changes

Verify every value live before production use. When checked:

### Models and core specifications

- The Claude API model ID was `claude-fable-5-1`; Mythos 5.1 exposed the same capabilities only to approved Project Glasswing participants.
- The context window was 1M tokens and maximum output was 128k tokens.
- Adaptive thinking was always on and effort was the supported thinking-depth control.
- The tokenizer matched Fable 5 and produced roughly 30 percent more tokens for the same text than models older than Claude Opus 4.7.

### Breaking changes

- Forced tool choice using `any` or a named tool was unsupported and returned an invalid-request error.
- Automatic or disabled tool choice remained supported; strict tool schemas or structured outputs were the alternatives for schema-valid output.
- Earlier Claude models could not read Fable 5.1 thinking blocks, although Fable 5.1 could read earlier-model blocks.
- Editing the system prompt, tools, earlier messages, or media bytes before a thinking block could invalidate it. The append-only and binding-control rules above are the migration path.

### Additive features

- Per-message effort allowed effort changes without invalidating the prompt cache.
- Turn-scoped system messages supported temporary high-authority instructions that remain append-only but stop rendering after the next user message.
- Progress-update display exposed readable status text while keeping raw reasoning hidden.
- Generated text carried Anthropic's statistical watermark, while supported generated media retrieved through the Files API could carry C2PA credentials.
- Per-message effort, turn-scoped messages, progress display, and binding controls were beta features with specific headers.

### Behaviors unchanged from Fable 5

- Adaptive thinking remained mandatory; explicit legacy thinking budgets or disabled-thinking requests were invalid.
- Raw chain of thought remained unavailable, while supported thinking displays could provide omitted or summarized output.
- Interleaved thinking between tool calls remained automatic.
- Assistant prefills and non-default `temperature`, `top_p`, or `top_k` were unsupported.
- The minimum cacheable prompt length was 512 tokens.
- Mid-conversation system messages and tool changes remained supported.

### Capability improvements

The linked model page describes gains in long-running agentic coding, finished document and spreadsheet work, multistep research, dense-image understanding, full-window long-context reasoning, and browser or desktop computer use. Multilingual performance is described as comparable to Fable 5.

### Refusals, fallback, and billing

The checked page documented HTTP 200 refusal responses with stop details, supported fallback to Claude Opus 4.8 or Claude Opus 5, a beta default-fallback mode, and cache-cost credit for eligible model switches. Verify the current permitted targets and billing behavior before deployment.

### Pricing

The checked page priced Fable 5.1 like Fable 5 except for cheaper cache reads and listed separate batch-processing rates. Read the live pricing table rather than copying numeric prices into a durable prompt.

### Availability and retention

The checked page listed the Claude API, AWS, Google Cloud, and Microsoft Foundry. It described 30-day data retention and no zero-data-retention availability unless expressly authorized. Treat platform identifiers, retention terms, and partner availability as live operational facts rather than prompt instructions.

Keep model selection, effort, thinking display, tool choice, strict schemas, history binding, beta headers, retries, refusal handling, fallback, cache policy, retention, and pricing outside ordinary prompt text.

## Migration checklist from Fable 5

1. Change the model ID only after the integration review.
2. Remove forced `tool_choice` modes; use automatic choice plus prompt instructions, strict tools, or structured outputs.
3. Replay thinking blocks unchanged and keep history append-only;
4. audit per-turn reminders, system changes, tool changes, trimming, and client-side summaries;
5. choose a production binding-mismatch policy and monitor input transformations;
6. retune effort from `high`, including per-message effort where useful;
7. inspect agent loops for serialized independent tool calls;
8. rerun representative evaluations for behavior, latency, cost, refusals, and token use.

## Evaluation checklist

1. Run the existing Fable 5 prompt before editing it.
2. Sweep effort levels on representative work.
3. Measure quality, latency, token use, and cost.
4. Check progress visibility, tool batching, history replay, completion, scope, test volume, source quotation, writing density, edit granularity, and visual inspection.
5. Test high-effort long outputs against the real token limit.
6. Verify append-only history and thinking-block replay in the actual harness.
7. Change one prompt group or runtime setting at a time.
8. Recheck beta headers and current capabilities before production use.

## Coverage map

This reference covers every heading on the live Fable 5.1 prompting page: effort; progress updates; tool batching; append-only history; writing density; chat formatting; source quotation; full-task completion; compaction; change and test scope; low-effort search; safeguard false positives; targeted edits; long high-effort outputs; concurrent subagents; and crop-and-zoom vision. It also covers the migration and runtime changes linked from the page. The separate shared reference covers the complete cross-model prompting guide.
