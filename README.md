# story-forge

Treynor's book-creation pipeline, rebuilt as a Claude Code plugin.

It is the same system documented in the writing wiki ([[chapter-generation-pipeline]], [[anti-slop]], [[story-hacker-prompts]], [[voice-matching]]) and recovered from the n8n book-automation exports (`memory/writing/automations/`, cataloged in `memory/writing/book-automation-workflows.md`). The n8n version orchestrated LLM calls externally. This version runs natively in Claude Code, where the agent **is** the orchestrator, so it is smarter, can read the craft wiki while it works, and does not need n8n.

## Architecture

- **Skills** (`skills/<name>/SKILL.md`) are the LEGO bricks. Each is one focused craft transform: persona + rules + the actual prompts pulled from the n8n workflow, plus iteration notes. Some bundle a small **python helper** for the deterministic glue (chunking a draft into ~1500-word windows, wordcount estimation, chronology diffing, file assembly). Python is the glue; Claude is the brain.
- **Commands** (`commands/<name>.md`) are the chains: a named workflow that invokes a sequence of skills with artifacts passed between them (e.g. the full braindump -> dossier -> outline -> chapters pipeline).
- **The plugin** is the box: install once, get the whole pipeline.

## Skills (planned set, from the 10 recovered workflows)

| Skill | From workflow | Stage |
|---|---|---|
| **de-sloppifier** ✅ (proof) | Line Editor and De-sloppifier | post-draft editing (3 passes) |
| story-hacker | Book/Script Story Hacking | analyze a comp title into a reusable plot template |
| braindump-to-dossier | Braindump to Dossier | pre-writing concept |
| dossier-to-outline | Dossier to Full Outline | outlining |
| outline-generator | Advanced Outline Generator | advanced outlining (emotional audit + sliders) |
| outline-to-chapters | Advanced Outline to Chapters | drafting (selectors, slider briefs, chronology checks) |
| logic-check | (chronology/logic nodes) | continuity audit |
| book-summarizer | Book to Summary+ | summaries + marketing quotes |
| public-domain-cleanup | Public Domain (Cleaned-up) | source prep |
| short-story-hacker | Short Story Hack | short-fiction structural breakdown |

## Workflows (planned commands)

- `/story-forge:full-book` — braindump-to-dossier → dossier-to-outline → outline-to-chapters, artifacts on disk between steps.
- `/story-forge:edit-pass` — the 3-pass de-sloppifier across a chapter or manuscript.
- `/story-forge:hack` — story-hacker on a comp title to extract a reusable structure.

## Status

v0.1.0. **de-sloppifier** is the first skill, built end-to-end as the pattern proof. The remaining nine skills + the workflow commands are cloned from that pattern next.

## Notes

- Naming is a placeholder; rename the plugin if you want.
- The n8n exports it descends from carry credential references only (no secrets); safe to version.
