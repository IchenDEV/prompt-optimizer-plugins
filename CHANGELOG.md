# Changelog

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
