### USNCO exam generator agent — VARIANT B (style card) ###

> **Experimental A/B fork.** Identical to the main `chem-oly-generator` except that Writer
> agents calibrate voice from a ~1,100-token `references/STYLE_CARD.md` distilled once by the
> Director, instead of each reading a full ~10,000-token past exam. Saves roughly 43,500
> input tokens per Part I run across 5 Writer shards.
>
> **What to watch when comparing against the control:** whether problem stems still read like
> real USNCO prose. Voice is harder to compress than a list of used setups, so this is the
> change most likely to cost quality. Compare stem phrasing, how given data is stated, and
> answer-choice formatting against the main folder's output before adopting.
>
> **Difficulty is the second thing to watch.** The card is scoped to voice — Brainstorm sets
> difficulty and never reads it — but the card is distilled from exams that are *easier* than
> what [SC] demands, so it can leak difficulty guidance it was never meant to carry. An earlier
> revision derived a "seldom more than 40-50 words" stem ceiling from the past-exam mean (real
> stems run 6–233 words, median 42) and used a one-line recall question as an exemplar; both
> capped difficulty. The card now states that length is uncapped and draws its exemplars from
> the most demanding past questions. Re-check this whenever the card is regenerated, and note
> that the Reviewer judges voice against the card, so anything the card implies is enforced.

A multi-agent skill for generating USNCO-style chemistry mock exams — deliberately harder than the real thing, built around hidden conceptual traps, and emitted as compilable LaTeX.

A Director agent shards the work across Brainstorm, Writer, Solver, Reviewer, and Compiler subagents. `SKILL.md` defines the pipeline; `AGENTS.md` is the canonical definition of the six roles and of the shared constraint list every generation agent is held to.

#### Setup

One file set runs on every supported host. The six subagents are registered twice, in each host's own dialect — `.agents/agents/chem-oly-*.md` for Antigravity, `.claude/agents/chem-oly-*.md` for Claude Code. Both sets are thin stubs pointing back at `AGENTS.md`, so **rules are edited in `AGENTS.md` only**; the stubs carry nothing but frontmatter, model tier, and tool grants.

- **Google Antigravity** — import the folder as a skill ([setup guide](https://antigravity.google/)). Subagents are discovered from the workspace at `.agents/agents/`, so if you run the pipeline from a different workspace, copy `.agents/agents/chem-oly-*.md` there or into `~/.gemini/config/agents/`.
- **Hermes** — reads `skill.yaml`; the frontmatter in `SKILL.md` is ignored harmlessly. Hermes has no subagent registry, so the Director falls back to reading role blocks out of `AGENTS.md` directly.
- **Claude Code** — drop the folder in `~/.claude/skills/chem-oly-generator/`. Claude Code discovers subagents from `~/.claude/agents/` or `<project>/.claude/agents/` — *not* from a `.claude/` nested inside a skill — so copy `.claude/agents/chem-oly-*.md` to one of those locations. Working directly inside a clone of this repo, they resolve as-is.

Ask for **one part at a time** (Part I, II, or III) — `references/FORMAT.md` specifies each.

#### A note on output limits

The pipeline's most common failure is an agent trying to emit a whole exam in a single write, truncating mid-call and leaving nothing on disk. The "Output Budget Discipline" section of `SKILL.md` is what prevents this, and it applies on every host — if you edit the skill, keep the sharding rules intact.

#### Credits

The `references/past_tests` folder contains TeX versions of past USNCO exams. Learn more about the USNCO [here](https://www.acs.org/education/olympiad.html). These are not my property and do not fall under the same license as the rest of the repo.

If you like my stuff, consider supporting me on [Patreon](https://www.patreon.com/cw/origamikoala). Thanks!
