# Changelog

## 3.2.0

- Added `optimize-claude-fable-5-1-prompts` while preserving the existing Fable 5 optimizer.
- Covered Fable 5.1 migration, effort tuning, agent-loop batching, progress visibility, append-only conversation history, task completion, scoped changes, source quotation, targeted edits, and runtime separation.
- Kept both Fable skill entrypoints compact and moved detailed behavior into routed references that cover every model-specific section plus the complete shared Anthropic methodology for clarity, examples, XML, long context, output control, tools, thinking, agentic systems, vision, frontend work, migration, runtime, source quotation, and compaction.
- Standardized all distributed skill instructions, output labels, and default prompts on English-only wording.
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
