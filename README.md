# Prompt Optimizer

One portable [Agent Plugins 1.0](https://agent-plugins.org/) package with eight model-specific prompt-optimization skills. The same skills work through the portable package, Codex marketplace, and Claude Code marketplace.

## Included skills

| Target | Skill | Codex | Claude Code |
| --- | --- | --- | --- |
| GPT-5.6 | `optimize-gpt-5-6-prompts` | `$optimize-gpt-5-6-prompts` | `/prompt-optimizer:optimize-gpt-5-6-prompts` |
| Claude Fable 5.1 | `optimize-claude-fable-5-1-prompts` | `$optimize-claude-fable-5-1-prompts` | `/prompt-optimizer:optimize-claude-fable-5-1-prompts` |
| Claude Fable 5 | `optimize-claude-fable-5-prompts` | `$optimize-claude-fable-5-prompts` | `/prompt-optimizer:optimize-claude-fable-5-prompts` |
| Claude Opus 5 | `optimize-claude-opus-5-prompts` | `$optimize-claude-opus-5-prompts` | `/prompt-optimizer:optimize-claude-opus-5-prompts` |
| Gemini | `optimize-gemini-prompts` | `$optimize-gemini-prompts` | `/prompt-optimizer:optimize-gemini-prompts` |
| Kimi | `optimize-kimi-prompts` | `$optimize-kimi-prompts` | `/prompt-optimizer:optimize-kimi-prompts` |
| GLM | `optimize-glm-prompts` | `$optimize-glm-prompts` | `/prompt-optimizer:optimize-glm-prompts` |
| DeepSeek-V4 | `optimize-deepseek-v4-prompts` | `$optimize-deepseek-v4-prompts` | `/prompt-optimizer:optimize-deepseek-v4-prompts` |

Every skill preserves explicit requirements, asks targeted questions only when missing information would materially change the result, and keeps runtime configuration separate from prompt text.

## Use with an Agent Plugins client

Clone or download this repository, then import `plugins/prompt-optimizer`. It is one self-contained Agent Plugins 1.0 package with a root `plugin.json` and eight immediate children under `skills/`.

Agent Plugins leaves installation and distribution to each client. See the [compatible clients list](https://agent-plugins.org/compatible-clients) and follow your client's local-directory or repository import instructions.

## Install with Codex

Add the marketplace once:

```bash
codex plugin marketplace add IchenDEV/prompt-optimizer-plugins
```

Install the single plugin:

```bash
codex plugin add prompt-optimizer@optimize-gpt-5-6-prompts
```

The Codex marketplace ID remains `optimize-gpt-5-6-prompts` so existing marketplace registrations do not need to change.

## Install with Claude Code

Add the marketplace and install the single plugin:

```bash
claude plugin marketplace add IchenDEV/prompt-optimizer-plugins
claude plugin install prompt-optimizer@prompt-optimizer-plugins
```

Claude Code namespaces every bundled skill with the single plugin name `prompt-optimizer`.

## Version 3 migration

Version 3 replaces the previous one-plugin-per-model layout. Existing users should uninstall the old model-specific plugins and install `prompt-optimizer`. Existing skill names are unchanged, so Codex invocations continue to use the same `$skill-name` values; Claude Code invocations use the `prompt-optimizer:` namespace shown above.

## Model-specific behavior

- **GPT-5.6:** produces lean, outcome-first prompts grounded in OpenAI guidance.
- **Claude Fable 5.1:** preserves working Fable 5 prompts, then applies targeted 5.1 guidance for effort, agent loops, progress visibility, completion, editing, and conversation history only where relevant.
- **Claude Fable 5:** uses context-rich prompting and targeted clarification grounded in Anthropic guidance.
- **Claude Opus 5:** optimizes subtractively, removing scaffolding the model no longer needs and adding only observed-behavior controls.
- **Gemini:** uses direct, consistently delimited prompts, explicit multimodal references, long-context anchoring, and runtime separation grounded in Google AI guidance.
- **Kimi:** emphasizes concrete context, input delimiters, reference-grounded fallbacks, and staged handling of long inputs.
- **GLM:** preserves system/user boundaries and uses parseable output contracts, references, examples, and decomposition where needed.
- **DeepSeek-V4:** keeps reasoning modes, special tokens, and tool protocols outside ordinary prompt prose, with explicit evidence boundaries.

## Layout

```text
.agents/plugins/marketplace.json         # Codex marketplace, one entry
.claude-plugin/marketplace.json          # Claude Code marketplace, one entry
plugins/prompt-optimizer/
├── plugin.json                          # Agent Plugins 1.0
├── .codex-plugin/plugin.json
├── .claude-plugin/plugin.json
└── skills/
    ├── optimize-gpt-5-6-prompts/
    ├── optimize-claude-fable-5-1-prompts/
    ├── optimize-claude-fable-5-prompts/
    ├── optimize-claude-opus-5-prompts/
    ├── optimize-gemini-prompts/
    ├── optimize-kimi-prompts/
    ├── optimize-glm-prompts/
    └── optimize-deepseek-v4-prompts/
```

Nothing depends on paths outside the plugin root, so portable clients and native Codex and Claude Code loaders can copy the package safely.

## Validation

The repository is checked with:

- the official Agent Plugins 1.0 JSON Schema for the portable manifest;
- the Agent Skills reference validator for all eight skills;
- the Codex Plugin and Skill validators;
- `claude plugin validate` for the marketplace and plugin;
- `git diff --check` and manifest/version consistency checks.

## Official sources

- [Agent Plugins specification](https://agent-plugins.org/specification)
- [Agent Skills specification](https://agentskills.io/specification)
- [Using GPT-5.6](https://developers.openai.com/api/docs/guides/latest-model)
- [Prompting Claude Fable 5.1](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-fable-5-1)
- [Prompting Claude Fable 5](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-fable-5)
- [Prompting Claude Opus 5](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-opus-5)
- [Gemini prompt design strategies](https://ai.google.dev/gemini-api/docs/prompting-strategies)
- [Kimi API: Prompt best practices](https://platform.kimi.com/docs/guide/prompt-best-practice)
- [Zhipu AI: Prompt engineering](https://docs.bigmodel.cn/cn/guide/platform/prompt)
- [DeepSeek-V4 technical report](https://arxiv.org/abs/2606.19348)

## License

MIT
