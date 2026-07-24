# Prompt Optimizer Plugins

A dual-protocol marketplace containing focused prompt optimizers for GPT-5.6, Claude Fable 5, and Claude Opus 5. The same Skill files are packaged for both Codex and Claude Code, so behavior stays consistent across the two runtimes.

## Included plugins

| Plugin | Target | Codex Skill | Claude Code Skill |
| --- | --- | --- | --- |
| `optimize-gpt-5-6-prompts` | GPT-5.6 | `$optimize-gpt-5-6-prompts` | `/optimize-gpt-5-6-prompts:optimize-gpt-5-6-prompts` |
| `claude-fable-5-prompt-optimizer` | Claude Fable 5 | `$optimize-claude-fable-5-prompts` | `/claude-fable-5-prompt-optimizer:optimize-claude-fable-5-prompts` |
| `claude-opus-5-prompt-optimizer` | Claude Opus 5 | `$optimize-claude-opus-5-prompts` | `/claude-opus-5-prompt-optimizer:optimize-claude-opus-5-prompts` |

Every optimizer preserves explicit requirements, asks targeted questions when missing information would materially change the result, and keeps runtime configuration separate from prompt text.

## Install with Codex

Add the marketplace once:

```bash
codex plugin marketplace add IchenDEV/prompt-optimizer-plugins
```

Install any subset of the plugins:

```bash
codex plugin add optimize-gpt-5-6-prompts@optimize-gpt-5-6-prompts
codex plugin add claude-fable-5-prompt-optimizer@optimize-gpt-5-6-prompts
codex plugin add claude-opus-5-prompt-optimizer@optimize-gpt-5-6-prompts
```

The Codex marketplace ID remains `optimize-gpt-5-6-prompts` so existing GPT-only installations can continue to upgrade after the repository rename.

## Install with Claude Code

Add the same GitHub marketplace:

```bash
claude plugin marketplace add IchenDEV/prompt-optimizer-plugins
```

Install any subset of the plugins:

```bash
claude plugin install optimize-gpt-5-6-prompts@prompt-optimizer-plugins
claude plugin install claude-fable-5-prompt-optimizer@prompt-optimizer-plugins
claude plugin install claude-opus-5-prompt-optimizer@prompt-optimizer-plugins
```

Claude Code namespaces plugin skills with the plugin name. Use the full slash-command names shown in the table above.

## What the optimizers do

- Preserve the original goal, facts, constraints, output contract, and prompt-layer boundaries.
- Ask one to three focused questions when outcome, input, evidence, schema, or permission boundaries are materially unclear.
- Avoid producing a provisional prompt while blocking information is missing.
- Rewrite clear requests into direct prompts suited to the target model.
- Define assessment versus implementation, authorization boundaries, evidence, verification, and completion where relevant.
- Replace requests for hidden chain of thought with concise rationale, supporting evidence, and checks.
- Use complex structure only when it improves behavior.

### What the Claude Opus 5 optimizer does differently

Opus 5 runs Claude Opus 4.8 prompts well out of the box, so that skill treats optimization as mostly subtractive:

1. **Subtract first.** Remove verification and re-check instructions, verifier subagents, "don't think" rules, conservative review filters, and legacy scaffolding — Opus 5 already self-verifies and self-corrects, and these instructions compound into wasted tokens.
2. **Tune only observed behavior.** One targeted block per symptom: long responses, heavy agentic narration, padded written deliverables, scope expansion, eager subagent delegation, correction narration, thinking-disabled output artifacts.
3. **Keep runtime settings out of the prompt.** Effort, `max_tokens`, thinking, retries, and fallbacks belong in the request, not the prompt text.

Its reference material lives in `plugins/claude-opus-5-prompt-optimizer/skills/optimize-claude-opus-5-prompts/references/`:

- `official-guidance.md` — dated working summary of the official docs, with canonical URLs.
- `snippets.md` — copy-ready instruction blocks from Anthropic's guide, one per behavior.

## Dual-protocol layout

```text
.agents/plugins/marketplace.json         # Codex marketplace
.claude-plugin/marketplace.json          # Claude Code marketplace
plugins/
├── optimize-gpt-5-6-prompts/
│   ├── .codex-plugin/plugin.json
│   ├── .claude-plugin/plugin.json
│   └── skills/optimize-gpt-5-6-prompts/
├── claude-fable-5-prompt-optimizer/
│   ├── .codex-plugin/plugin.json
│   ├── .claude-plugin/plugin.json
│   └── skills/optimize-claude-fable-5-prompts/
└── claude-opus-5-prompt-optimizer/
    ├── .codex-plugin/plugin.json
    ├── .claude-plugin/plugin.json
    └── skills/optimize-claude-opus-5-prompts/
```

Each plugin owns its Skill directory. Nothing depends on paths outside the plugin root, so both runtimes can safely copy plugins into their versioned caches.

## Validation

The repository is checked with:

- the Codex Plugin validator for every plugin;
- the Codex Skill validator for every skill;
- `claude plugin validate` for the marketplace and every plugin;
- clean-environment installation tests for both Codex and Claude Code;
- representative forward tests for clarification, prompt-only output, external actions, long-running work, hidden-reasoning requests, and long-document question answering.

## Official sources

- [Codex: build skills](https://learn.chatgpt.com/docs/build-skills)
- [Codex: build plugins](https://learn.chatgpt.com/docs/build-plugins)
- [Claude Code: create plugins](https://code.claude.com/docs/en/plugins)
- [Claude Code: create a plugin marketplace](https://code.claude.com/docs/en/plugin-marketplaces)
- [Prompting Claude Fable 5](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-fable-5)
- [Prompting Claude Opus 5](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-opus-5)
- [What's new in Claude Opus 5](https://platform.claude.com/docs/en/about-claude/models/whats-new-opus-5)
- [Effort](https://platform.claude.com/docs/en/build-with-claude/effort)
- [Using GPT-5.6](https://developers.openai.com/api/docs/guides/latest-model)

## License

MIT
