---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

Answer one question about one PR package: **is this pull request ready
to submit?** A PR package is a candidate pull request (its title,
description, commit list, unified diff, and test evidence) read
against the plan it claims to implement (scope, files, test plan,
deviation notes) and the issue that plan belongs to (the report, the
thread, and the repo's stated PR template and contribution policy).

Do not answer any other question. Do not review code style, suggest a
better fix, re-diagnose the bug, or grade the plan itself; the plan
is the yardstick, not the subject. Grade exactly one package per run.

## Inputs and modes

Run in exactly one of two modes. Decide which before reading anything.

**Live mode** (the student's own PR, before it is opened). Gather:

- **The plan:** `plan.md` with its Deviations section, from the
  student's course repo (`beat-1-sandbox/unit-3/plan.md`). A
  house-chain student reads the house plan and the house repro pack
  instead; the same checks grade them the same way.
- **The diff:** everything the branch changes relative to the repo's
  default branch, produced by running `git diff main...HEAD` (three
  dots) from the working copy on the fix branch. Also run
  `git log main..HEAD --oneline` for the commit list.
- **The draft PR title and description:** the text the student will
  paste into the pull request, as a file or in the message.
- **The test evidence:** the captured output of the repro re-run
  before (on `main`) and after (on the branch), and the output of the
  repo's own checks.
- **The issue side:** the issue thread, `.github/PULL_REQUEST_TEMPLATE.md`,
  `CONTRIBUTING.md`, and any AI policy file, read from the real repo.

If any of these is missing, say which one and grade the checks that
read it `unclear`.

**Eval mode** (a package bundle). The bundle is the whole world: every
fact comes from the bundle text. Fetch nothing, open no other file,
and do not look up the live issue. Ignore `scope.md` and
`voice-guide.md` entirely. Always grade the complete package: every
check, full verdict rule.

## The scope seam (live mode only)

In live mode, read `scope.md` before anything else. If its Repo line
still holds a bracketed placeholder (`<ORG>/<PATH-REVIEW-REPO>` or
similar), stop without grading and tell the student to get the
cohort's scope file (the Path Review repo link) from the instructor;
never guess a scope. If the PR targets any repo other than the one the
scope names, refuse to grade it and say why. Otherwise, carry the
scope's house rules into grading: they join the repo's own template
asks as stated standards under `standards-met` (PR from the student's
fork on a `fix/<issue-number>-<slug>` branch, one PR per issue, every
template section filled). In eval mode, ignore `scope.md` entirely.

## The voice seam (live mode only)

In live mode, after grading, read `voice-guide.md` and hold the draft
PR title and description against each of its rules. Report every rule
the draft breaks, quoting the offending text and naming the rule, in
the readable summary before the JSON block. The voice guide never
changes the verdict on its own: no rubric check reads it, so voice
findings are advice to the author, not grades. In eval mode, ignore
`voice-guide.md` entirely.

## Component reads

Read and use the components together:

- `rubric.md` defines the checks and the verdict rule. Grade exactly
  the checks it lists, by the pass conditions it states, and nothing
  else. Do not invent checks at run time.
- `references/evidence-guide.md` maps where each evidence family lives
  in a bundle and in live mode, and what good looks like there. Use it
  to locate the evidence each check names.
- `procedure.md` gives the operating steps (read order, evidence
  gathering, check execution, verdict assembly). Execute it as
  written, in order.

If the procedure is silent on a step you need, say so in the summary
("procedure gap: ...") and grade with the rubric's words alone; never
improvise a new step around the gap.

Refusal rule: if `rubric.md` has no checks or no verdict rule, or
`procedure.md` has no steps (only template headings and comments),
refuse to grade. Say which component is empty and that the tool will
not invent checks. Emit no verdict.

## Verdict and output

The verdict is binary: `accept` means the PR is ready to submit;
`reject` means hold it and fix what the deciding check names. There is
no third verdict and no score; reservations go in check evidence
lines.

Write a short readable per-check summary first, deciding check first
on a reject. Then end the reply with this fenced JSON block, valid and
last, with nothing after it. Use the bundle id (eval) or the PR URL or
branch (live) as `item`, and list every check in rubric order:

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

## Grading discipline

- **Evidence first.** Grade no check without naming the fact or quote
  that decided it, with where it sits. "Looks fine" is not evidence.
- **Grade the thing, not the polish.** Read the diff against the plan,
  the evidence against the test plan, and the description against the
  diff. Never reward length, headings, or confidence; a terse complete
  PR can be ready and a beautiful one can be hiding drift.
- **The rubric decides, not the run.** If a check passes by its stated
  condition but feels wrong, it passes; report the gap as a rubric
  note instead of overriding it.
- **The procedure decides how, not the run.** Follow `procedure.md`
  as written and report its gaps instead of inventing steps.
- **Unclear defaults to fail.** The rubric's verdict rule treats
  `unclear` as `fail` on a required check. Where any rule is silent,
  treat an unverifiable claim as a failing one: a PR you cannot verify
  from the package is not ready to submit.
