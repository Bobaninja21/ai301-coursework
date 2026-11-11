# Contribution: Document what each LLM provider retains and where

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** 1
**Student:** [Student name]
**GitHub Username:** Bobaninja21
**Issue:** https://github.com/DarkWorldCoder/market-mirror/issues/164
**Pull Request:** [GitHub PR link, once opened]

One repository, built across units 5 to 8, telling the whole story of your real
contribution. Fill it as you work, not at the end — the sections below ask for evidence that
is far easier to write down as it happens than to reconstruct in the last weekend.

Contribution Number stays `[1]` unless you make more than one contribution this term; it
just numbers them.

---

## Toolkit

Every story section below, from Why I Chose This Issue through Pull
Request, ends with a Toolkit block: which tool did that phase's work
and how you used it, what it produced, what difficulties or
shortcomings it presented, and what you changed or would change
because of them. Together the blocks are the thread the rubric's
Toolkit points read, and they are the raw material for your unit-9
portfolio, so keep them current as you work rather than
reconstructing them later.

A Toolkit block looks like this (copy it into each story section and
fill it there):

```text
**Toolkit**

- Tool: [which tool did this phase's work, and how you used it]
- What it produced: [described in a sentence or a short excerpt]
- Difficulties: [what fought you or fell short, or "none met"]
- What changed: [the patch, revision, or workaround, what you would
  update, or "nothing"]
```

This section also keeps one short dated entry per week of tool work,
starting below: what you built or changed that week, what broke, and
what you changed because of it. The blocks tell the story where it
happened; these entries are the toolkit's own timeline.

All four entries are graded. Keep every heading below, and fill each
one in the week it belongs to.

**Unit 5 (2026-11-10): installed issue-scout, wrote the strategy, ran two scans, found a rubric gap**

I installed the scout over my unit-1 skill, carried my fit profile forward with a new
"where I want to search" sentence, and wrote five `gh search issues` queries, each traced to
a profile sentence. `gh` was not logged in, so I ran the same queries through the
unauthenticated REST search API, whose 60-calls/hour limit forced a 15-minute wait mid-grading.
Scan 1's "fewer comments first" ordering floated issues filed minutes earlier to the top, and
3 of the 5 already had open PRs with 0 comments, so I patched the strategy to drop issues
younger than 3 days. Scan 2 then showed my rubric accepting two aws/agent-toolkit-for-aws issues
whose broken text lives in a different repo; I wrote up a `fix-is-in-this-repo` check as a
proposed patch, not yet applied.

**Unit 6: [one line on the tool work]**

[2-4 sentences: the voice-guard, the repo-memory file. What broke or
fell short, and what changed because of it. "Nothing broke" counts when
it is true - say how you know.]

**Unit 7: [one line on the tool work]**

[2-4 sentences: the ship-gate and your gate manifest. What it produced
on your real branch, what fought you, what changed because of it.]

**Unit 8: [one line on the tool work]**

[2-4 sentences: the PR-monitor and your playbook. What it read, what it
drafted, what you did with the draft.]

---

## Why I Chose This Issue

I searched with my own issue-scout: five queries built from my fit profile (Python bugs,
Python LLM/CLI/agent tooling, FastAPI/SQLAlchemy/pytest, Python docs, scoped TypeScript bugs),
all on `good first issue`, unassigned, open. Scan 1 combined 394 unique results, ranked them,
and graded the top five: it accepted only openslop/openslop#1220 (a TypeScript prompt fix) and
rejected the rest, three of them because an open PR already existed (curvine-doc #120, repowise
#5490/#5493, yasbd-lib #426/#427) and one because its whole body was "Review attack vectors".
After patching the strategy, scan 2 surfaced this issue at rank 1 from the LLM/CLI/agent query
(signals: `documentation` label, 0 comments, updated 2026-11-06) and the scout graded it
**accept**: all six required checks pass (repo pushed 2026-11-10, no AI ban in CONTRIBUTING.md,
no assignee/comments/linked PRs, opened by the owner with named files and a "Done when" list).
`maintainer-responsive` came back `unclear` because I skipped that fetch to save API calls.

It fits because it is a Python CLI for LLM providers, the exact kind of tooling I use daily,
and the change is a docs table plus a small `privacy` field in `CATALOG` in
`backend/claude_cli/providers.py` with a pytest test, which I can verify locally (the suite mocks
the model, no API key needed). I picked it over openslop#1220 because verifying that one needs a
live LLM run. The honest cost: `CATALOG` has about 26 entries, roughly 15 of them hosted APIs whose
retention, DPA and region terms I have to research from each provider's own pages, so this is
more reading than coding. I want to learn to land a small, sourced change in someone else's repo.

On the tool: the rubric held up on claims (it caught every open PR), but the ranking misread the
wild twice. Brand-new issues get claimed by PR within hours, not by comment, so "fewer comments
first" was rewarding exactly the wrong issues; and the rubric accepted two aws issues whose broken
README text is generated by a separate CLI, which a search of 99 docs in that repo confirmed.
I patched the strategy for the first and drafted a rubric check for the second.

Claim comment: https://github.com/DarkWorldCoder/market-mirror/issues/164#issuecomment-6788636938 (posted 2026-11-10).

**Toolkit**

- Tool: issue-scout (my search strategy + my unit-1 rubric), run as two scans on 2026-11-10;
  queries went through the REST search API because `gh` was not logged in.
- What it produced: scan 1 graded 5 (1 accept: openslop#1220); scan 2 graded 5 (3 accept),
  with market-mirror#164 at rank 1, verdict accept, six required checks pass,
  `maintainer-responsive` unclear.
- Difficulties: "fewer comments first" ranked minutes-old issues highest, and 3 of 5 were
  already claimed by open PRs; my `sort=updated` in the API workaround made the 90-day filter
  a no-op; the rubric accepted aws#543/#541 although their fix lives in another repo.
- What changed: added a "drop issues created < 3 days ago" filter and went back to best-match
  order; drafted a `fix-is-in-this-repo` required check (pending a unit-1 harness regression run).

---

## Understanding the Issue

**Problem Description**

[In your own words, what is broken or missing?]

**Expected Behavior**

[What should happen?]

**Current Behavior**

[What actually happens?]

**Affected Components**

[Which parts of the codebase are involved?]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Reproduction Process

**Environment Setup**

[What a stranger would need to stand this up: your operating system,
the relevant versions (language, runtime, package manager, the project
itself), and how you set the project up. Then the challenges you faced
and how you solved them, or that none came up.]

**Steps to Reproduce**

1. [Step 1]
2. [Step 2]
3. [Observed result]

**Reproduction Evidence**

- **Commit showing reproduction:** [link to commit in your fork]
- **Screenshots/logs:** [if applicable]
- **My findings:** [what you discovered during reproduction; an
  honest, evidenced cannot-reproduce that led you to re-pick or ask
  the maintainer belongs here and costs you nothing]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Solution Approach

**Analysis**

[Your analysis of the root cause: what is causing the issue, traced
to the files and functions involved.]

**Proposed Solution**

[High-level description of your fix approach, and what you are
deliberately NOT changing.]

**Implementation Plan**

1. [Modify file X to do Y]
2. [Add or change Z]
3. [Update tests]

[Record deviations here as they happen: what changed mid-build and
why. A recorded deviation is honest work; silent drift is not.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Testing Strategy

[What observable checks prove the fix: your reproduction steps re-run
against the built change (before and after), and the repo's own test
suite or checks with the outcome shown. "Tests pass" with nothing
observable named is not a strategy.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Implementation Notes

[A running log, added to as you work. Date each entry. What you
built, decisions you made when you made them, wrong turns included.]

**[Date]**

[Entry]

**[Date]**

[Entry]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Pull Request

**PR Link:** [GitHub PR URL when opened]

**PR Description:** [what the PR says; much of the content above
adapts into it]

**Maintainer Feedback:**

- [Date]: [feedback received, or "none yet"; silence is a normal
  state and costs you nothing]
- [Date]: [how you addressed it]

**Status:** [Awaiting review / Iterating / Merged / Closed. The
status is a fact you record, never a grade: the PR is graded as
opened.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Learnings and Reflections

**Technical Skills Gained**

[What you learned technically, named concretely.]

**Challenges Overcome**

[What was hard and how you solved it; point at the real events, the
Toolkit blocks above are full of them.]

**What I'd Do Differently Next Time**

[Reflection on your process.]

---

## Resources Used

[What actually helped, not a reading list. List things this story
already shows you using: the doc you followed to set the environment
up, the thread that explained the bug, the person who answered you.
One real entry beats ten plausible ones.]

- [Link to documentation that helped]
- [Discussion, issue, or post that helped]
