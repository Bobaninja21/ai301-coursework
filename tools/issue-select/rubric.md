# Rubric: is this a good first issue?

Six required checks, two preferred. The required six are ordered by how
cheaply they can kill a contribution: a dead repo wastes the most time, a
vague spec the least. Grade them all anyway — the note column in the eval
output is only useful when every check reports.

Every recency threshold below is measured against the **capture date** of
the bundle in eval mode (the `captured:` line at the top), and against
**today** in live mode.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| `repo-alive` | The `archived:` flag on the repo line, and the newest date in the `last 5 default-branch commits` list, both under Repo facts. Live mode: the archive banner and the newest commit date on the repo front page. | `archived: no` AND the newest default-branch commit is dated within 180 days of the capture date. Releases are NOT required: a repo that has never published a release still passes when its commits are recent, and a stale release date never fails a repo whose commits are fresh. Bot-authored commits count when they are merging a human pull request. | required |
| `contributions-allowed` | The `contribution policy` line under Repo facts. Live mode: `CONTRIBUTING.md`, `.github/CONTRIBUTING.md`, any AI policy file they link, and the PR template. | The test is whether a compliant path exists for an AI-assisted contributor, not how disapproving the wording sounds. **Fails** when the policy prohibits AI-generated code or documentation and nothing in it permits AI-assisted work — "We do not accept AI-generated code or documentation", standing alone, is a ban and fails. **Passes** when the policy is silent; when it sets conditions a contributor can meet (disclose AI use, understand and test every change, human review required, no unreviewed machine translation); when it merely discourages; or when it rejects only *fully* AI-generated output while explicitly allowing assistive use in the same policy ("fully AI-generated contributions are not accepted; assistive AI use is allowed"). A carve-out only counts when the policy states it — do not infer one from a ban that is silent about assisted use. | required |
| `unclaimed` | The `this issue: assignees: ... ; linked PRs: ...` line under Repo facts, plus every comment in the Comments section with its author and date. | Passes unless at least one of these holds: (a) `assignees:` names anyone; (b) any linked PR is in state `open`, including one in a fork; (c) a comment dated within 90 days of the capture date states intent to work on it ("I'll take this", "working on this", "can I work on this", `/assign`, `@bot claim`) and no later maintainer comment releases it; (d) a maintainer comment reserves the issue for a named person. A claim comment older than 90 days is stale and does not block, and a `closed` or `merged` linked PR is not a claim. | required |
| `not-a-tracking-issue` | The issue title and body. | Passes unless the issue is a container rather than a change: it calls itself an umbrella, tracking, mega, or meta issue; its body's work items are a list of OTHER issue or PR numbers; or it asks for an open-ended, incremental programme of work across the codebase ("keep adding X wherever it makes sense", "PRs welcome both big and small"). Several files, several sub-tasks, or a checklist INSIDE one deliverable is not a tracking issue and passes. | required |
| `no-repeat-failures` | The `linked PRs:` states on the `this issue:` line under Repo facts, plus any pull requests named in the comment thread. | Passes unless 2 or more linked PRs are in state `closed` (closed without merging). Two or more people have already tried and failed here; the issue is harder than its label claims. One closed attempt passes. `merged` PRs never count against it. | required |
| `change-is-specified` | The issue body, the opener's `author_association`, and the comment thread. | Passes when the body names the concrete thing to change — a file, function, page, command, UI element, or a described wrong behaviour — or when a maintainer (`OWNER`, `MEMBER`, `COLLABORATOR`) opened the issue or endorsed a specific approach in the thread. Fails only when a key part of the wanted end state is explicitly left open (`TBD`, "possibly", "asset to be decided", an unanswered product question) AND no maintainer has commented to settle it. A terse body is not a failure: grade the size and clarity of the work asked for, not the polish of the write-up. Pure support questions ("how do I get this to work?") fail. | required |
| `newcomer-signposted` | The issue's `labels:` list and the body. | Passes when the issue carries a `good first issue`, `help wanted`, `easy`, or `documentation` label, or the body gives a starting point: file paths, acceptance criteria, a suggested fix, or a step-by-step walkthrough. | preferred |
| `maintainer-responsive` | The `maintainer first-response sample` block under Repo facts. | Passes when at least one sampled issue shows a first owner, member, or collaborator response within 30 days. | preferred |

## Verdict rule

**`accept` if and only if every `required` check grades `pass`.** Any
required `fail` gives `reject`. There is no third verdict.

`unclear` counts as `fail` on a required check. Note that five of the six
required checks are written as *rebuttable passes* — they pass unless a
named disqualifying fact is present in the evidence — so absence of
evidence is a `pass`, not an `unclear`. Reserve `unclear` for a check
whose evidence line is genuinely missing from the bundle.

`preferred` checks never change the verdict. Report their grades; on an
accepted issue they are the reasons to prefer it over the other issues
the rubric accepted, and they order the ranked read-out in live mode.
