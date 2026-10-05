# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, the branch you built it on, and the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in the
repository is not read.

---

## Posted upstream

**GitHub username**

Bobaninja21

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/61#issuecomment-5989395146

> Plan for this one, built from my repro above (comment 5810627150, at 2f4e82f).
>
> What I read the problem to be: `api/routes/health.py:32` passes the bare string `"SELECT 1"` to `db.execute()`. SQLAlchemy 2.x raises `ArgumentError` on that during statement coercion, before anything reaches the database, and the probe's `except Exception` turns it into `"postgres": "unhealthy"`. In my run B, the same session executes `text("SELECT 1")` and gets `1` back, which is why I think the database side is fine and the call is what needs to change.
>
> What I intend to change, following the issue's own pointer to `sqlalchemy.text()`: import `text` from `sqlalchemy` and make the probe `await db.execute(text("SELECT 1"))`. I'll also add one regression test (`tests/unit/test_health_route.py`) that runs `health_check` against an aiosqlite session and asserts `postgres` comes back `healthy`. I grepped for other `execute("...")` call sites and this is the only one.
>
> What I'm leaving out: the Redis `settings.redis_host` error (#62), narrowing the broad `except Exception` (I'd rather raise that separately than fold it in here), and other cleanup in the file.
>
> How I'll know it worked: re-running `repro_61.py`, run C should show `'postgres': 'healthy'` where it showed `unhealthy`. The status will still be 503 until #62 is fixed, so I'm checking the postgres field, not the status code. The new test should fail on main and pass on the branch.
>
> Open question: I've only run this on SQLite, since I don't have Docker here. rueiliu's and Aniruthan-0709's repros above show the same `ArgumentError` on Postgres 16 with asyncpg, so I expect the fix to carry over, but I haven't seen it pass on Postgres myself. A check from someone with the Compose stack up would be welcome.
>
> Building on `fix/61-health-probe-text` in my fork. I used an AI assistant (Claude) to help organise this plan and draft the test; I read and ran everything myself.

---

## Your branch

**Branch**

fix/61-health-probe-text

(on my fork: https://github.com/Bobaninja21/pathreview-ai301-fa26-s3/tree/fix/61-health-probe-text)

**Evidence**

My unit-2 `repro_61.py`, re-run unchanged against the built change. Run A still raises,
because it calls `session.execute("SELECT 1")` directly to show SQLAlchemy's behaviour; it
does not go through the route. Run C is the real `health_check()` code path, and it flips from
`'postgres': 'unhealthy'` to `'postgres': 'healthy'`, with `postgres_health_check_passed`
in place of `postgres_health_check_failed`. The status stays `503` because of the separate
Redis defect (#62), which is out of scope, as the plan says. After that, the new regression test fails
on unfixed `main` and passes on the branch.

```
# BEFORE: main @ 2f4e82f (unfixed)
$ git switch main
$ python repro_61.py
sqlalchemy 2.0.54
database_url sqlite+aiosqlite:///./repro61.db
--- A. the probe exactly as api/routes/health.py runs it ---
sqlalchemy.exc.ArgumentError: Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')
--- B. control: the same statement wrapped in text() ---
2026-10-04 23:13:38,673 INFO sqlalchemy.engine.Engine BEGIN (implicit)
2026-10-04 23:13:38,673 INFO sqlalchemy.engine.Engine SELECT 1
2026-10-04 23:13:38,673 INFO sqlalchemy.engine.Engine [generated in 0.00019s] ()
returned 1, no exception
2026-10-04 23:13:38,675 INFO sqlalchemy.engine.Engine ROLLBACK
--- C. the GET /health handler itself ---
2026-10-04 23:13:38 [error    ] postgres_health_check_failed   error="Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')"
2026-10-04 23:13:38 [error    ] redis_health_check_failed      error="'Settings' object has no attribute 'redis_host'"
2026-10-04 23:13:38 [debug    ] vector_db_health_check_passed
503 status=unhealthy
    dependencies={'postgres': 'unhealthy', 'redis': 'unhealthy', 'vector_db': 'healthy'}

# AFTER: fix/61-health-probe-text @ ad7b784
$ git rev-parse --abbrev-ref HEAD
fix/61-health-probe-text
$ python repro_61.py
sqlalchemy 2.0.54
database_url sqlite+aiosqlite:///./repro61.db
--- A. the probe exactly as api/routes/health.py runs it ---
sqlalchemy.exc.ArgumentError: Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')
--- B. control: the same statement wrapped in text() ---
2026-10-04 23:14:14,116 INFO sqlalchemy.engine.Engine BEGIN (implicit)
2026-10-04 23:14:14,116 INFO sqlalchemy.engine.Engine SELECT 1
2026-10-04 23:14:14,116 INFO sqlalchemy.engine.Engine [generated in 0.00019s] ()
returned 1, no exception
2026-10-04 23:14:14,117 INFO sqlalchemy.engine.Engine ROLLBACK
--- C. the GET /health handler itself ---
2026-10-04 23:14:14,119 INFO sqlalchemy.engine.Engine BEGIN (implicit)
2026-10-04 23:14:14,119 INFO sqlalchemy.engine.Engine SELECT 1
2026-10-04 23:14:14,119 INFO sqlalchemy.engine.Engine [cached since 0.003106s ago] ()
2026-10-04 23:14:14 [debug    ] postgres_health_check_passed
2026-10-04 23:14:14 [error    ] redis_health_check_failed      error="'Settings' object has no attribute 'redis_host'"
2026-10-04 23:14:14 [debug    ] vector_db_health_check_passed
503 status=unhealthy
    dependencies={'postgres': 'healthy', 'redis': 'unhealthy', 'vector_db': 'healthy'}
2026-10-04 23:14:14,197 INFO sqlalchemy.engine.Engine ROLLBACK
$ python -m pytest tests/unit/test_health_route.py -q
  C:\Users\pieta\Documents\GitHub\pathreview-ai301-fa26-s3\api\routes\health.py:28: DeprecationWarning: datetime.datetime.utcnow() is deprecated and scheduled for removal in a future version. Use timezone-aware objects to represent datetimes in UTC: datetime.datetime.now(datetime.UTC).
    "timestamp": datetime.utcnow().isoformat(),
-- Docs: https://docs.pytest.org/en/stable/how-to/capture-warnings.html
1 passed, 2 warnings in 0.65s

# Regression test: control on unfixed main, then on the branch
$ git switch main   # unfixed, 2f4e82f
$ python -m pytest tests/unit/test_health_route.py -q
E       AssertionError: assert 'unhealthy' == 'healthy'
E         
E         - healthy
E         + unhealthy
E         ? ++
2026-10-04 23:15:33 [error    ] postgres_health_check_failed   error="Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')"
$ git switch fix/61-health-probe-text
$ python -m pytest tests/unit/test_health_route.py -q
1 passed, 2 warnings in 0.64s
```

## Eval iterations

**Run history**

1. **No score: the harness crashed before grading (Oct 4).** The first full run failed with
   `FileNotFoundError: [WinError 2]`, because on Windows `subprocess` could not resolve
   `claude` (only the `claude.cmd` npm shim was on PATH). No package was graded. I fixed it by
   putting `~/.local/bin` (where `claude.exe` lives) on PATH. The harness and my components were
   unchanged.
2. **19/20 (confirming full run, Oct 4)**: `agreement: 19/20 scored items  (bar: 18/20: PASS)`,
   `categories: clear-accept 7/7  scope-creep 4/4  thread-convention 2/2  unbuildable 3/3
   wrong-cause 3/4`. The only disagreement was `pkg-11` (see Package analysis). This is the run in
   `eval-run.txt` next to this file.

**Package analysis**

`pkg-11` (mikefarah/yq#2782, category `wrong-cause`).

My rubric decided **accept**. The gold label is **reject**.

Every required check passed. The run's note column names only `risks-named`, which is
preferred and never changes the verdict. The check that should have caught it is
`cause-grounded`. The plan's diagnosis says "The collect operator is the defect: `[...]`
fails to synthesize a null entry for a missing key, producing an empty array instead of
`[null]`." The package's own control says the opposite: the first command,
`yq -n '{} | ([.a] | length)'`, prints `1`, and the control line states the top-level collect
"produces `[null]` (length 1) exactly as documented; only the select/and/or contexts drop the
missing key." So collect works in the same build, and the defect lives in how the
condition contexts (select/and/or) evaluate their operand. That is exactly what the plan puts out
of scope as "just consumers".

My grader read it the other way. Its `cause-grounded` evidence line was: "Top-level control
shows collect producing [null] (length 1); failing runs inside select/and show it dropping the
key — consistent with the plan's cause that collect's null-synthesis is context-sensitive."
The plan never says "context-sensitive". The grader inferred that from the Approach line
("regardless of where the collect appears") and then credited the plan with it. So the
rubric already names this failure ("the control shows the blamed component working in the
same build"), but my procedure let the executor paraphrase the plan's cause into something
the control agrees with, instead of testing the cause sentence as written. The fix I would
make is in `procedure.md`'s evidence-gathering step for `cause-grounded`: quote the plan's
cause sentence verbatim and test that sentence, not a reconstruction of it, against each
control. With the deadline that night, I didn't spend another full run on it. At 19/20, with
every category floor met, the miss is affordable, and it's the right one to write about: it
names a gap between what my rubric says and how my procedure lets it be executed.

**Check rationale**

The check I want to account for is `cause-grounded`, as it now reads:

| `cause-grounded` | The plan's stated cause (its Diagnosis line or equivalent) read against the Repro evidence block: every step's output and, above all, every **control run** (the same trigger with one variable changed) and any `--debug`/trace/intermediate-state step. | Passes when the stated cause is consistent with every shown artifact: each control that came back clean is explained by the cause (the cause is absent in that run), and no step shows the failure already present *before* the component the plan blames runs. **Fails** when any control or intermediate step rules the blamed component out: the failure persists with the blamed component removed or bypassed, the control shows the blamed component working in the same build, or the evidence shows the damage done upstream of where the plan says it happens. Also fails when the plan dismisses a variable the evidence isolates ("a red herring", "a side effect") without showing why. Adopting the thread's or the issue's diagnosis does not pass by itself; it passes only if the repro's controls agree with it. | required |

It reads that way because the wrong-cause category in this set is built around one move.
The plan blames a component, and the package's own control shows that component working
(pkg-01's no-`-v` run, pkg-07's instance-method friendly error, pkg-11's top-level collect),
or an intermediate step shows the damage was already done upstream (pkg-16's step 4,
where pyarrow's table has lost the zeros before any cast runs). So the pass condition is written
around controls, not around how convincing the diagnosis sounds. Two sentences are there
deliberately. "Adopting the thread's or the issue's diagnosis does not pass by itself"
covers the calib-03 trap from the activity, where a polished plan adopted the thread's confident
key-binding theory that the timing matrix ruled out. "Dismisses a variable the evidence
isolates ('a red herring')" covers pkg-01, whose plan calls the Python-version difference a red
herring. I rejected a softer "the cause is plausible given the evidence" wording. Plausibility
is a judgment two graders split on, while "a control shows the blamed component working"
is a fact a grader can point at.

**Trade-offs**

What the quoted check gives up: it makes controls decisive, so it can only see as far as the
package's controls reach. A wrong diagnosis with no control run in the evidence has nothing to
contradict it, and `cause-grounded` passes it. I accept that miss. The alternative is
failing plans for not proving a negative, and that would flip clear accepts whose repro has no
meaningful control. That same reliance on controls is also where it failed in this run:
`pkg-11` has the contradicting control right there, and the grader still passed it, because
the check depends on the executor reading the plan's cause literally. If I tighten the
procedure as described above, the canary re-run before any confirming full run is
`--only pkg-11,pkg-01,pkg-16,pkg-02,pkg-13`. That is the miss itself, two wrong-cause packages that
must stay rejected, and two clear accepts (`pkg-02`, `pkg-13`) whose diagnoses lean on controls
and must keep passing, so a stricter reading of the cause sentence doesn't start failing
grounded plans.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
