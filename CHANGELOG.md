# Changelog

## 3.6.0

- Added `optimize-claude-opus-5-5-prompts`, grounded in Anthropic's [Prompting Claude Opus 5.5](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-opus-5-5) guide.
- Covered always-on thinking, effort calibration from `medium`, thinking-disabled migration, unattended early stops, progress-update display, multi-app exploration, time signals, chat thinking latency, pasted-content marking, vision, and frontend defaults.
- Cross-linked Opus 5 and Fable 5 skill descriptions so routing excludes Opus 5.5 when that model is intended.
- Updated portable, Codex, and Claude Code plugin metadata to 3.6.0.

## 3.5.0

- Added `optimize-gpt-6-sol-luna-prompts`, grounded in OpenAI's [Using GPT-6](https://developers.openai.com/api/docs/guides/latest-model) family guide plus the [GPT-6 Sol](https://developers.openai.com/api/docs/models/gpt-6-sol) and [GPT-6 Luna](https://developers.openai.com/api/docs/models/gpt-6-luna) model pages.
- Covered Sol vs Luna routing (`gpt-6-sol` for complex coding/agentic work, `gpt-6-luna` for efficient high-volume work), `none` reasoning support, Responses vs Chat Completions tool rules, and GPT-5.6 → GPT-6 migration notes (no GPT-6 Terra).
- Left `optimize-gpt-5-6-prompts` in place for GPT-5.6 workloads.
- Kept GPT-6 family prompting snippets as evaluate-on-Sol/Luna mitigations rather than Astra-only defaults.
- Updated portable, Codex, and Claude Code plugin metadata to 3.5.0.

## 3.4.0

- Rebased `audit-gpt-6-astra-skills` on the official OpenAI Developers blog [Rethinking skills and prompts for GPT-6 Astra](https://developers.openai.com/blog/rethinking-skills-and-prompts-for-gpt-6-astra) (replacing the prior X Article as the canonical source).
- Added blog-faithful bad/good examples for skill descriptions and `AGENTS.md` pre-reads, plus failure modes for description truncation, contradictory triggers, progressive-disclosure routers, aligned decision boundaries, and completion/persistence.
- Shortened all nine bundled skill `description` fields into trigger-focused routing text so Codex is less likely to truncate them when many skills are installed.
- Cross-linked `optimize-gpt-6-astra-prompts` to the blog for harness cleanup; kept product-prompt rewriting on the latest-model API guide.
- Updated portable, Codex, and Claude Code plugin metadata to 3.4.0.

## 3.3.0

- Added `audit-gpt-6-astra-skills`, grounded in Eric Provencher's [Rethinking skills and prompts for GPT-6 Astra](https://x.com/pvncher/status/2095991462416490862) practitioner guidance.
- Covered Skills description hygiene, progressive disclosure, AGENTS.md full-repo/pre-read cleanup, testing recalibration, decision boundaries, and persistence/completion audits.
- Kept `optimize-gpt-6-astra-prompts` for official-docs prompt rewriting; this skill focuses on harness and scaffolding cleanup.
- Updated the portable, Codex, and Claude Code plugin metadata for nine bundled skills.

## 3.2.0

- Added `optimize-gpt-6-astra-prompts`, grounded in OpenAI's [Using GPT-6 Astra](https://developers.openai.com/api/docs/guides/latest-model) guide.
- Covered Astra initiative and follow-through, instruction priority for skills/`AGENTS.md`, writing-style controls, subagent delegation, testing calibration, and migration/runtime separation.
- Kept the existing GPT-5.6 optimizer and pointed its canonical model guide at the GPT-5.6-specific latest-model URL.
- Updated the portable, Codex, and Claude Code plugin metadata for eight bundled optimizers.

## 3.1.0

- Added `optimize-gemini-prompts`, grounded in Google AI's official prompt design strategies.
- Covered Gemini 3 structure, multimodal references, long-context ordering, few-shot examples, grounding, and runtime-setting separation.
- Updated the portable, Codex, and Claude Code plugin metadata for seven bundled optimizers.

## 3.0.0

- Consolidated all model optimizers into one `prompt-optimizer` plugin with six bundled Agent Skills.
- Added Kimi, GLM, and DeepSeek-V4 skills alongside the existing GPT-5.6 and Claude skills.
- Replaced the model-specific Codex and Claude Code marketplace entries with one installable plugin while keeping both marketplace IDs stable.
- Added one portable Agent Plugins 1.0 package at `plugins/prompt-optimizer` and documented the version 3 migration.

## 2.2.0

- Added a portable Agent Plugins 1.0 `plugin.json` to every plugin package.
- Kept the existing Codex and Claude Code manifests as additive compatibility layers.
- Aligned plugin versions across the portable, Codex, and Claude Code manifests.
- Documented portable package paths, client-managed installation, and Agent Plugins validation.

## 2.1.0

- Merged the Claude Opus 5 prompt optimizer in from the standalone `skil` marketplace.
- Registered `claude-opus-5-prompt-optimizer` in both the Codex and Claude Code marketplaces.
- Aligned the merged plugin's author, homepage, and repository metadata with the other plugins.

## 2.0.0

- Renamed the repository to `prompt-optimizer-plugins`.
- Added the Claude Fable 5 prompt optimizer alongside the GPT-5.6 optimizer.
- Added Claude Code plugin manifests and marketplace metadata.
- Kept Codex plugin manifests and marketplace metadata.
- Preserved the existing plugin, Skill, and Codex marketplace names for compatibility.

## 1.0.0

- Published the GPT-5.6 Prompt Optimizer as a Codex plugin and marketplace.
