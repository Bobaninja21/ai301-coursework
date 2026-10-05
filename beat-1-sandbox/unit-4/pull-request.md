# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

Record of the pull request you opened against the Path Review repo, and of the evaluation
runs that produced `eval-run.txt`. This file is graded at the path above; a copy kept
anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s3/pull/96

**Branch**

fix/61-health-probe-text

(on my fork: https://github.com/Bobaninja21/pathreview-ai301-fa26-s3/tree/fix/61-health-probe-text, against `codepath/pathreview-ai301-fa26-s3` main; it fixes #61, the issue my plan belongs to)

**My pr-precheck verdict on the draft (live mode, before opening)**

`accept`. All five required checks passed (`diff-matches-plan`, `description-faithful`, `evidence-decisive`, `diff-is-clean`, `standards-met`), and so did both preferred checks. The run did more than grade, though. Before that live run, running the repo's own checks turned up a real defect: the Unit 3 test pointed `DATABASE_URL` at `sqlite+aiosqlite`, but `aiosqlite` is not a Path Review dependency, so CI's `test-unit` job would have failed to import it. The test also had no `@pytest.mark.unit`, so `make test-unit` deselected it. I fixed both in `fb0a439` and recorded the change as Deviation 3 in `beat-1-sandbox/unit-3/plan.md` before re-running the tool. The live run's voice-guide findings (a SQLite-versus-Postgres difference stated too late, and "unchanged" for a repro script that had gained a docstring) and its fix suggestions (`make lint` versus CI's black step, an uncaptured main-side test count) all went into the description before it was posted. Re-checking one of my own claims also caught a mistake: I had written that the `api.routes.health` mypy override has no `call-overload` error. With the dev stubs installed it does, on the Redis call, which is #62's code, so the description now says that.

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. **No score: the harness crashed (Oct 4).** The first full run died in `subprocess` with `UnicodeDecodeError: 'charmap' codec can't decode byte 0x90`. On Windows, `text=True` decodes the CLI's output as cp1252, the harness then got `proc.stdout` as `None` (`TypeError: expected string or bytes-like object, got 'NoneType'`), and no run file was written. I re-ran with `PYTHONUTF8=1`. The harness and all four components were unchanged.
2. **20/20 (confirming full run, Oct 5)**: `agreement: 20/20 scored items  (bar: 18/20: PASS)`, `categories: clear-accept 7/7  not-tested 4/4  silent-drift 4/4  standards-wall 2/2  unreviewable 3/3`. This is the run in `eval-run.txt` next to this file.

**Package analysis**

`pkg-20` (ghostty-org/ghostty#13604, category `standards-wall`).

My rubric decided **reject**. The gold label is **reject**.

Four of the five required checks passed, and the run's evidence lines show why this is the hard one. The fix is good: `diff-matches-plan` passed ("Hunk 1 adds the `key != .theme` guard in `changeConditionalState`; hunk 2 updates the existing test and adds a second negative-case test"), `evidence-decisive` passed (before `ESC[?997;2n`, after `ESC[?997;1n`, and "`zig build test` passes" named), and `diff-is-clean` and `description-faithful` passed too. Every check about the change says ship it. The only failure is `standards-met`, with the evidence line: "AI policy is disclose-all ('All AI usage in any form must be disclosed, stating the tool used and the extent'); PR description contains no disclosure statement."

My rubric read it that way because of two pieces of wording. First, the third rule above the table: "Treat every package as AI-assisted work... Silence is non-compliance, not an absence of obligation." The description never mentions AI at all, so a grader without that rule could reason "nothing says AI was used, so nothing needed disclosing" and pass it. The rule closes that door. Second, the procedure's read order records the policy in one of four named shapes (silent, permissive-without-disclosure-ask, own-words, disclose-all) before the diff is opened. So "disclose-all" is a recorded fact by the time `standards-met` runs, not something the grader has to infer late after reading an excellent diff. That ordering is what keeps the strength of the fix from bleeding into the standards check.

**Check rationale**

The check I want to account for is `standards-met`, as it now reads:

| `standards-met` | The Repo facts block's `pull requests` line (template sections, checklist items, required issue link, required files such as CHANGELOG or whatsnew entries) and `contribution policy` line (AI-use policy), read against the PR title, description, and diff file list. Live mode: the repo's `PULL_REQUEST_TEMPLATE.md`, `CONTRIBUTING.md`, any AI policy file, and `scope.md`'s house rules. | **Template:** passes when no template or required asks are stated, or when every section or checklist item the template names has real content (a brief "n/a: reason" counts), the issue link the template asks for is present (`closes #N`, `fixes #N`), and every file the template or guide requires for this kind of change (a CHANGELOG or whatsnew entry for a bug fix) appears in the diff. **Fails** when a required checklist or named section is visibly absent, a required issue link is missing, or a required entry is absent from the diff. Asks about testing are graded by `evidence-decisive`, not here. **Disclosure:** fails when the policy requires AI use to be disclosed (in any form, or on PRs) and the description contains no disclosure statement; passes when the policy is silent on AI, permissive without a disclosure ask, or when the description discloses. An own-words policy passes when the description reads as one person's writing and, where the policy asks, says so. | required |

It reads that way because the eval set's two `standards-wall` packages fail in two different ways, and the category floor means missing either one costs the whole category. `pkg-01` (pandas) is a template wall: no `closes #66657`, and no `doc/source/whatsnew` entry even though the repo requires one for bug fixes. `pkg-20` (ghostty) is a disclosure wall. So the check has two labelled halves, **Template** and **Disclosure**, each with its own fail condition. Three phrasings are deliberate. "Appears in the diff" (not "is mentioned") is what fails `pkg-01`: its description never claims a whatsnew entry, so only the diff's file list shows the absence. "Asks about testing are graded by `evidence-decisive`, not here" stops double grading: pandas' checklist also says "tests added and passed", and without that sentence a strict grader would split on whether a test-suite run satisfies a checklist the description never reproduces. And "a brief 'n/a: reason' counts" protects terse clear accepts like `pkg-08` ("no UserConfig changes") from failing on form. I rejected a softer wording, "the PR follows the repo's contribution guidelines", because "follows the guidelines" is a judgment two graders split on, while "the required entry is in the diff's file list" and "the description contains a disclosure statement" are facts a grader can point at.

**Trade-offs**

What the quoted check gives up:
- **The own-words accepted miss.** An own-words AI policy passes "when the description reads as one person's writing", so a heavily assisted description that reads human passes. Voice carries no gold labels (eval mode ignores `voice-guide.md`), so the check grades the only thing it can observe.
- **A checklist answered in prose passes.** Template compliance is graded on content, not form: a description that answers every checklist item in sentences passes even if it drops the template's literal headings. The cost is that a maintainer who wanted the exact checkbox format would bounce what my tool accepts.
- **Nothing flipped elsewhere, and how I know.** This run is the first scored run, and all 20 agree, so no package changed result between runs. The scope sentence about testing asks was written in advance to keep `pkg-04`, `pkg-10`, and `pkg-14` failing for the right reason (`evidence-decisive`) rather than on standards.
- **The canary list if this check is ever loosened or tightened:** `--only pkg-01,pkg-20,pkg-11,pkg-08,pkg-13`. That is the two walls it must keep failing (`pkg-01` template, `pkg-20` disclosure), the own-words policy it must keep passing (`pkg-11`), a checklist accept with "n/a"-style answers (`pkg-08`), and a template-sections accept with no `closes #` form required (`pkg-13`). That covers each policy and template shape the change could touch, about $1.25, before spending a confirming full run.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/pr-precheck/`.
