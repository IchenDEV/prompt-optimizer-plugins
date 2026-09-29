# Practitioner guidance: GPT-6 Astra skills and AGENTS.md cleanup

Dated working summary checked against OpenAI's Developers blog on 2026-09-13. This file paraphrases official practitioner advice for optimizer use; it is not a verbatim copy of the source.

## Canonical sources

- Eric Provencher, [Rethinking skills and prompts for GPT-6 Astra](https://developers.openai.com/blog/rethinking-skills-and-prompts-for-gpt-6-astra) (OpenAI Developers blog, 2026-09-11)
- Complementary API docs: [Using GPT-6 Astra](https://developers.openai.com/api/docs/guides/latest-model)

Treat the blog as the canonical harness / Skills / `AGENTS.md` cleanup guide. Treat the latest-model docs as the canonical API prompting and migration reference. Prefer `$optimize-gpt-6-astra-prompts` when the task is rewriting a product prompt rather than cleaning repo scaffolding.

## Core thesis

Coding agents needed heavy scaffolding a year ago. With more capable models—especially GPT-6 Astra—that handholding often burns context, triggers wrong skills, or stops work too early. Revisit Skills, `AGENTS.md`, and task prompts; subtract first.

## Skill files

Skills are markdown prompts that may ship with resources and bundled scripts. They help most for a specific workflow or app—not as a dump of every tip.

### Description budget and truncation

Name and description are always loaded so the model can choose when to open a skill. Long descriptions plus many installed skills cause Codex to shorten descriptions to fit. Truncated text makes routing worse: the model sees less of each description and picks poorly.

Also flag descriptions that contradict each other or over-emphasize when a skill should fire—those load instructions that do not help the task.

### Short, trigger-specific descriptions

Keep descriptions as short as possible while stating when the skill applies. Prefer routing logic over marketing copy.

Bad (too broad—fires on any database-adjacent work):

```text
Create and validate Postgres schema migrations. Use when working with
databases, queries, models, or persistence.
```

Good (narrow trigger):

```text
Create and validate Postgres schema migrations. Use when adding or
changing a migration, or reviewing its rollout.
```

Front-load the job and trigger words so matching still works if Codex truncates the tail.

### Progressive disclosure (minimal router)

Reading a skill costs context, advances compaction, and can inject guidance that does not apply. For multi-workflow skills, make the root `SKILL.md` a minimal router that points to supporting docs and scripts. Give enough guidance to know where to look—do not force a full itinerary up front.

### Less recipe, more outcome

Elaborate step-by-step recipes helped weaker models; stronger models handle nuance better, and over-specific guidance can now hinder results. Prefer outcomes, constraints, evidence, and stop conditions.

### Multi-model repositories

Repository skills also steer contributors' agents on other models (e.g. Sol or Luna). Guidance that helps a weaker model may overconstrain Astra. Note shared vs Astra-specific instructions when proposing edits.

### Skill authoring tooling

Codex `$skill-creator` guidance was updated for these failure modes. When recommending new skills, favor short descriptions, progressive disclosure, and lean bodies.

## AGENTS.md

`AGENTS.md` applies whenever the model works in the repository. Revisit each always-on rule and ask whether every task still needs it.

### Contextual doc pointers, not mandatory pre-reads

Requiring a stack of docs or a full repo map before every edit is excessive for small changes. Astra can decide what to read. Pointing to docs remains useful when contextual.

Bad:

```text
Before every edit, read architecture.md, database.md, and deployment.md.
```

Good:

```text
Use architecture.md for service boundaries, database.md for schema
changes, and deployment.md when preparing a deployment.
```

### Recalibrate testing instructions

Earlier models needed encouragement to run tests. Astra tends to verify on its own, so the same "always test broadly" instructions can cause unnecessary testing. Calibrate verification to change impact; keep required checks for meaningful risk.

### Give narrow permissions where it hesitates

Astra is thorough but can be tentative about how far to take a task. Use `AGENTS.md` to grant permission for specific safe workflows:

```text
The local tests use disposable fixtures and have no production access.
Run them, fix failures caused by the requested change, and rerun
affected tests without asking for approval at each step.
```

Keep production, destructive, and external-write gates explicit.

## Decision boundaries

Strong ask-first language added for older models that acted without permission can now stop Astra too early. Astra is OpenAI's most aligned model: it has better judgment and will not perform tasks unless it knows they are safe—treat it that way.

Keep boundaries for irreversible or external actions. Soften or remove blanket approval requirements for reversible, in-scope, read-only, or already-authorized work. If switching to Astra from models that needed tighter reins, update language that Astra may take too seriously and stop where you would be happy for it to continue.

## Persistence and completion

Compared with GPT-5.6 Sol, Astra may feel more tentative: it may return after a first implementation for review while work remains.

- Define completion before starting when the task includes getting work running, inspecting results, and fixing failures.
- A requirement to stop for review after the first implementation pulls the model toward an earlier stopping point—keep it only if you actually want that checkpoint.
- If further exploration is desired, say what to explore and where to stop.
- Prefer clarifying the finish line over adding another layer of process scaffolding.

## Suggested audit prompt

```text
Audit this project's Skills, AGENTS.md, and related agent scaffolding
against OpenAI's Rethinking skills and prompts for GPT-6 Astra guidance.

Find unclear, conflicting, or obsolete instructions that cause early
stops, redundant approvals, wrong skill loads, description bloat /
truncation risk, mandatory full-repo reads, or over-testing. Quote each
issue, name the file, explain the Astra impact, and propose a specific
edit. Preserve intentional safety gates and flag any change that expands
authority. Prioritize the highest practical wins. Propose edits for
review before changing files.
```

## Evaluation

After cleanup, re-run representative tasks:

1. small edit / typo-class change (should not trigger full-repo ritual or huge test matrix);
2. medium feature with local tests (should persist through fix cycles when permitted);
3. irreversible or external action (should still stop for approval);
4. skill selection on a near-miss task (wrong skills should no longer auto-attach);
5. crowded skill set (routing still works when descriptions are short).

Measure fewer unnecessary pauses, lower context burn, correct skill selection, and unchanged safety behavior on gated actions.
