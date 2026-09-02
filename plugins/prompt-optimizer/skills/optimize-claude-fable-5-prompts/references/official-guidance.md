# Model-specific official guidance for Claude Fable 5

Checked against the live Anthropic documentation on 2026-09-02. This file covers every section of the model-specific Fable 5 prompting page and its linked runtime and migration requirements. Read [shared-prompting-guidance.md](shared-prompting-guidance.md) for the complete shared prompting methodology. This file is a comprehensive paraphrase, not a copy of the copyrighted source. Short excerpts are marked and attributed.

## Canonical sources

- [Prompting Claude Fable 5](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-fable-5)
- [Introducing Claude Fable 5 and Claude Mythos 5](https://platform.claude.com/docs/en/models/fable-5/introducing-claude-fable-5-and-claude-mythos-5)

Use the live pages for current model IDs, parameters, availability, limits, pricing, safety behavior, and fallback support.

## Source scope

The model-specific page covers behavior and scaffolding changes for Fable 5 and Mythos 5. It sends general prompt-construction techniques to the shared best-practices page and API behavior to the model introduction. Fable 5 is positioned for difficult, ambiguous, long-running work, but also handles ordinary tasks. Anthropic recommends evaluating whether older instructions, tools, and guardrails are still necessary instead of carrying them forward automatically.

Unless a paragraph is labeled `Local application note` or `Derived`, it paraphrases the official sources above. Local notes adapt the source to this optimizer and should not be presented as Anthropic quotations.

Short excerpt from the Fable 5 page:

> “When you have enough information to act, act.”

## Capability improvements

Anthropic identifies these improvements over Claude Opus 4.8:

- sustained long-horizon autonomy with instruction retention over complex runs;
- stronger first-pass correctness on difficult, well-specified problems;
- better interpretation of dense technical images, applications, and screenshots, including use of shell and crop tools for difficult images;
- stronger financial, spreadsheet, slide, document, and other enterprise workflows;
- higher bug-finding recall across codebases and repository history outside restricted cybersecurity domains;
- better handling of complex, multithreaded ambiguity;
- more dependable delegation, parallel subagents, and ongoing agent-to-agent communication.

These are capability descriptions, not instructions to add every associated tool or workflow to a prompt.

## Longer turns by default

Hard requests at higher effort can take many minutes, and autonomous runs can continue for hours. Before migration, applications should review timeouts, streaming, progress indicators, and asynchronous check-in mechanisms. If the model overplans, tell it to act once it has enough information, preserve established facts and decisions, avoid narrating unused options, and recommend rather than exhaustively survey.

## Consider all effort levels

Effort is the primary intelligence, latency, and cost control:

- start with `high` for most demanding work;
- consider `xhigh` for capability-sensitive tasks;
- test `medium` or `low` for routine or interactive work;
- lower effort when a task succeeds but deliberation or context gathering exceeds its value.

Higher effort can improve rigor and verification but can also cause unnecessary context collection, refactoring, abstraction, validation, fallback logic, compatibility work, or hypothetical design. Counter only the failure modes relevant to the task. Keep implementation proportional, validate at real boundaries, and avoid speculative infrastructure.

## Strong instruction following

Fable 5 generally needs fewer enumerated prohibitions than older models. Prefer one clear instruction that names the desired behavior. For concise final responses, require the outcome first, then only details that affect the reader's decision. Do not achieve brevity through fragments, unexplained jargon, abbreviation chains, or compressed notation.

For checkpoints, define the true reasons to stop: destructive or irreversible action, genuine scope change, or information only the user can provide. Otherwise continue through safe, reversible, authorized work.

## Ground progress claims during long runs

Require every progress or completion claim to be supported by a tool result from the current session or supplied evidence. Failed tests, skipped steps, and unverified work must be reported plainly. Verified completion should also be stated plainly rather than hedged.

## State the boundaries

Fable 5 can occasionally take unrequested actions. Make the requested action level explicit:

- problem description, question, or exploratory discussion means assess and report;
- implementation requires an implementation request or equivalent authorization;
- before state-changing commands, ensure the evidence supports that particular action;
- do not infer a fix solely because a symptom resembles a familiar failure.

## Parallel subagents

Fable 5 delegates more readily and can sustain parallel agents. When delegation is genuinely useful:

- split independent work;
- let the lead continue useful local work rather than block immediately;
- keep long-lived agents for related follow-ups when their retained context saves time;
- intervene when an agent lacks context or drifts.

Local application note: Do not add subagents to simple work merely because the model supports them.

## Construct a memory system

For workflows that benefit from durable learning, provide a writable note location. Store one lesson per entry with a brief summary, the confirmed approach or correction, and why it mattered. Avoid duplicating repository or conversation facts, update an existing lesson instead of creating a duplicate, and delete lessons proven wrong. Existing sessions can be reviewed to bootstrap themes and lessons.

Local application note: Memory is optional and should not be added without a real recurring need.

## Rare cases of early stopping

Deep into long sessions, the model can end with an intention instead of issuing the tool call or ask for permission already granted. For autonomous pipelines, state that reversible work within the request should proceed, that promised next actions should be executed before ending, and that the turn should end only on completion or input that only the user can provide. Pair this with the explicit checkpoint boundary so autonomy does not override destructive-action safeguards.

## Rare cases of context-budget concern

If the harness exposes remaining-token counts, Fable 5 may prematurely suggest a handoff, summary, or new session. Prefer not to expose countdowns. If the harness must expose them, reassure the model that context is sufficient and tell it to continue rather than stop solely because of the displayed budget.

## Give the reason, not only the request

State the larger task, intended audience, and what the result enables when that context helps the model resolve edge cases. The reason should improve decisions, not become decorative background.

## Readability when communicating with the user

Long agentic sessions can produce final messages that inherit private shorthand, dense implementation detail, or references to unseen intermediate work. Final summaries should re-ground a reader who saw none of the process:

1. lead with the outcome;
2. explain any required user decision as new information;
3. use complete sentences and reintroduce necessary terms;
4. avoid arrow chains, stacked abbreviations, and private labels;
5. prefer clarity over extreme compression.

## Create a send-to-user tool

Long asynchronous products can expose a client-side tool whose input is displayed to the user verbatim without ending the agent turn. Use it for partial deliverables, direct answers, or content the user must see exactly. Tool inputs should contain user-facing content, not internal reasoning or routine narration. The tool needs both a schema and a system instruction describing when to call it. Ordinary agents that only need routine progress may not need this tool.

Derived minimal schema:

```json
{
  "name": "send_to_user",
  "description": "Display user-facing content without ending the run.",
  "input_schema": {
    "type": "object",
    "properties": {
      "message": { "type": "string" }
    },
    "required": ["message"]
  }
}
```

## Recommended scaffolding changes

Anthropic's closing recommendations are:

- evaluate Fable 5 on genuinely difficult work rather than only easy benchmarks;
- make self-verification explicit for long runs and consider independent fresh-context verification where it improves reliability;
- simplify older prompts and skills that may now over-prescribe behavior;
- never ask the model to reproduce hidden reasoning, because reasoning-extraction requests can trigger refusal;
- use supported thinking output and a user-message mechanism when visible progress is required;
- add a send-to-user tool only for products that need verbatim delivery during a continuing run.

## Runtime and integration boundaries

- The API model ID was `claude-fable-5` when checked; verify live before use.
- The linked model page listed a 1M-token context window and up to 128k output tokens; verify live limits before deployment.
- Adaptive thinking is always enabled; explicit thinking-token budgets are unsupported.
- Raw chain of thought is not returned. Request visible conclusions, rationale, evidence, checks, and uncertainty.
- Assistant-response prefills are unsupported on current Claude 4.6-and-later models, including Fable 5.
- Refusals can arrive as successful HTTP responses with a refusal stop reason; handle detection and fallback in application code.
- Keep model choice, effort, retries, fallback, tool schemas, and refusal handling outside ordinary prompt prose.
- Safety classifiers can affect offensive cybersecurity, biology and life-science content, and reasoning-extraction requests; check current policy and fallback documentation rather than encoding guesses in the prompt.

## Linked model and API reference

The model introduction linked by the prompting page adds these integration requirements:

- Fable 5 and limited-release Mythos 5 shared capabilities, context, output limits, and pricing, while only Fable 5 used the described safety classifiers.
- A refusal returned HTTP 200 with `stop_reason: "refusal"` and classifier details rather than an API error.
- Retry options included server-side beta fallback, SDK middleware, or manual fallback; eligible fallback credit avoided paying the prompt-cache switch cost twice.
- The checked availability list covered the Claude API, Amazon Bedrock, Claude Platform on AWS, Google Cloud, and Microsoft Foundry.
- The checked data policy described 30-day retention and no zero-data-retention availability unless expressly authorized.
- Adaptive thinking could not be disabled. Effort controlled thinking depth.
- Thinking display supported summarized or omitted blocks, and multi-turn conversations had to replay thinking blocks unchanged on the same model.
- Supported features listed effort, beta task budgets, memory, code execution, programmatic tool calling, beta context editing for tool-result clearing, compaction, and vision.
- Migration guidance linked separate paths from Mythos Preview and Claude Opus 4.8.

Treat these as live integration facts, not durable prose to insert into an optimized prompt.

## Evaluation and migration

1. Establish a representative baseline with the previous model, prompt, and runtime settings.
2. Run Fable 5 with the same prompt and an evaluated effort level.
3. Inspect observed failures before editing the prompt.
4. Remove obsolete scaffolding and change only the instruction tied to the failure.
5. Test high-effort work for excess exploration, features, refactors, abstractions, validation, and repeated analysis.
6. Measure output-contract adherence, evidence, tool behavior, completion accuracy, latency, token use, and cost.
7. Change one prompt group or runtime setting at a time.

Do not declare a prompt better merely because it is shorter, more structured, or more detailed.

## Coverage map

This reference covers every heading on the live Fable 5 prompting page: capability improvements; longer turns; effort; strong instruction following; grounded progress; boundaries; parallel subagents; memory; early stopping; context-budget concern; purpose and context; readability; send-to-user tooling; and recommended scaffolding changes. The separate shared reference covers the complete cross-model prompting guide.
