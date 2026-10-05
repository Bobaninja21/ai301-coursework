# Procedure: how this tool grades a PR package

Four stages, always in this order. Do not grade any check until the
read order and the evidence gathering are complete: every required
check is a side-by-side, and a side-by-side graded from memory of one
side is the failure this tool exists to catch.

## Read order

Read the parts in this order, and write down the listed notes from
each before moving on. In eval mode the parts are sections of the
bundle; in live mode they are the files and pages named in brackets.

1. **Repo facts** (live: `PULL_REQUEST_TEMPLATE.md`, `CONTRIBUTING.md`,
   any AI policy file, `scope.md`'s house rules). Note: every template
   section or checklist item by name; any required issue-link form
   (`closes #`, `fixes #`); any file the repo requires for a bug fix
   (CHANGELOG, whatsnew); the AI-use policy in one of four shapes:
   silent, permissive-without-disclosure-ask, own-words, or
   disclose-all.
2. **Issue and thread highlights** (live: the issue page). Note the
   failing trigger the issue reports and any maintainer direction.
3. **Plan context** (live: `plan.md`, Deviations section included).
   Note, as separate lists: (a) the mechanism the plan commits to and
   where (file, function); (b) every item In scope, including the
   Files list and promised docs or tests; (c) every item Not in scope;
   (d) every deviation or deferral note; (e) every repro step or
   failure mode the Test plan names, each with its expected-after.
4. **Diff** (live: `git diff main...HEAD` from the working copy). Note
   the changed-file list, then one line per hunk saying what it does.
5. **Commits.** Note each message.
6. **Description and title** (live: the draft PR text). Note every
   factual claim about scope or contents as a separate line, quoted.
7. **Test evidence** (live: the captured test output). Note, per
   repro step from 3(e): is a before shown, is an after shown, is it
   the same trigger; then note any repo check named and its outcome.

The plan is read before the diff so the diff is measured against the
plan, not the plan reconstructed from the diff. The description is
read after the diff so its claims are tested against what the diff
already showed, not believed first.

## Evidence gathering

Build these five pairings from the notes. Each pairing feeds exactly
one required check; record each pairing in a line or two before
grading.

- **For `diff-matches-plan`:** put the hunk list (4) beside the plan
  lists (3a-3d). Mark each hunk `in-plan` (the mechanism, its test, a
  named item), `deviation-noted`, or `unplanned`. Then walk the
  In-scope list (3b) and mark each item `present` or `missing`; a
  missing item counts only if no deviation note or description
  restatement covers it. Record any `unplanned` hunk or uncovered
  `missing` item by file and what it does.
- **For `description-faithful`:** put each quoted claim (6) beside the
  file list and hunk lines (4). Mark each claim `shown` or `not in
  diff`. A fidelity claim ("exactly", "no other changes") is `not in
  diff` whenever the first pairing found an `unplanned` hunk.
- **For `evidence-decisive`:** put the Test plan's repro steps (3e)
  beside the test-evidence notes (7). For each step record: same
  trigger as the issue (yes/no), before visible (yes/no), after
  visible (yes/no). Then record the repo check named, verbatim, with
  its outcome, or "none named".
- **For `diff-is-clean`:** scan every added and removed line for the
  debris tells in the rubric (debug print, commented-out code, dead
  function, added TODO, re-indent or re-print hunk, unneeded import
  restructure). Record each by file and quoted line. A hunk whose
  removed and added lines are textually identical apart from
  whitespace or order is a re-print.
- **For `standards-met`:** put the repo-facts notes (1) beside the
  description, title, and file list. Mark each template section or
  checklist item `filled` or `absent`, the issue link `present` or
  `absent`, each required file `in diff` or `absent`, and the
  disclosure `present`, `absent`, or `not required` per the policy
  shape.

## Check execution

Run the required checks in rubric order (`diff-matches-plan`,
`description-faithful`, `evidence-decisive`, `diff-is-clean`,
`standards-met`), then the preferred checks. For each check:

1. Read only that check's pairing from the gathering stage, then apply
   the rubric's pass condition as written. Re-open the package only to
   quote the deciding line.
2. Grade `pass` or `fail` by the stated condition, even if the PR
   feels better or worse than the condition says. A check that passes
   by its words but feels wrong still passes; report the gap.
3. Grade `unclear` only when the part the check reads is absent from
   the package (no diff, no plan, no test evidence section). Absence
   of a disqualifying fact is a `pass` for rebuttable checks, not
   `unclear`.
4. Write one evidence line: the fact or quote that decided it, with
   its location (hunk file, description sentence, evidence block).

Grade every check every time, even after a required check has failed:
eval mode always grades the complete package.

## Verdict assembly

1. Apply the rubric's verdict rule: `accept` only if every required
   check is `pass`; any required `fail` or `unclear` gives `reject`.
   Preferred checks never move the verdict.
2. The deciding check is the first required check, in rubric order,
   that did not pass. Put its evidence line first in the readable
   summary. On an `accept`, the summary names no deciding check and
   states which preferred checks failed, if any.
3. Write a short per-check summary, then end the reply with the fenced
   JSON block in the contract's schema: every check (required and
   preferred) in rubric order, each with `name`, `grade`, and one-line
   `evidence`, then the verdict. Nothing follows the block.
4. Live mode only: after the summary and before the JSON block, list
   any voice-guide rule the draft title or description breaks. These
   never change the verdict.
