# Evidence guide: where evidence lives in a PR package

Four families, one per harness category. For each: where to look in
an eval bundle, where to look in live mode, and what good looks like
as a condition someone else can apply.

## Plan fidelity (harness category: silent-drift)

**Where it lives.** Eval: the Plan context block (the "Plan:" text
with its Scope / one bounded change, Not in scope, Files, and Test
plan clauses, plus any deviation or deferral note), read against the
Candidate PR's Diff (the `+++ b/` file headers and each `@@` hunk)
and the Description's claims. Live: `plan.md` (Scope, Files, Test
plan, Deviations sections) against `git diff main...HEAD` run from the
working copy, and the draft PR description.

**What good looks like.** Every changed file and every hunk falls
inside the plan's In-scope change, its named files, its regression
test, or a recorded deviation note; every item the plan commits to is
in the diff or explicitly deferred. The description's scope claims
are each visible in the diff.

**What drift looks like.** More than the plan: a new config option,
flag, or setting; a rename pass; a rewrite of a neighbouring function;
a file the plan never names. Less than the plan: a promised docs
change, fix site, or failure mode missing with no note. Either
direction counts double when the description asserts fidelity
("implements the plan exactly", "no functional changes outside X",
"docs now document the limitation", "CHANGELOG entry added") that the
file list contradicts. An honest deviation re-ties a mismatch: a
deferral stated in the plan's note and restated in the description is
not drift.

## Test evidence (harness category: not-tested)

**Where it lives.** Eval: the Candidate PR's Test evidence section
and any Before/After blocks in the Description, read against the Plan
context's Test plan clause and the "Repro evidence" paragraph (the
failing command and its expected-after). Live: the captured terminal
output for the repro re-run on `main` and on the branch, and the
output of the repo's own checks (for Path Review: `make test-unit` /
`pytest tests/unit`, `ruff check .`, `black --check .`, `mypy`, per
the PR template's Testing checklist and `.github/workflows/ci.yml`).

**What good looks like.** The issue's own failing trigger re-run with
the before result and the after result both shown, matching the
plan's stated expected-after; every repro or failure mode the Test
plan names appears; and at least one repo check named as a command
with its outcome ("`go test ./...` passes (4108 tests)"). An honest
failing-check report with a reason counts.

**What not-tested looks like.** "Tested locally", "works now",
"verified for a full day", "all tests pass" with no command; only the
after side shown with no before anywhere; a run of the control or the
unchanged path (single-file when the bug needs two files, GET when the
bug is a POST, a header-casing check when the bug is Content-Type); one
of two named repros silently skipped.

## Diff quality (harness category: unreviewable)

**Where it lives.** Eval: the Candidate PR's Diff (every `+` and `-`
line) and the Commits list. Live: `git diff main...HEAD` and
`git log main..HEAD --oneline`.

**What good looks like.** Every changed line is the fix, its test, or
a comment explaining the new code; the reviewer sees the change and
nothing else.

**Debris tells.** `eprintln!("DEBUG ...`, `print(`/`console.log` used
for tracing; a commented-out line of code (`// let x = ...`,
`# old_call()`), including a commented-out first attempt or debug
print; a function nothing calls, often behind `#[allow(dead_code)]`
or named `_unused_*`; an added `TODO`/`FIXME`; a hunk that re-indents
or re-prints lines identically; an import block restructured with no
new name needed. Commit messages like "wip", "fmt + cleanup", "misc
cleanups while debugging" point at where to look.

## Standards and comms (harness category: standards-wall)

**Where it lives.** Eval: the Repo facts block's `pull requests` line
(template sections, checklist, issue-link form, required CHANGELOG or
whatsnew entry) and `contribution policy` line (AI-use policy),
read against the PR Title, Description, and the Diff's file list.
Live: `.github/PULL_REQUEST_TEMPLATE.md`, `CONTRIBUTING.md`, any
`AI_POLICY.md`, and `scope.md`'s house rules (for Path Review: the
template is always used, every section gets real content, PR from
the `fix/<issue>-<slug>` branch on your fork).

**What compliant looks like.** Each template section the repo names
has real content under it ("n/a" with a reason counts); the issue
link is present in the form asked; a required entry file (CHANGELOG,
`whatsnew/vX.rst`) is in the diff, not just claimed; and when the
policy requires AI disclosure, the description says plainly that AI
was used, naming the tool and the extent. Policy shapes: silent (no
disclosure needed), permissive (no disclosure needed), own-words (the
text must read as one person's writing; disclose if asked),
disclose-all (a disclosure statement is required, and its absence
fails).

**What a wall looks like.** A required checklist missing entirely; no
`closes #N` where the template asks for it; a whatsnew or CHANGELOG
entry the repo requires absent from the diff; a disclose-all policy
with no disclosure in the description. Whether the description's
claims match the diff is plan fidelity, above, not this family.
