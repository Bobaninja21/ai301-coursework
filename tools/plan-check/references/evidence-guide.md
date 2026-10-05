# Evidence guide: where evidence lives in a plan package

## Diagnosis and grounding

- Where it lives: eval mode, the candidate plan's `Diagnosis` / `Cause`
  line (or its first paragraph when unlabeled), read against the
  `Repro evidence` block: its numbered steps, every line labeled
  `Control` or `Control run`, any `--debug` / trace / intermediate-state
  step, and the closing `Expected:` / `Actual:` pair. Live mode: the
  student's `plan.md` diagnosis against their own posted repro comment
  on the issue (or the house repro pack quoted in the drafts).
- What good looks like: the stated cause explains why every failing step
  fails *and* why every control passes. Controls are the decisive
  evidence: if a control removes the component the plan blames and the
  failure persists, or shows that component working in the same build,
  or an intermediate step shows the damage already done before that
  component runs, the diagnosis is contradicted no matter how
  confidently it is written or whether the thread agrees with it.

## Scope

- Where it lives: the plan's `Scope` / `In scope` / `Not in scope` lines,
  its `Files` list, and every numbered `Approach` / `Proposed changes`
  item; the plan comment's summary of what will be built.
- What good looks like: every item the plan will build is the fix, a
  sibling occurrence of the same defect, or a test of the fix. Deferred
  work listed as not in scope is good scoping. A drive-by looks like:
  "while touching X", "replace library A with B", "add a setting",
  "rewrite as a state machine", "restructure the module", "port to Y",
  "migrate the test harness", "CI matrix", or a second, different
  symptom fixed in the same plan.

## Executability

- Where it lives: the plan's `Files` / `Files and areas` list and its
  `Approach` / `Change` steps.
- What good looks like: a named file or named component/handler plus a
  chosen mechanism (clamp, separator, refresh, wrap, counter, drain),
  so a stranger could open the file and start. "Exact function pinned
  during implementation" is fine when the area and mechanism are
  chosen. Not good: no files, a menu of options left open ("A? B? not
  sure", "whichever is easier"), "profile and optimize", "somewhere",
  "maybe also check other X".

## Test plan

- Where it lives: the plan's `Test plan` / `Test` line, read against
  the repro's commands and its `Expected:` line.
- What good looks like: re-run the repro (named command, script, or
  steps) with a stated observable result the fix flips: exit code,
  printed output, HTTP status, rendered behavior, N-of-N successes; or a
  named regression test asserting it. Vague: "full suite passes",
  "CI green", "nothing feels broken", "should feel fast", docs build
  renders.

## Honesty

- Where it lives: the plan's `Risk` / `unknowns` / `open question` lines,
  its diagnosis wording, and the certainty language in the plan comment;
  live mode also the plan's `Deviations` section.
- What good looks like: facts asserted are facts the repro shows;
  unverified parts are named as unknowns ("I have not yet verified which
  layer clamps", "not measured yet"). False confidence: "the root cause
  is X" with no artifact showing X, "fixes all platforms" from one
  platform, dismissing an isolated variable as "a red herring". An
  honest mid-build deviation is recorded in `plan.md`'s Deviations
  section and, if the posted intent changed, in a thread update.

## Comms

- Where it lives: eval mode, the `Thread highlights` list (each line
  carries the author's role: OWNER, MEMBER, COLLABORATOR, CONTRIBUTOR
  are maintainers; NONE is a user) and the `contribution policy` line
  of the `Repo facts` block, read against the `Candidate plan comment`.
  Live mode: the live issue thread, `CONTRIBUTING.md`, any linked
  `AI_POLICY.md`, and the draft plan comment.
- What good looks like: when a maintainer named a culprit, posted a
  patch or test build, proposed an approach, or rejected one, the
  comment follows it or names it and says why it differs; when another
  contributor has an open PR, the comment engages it rather than racing
  it. Policy shapes: silent on AI, or permissive with responsibility,
  or disclosure asked only in the PR, need nothing in the comment;
  "comments in your own words" needs a comment that reads as one
  person's writing; "disclose all AI usage" (any form, including issues
  and comments) needs a disclosure sentence in the comment naming the
  tool and extent. Every package is treated as AI-assisted, so silence
  under a disclose-all policy is non-compliance.
