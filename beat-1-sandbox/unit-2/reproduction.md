# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

Record of your claim and reproduction on the issue you chose in Unit 1, and of the
evaluation runs that produced `eval-run.txt`. This file is graded at the path above; a copy
kept anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your identity upstream

**GitHub username**

Bobaninja21

---

## Posted upstream

**Claim comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/61#issuecomment-5810624799

> I'd like to investigate this one as a first contribution.
>
> I've reproduced it at 2f4e82f. await db.execute("SELECT 1") in api/routes/health.py raises ArgumentError: Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1') on SQLAlchemy 2.0.54, and because the probe sits inside a broad except Exception, the endpoint answers 503 with "postgres": "unhealthy" while the database itself is reachable. Full report in a follow-up comment on this issue.
>
> Two things I want to read before I propose anything: whether the same raw-string pattern appears at any other execute() call site in the repo, and whether the probe's except Exception should be catching an argument error at all, given it is a code defect rather than a dependency being down. I'll report back what I find.
>
> One note so it doesn't get mixed into this issue: the same /health response also reports "redis": "unhealthy", from AttributeError: 'Settings' object has no attribute 'redis_host'. That is #62, a separate defect in the same file, and I'm leaving it alone here.

**Reproduction comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/61#issuecomment-5810627150

> Reproduced. Details below so anyone can re-run it.
>
> **Environment**
>
> - PathReview at commit 2f4e82f, git status --porcelain --untracked-files=no empty (no changes to tracked files; the repro script below is untracked).
> - Python 3.14.7 in a fresh venv, SQLAlchemy 2.0.54, aiosqlite 0.22.1, FastAPI 0.141.1, structlog 26.1.0.
> - Windows 11 (10.0.26200).
>
> **Deviation from the issue's steps, stated up front:** I don't have Docker on this machine, so I could not run GET /health against the Compose stack on Postgres. Instead I drove the real route handler with an AsyncSession bound to sqlite+aiosqlite. Run B below is the control that shows why the substitution still answers the question: the error is raised during SQLAlchemy's statement coercion, before a connection or a dialect is involved, and the same session executes the identical SQL successfully once it is wrapped in text(). It is still a substitution, so an end-to-end GET /health against Postgres from someone with the stack up would be worth having.
>
> **Steps**
>
> 1. python -m venv .venv at the repo root, then install the subset the route needs: sqlalchemy>=2.0.0 aiosqlite greenlet fastapi structlog pydantic-settings "pydantic[email]" redis httpx.
> 2. Save this as repro_61.py at the repo root:
>
> ```python
> import asyncio
> import os
>
> os.environ.setdefault("DATABASE_URL", "sqlite+aiosqlite:///./repro61.db")
>
> import sqlalchemy
> from fastapi import HTTPException
> from sqlalchemy import text
>
> from api.routes.health import health_check
> from core.database import AsyncSessionLocal
>
>
> async def main() -> None:
>     print(f"sqlalchemy {sqlalchemy.__version__}")
>     print(f"database_url {os.environ['DATABASE_URL']}")
>
>     async with AsyncSessionLocal() as session:
>         print("\n--- A. the probe exactly as api/routes/health.py runs it ---")
>         try:
>             await session.execute("SELECT 1")
>             print("no exception raised")
>         except Exception as exc:
>             print(f"{type(exc).__module__}.{type(exc).__name__}: {exc}")
>
>         print("\n--- B. control: the same statement wrapped in text() ---")
>         result = await session.execute(text("SELECT 1"))
>         print(f"returned {result.scalar()!r}, no exception")
>
>     print("\n--- C. the GET /health handler itself ---")
>     async with AsyncSessionLocal() as session:
>         try:
>             body = await health_check(db=session)
>             print(f"200 {body}")
>         except HTTPException as exc:
>             print(f"{exc.status_code} status={exc.detail['status']}")
>             print(f"    dependencies={exc.detail['dependencies']}")
>
>
> asyncio.run(main())
> ```
>
> 3. python repro_61.py from the repo root.
>
> **Observed**
>
> ```
> sqlalchemy 2.0.54
> database_url sqlite+aiosqlite:///./repro61.db
>
> --- A. the probe exactly as api/routes/health.py runs it ---
> sqlalchemy.exc.ArgumentError: Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')
>
> --- B. control: the same statement wrapped in text() ---
> returned 1, no exception
>
> --- C. the GET /health handler itself ---
> 503 status=unhealthy
>     dependencies={'postgres': 'unhealthy', 'redis': 'unhealthy', 'vector_db': 'healthy'}
> ```
>
> (plus the structlog lines: postgres_health_check_failed with the same ArgumentError, redis_health_check_failed with the settings AttributeError, vector_db_health_check_passed.)
>
> **Expected:** the probe issues SELECT 1, the database answers, and /health returns 200 with "postgres": "healthy".
>
> **Actual:** run A raises the exact ArgumentError this issue names. Run B is the control: the same session, the same SQL, wrapped in text(), returns 1 — so the database is reachable and the statement is valid, and what fails is the raw string at api/routes/health.py:31. Run C shows the consequence at the endpoint: the handler's except Exception swallows the ArgumentError, logs postgres_health_check_failed, marks postgres unhealthy, and the endpoint answers 503.
>
> Two things I am *not* claiming. The "redis": "unhealthy" line in run C is a different defect (AttributeError on settings.redis_host, #62) and nothing here is evidence about it. And because I ran on SQLite rather than Postgres, I have shown that the probe fails before it reaches a database, not that it fails against a live Postgres specifically — though run B makes it hard to see how the backend could matter.
>
> I used an AI assistant to help organise this report; I ran every step above myself and the output is from my machine.

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. **16/17 (partial, Sep 24)** — 20 packages attempted; 3 errored on stdin delivery (`pkg-04`, `pkg-07`, `pkg-11`: "no stdin data received", harness-side flakes, not verdicts). Among the 17 graded, 16 agreed with gold; the sole disagreement was `pkg-05` (see Package analysis). Recorded in the harness `--out results.json`, not committable — partial runs refuse `--save-run`.
2. **19/20 (confirming full run, Sep 26)** — `agreement: 19/20 scored items (bar: 18/20: PASS)`,
`categories: clear-accept 7/8 disclosure 1/1 no-evidence 4/4 unfollowable-comms 3/3
wrong-target 4/4`. Sole disagreement `pkg-12` (see Package analysis). This is the run in
`eval-run.txt` next to this file.

**Package analysis**

`pkg-12` (prettier/prettier#19795, category `clear-accept`).

My rubric decides **reject**. The gold label is **accept**.

The failure is exactly one required check: `steps-rerunnable` (the run's note also names
`control-run-shown`, but that check is preferred and never changes the verdict).
Everything else passes — the environment names version and platform with the version
delta stated outright ("filed against 3.8.4; both shapes still reproduce on 3.9.6", so
`deviations-disclosed` passes by naming), both artifacts show the issue's exact symptoms
(`};);` unparseable for shape A, `};;` for shape B), the outcome claims nothing beyond
them, the claim names both shapes, the release, and the concrete next trace (the fix
that closed #12964), and the conditional AI policy passes without comment disclosure.
But the steps say "ran the issue's script verbatim in an empty directory: `$ node
repro.mjs` with `repro.mjs` containing the issue's two `prettier.format` calls" — and the
frozen issue section contains no such script. It describes the inputs (exact strings,
exact rangeStart/rangeEnd, `parser: "babel"`) but supplies no runnable file, so
`repro.mjs` is referenced but not obtainable: not pasted inline, not quoted from the
issue (it isn't there), not produced by a given command, not a named public source. Under
the input-obtainability rule that fails, and one required fail gives `reject`.

The gold evidently reads the fully-specified inputs as sufficient — a stranger could
write those two `prettier.format` calls in a minute — and it has a point. This is the
same strictness line as the earlier run's `pkg-05` miss (a prose-described `env.yml`),
and notably the two flipped between runs: `pkg-05` rejected-then-accepted,
`pkg-12` accepted-then-rejected. Both sit exactly on the line, and I am holding the line
rather than chasing each flip. The strictness is what protects `pkg-18` (an explicitly
unshared private config) and `pkg-06` (a trigger step the issue names, `--driver
vmware`, simply missing): "obtainable from the page" is a fact a grader can check,
"reconstructible by a competent stranger" is a judgment it will blur. At 19/20 with
every category floor met — including `disclosure 1/1` — the miss is affordable. If I ever
loosen this check, the canary re-run is `--only pkg-12,pkg-05,pkg-18,pkg-06` before any
confirming full run.

**Check rationale**

The check I want to account for is `repo-conventions-met`, currently written as:

> | `repo-conventions-met` | The `contribution policy` line and the `bug reports` template line in the Repo facts block, read against what the claim comment and the repro report actually contain. Live mode: `CONTRIBUTING.md`, any linked AI policy file, and the issue template. | Read the policy for requirements that land on a **comment**, and check the comments against those and only those. **Fails** when the policy requires AI assistance to be disclosed and neither comment discloses it. Treat the package as AI-assisted work: these comments are drafted with an assistant, so a disclosure requirement is live even when the text never mentions a tool, and silence is non-compliance rather than an absence of obligation. **Passes** when the policy is silent on AI; when it is permissive or conditional without asking for disclosure on comments (be responsible for your contribution, understand and test every change, low-quality AI content is closed, disclosure asked only in the pull request); when it requires comments to be in the contributor's own words and the comments read as one person's own writing rather than generated boilerplate; or when the policy does ask for disclosure and a comment states it, naming the tool and the extent of the help. A template's asks (version, OS, steps, expected, actual) are graded here only as the repo's stated wants, and only when a comment omits something the template names outright; the proof checks above already own whether the content is sufficient. | required |

It reads that way for three reasons. First, the eval set contains exactly one disclosure-wall
package (`pkg-20`), and the category floor has teeth: a rubric with no conventions check
cannot buy that miss back on volume, so the check exists to see the one surface none of
the proof checks can see. Second, Unit 1 taught what the failure looks like from the inside:
my `contributions-allowed` check had an example that trained the grader to infer a carve-out
a ban never granted, so here the operative test is stated as a rule about obligation
("treat the package as AI-assisted; silence is non-compliance") with the four live policy
shapes (silent, permissive-with-responsibility, own-words, disclose-all) enumerated as the
pass/fail map, and the evidence guide's Comms section carries the same four shapes so the
grader never has to improvise one. Third, the scoping sentence ("requirements that land on
a **comment**, and only those") is deliberate restraint: without it the check would
double-grade proof content through the template's asks, or fail comments over a disclosure
the policy asks for only in pull requests — both of which would flip clear accepts
(`pkg-05`'s permissive conda policy, `pkg-03`'s own-words ripgrep rule) that the proof
checks already pass.

**Trade-offs**

What the quoted check gives up: it deliberately passes packages whose policy asks for
disclosure only in the pull request, even when neither comment discloses anything — the
"disclosure asked only in the pull request" carve-out in the pass condition. That is a
case I accept it will miss in one direction (a comment that is AI-assisted to the bone
but compliant on paper), in exchange for never failing a comment over an obligation that
does not land on comments. The symmetric accepted miss is on own-words policies: the test
is whether the comment "reads as one person's own writing rather than generated
boilerplate", so a heavily assisted comment that reads human passes; voice carries no gold
labels by design (eval mode ignores `voice-guide.md`), so the check grades the only thing
it can observe. If this check is ever loosened or tightened, the canary re-run is
`--only pkg-20,pkg-05,pkg-03`: `pkg-20` (the disclose-all reject it must keep failing),
`pkg-05` (the permissive-with-responsibility accept it must keep passing), and `pkg-03`
(the own-words accept it must keep passing) — one package from each policy shape the
change could touch, before spending on a confirming full run.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/repro-check/`.
