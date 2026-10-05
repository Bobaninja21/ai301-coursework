# Rubric: is this plan ready to post and build from?

Seven required checks, two preferred. The required seven follow the
failure families: the diagnosis follows the evidence, the change acts
on the cause, the change is one bounded change, a stranger could start
it, the test plan proves something observable, the plan is honest
about what it does not know, and the comment respects the thread and
the repo's stated rules.

Two rules apply to every check:

1. **Grade the thing, not the shape.** No check reads length, headings,
   section count, or confident tone. A terse plan that names its cause,
   its file, its bound, and its observable test is ready; a long
   polished one that fixes the wrong thing is not.
2. **Deferral is not a defect.** Naming work the plan will *not* do
   (a bigger rework, a variant it cannot test, a sibling bug filed
   separately) is honest scoping and never fails a check. What fails is
   work the plan *will* do that the issue does not need.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| `cause-grounded` | The plan's stated cause (its Diagnosis line or equivalent) read against the Repro evidence block: every step's output and, above all, every **control run** (the same trigger with one variable changed) and any `--debug`/trace/intermediate-state step. | Passes when the stated cause is consistent with every shown artifact: each control that came back clean is explained by the cause (the cause is absent in that run), and no step shows the failure already present *before* the component the plan blames runs. **Fails** when any control or intermediate step rules the blamed component out: the failure persists with the blamed component removed or bypassed, the control shows the blamed component working in the same build, or the evidence shows the damage done upstream of where the plan says it happens. Also fails when the plan dismisses a variable the evidence isolates ("a red herring", "a side effect") without showing why. Adopting the thread's or the issue's diagnosis does not pass by itself; it passes only if the repro's controls agree with it. | required |
| `targets-the-cause` | The plan's In-scope change and Approach steps, read against the cause the repro evidence isolates (the variable the controls flip) and against any culprit location a maintainer named in the thread highlights. | Passes when the planned change acts on the mechanism the evidence isolates: the code path, constant, call site, or handler that produces the failure. **Fails** when the plan leaves that mechanism unchanged and only works around the symptom: documenting a workaround, telling users to change their invocation, catching and hiding the error, or adding a setting that lets users dodge it, while the code that misbehaves stays as is. A plan whose evidence pins a code defect and whose scope says "not in scope: any change to the code" fails here. | required |
| `one-bounded-change` | The plan's In-scope / Not-in-scope statements, its Files list, and every numbered Approach or "Proposed changes" step, each read against what the issue and repro evidence actually need fixed. | Passes when every item the plan commits to building is needed to fix the reported behavior or to test that fix (the fix, its sibling occurrences of the *same* defect in the same function or file, regression tests). **Fails** when the plan commits to building anything the issue does not need, however reasonable: a dependency migration or upgrade, a rewrite or redesign of the surrounding module, a new user-facing option or setting, a refactor or restructure, a new framework (retry, abstraction layer, state machine), CI-matrix work, or a fix for a *different* symptom "while in the area". One such item is enough to fail, even when the core fix inside the plan is correct. Deferred, separate-issue, or "not in scope" mentions never fail; auditing sibling sites for the same defect and *noting* findings never fails. | required |
| `stranger-can-start` | The plan's Files / areas list and its Approach, read as if by someone who has never seen the thread. | Passes when the plan names (a) where the change lands, at least to the file or the named component/handler, and (b) the chosen mechanism of the change (clamp, separator, refresh call, wrap in X, counter, drain), so a stranger could open that file and begin. Pinning the exact function or line during implementation is allowed when the area and mechanism are chosen and the uncertainty is stated. **Fails** when the plan defers a real decision to build time: no file or component named ("somewhere", "the input stack", "profile and see"), no chosen approach ("gocui? tcell? not sure", "upstream or vendored, whichever is easier", "optimize what's slow"), or open-ended additions ("maybe also check other X"). | required |
| `test-is-decisive` | The plan's Test plan, read against the Repro evidence's steps, commands, and expected/actual lines. | Passes when the test plan names at least one **observable outcome that the fix flips**: re-running the repro (or its named command/script/steps) with a stated expected result (exit code, printed output, status, rendered behavior, N-of-N runs succeeding), or a named regression test asserting that outcome. **Fails** when the only success signal is one that would pass without the fix or that no one can observe: "the full test suite passes", "nothing else feels broken", "should feel fast", "works better", "CI is green", or a test plan that checks only the docs build or the new code's own unrelated behavior. | required |
| `honest-unknowns` | The plan's Diagnosis, Risk/unknowns lines, and the plan comment's certainty language, held against what the repro evidence shows. | Passes when the plan's confidence is no greater than the evidence: what it asserts as fact, the repro shows; what it has not verified, it either states as an unknown or does not claim. **Fails** when the plan or comment presents as confirmed a cause, a scope, or an outcome the package does not show ("I dug into this and the root cause is…" with no artifact behind it, "this fixes all platforms" with one platform tested, "guaranteed"). A plan with no Risk section passes if it overclaims nothing; stated unknowns are a strength, never a fail. A promise of a PR or report-back ("will send the PR shortly") is not overclaiming. | required |
| `thread-and-policy-respected` | The plan comment and the plan, read against (1) the Thread highlights, specifically comments by OWNER / MEMBER / COLLABORATOR / CONTRIBUTOR (maintainers) and any open PR named there, and (2) the `contribution policy` line in the Repo facts block. Live mode: the live issue thread and `CONTRIBUTING.md` / any linked AI policy file. | **Thread:** passes when no maintainer gave explicit direction, or when the plan follows that direction or explicitly engages it (names it and says why it diverges or defers). Explicit direction is: a maintainer naming the culprit location, posting a patch or test build, proposing an approach, or rejecting an approach. **Fails** when such direction exists and the plan/comment neither follows nor mentions it (e.g. a docs-only plan when the owner isolated the code culprit and posted a patched build), or when the plan adopts an approach a maintainer already rejected. **Policy:** treat every package as AI-assisted work. **Fails** when the policy requires disclosure of AI use in any form (or on issues/comments specifically) and the plan comment contains no disclosure statement naming the tool or the extent of help. **Passes** when the policy is silent on AI, permissive with responsibility, asks for disclosure only in the pull request, or requires comments in the contributor's own words and the comment reads as one person's own writing; a comment that discloses passes in every case. | required |
| `risks-named` | The plan's Risk / unknowns / open-question lines. | Passes when the plan names at least one concrete risk or open question about its own change (a case it could break, a site it has not verified) and how it will handle it. Never gates. | preferred |
| `comment-matches-plan` | The plan comment read against the plan's scope and approach. | Passes when the comment states the same change the plan commits to (same mechanism, same bound) so a maintainer reading only the thread knows what will be built. Never gates. | preferred |

## Verdict rule

**`accept` if and only if every `required` check grades `pass`.** Any
required `fail` gives `reject`. There is no third verdict.

`unclear` counts as `fail` on a required check: a plan I cannot verify
from the package is a plan that is not ready to build from. Reserve
`unclear` for when the evidence line a check names is genuinely missing
from the package (for example, no plan comment at all). `honest-unknowns`
and `thread-and-policy-respected` are rebuttable passes: they pass unless
the named disqualifying fact is present, so a silent policy or a thread
with no maintainer direction is a `pass`, never `unclear`.

`preferred` checks never change the verdict; report them so the author
sees what would make an accepted plan stronger.
