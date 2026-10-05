# Rubric: is this pull request ready to submit?

Five required checks, two preferred. The required five follow the
failure families, one or two per family: the diff matches the plan
(silent drift), the description claims exactly what the diff does
(silent drift), the evidence proves the fix on the failing path
(not tested), the diff carries nothing but the change (unreviewable),
and the repo's stated asks are met (standards wall).

Three rules apply to every check:

1. **Grade the thing, not the shape.** No check reads length, heading
   count, commit count, or confident tone. A terse PR whose diff
   matches the plan, whose evidence flips the repro, and whose stated
   asks are met is ready; a polished one hiding an extra hunk is not.
2. **A disclosed shortfall is not a defect.** Work the plan or a
   recorded deviation note defers (a variant left for later, a path
   kept as-is after maintainer discussion, a mitigation instead of the
   full fix), restated plainly in the description, never fails a
   check. What fails is a gap or an extra nobody wrote down.
3. **Treat every package as AI-assisted work.** Course PRs are drafted
   with an assistant by design, so a repo's disclosure requirement is
   live even when the PR text never mentions a tool. Silence is
   non-compliance, not an absence of obligation.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| `diff-matches-plan` | The diff's changed files and every hunk, read against the plan's In-scope change, its Files list, its Not-in-scope lines, and any recorded deviation note (Plan context block; live mode: `plan.md` and its Deviations section, against `git diff main...HEAD`). | Passes when (a) the diff implements the plan's stated mechanism (the change the plan says it will make, in the place it names) and (b) every hunk is that change, its regression test, or an item the plan or a recorded deviation note names. **Fails** when any hunk adds work the plan never mentions or explicitly scopes out: a new option, flag, setting, or config key; a rename or refactor pass; a rewrite of an adjacent function; a dependency bump; a fix for a different symptom. One such hunk is enough, even when the core fix is correct and even when the extra work is adjacent or reasonable. Also **fails** when the diff omits an item the plan commits to (a fix site, a docs change, a second failure mode) with no deviation note and no restatement in the description, or when the diff never implements the plan's mechanism at all. Deferred items the plan or a deviation note records never fail. | required |
| `description-faithful` | Every factual claim in the PR title and description about what the change does, which files or docs it touches, and how far it reaches ("implements the plan exactly", "no other changes", "CHANGELOG entry added", "docs now document X", "tests cover Y"), read against the diff's file list and hunks. | Passes when every claim the description makes about the change is visible in the diff, and nothing the diff changes in behavior is contradicted by the description. **Fails** when the description claims a file, entry, doc, or test the diff does not contain (claiming more than it delivers), or asserts fidelity ("exactly as planned", "no functional changes outside X", "nothing beyond the plan") over a diff that changes more than the plan. A description that honestly states a limitation, deferral, or deviation passes. | required |
| `evidence-decisive` | The PR's Test evidence section and any before/after in the description, read against the plan's Test plan and the repro evidence it built on (the failing command, script, or steps and their expected-after). Live mode: the captured test output against `plan.md`'s Test plan. | Passes when **all three** hold: (1) the plan's repro (the issue's failing trigger: the same command, input, or script, not a control and not a different request) is re-run with the **before** result and the **after** result both visible somewhere in the PR (output, transcript, table, or a stated observed result per side); (2) every failure mode or repro step the plan's Test plan names is shown, not just one of them; (3) the repo's own test suite or check command is named with its outcome stated ("`cargo test -p x` passes (214 tests)", "`pytest tests/ -q` passes"). **Fails** when the only evidence is a bare claim ("tested locally", "works on my machine", "verified for a day", "all tests pass" with nothing named), when the evidence exercises only a control or the unchanged path (single-file case when the bug needs two files, a GET when the bug is a POST), when one of the plan's named repros is silently skipped, or when no repo check is named at all. An honest report of a failing or unrunnable check with a stated reason passes condition (3). | required |
| `diff-is-clean` | Every hunk of the unified diff, and the commit list. | Passes when every changed line serves the fix or its test. **Fails** on any debris: a debug print or log line left in (`eprintln!("DEBUG`, `console.log`, `print(` used for tracing); a commented-out code line or block (including a commented-out first attempt or debug statement); a dead function or variable (`_unused_*`, `#[allow(dead_code)]` helpers nothing calls); a stray TODO/FIXME added by the PR; a hunk that only re-indents, reformats, re-orders, or re-prints lines without changing them; an import-restructure hunk the fix does not need. One instance fails. Comments that explain the new code are not debris. | required |
| `standards-met` | The Repo facts block's `pull requests` line (template sections, checklist items, required issue link, required files such as CHANGELOG or whatsnew entries) and `contribution policy` line (AI-use policy), read against the PR title, description, and diff file list. Live mode: the repo's `PULL_REQUEST_TEMPLATE.md`, `CONTRIBUTING.md`, any AI policy file, and `scope.md`'s house rules. | **Template:** passes when no template or required asks are stated, or when every section or checklist item the template names has real content (a brief "n/a: reason" counts), the issue link the template asks for is present (`closes #N`, `fixes #N`), and every file the template or guide requires for this kind of change (a CHANGELOG or whatsnew entry for a bug fix) appears in the diff. **Fails** when a required checklist or named section is visibly absent, a required issue link is missing, or a required entry is absent from the diff. Asks about testing are graded by `evidence-decisive`, not here. **Disclosure:** fails when the policy requires AI use to be disclosed (in any form, or on PRs) and the description contains no disclosure statement; passes when the policy is silent on AI, permissive without a disclosure ask, or when the description discloses. An own-words policy passes when the description reads as one person's writing and, where the policy asks, says so. | required |
| `title-states-change` | The PR title. | Passes when the title names the behavior changed (what now works or what was fixed), so a maintainer knows the PR in one line. Never gates. | preferred |
| `commits-describe-work` | The commit list. | Passes when each commit message says what that commit does ("wip", "fix", "misc cleanups" fail). Never gates; debris inside the commits is `diff-is-clean`'s job. | preferred |

## Verdict rule

**`accept` if and only if every `required` check grades `pass`.** Any
required `fail` gives `reject`. There is no third verdict.

`unclear` counts as `fail` on a required check: a PR I cannot verify
from the package is a PR that is not ready to submit. Reserve
`unclear` for when the evidence a check names is genuinely missing
from the package (no diff, no plan context, no test evidence section).
`standards-met` is a rebuttable pass: a repo with no template, no
stated required asks, and no AI policy passes it, never `unclear`.

`preferred` checks never change the verdict; report them so the author
sees what would make an accepted PR stronger.
