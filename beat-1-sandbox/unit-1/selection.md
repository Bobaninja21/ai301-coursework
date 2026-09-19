# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`.

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/61

**Verdict output**

Live-mode run of `issue-select` on three candidates from
`codepath/pathreview-ai301-fa26-s3`, 2026-09-19. Repo facts checked that day:
`archived: false`, newest default-branch commit 2026-09-16 by `Aburke225`,
`docs/CONTRIBUTING.md` states nothing about AI or contribution tooling.

```
Accepted, in fit order

1. #61 - Health check DB probe passes a raw SQL string. Fits best: a FastAPI
   route plus SQLAlchemy 2.x, the two things my fit profile names outright,
   with a stated reproduction (GET /health) and the fix shape already
   identified (sqlalchemy.text()).
2. #72 - verify_password raises UnknownHashError on malformed hashes. Backend
   Python with a stated fail-closed expectation and a named xfail test to
   un-mark; 1-2 hours, well inside the time I have.
3. #68 - Keyword search raises ZeroDivisionError when the index is empty. Good
   stack fit (retrieval, pytest) but the longest at 2-4 hours, and the one
   candidate a classmate has already announced on.

Rejected: none.
```

```json
[
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/61",
    "checks": [
      {"name": "repo-alive", "grade": "pass", "evidence": "archived: false; newest default-branch commit 2026-09-16 by Aburke225, 3 days before the 2026-09-19 check"},
      {"name": "contributions-allowed", "grade": "pass", "evidence": "docs/CONTRIBUTING.md: no statement on AI or contribution tooling; silence passes"},
      {"name": "unclaimed", "grade": "pass", "evidence": "assignees: none; Development box lists no linked branches or PRs; zero comments"},
      {"name": "not-a-tracking-issue", "grade": "pass", "evidence": "one defect in one file, api/routes/health.py; no list of other issue numbers"},
      {"name": "no-repeat-failures", "grade": "pass", "evidence": "no linked PRs at all, so zero closed-unmerged attempts"},
      {"name": "change-is-specified", "grade": "pass", "evidence": "names the file and the wrong behaviour: the probe executes the literal string 'SELECT 1', which SQLAlchemy 2.x rejects unless wrapped in sqlalchemy.text()"},
      {"name": "newcomer-signposted", "grade": "pass", "evidence": "labels include 'good first issue' and 'tier-1'; body gives the file path and the reproduction"},
      {"name": "maintainer-responsive", "grade": "unclear", "evidence": "no issue in the tracker carries a maintainer reply to sample; the repo was seeded 2026-09-10 and has one comment in total"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/72",
    "checks": [
      {"name": "repo-alive", "grade": "pass", "evidence": "archived: false; newest default-branch commit 2026-09-16 by Aburke225, 3 days before the 2026-09-19 check"},
      {"name": "contributions-allowed", "grade": "pass", "evidence": "docs/CONTRIBUTING.md: no statement on AI or contribution tooling; silence passes"},
      {"name": "unclaimed", "grade": "pass", "evidence": "assignees: none; no linked branches or pull requests; zero comments"},
      {"name": "not-a-tracking-issue", "grade": "pass", "evidence": "one defect in core/security.py with one covering test; no sub-issue list"},
      {"name": "no-repeat-failures", "grade": "pass", "evidence": "no linked PRs at all, so zero closed-unmerged attempts"},
      {"name": "change-is-specified", "grade": "pass", "evidence": "body names the file and the wanted end state: 'Verification against a malformed hash should fail closed (return False), not raise'"},
      {"name": "newcomer-signposted", "grade": "pass", "evidence": "labels include 'good first issue' and 'tier-1'; body names core/security.py, tests/unit/test_security.py and a 1-2 hour estimate"},
      {"name": "maintainer-responsive", "grade": "unclear", "evidence": "no issue in the tracker carries a maintainer reply to sample; the repo was seeded 2026-09-10 and has one comment in total"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/68",
    "checks": [
      {"name": "repo-alive", "grade": "pass", "evidence": "archived: false; newest default-branch commit 2026-09-16 by Aburke225, 3 days before the 2026-09-19 check"},
      {"name": "contributions-allowed", "grade": "pass", "evidence": "docs/CONTRIBUTING.md: no statement on AI or contribution tooling; silence passes"},
      {"name": "unclaimed", "grade": "pass", "evidence": "assignees: none, no linked PRs; the one claim comment (acordero4852, author_association NONE, 2026-09-19, 'I'd like to take this one on') is a classmate's, which the Path Review house rule in scope.md says does not block"},
      {"name": "not-a-tracking-issue", "grade": "pass", "evidence": "one defect in rag/retriever/keyword_search.py; no list of other issues"},
      {"name": "no-repeat-failures", "grade": "pass", "evidence": "no linked PRs at all, so zero closed-unmerged attempts"},
      {"name": "change-is-specified", "grade": "pass", "evidence": "body states index([]) raises ZeroDivisionError via BM25Okapi and that index() should match search(), which already returns an empty list"},
      {"name": "newcomer-signposted", "grade": "pass", "evidence": "labels include 'good first issue' and 'tier-1'; body names both the source file and its test file"},
      {"name": "maintainer-responsive", "grade": "unclear", "evidence": "no issue in the tracker carries a maintainer reply to sample; the repo was seeded 2026-09-10 and has one comment in total"}
    ],
    "verdict": "accept"
  }
]
```

---

## Eval iterations

**Run history**

Four runs, in order:

1. **6/6** — partial run, `--only issue-04,issue-06,issue-09,issue-14,issue-15,issue-20`.
   A canary on the six bundles I thought my rubric was most likely to get
   wrong, run before spending on a full pass. No bar is printed on a partial
   run.
2. **19/20** — first full run. One disagreement, `issue-12`, and the run
   failed on the category floor rather than on the total:
   `categories: claimed 4/4  clear-accept 8/8  dead-repo 3/3  policy 0/1  scope 4/4`.
   19/20 is over the 18 bar, but `policy 0/1` meant the whole policy category
   was invisible to my rubric, so the run did not pass.
3. **3/3** — partial run, `--only issue-01,issue-10,issue-12,calib-01
   --include-calibration`, after rewriting `contributions-allowed`.
   `issue-12` flipped to `reject`, and I re-ran `issue-01`, `issue-10` and
   `calib-01` alongside it as canaries to confirm the rewrite had not started
   failing repos whose policies are permissive or merely discouraging.
4. **20/20** — confirming full run, `--save-run eval-run.txt`.
   `categories: claimed 4/4  clear-accept 8/8  dead-repo 3/3  policy 1/1  scope 4/4`,
   `agreement: 20/20 scored items  (bar: 18/20: PASS)`. This is the run in
   `eval-run.txt` next to this file.

**Issue analysis**

`issue-12` (bookwyrm-social/bookwyrm#1133).

My rubric decided **accept**. The gold label is **reject**.

Every check I had was right about it, and that was the problem: the repo
pushed the day it was captured, nobody was assigned, no PR was linked, and
"Include in-progress books in the reading-goal progress-bar" is a bounded UI
change that a maintainer (`mouse-reeve`, MEMBER) had already advised on in the
thread. Four of the five criterion families pass honestly. The one thing that
disqualifies it sits in the repo-facts policy line:

> "Meaningful human interaction is the whole point of BookWyrm. We do not
> accept AI-generated code or documentation."

I did have a check pointed at that line — `contributions-allowed` — so this
was not a missing check. It was a badly worded one. The version that ran said
a policy passes if it sets conditions rather than a ban, and then offered as
an illustration: `"fully AI-generated contributions are not accepted" (which
still permits assisted use)`. That example is from p5.js, whose policy really
does allow assistive AI in the same breath. But its phrasing is close enough
to BookWyrm's that the grader read the two as the same kind of statement and
inferred a carve-out that BookWyrm never grants. My own example taught the
check to explain the ban away.

The fix was to stop describing the two categories and state the test instead:
does a compliant path exist for an AI-assisted contributor? A carve-out counts
only when the policy states one, and a prohibition that is silent about
assisted use is a ban. `issue-12` now rejects on
`contributions-allowed`, and the permissive cases I checked alongside it
(`issue-01`'s "generative AI tools welcome", `issue-10`'s discouragement of
unreviewed AI, `calib-01`'s explicit assistive-use allowance) all still pass.

**Check rationale**

The check I want to account for is `change-is-specified`, currently written as:

> | `change-is-specified` | The issue body, the opener's `author_association`,
> and the comment thread. | Passes when the body names the concrete thing to
> change — a file, function, page, command, UI element, or a described wrong
> behaviour — or when a maintainer (`OWNER`, `MEMBER`, `COLLABORATOR`) opened
> the issue or endorsed a specific approach in the thread. Fails only when a
> key part of the wanted end state is explicitly left open (`TBD`, "possibly",
> "asset to be decided", an unanswered product question) AND no maintainer has
> commented to settle it. A terse body is not a failure: grade the size and
> clarity of the work asked for, not the polish of the write-up. Pure support
> questions ("how do I get this to work?") fail. | required |

It is written that way because my first instinct — fail anything vague — was
the wrong shape. The evidence guide says outright that "Short is not the same
as unscoped", and the calibration set backs it up: `calib-01` opens "This is
not an exact bug report, but a request" and is still a perfectly good first
issue, because it names the function (`min`) and the behaviour it wants back.
So the condition is not "is this well written" but "is the wanted end state
settled". Two things make it settled: the body names something concrete, or a
maintainer has put their name behind an approach. The failure clause is
narrow on purpose — it needs an *explicitly* open decision (`TBD`, an
unanswered product question) *and* maintainer silence, both at once — so that
a terse maintainer-filed bug cannot trip it.

**Trade-offs**

It gives up the ability to reject an issue that is under-specified without
saying so. The concrete case it will miss: an issue whose body is confidently
written and names real files, but whose premise is wrong — the "fix" would be
rejected on review even though every word of the issue is specific. Nothing in
this check looks at whether the change is *correct*, only whether it is
*decided*, and I accepted that: judging correctness needs the codebase, which
the eval bundles do not contain.

It also deliberately declines to fail on age or comment volume. An issue open
for years with a long thread passes this check; `no-repeat-failures` is what
catches those, and only when two or more attempts have actually closed
unmerged. That split is why the check does not double-count history.

---

## Selection rationale

**Selection rationale**

*1. Fit to my interests and to the time available.* Issue #61 is a FastAPI
route talking to SQLAlchemy 2.x, which is the stack I am most comfortable in,
and the whole change is one call wrapped in `sqlalchemy.text()`. It is
labelled `tier-1`, and it has a reproduction I can run locally (`GET /health`
against the stack, or the route handler in a unit test) — which matters more
to me than size, because "I can verify the fix myself" was the thing I put in
my fit profile as non-negotiable. I have a few evenings, not a few weeks, and
this is comfortably inside that.

*2. What the verdict identified correctly, and what I weighed that the rubric
could not.* The rubric got the mechanical facts right and got them fast:
nobody is assigned, nothing is linked, the repo pushed three days ago, the
contributing docs say nothing that would rule out an AI-assisted workflow, and
the body names both the file and the wrong behaviour. What it could not weigh
is that all three candidates passed every required check, so the rubric had
nothing left to say — the verdict was a floor, not a ranking. Choosing between
three accepts was my call, made on the fit profile: #72 is equally clean but
sits in passlib rather than SQLAlchemy, and #68 is the biggest of the three at
2-4 hours. I also noticed something the rubric is blind to by design: the
`maintainer-responsive` check came back `unclear` on all three, because this
tracker is nine days old and has exactly one comment on it. In a real repo that
would worry me; here it is an artifact of the course, so I let it go.

*3. The anticipated difficulty in claiming it.* Low, and that is mostly the
house rule's doing. Path Review is a classroom, so a classmate's claim comment
does not block anything and credit attaches to the PR I open rather than to
whether it merges. #61 had no comments at all when I graded it, so I do not
even have that to navigate — though by the time I claim in Unit 2 someone else
may well have commented, and per `scope.md` I should claim anyway. The real
difficulty is not social, it is the reproduction: I need the stack running
with a live database session before I can watch the probe fail, and standing
that up is the part of Unit 2 I expect to cost me the most.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/issue-select/`.
