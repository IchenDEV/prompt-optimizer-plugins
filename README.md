# GPT-5.6 Prompt Optimizer

A Codex plugin that turns rough ideas and existing prompts into lean, outcome-first prompts for GPT-5.6. It follows OpenAI's current GPT-5.6 prompting guidance and asks targeted questions when missing information would materially change the result.

## Install

Add this GitHub repository as a Codex plugin marketplace, then install the plugin:

```bash
codex plugin marketplace add IchenDEV/optimize-gpt-5-6-prompts
codex plugin add optimize-gpt-5-6-prompts@optimize-gpt-5-6-prompts
```

If the plugin does not appear immediately, restart Codex.

## Use

Invoke the bundled skill explicitly:

```text
$optimize-gpt-5-6-prompts Optimize this prompt for GPT-5.6: ...
```

The skill also supports Chinese requests:

```text
$optimize-gpt-5-6-prompts 优化下面的 GPT-5.6 提示词：……
```

## What it does

- Preserves the original goal, facts, constraints, output contract, and prompt-layer boundaries.
- Removes repetition, contradictions, obsolete scaffolding, and irrelevant instructions.
- Rewrites toward explicit outcomes, completion criteria, evidence, validation, and stop rules.
- Asks one to three focused questions when the goal, input, output, evidence, or permission boundary is materially unclear.
- Stops before assuming authority for external, destructive, costly, or scope-expanding actions.
- Keeps API reasoning settings separate from prompt text.

## Distribution structure

This repository follows OpenAI's plugin distribution layout:

```text
.agents/plugins/marketplace.json
plugins/optimize-gpt-5-6-prompts/
├── .codex-plugin/plugin.json
└── skills/optimize-gpt-5-6-prompts/
    ├── SKILL.md
    ├── agents/openai.yaml
    └── references/official-guidance.md
```

## Validation

The skill was iterated through three rounds of independent forward tests. The first round exposed over-assumption in underspecified prompts; after tightening the clarification gate, all six independent cases in the next two rounds passed. The published package also passes the OpenAI Skill and Plugin validators.

## Official sources

- [Build skills](https://learn.chatgpt.com/docs/build-skills)
- [Build plugins](https://learn.chatgpt.com/docs/build-plugins)
- [Using GPT-5.6](https://developers.openai.com/api/docs/guides/latest-model)
- [Prompting guidance for GPT-5.6 Sol](https://developers.openai.com/api/docs/guides/prompt-guidance-gpt-5p6)

## License

MIT
