---
name: edit-pass
description: Run the full editing chain on a draft: 3-pass de-sloppifier then logic-check continuity audit.
argument-hint: <chapter-or-manuscript-file>
allowed-tools: [Read, Write, Edit, Bash, Glob, Grep]
---

# edit-pass

Run the full editing chain on a draft file. Two skills run in sequence: de-sloppifier (3-pass line edit), then logic-check (continuity and chronology audit). Each step hands its output to the next. If a required artifact is missing at any step, the chain stops and tells you what to build first.

**Input:** `$ARGUMENTS` (path to a chapter or manuscript file)

---

## Step 0: Validate Input

Confirm `$ARGUMENTS` points to a readable file. If the path is missing or unreadable, stop and say: "Provide a path to the chapter or manuscript file you want to edit."

---

## Step 1: Dependency Check

Before running either skill, check for the story bible. The logic-check skill requires it. Run:

```bash
ls story-forge/skills/logic-check/
```

Look for a story bible file (produced by the `dossier-to-outline` skill: dossier, character sheet, worldbuilding sheet, and outline). If no story bible exists, note it now. Do not stop the chain yet. The de-sloppifier runs without it. You will stop at Step 3 instead if it is still missing.

---

## Step 2: Run the de-sloppifier skill

Run the de-sloppifier skill on `$ARGUMENTS`.

The skill uses `story-forge/chunk.py` to split the file into chunks of roughly 1500 words on paragraph boundaries, runs all three passes on each chunk in order (Pass 1: pacing and paragraph structure; Pass 2: line editing; Pass 3: slop removal), then reassembles the output. Follow the skill's own chunking instructions exactly.

The output file from this step is the input to Step 3. Write it to a clearly named path, for example: `<original-stem>-deslopped.md` alongside the source file. Confirm the path before continuing.

---

## Step 3: Dependency Check for logic-check

Check again whether the story bible is present (dossier, character sheet, worldbuilding sheet, and outline produced by `dossier-to-outline`).

If it is still missing, stop the chain here and say:

> "De-sloppifier complete. Logic-check requires the story bible (dossier, character sheet, worldbuilding, and outline). Build it first by running the `dossier-to-outline` skill, then re-run `edit-pass` or run `logic-check` directly on the deslopped file at `<output path>`."

Do not proceed to Step 4 without the story bible.

---

## Step 4: Run the logic-check skill

Run the logic-check skill on the deslopped file from Step 2, passing the story bible as the reference document.

The skill runs the Full Six-Category Audit (premise logic, character-world fit, worldbuilding coherence, plot setup plausibility, convenience flags, and specific fixes). If the input is a scene brief or chapter draft, it also runs the Chapter Chronology Layer.

Follow the logic-check skill's own dependency check at the top of that skill. If it surfaces any additional missing prereqs beyond the story bible, stop and report them.

---

## Step 5: Present results

After both skills complete, present:

1. The path to the deslopped output file.
2. The logic-check audit report inline.
3. A brief summary of the most critical findings from each step (three to five bullets total).

If either skill surfaced issues that require another editing cycle before the draft is usable, say so plainly. Do not bury it.

---

## Notes for the orchestration layer

- This command is the orchestration layer only. The real prompts live in the individual skills. Do not reproduce or paraphrase the skill instructions here; invoke the skills by name and follow them.
- Preserve all filenames and Markdown formatting across steps. Do not rename files beyond the `-deslopped` convention above.
- If the input file is a full manuscript (multiple chapters), chunk and process each chapter independently through the de-sloppifier before passing the full reassembled output to logic-check.
- Em dashes are banned. The de-sloppifier removes them in Pass 3. If any survive into the final output, flag them in the summary.
