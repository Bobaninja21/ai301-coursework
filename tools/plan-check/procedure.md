# Procedure: how this skill grades a plan package

## Read order

1. **Repro evidence first.** Read the `Repro evidence` block (live: the
   student's posted repro comment) before the plan. Write down: the
   trigger, the failing artifact, every control run and what it showed,
   any intermediate/debug step, and the one variable the controls
   isolate. Reading the evidence before the plan keeps the plan's
   confident wording from setting the frame for `cause-grounded`.
2. **Issue, then thread highlights.** Note the reported symptom, and for
   each thread line by OWNER / MEMBER / COLLABORATOR / CONTRIBUTOR,
   record any explicit direction (culprit location, patch or test
   build, proposed approach, rejected approach) and any open PR named.
3. **Repo facts.** Record the `contribution policy` line's AI rule as
   one of: silent, permissive, PR-only disclosure, own-words, or
   disclose-all.
4. **Candidate plan.** Note the stated cause, every item it commits to
   build, files/areas, mechanism, test plan, and risks/unknowns.
5. **Candidate plan comment** last, read the way a maintainer on the
   thread would.

## Evidence gathering

For each check, pull exactly these facts into notes before grading:

- `cause-grounded`: the plan's cause sentence, and for each control /
  intermediate step, one line saying whether it is consistent with that
  cause (is the blamed component present in the failing run and absent
  or bypassed in the passing control?).
- `targets-the-cause`: the mechanism the controls isolate, and the code
  location (if any) a maintainer named; then the plan's in-scope change.
  Record whether the change modifies that mechanism or only documents /
  hides / works around it.
- `one-bounded-change`: list every item the plan commits to build
  (numbered steps, "proposed changes", files). Mark each as fix,
  same-defect sibling, test, or extra. Ignore items under "not in
  scope" / "deferred" / "separate issue".
- `stranger-can-start`: the named file or component, and the chosen
  mechanism, or the words that leave either open.
- `test-is-decisive`: the test plan's success signals, each marked as
  flipped-by-the-fix (observable) or not.
- `honest-unknowns`: certainty phrases in plan and comment, each paired
  with the artifact that supports it, or "none".
- `thread-and-policy-respected`: the maintainer-direction notes from
  read step 2 paired with whether the plan/comment follows or names
  each; the policy shape from step 3 paired with whether the comment
  contains a disclosure sentence.
- Preferred: any Risk/open-question line; a one-line comparison of the
  comment's stated change to the plan's.

In eval mode the bundle is the only source; never fetch anything. In
live mode, gather per the evidence guide's live locations.

## Check execution

Run the required checks in rubric order, then the preferred ones. Grade
each check only from the notes gathered for it, against its pass
condition, and quote the deciding fact in the check's `evidence` line.
Each check is independent: do not fail a check because another failed,
and do not pass a check because the plan is good overall. When the
evidence a check names is genuinely absent (for example, no plan
comment), grade `unclear`; but where the rubric says a check is a
rebuttable pass (no maintainer direction, silent policy, no
overclaiming), absence of the disqualifying fact is a `pass`. A check
may be graded from notes without re-reading the package, except
`cause-grounded`, where any doubt means re-reading the controls once
before grading.

## Verdict assembly

Apply the rubric's verdict rule: `accept` only if all seven required
checks are `pass`; any required `fail` or `unclear` gives `reject`.
Preferred grades are reported but never change the verdict. In the
readable summary, name the first required check that failed (in rubric
order) and quote its deciding evidence; on an accept, say so in one
line. Then emit the JSON block exactly as SKILL.md specifies, listing
every check, as the last thing in the output.
