---
name: chem-oly-generator
description: Multi-agent pipeline for drafting, verifying, test-solving, and compiling advanced USNCO-level chemistry exams.
compatibility: Requires python3 with numpy and scipy
---

# Skill: End-to-End Olympiad Test Generation Engine

## Objective
Run a multi-agent assembly line that produces counterintuitive, error-free chemistry problems testing deep conceptual understanding, with guaranteed LaTeX compilability and exact arithmetic.

## Shared Files
- **References**: `references/FORMAT.md`, `SYLLABUS.md`, `EXCLUDED_TOPICS.md`, `constants.json`
- **History**: `references/past_tests/`
- **Artifacts**: `Master Outline` → `Problems` (LaTeX) → `Solutions` (LaTeX) → final compiled exam

## Delegation
`AGENTS.md` is the canonical definition of every role and of the **Shared Constraints** all generation agents must obey (content, voice, LaTeX, shard discipline). Load it before dispatching.

Six subagent stubs are registered per host — `.agents/agents/chem-oly-*.md` for Antigravity, `.claude/agents/chem-oly-*.md` for Claude Code — each pointing back at its section of `AGENTS.md`. Dispatch roles by invoking those subagents (`invoke_subagent`, the Agent/Task tool, or the host's equivalent). If the host registers no subagents, read `AGENTS.md` and pass each role's block to a freshly spawned agent instead.

Never simulate multiple roles inside one context window — that is the fastest route to the truncation failure below. Await each subagent's result before proceeding.

---

## Output Budget Discipline
***CRITICAL: this is the most common failure mode of this pipeline, on every host.***

An agent asked to emit a large artifact in ONE tool call truncates mid-argument. The call becomes malformed, nothing reaches disk, and the agent appears to freeze — full budget spent reading, no output file. An empty working directory after a long silent run is this bug. A half-length exam is the same bug in milder form.

Four rules, enforced on every generation step:

1. **Shard by topic.** Never assign one agent all 60 Part I questions, all 8 Part II problems, or all their solutions. Cap each agent at 2 topics / 12 MCQs / 2 free-response problems, and run shards in parallel. Target under ~4000 output tokens per agent.
2. **Write incrementally.** Create the file after the FIRST topic, then append one topic at a time. Never buffer a whole artifact for a single closing write.
3. **Preload shared reading.** The Director distills `references/past_tests/` ONCE (Step 1) and passes compact text to subagents. Subagents must not each re-read `references/past_tests/` — that is ~120 KB per agent, crowding out generation headroom.
4. **One file per shard.** Each agent writes only its own `*_shard_<n>` file; the Director concatenates. Two agents never share a file.

If an agent returns having written nothing, the shard was too wide. Split it and re-dispatch — do not retry it unchanged.

## The Healing Loop Rule
Referenced by Steps 3 and 4. When any agent flags a question:
- **Structural error** (too easy, out of scope, unsound): the Director halts the chain, invalidates that question, and routes it back to Brainstorm/Writer for a clean substitution.
- **Syntax error only**: the Director routes it to the Writer for a fix.

***CRITICAL: heal the flagged questions ONLY — never the shard around them.*** A flag names specific question numbers. Those are the only questions that may be regenerated. If a 12-question shard comes back with one defect, exactly one question is replaced and the other 11 are kept byte-for-byte as they already sit on disk.

- Dispatch repairs by question number, never by shard. A repair prompt says "replace Q7"; it never says "redo shard 2".
- The repair agent rewrites only the named questions and edits them into the existing shard file in place. It must not re-emit, re-verify, or "improve" untouched questions — surrounding questions already passed Solver and Reviewer, and rewriting them discards that verification and re-rolls the dice on 11 good questions to fix one bad one.
- Never delete and regenerate a whole shard file to fix part of it. Edit in place.
- Regenerating a passing question is a defect in itself, on par with shipping the bad one. If a repair returns a shard whose unflagged questions have changed, discard that result and re-dispatch it correctly.

---

## Pipeline

### Step 1 — Blueprint (Director & Brainstorm)
- **Director** confirms all reference files are present.
- **Director** builds two caches before dispatching anything, in the same pass over `references/past_tests/`. This is the ONLY point in the pipeline where full past tests are read.
  - `references/PAST_SETUPS.md` — the Past Setups Catalogue: one line per past question (topic, chemical system, what was asked).
  - `references/STYLE_CARD.md` — a compact voice specification distilled from the past exams, target ~1,100 tokens: stem phrasing and sentence rhythm, how conditions and given data are stated, answer-choice conventions (ordering, significant figures, units), and the characteristic neutral register. Capture how the exams *sound*, not what they ask — that is the catalogue's job.
    - State explicitly that stem length is **not** capped. Never derive a word-count ceiling from the exams: the mean Part I stem is short only because most past questions are easy, and a ceiling would silently cap the difficulty [SC] demands.
    - Include 3–4 verbatim stem openings as exemplars, chosen from the **most demanding** questions in `references/past_tests/` — multi-step inference, experimental design, error analysis. Never use a one-line recall stem as an exemplar; exemplars get imitated, and a recall exemplar teaches recall.
  - Cache both and reuse them on later runs.
- **Brainstorm** runs as parallel shards — one agent per 2 topics for Part I (5 shards), one per 2 problems for Parts II and III. Each shard receives FORMAT, SYLLABUS, EXCLUDED_TOPICS, and the catalogue — never the raw past tests.
- Each shard writes its own slice of the `Master Outline` incrementally, mapping brand-new, never-seen-before ways of testing syllabus knowledge (avoiding catalogued setups) onto unique chemical systems loaded with hidden traps students will fall into without realizing.
- **Director** concatenates the slices and verifies the question count against `references/FORMAT.md`.

### Step 2 — Drafting (Writer)
- Parallel shards on the same topic boundaries. Each Writer reads only its own outline slice and appends to only its own `Problems` shard file, one question per write.
- Writers calibrate voice from `references/STYLE_CARD.md` and must NOT open `references/past_tests/` at all. A full past exam is ~10,000 tokens per Writer shard; the style card is ~1,100.
- LaTeX carrying TikZ and chemfig is far denser than outline prose, so never widen a Writer shard past 12 MCQs or 2 free-response problems — this step hits the ceiling before any other.

### Step 3 — Blind Verification (Solver)
- Parallel shards on the same boundaries. The Solver reads *only* the problem text of its shard and must evaluate the problem programmatically in the sandbox to isolate the unique true value **before** looking at the answer choices.
- **Mathematical integrity**: verify internal consistency (cell potentials match Gibbs free energy changes, equilibrium concentrations stay positive, etc.).
- Append full, non-truncated solution math to its own `Solutions` shard file, one problem per write. Worked solutions are the longest artifact here; a shard wider than 12 questions will truncate.
- Review question text and answer choices for quality; flag defects per the Healing Loop Rule.

### Step 4 — Adversarial Audit (Reviewer & Director)
- **Reviewer** compares `Problems` against `Solutions`, checks strict compliance with `EXCLUDED_TOPICS.md` and the constraints in `AGENTS.md`, and flags defects per the Healing Loop Rule.

### Step 5 — Compilation (Compiler)
- Assemble the final document by CONCATENATING verified shard files mechanically (`cat`, or `\input{}` per shard). NEVER re-emit question or solution text — a full exam is tens of thousands of tokens and will truncate.
- The Compiler authors only the document class, preamble, section scaffolding, and closing matter. Everything else is copied byte-for-byte from shards that passed Solver and Reviewer.
- Part I in two-column layout; Parts II and III single-column with explicit `\vspace` blocks for student work.
- Verify the document compiles under pdfLaTeX with zero errors. Fix errors by editing the offending shard in place and re-concatenating, never by rewriting the whole document.
- Delete all intermediates when done (`Master Outline`, `Problems`, `Solutions`, shard files).

---

## Output Standard
Produce on disk a single `.tex` file:

```
[preamble] + [questions, per-part layout] + [solutions] + [closing matter]
```

Report the output path, page count, and pdfLaTeX exit status. Do NOT paste the assembled exam into the chat response — reproducing a full exam inline causes the truncation failure above.

If the user has no TeX compiler, do not install one; deliver the `.tex` only.

***CRITICAL: save ONLY the final PDF/TeX file to the user's Downloads folder.***
