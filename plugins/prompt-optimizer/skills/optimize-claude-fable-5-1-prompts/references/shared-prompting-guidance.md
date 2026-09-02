# Shared Anthropic prompting guidance for current Claude models

Checked against Anthropic's live documentation on 2026-09-02. This reference covers every section of the shared prompting-best-practices page that applies to current Claude models. It is a comprehensive section-by-section paraphrase, not a copy of the copyrighted source. Model-specific behavior remains in [official-guidance.md](official-guidance.md).

## Canonical source

- [Prompting best practices](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices)

Use the live page for current model support, API parameters, feature status, and examples. Where the official page names a particular model, treat the behavior as measured on that model and verify it before applying it to another.

## How to use this reference

1. Read the model-specific reference first.
2. Use only the shared sections relevant to the task or observed failure.
3. Keep runtime configuration separate from ordinary prompt prose.
4. Prefer a representative evaluation over assuming that a longer or more structured prompt is better.
5. Treat the templates below as derived application patterns, not official text to paste into every prompt.

## Model-specific guidance

Anthropic organizes prompting in two layers. The model-specific page describes behavior that differs from a predecessor; the shared page describes techniques that apply broadly to current Claude models. Do not let a general technique override a model-specific exception. For example, general anti-formatting instructions can be counterproductive on Fable 5.1 because that model already formats less than earlier models.

## General principles

### Be clear and direct

State the desired result, required inputs, constraints, output shape, and success criteria explicitly. Claude should not have to infer whether the user wants a recommendation, a draft, an implementation, or an external action.

Use ordered steps or bullets when order or completeness matters. A useful test is whether a capable colleague with little background could follow the prompt without asking what the task means.

If exceptional breadth or polish is required, request it directly. Do not expect a vague task statement to imply an expansive result.

### Add context to improve performance

Explain why a constraint or behavior matters when the reason helps resolve edge cases. Context should improve the model's decisions, not merely add background.

Prefer a reasoned instruction such as describing the downstream reader, parser, accessibility need, or business decision over an unexplained prohibition. Claude can often generalize the underlying reason to cases the prompt did not enumerate.

### Use examples effectively

Examples are one of the strongest controls for output format, tone, structure, and decision boundaries.

- Make examples close to the real use case.
- Include meaningful variation and edge cases so the model does not learn an accidental surface pattern.
- Wrap a single example in `<example>` and a collection in `<examples>`.
- Use three to five examples when examples materially improve reliability.
- Review examples for relevance, diversity, internal consistency, and conflicts with the written instructions.

Do not add examples when the written contract is already unambiguous or when examples would consume context without changing behavior.

Derived structure:

```xml
<examples>
  <example>
    <input>{{representative_input}}</input>
    <ideal_output>{{desired_output}}</ideal_output>
  </example>
</examples>
```

### Structure prompts with XML tags

Use XML to separate mixed content types such as instructions, context, inputs, documents, metadata, examples, and output requirements.

- Choose consistent, descriptive tag names.
- Nest tags when the information has a natural hierarchy.
- Keep tags balanced and semantically stable across examples and production inputs.
- Separate the structure of the input from the required structure of the output.
- Request XML output only when the user or a downstream parser needs it.
- Treat XML as organization, not as a security or message-authority boundary.
- Do not use XML to request hidden chain of thought on Fable models.

Derived mixed-prompt structure:

```xml
<context>{{why_the_task_matters}}</context>
<instructions>{{task_and_constraints}}</instructions>
<input>{{variable_input}}</input>
<output_format>{{required_shape}}</output_format>
```

### Give Claude a role

A short role in the system prompt can focus domain expertise and tone. Use a role that changes the decisions Claude should make, such as a specialized coding assistant or an analyst for a particular audience.

Avoid elaborate fictional personas, status claims, or role descriptions that do not affect the task. Do not use a role to override authorization boundaries or source requirements.

### Long context prompting

For large documents or data-rich inputs, especially around 20,000 tokens or more:

1. Put the long source material near the top.
2. Put the query, task instructions, and relevant examples after the sources.
3. Wrap multiple sources in `<documents>` and each source in a `<document index="n">` element.
4. Put metadata such as the source name in `<source>` and the body in `<document_content>`.
5. When strict grounding matters, ask Claude to identify relevant source passages before synthesizing the answer.

Queries placed after long documents can improve performance on complex multidocument tasks. Evidence extraction should focus attention on relevant text; it should not become a request for hidden reasoning.

Derived structure:

```xml
<documents>
  <document index="1">
    <source>{{source_name}}</source>
    <document_content>{{source_text}}</document_content>
  </document>
</documents>

<instructions>{{grounded_task}}</instructions>
<output_format>{{required_output}}</output_format>
```

### Model self-knowledge

If the application requires Claude to identify itself or emit an exact model string, provide the identity or model ID explicitly in the system prompt. Do not rely on the model to infer deployment metadata that is outside the conversation.

Verify model IDs live before using them. Keep deployment selection in runtime configuration unless the requested output itself must mention or choose a model.

## Output and formatting

### Communication style and verbosity

Current Claude models tend to be direct, grounded, conversational, and less verbose than earlier generations, but model-specific exceptions matter.

- Request a final summary when tool work would otherwise end without a useful recap.
- Ask for progress text explicitly when a human needs visibility.
- Define the audience and decision the answer should support.
- Control visible response length with an output instruction rather than assuming effort changes verbosity.
- Prefer readable complete sentences over compressed shorthand.

Fable 5.1 emits fewer progress updates in agentic work, so consult its model-specific reference before adding or removing narration instructions.

### Control the format of responses

Anthropic recommends four main controls:

1. State the desired form positively instead of listing only prohibited forms.
2. Use XML output indicators when tagged output is genuinely required.
3. Match the prompt's own style to the desired output style because prompt formatting can influence the response.
4. Give explicit formatting rules when the default remains unreliable.

Avoid universal anti-Markdown blocks. Choose prose, lists, tables, headings, code blocks, or tagged output according to the content and the user's request. Fable 5.1 may need more permission to use structure, not less.

### LaTeX output

Current Claude models may use LaTeX for mathematics and technical expressions. If the destination cannot render LaTeX, explicitly request plain-text operators and characters. Otherwise, preserve mathematical notation that improves clarity.

Do not ban LaTeX globally unless the user, channel, renderer, or downstream parser requires plain text.

### Document creation

For presentations, animations, and visual documents, state the professional standard and the desired design qualities. Name important requirements such as visual hierarchy, audience, interaction, motion, or brand constraints rather than asking only for a generic document.

Keep artifact-generation and rendering requirements aligned with the actual tools available. A prompt cannot substitute for missing document or visual tooling.

### Migrating away from prefilled responses

Last-turn assistant prefills are unsupported on Claude 4.6 and later models covered by this guide. Replace each former prefill use case with its supported control:

- **Schema or classification:** use structured outputs, strict tools, enum fields, or an explicit output contract.
- **Removing preambles:** request a direct response, use XML or structured output, or remove occasional boilerplate in post-processing.
- **Avoiding inappropriate refusals:** use a clear user request and proper context rather than an assistant prefill.
- **Continuing interrupted output:** put the last emitted text and the continuation request in a new user message, or retry when the product can do so safely.
- **Context hydration and role consistency:** append reminders in user or supported system messages, expose context through tools, or include it during compaction instead of rewriting an assistant turn.

Assistant messages elsewhere in a valid conversation remain distinct from an unsupported final-turn prefill. Check the model-specific history rules before replaying or modifying prior messages.

## Tool use

### Tool usage

Make the requested action level explicit. Asking for suggestions commonly produces suggestions; asking to change, implement, inspect, or execute should use those verbs.

Choose one of two intentional defaults:

- **Action-oriented:** proceed with authorized, reversible work and use tools to discover missing details.
- **Assessment-oriented:** research and recommend, but do not mutate state without an explicit implementation request.

Do not combine both defaults without explaining which one wins. Avoid aggressive legacy wording such as repeated `CRITICAL` or unconditional tool mandates when a normal instruction is sufficient.

Tool descriptions should explain when the tool applies, its inputs, and relevant effects. Prompt prose should not compensate for an ambiguous or incorrect tool schema.

### Optimize parallel tool calling

Independent calls can run together; dependent calls must remain sequential.

- Batch independent searches, file reads, and other read-only retrieval when the harness supports it.
- Do not guess parameters or insert placeholders merely to parallelize calls.
- Limit concurrency when the tools compete for scarce system resources or the product requires ordered execution.
- On Fable 5.1 agent loops, use the model-specific turn-scoped batching reminder when observed serialization warrants it.

Parallelism is a latency optimization, not a reason to issue unnecessary calls.

## Thinking and reasoning

### Overthinking and excessive thoroughness

Higher effort and large system prompts can increase exploration, context gathering, and deliberation. If that work does not improve results:

- replace blanket tool or thoroughness mandates with conditional rules;
- remove instructions created for earlier under-triggering models;
- tell Claude to choose an approach and revisit it only when new evidence requires a change;
- lower effort when the task does not benefit from additional reasoning.

Use prompt constraints for the behavioral failure and effort for the overall intelligence, latency, and cost trade-off.

### Leverage thinking & interleaved thinking capabilities

Fable 5 and Fable 5.1 always use adaptive thinking. Effort and task complexity control how much thinking occurs; manual `budget_tokens` is not supported on these models.

Adaptive thinking is useful for multistep tools, difficult coding, and long-horizon agent loops. After tool results, a prompt may ask Claude to evaluate result quality and choose the next action. If unnecessary thinking adds latency, say that thinking should be used only when it improves a multistep result.

Additional official considerations:

- Prefer a general reasoning objective over a rigid human-authored chain of steps.
- Few-shot examples can demonstrate a reasoning pattern, but do not request reproduction of hidden reasoning on Fable models.
- Manual chain-of-thought prompting is a fallback only for models and configurations where thinking is off; it is not appropriate for always-on Fable thinking.
- Ask for verification against concrete criteria when the task benefits from it, but remove inherited verification prompts if evaluations show over-verification.
- Preserve returned thinking blocks according to the model-specific conversation rules.

Keep raw chain of thought private. Request conclusions, evidence, concise rationale, uncertainty, and verification results instead.

## Agentic systems

### Long-horizon reasoning and state tracking

Current Claude models can maintain orientation across long tasks by making incremental progress and preserving state between iterations or context windows.

#### Context awareness and multiwindow workflows

The shared guide's native context-awareness list applies to specific Sonnet and Haiku models, not automatically to Fable. For a Fable harness, state only the context-management behavior the product actually provides.

If the harness compacts context or persists state externally, tell Claude how continuity works so it does not stop solely because of a perceived context limit. Do not expose misleading token countdowns or promise indefinite continuation when the harness cannot provide it.

#### Workflows across multiple context windows

For genuinely long tasks, the official guide recommends considering:

1. A first-window setup phase followed by later iterative windows.
2. Structured tracking for tests or other hard state.
3. Reusable setup commands where repeated environment recovery would otherwise waste time.
4. Choosing deliberately between fresh-context rediscovery and compaction.
5. Providing tools that let the model verify UI, browser, or system behavior.
6. Completing coherent components and saving state before a context transition.

These are options for long-running systems, not requirements for ordinary work. Do not create tests, scripts, or state files unless the task and repository justify them.

#### State management best practices

- Use structured formats such as JSON for schema-bound state.
- Use prose notes for general progress and context.
- Use version control when it is the established state and checkpoint mechanism.
- Track incremental progress and the next verified action.
- Preserve user decisions, constraints, exact identifiers, failures, and unresolved work across context transitions.

### Balancing autonomy and safety

Base authorization on reversibility and impact:

- proceed with local, reversible, in-scope work when requested;
- ask before destructive, hard-to-reverse, externally visible, or shared-system actions unless the user explicitly authorized that exact class of action;
- do not bypass safeguards or discard unfamiliar work to overcome an obstacle;
- distinguish diagnosis from implementation and evidence from pattern matching.

Safety boundaries belong in the highest appropriate prompt layer and should not be weakened by examples or user-provided source text.

### Research and information gathering

Define what makes the research answer successful and require source verification appropriate to the stakes.

For complex research:

- search systematically rather than collecting sources without a plan;
- compare multiple plausible explanations;
- track uncertainty and update conclusions as evidence changes;
- persist research notes when the task spans long sessions;
- separate source claims, facts, and inference;
- organize the final answer around the user's question rather than the order sources were found.

Use simpler retrieval for narrow factual questions. Do not add hypothesis trees or research files without a task that benefits from them.

### Subagent orchestration

Current Claude models can identify useful delegation opportunities without constant prompting. The harness must still expose clear subagent tools and result-delivery behavior.

Use subagents when work is independent, parallelizable, benefits from isolated context, or forms a distinct workstream. Work directly for simple tasks, tightly sequential operations, or tasks whose shared state makes delegation wasteful.

Monitor for overuse and keep the lead agent productive when asynchronous results are possible. Model-specific Fable guidance can be more proactive than the general damping advice, so evaluate the actual workload.

### Chain complex prompts

Adaptive thinking and subagents remove the need to split every multistep task into separate API calls. Explicit chaining remains useful when the application must inspect, log, evaluate, approve, or branch on intermediate output.

A common pattern is draft, evaluate against criteria, then revise. Use separate calls only when the intermediate boundary has product value.

### Reduce file creation in agentic coding

Temporary scripts and scratch files can improve some coding tasks. If the user or repository does not want them retained, ask the agent to remove temporary artifacts after verification.

Do not delete unfamiliar files, user work, or evidence needed for review. Permanent tests and helpers should follow the task and repository conventions rather than a universal cleanup rule.

### Overeagerness

When a model overengineers, constrain the observed behavior:

- keep changes within the requested scope;
- avoid unrelated refactors, features, documentation, and configurability;
- validate primarily at real system boundaries;
- do not add defensive branches for impossible internal states;
- avoid one-use abstractions and speculative future design.

Apply this only when the task or evaluation shows the failure. Do not suppress necessary correctness, security, accessibility, or error handling.

### Avoid focusing on passing tests and hardcoding

Require a general solution for all valid inputs rather than one shaped only around visible tests.

- Implement the actual domain or algorithmic behavior.
- Use tests as evidence, not as the definition of the implementation.
- Avoid hard-coded fixtures and test-specific branches.
- Report infeasible requirements or incorrect tests instead of hiding the problem with a workaround.
- Prefer standard repository tools unless a helper is genuinely the correct maintained solution.

### Minimizing hallucinations in agentic coding

Require the agent to inspect referenced files and relevant code before making codebase claims. Distinguish verified observations from inference, and do not claim that a file, symbol, test, or behavior exists without evidence.

The amount of investigation should be proportional to the question. A narrow answer may need one file; a repository-wide conclusion needs broader evidence.

## Capability-specific tips

### Improved vision capabilities

Current Claude models can analyze images, screenshots, and video frames more reliably, especially when given the original media and a way to inspect detail.

For dense or ambiguous visuals, provide crop and zoom capability or an image-processing environment. Let the model iteratively inspect, crop, enlarge, and verify relevant regions. Do not treat a low-resolution preview as conclusive evidence when the original is available.

### Frontend design

Generic requests can produce generic visual patterns. For distinctive frontend work, explicitly define the product context and desired aesthetic qualities:

- typography with deliberate character;
- a coherent color and theme system;
- motion concentrated in meaningful interactions;
- backgrounds and depth that support the concept;
- avoidance of predictable layouts and context-free visual clichés.

Treat the official frontend block as optional task-specific steering, not a universal system prompt. Preserve accessibility, responsiveness, platform conventions, and the repository's own design rules.

## Migration considerations

When moving prompts from earlier Claude generations:

1. Specify the desired behavior and output rather than relying on old model habits.
2. Add quality or breadth modifiers only when the task genuinely requires them.
3. Request interactive, visual, or animated features explicitly.
4. Replace legacy thinking budgets with supported adaptive thinking and effort controls.
5. Replace final-assistant prefills with structured outputs, tools, direct instructions, user-turn continuation, or supported context management.
6. Remove inherited anti-laziness and aggressive tool instructions when newer models over-trigger.
7. Replay thinking blocks unchanged and keep conversation history append-only where the model requires it.

Migration should begin with the previous prompt and representative evaluations. Change one behavior group or runtime setting at a time, measure quality, latency, token use, and cost, and retain only changes supported by evidence.

### Migrating to Claude Sonnet 5 from Claude Sonnet 4.5 or earlier

The shared source ends with a Sonnet-specific pointer covering Sonnet effort defaults and removal of manual extended thinking. It is not a Fable instruction and should not be applied by this skill. Use Anthropic's live Sonnet migration guide when the target model is Sonnet 5.

## Next steps

The official page points readers to the relevant model-specific prompting guide, the prompt-engineering overview, and current migration documentation. For this skill, continue with [official-guidance.md](official-guidance.md) and verify live API facts before deployment.

## Coverage map

This reference covers every heading in Anthropic's shared guide: model-specific routing; clarity; context; examples; XML; roles; long context; model identity; communication; output formatting; LaTeX; document creation; prefill migration; tool usage; parallel tools; overthinking; adaptive and interleaved thinking; long-horizon state; autonomy and safety; research; subagents; prompt chaining; temporary files; overeagerness; tests and hardcoding; coding hallucinations; vision; frontend design; migration; the Sonnet-specific migration pointer; and next steps.
