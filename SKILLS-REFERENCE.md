# story-forge Skills Reference

Your book-creation pipeline, rebuilt as a Claude Code plugin from the recovered n8n automations. Eleven skills plus three chaining commands. Claude runs each skill; small python helpers handle the deterministic glue (chunking, wordcount, file assembly).

Every consuming skill has a **dependency check**: if it needs a world, a voice, an outline, or a dossier that does not exist yet, it stops and tells you which upstream skill to run first, instead of guessing.

---

## The main pipeline (idea to drafted chapters)

These run in order. The artifact each one produces is the input to the next.

**1. braindump-to-dossier.** Turns a raw story idea into a structured dossier. Give it a braindump and a title; it generates premises, picks the strongest on logic, originality, and emotional gut-punch, then builds a full dossier (characters, worldbuilding, synopsis, outline plan) through a critique-and-rewrite loop.
- *Needs:* a braindump and a title. *Produces:* the dossier.

**2. dossier-to-outline.** Turns the dossier into the three production documents: a character bible, a worldbuilding sheet, and a chapter-by-chapter outline. Each goes through generate, critique, rewrite.
- *Needs:* a dossier (from step 1). *Produces:* character bible, worldbuilding sheet, outline.

**3. outline-generator.** The advanced outliner. Builds a chapter-by-chapter outline with a six-dimension emotional audit and per-chapter sliders (Tension, Dread, Emotional Intimacy, Relationship Tension, Pacing Energy, Humor), plus a logic check. Use it when you want the richer, instrumented outline before drafting.
- *Needs:* dossier, character sheet, worldbuilding sheet. *Produces:* the advanced outline.

**4. voice.** Extracts an author voice fingerprint, a style sheet, from writing samples. This is the voice spec the drafting skill anchors to, so the prose sounds like a chosen author and not like generic AI.
- *Needs:* writing samples. *Produces:* the voice spec. (Run it any time before drafting.)

**5. outline-to-chapters.** The drafting engine. Runs the full thirteen-step chapter-generation pipeline per chapter: context-slicing selectors for plot, characters, and world; wordcount estimation; a three-part scene brief; chronology checks before and after the draft; first draft; style check; final rewrite.
- *Needs:* an outline, a character bible, a worldbuilding sheet, and a voice spec. *Produces:* drafted chapters.

---

## Editing and QC

**de-sloppifier.** The anti-slop line editor. Runs a three-pass edit on roughly 1,500-word chunks: pacing and rhythm, line-level edits, then AI-pattern removal (kills generic AI tells, cliche, vocabulary inflation, and em-dashes).
- *Needs:* a drafted chapter or passage. *Produces:* cleaned prose.

**logic-check.** The continuity auditor. Runs a six-category logic check plus chapter-level chronology criteria against your story bible, catching plot holes, timeline breaks, convenience failures, information revealed too early, foreshadowing gaps, and worldbuilding contradictions.
- *Needs:* a draft (or outline or dossier) and the story bible. *Produces:* an audit report.

---

## Analysis and source tools (standalone)

These do not need the pipeline. Each just needs its own input text.

**story-hacker.** Reverse-engineers an existing book or script into an anonymized, reusable plot template. Per-chapter scene analysis, then a two-pass structural breakdown into a genre-neutral skeleton you can build a new story on.
- *Needs:* a source text. *Produces:* a reusable plot template.

**short-story-hacker.** The same move for short fiction. Breaks a short story into its structure: the magic sword, the try/fail cycles, the heart's-desire arc, the inciting incident, the conceptual hook, with verbatim prose examples.
- *Needs:* a short story. *Produces:* a structural breakdown.

**book-summarizer.** Per-chapter analysis of a finished or draft manuscript: five-to-six-sentence summaries, character breakdowns with each character's heart's desire, setting-function notes, conflict identification, trope detection, and marketing-ready quote extraction.
- *Needs:* a manuscript or chapters. *Produces:* summaries and pull-quotes.

**public-domain-cleanup.** Preps a raw public-domain text (often OCR-scanned or archaic) by modernizing spelling, fixing OCR errors, and cleaning punctuation in roughly 1,000-word passes, without changing the vocabulary or meaning.
- *Needs:* a public-domain source text. *Produces:* a clean file ready for the pipeline.

---

## Commands (the chains)

Commands wire skills together so you run a whole stage with one call.

**/story-forge:full-book.** The whole pipeline end to end: voice, then braindump-to-dossier, then dossier-to-outline, then outline-to-chapters, passing artifacts between steps.

**/story-forge:edit-pass.** The editing chain on a draft: de-sloppifier, then logic-check.

**/story-forge:hack.** story-hacker on a comp title to pull a reusable structure (use short-story-hacker for short fiction).

---

*Tip: the individual skills hold the real prompts and run one transform each. The commands are the orchestration layer that chains them. You can always invoke a single skill directly when you only need that one step.*
