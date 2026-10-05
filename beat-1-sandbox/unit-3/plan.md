# Plan: codepath/pathreview-ai301-fa26-s3#61

`/health` reports Postgres unhealthy while the database is up.

## What I read the problem to be

`api/routes/health.py:32` runs the Postgres probe as
`await db.execute("SELECT 1")`, passing a bare string. SQLAlchemy 2.x
refuses a bare string in `execute()` and raises `ArgumentError: Textual SQL
expression 'SELECT 1' should be explicitly declared as text('SELECT 1')`
before the statement reaches any database. The probe sits inside
`except Exception`, so that error is caught, logged as
`postgres_health_check_failed`, and the dependency is marked `unhealthy`.

Grounding, from my posted repro (issue comment 5810627150, commit 2f4e82f,
SQLAlchemy 2.0.54):

- Run A, the probe exactly as the route runs it, raises the `ArgumentError`.
- Run B, the control: the same session and the same SQL wrapped in `text()`,
  returns `1`. So the database is reachable and the statement itself is
  valid. What fails is how the string gets passed.
- Run C, the real handler, answers `503` with `"postgres": "unhealthy"`.

## Scope: one bounded change

In scope: the Postgres probe's statement in `api/routes/health.py`. I'll
import `text` from `sqlalchemy` and change the call to
`await db.execute(text("SELECT 1"))`. I'll also add one regression test.

Not in scope, deliberately:

- The Redis check's `AttributeError` on `settings.redis_host`. That is #62,
  a separate defect in the same file.
- Narrowing the probe's broad `except Exception`. I raised that as an open
  question in my claim comment, and it's worth its own discussion, but this
  bug doesn't need it.
- `datetime.utcnow()` deprecation warnings and any other cleanup in the file.

I searched for the same pattern elsewhere: `grep -rn 'execute("' --include=*.py`
finds only `api/routes/health.py:32` (plus my untracked repro script), so
there are no other call sites to fix.

## Files

- `api/routes/health.py`: one import, one changed line.
- `tests/unit/test_health_route.py` (new): runs `health_check` against an
  aiosqlite `AsyncSession` and asserts `dependencies["postgres"] == "healthy"`.

## Test plan

1. Re-run my unit-2 `repro_61.py` on the branch. Run C must show
   `'postgres': 'healthy'` and a `postgres_health_check_passed` log line
   where it showed `unhealthy` before. The overall status will still be
   `503` because of the Redis defect (#62), which this change doesn't touch.
   So the observable to check is the postgres field, not the status code.
2. The new regression test must **fail** on unfixed `main` (asserting
   `'unhealthy' == 'healthy'`) and **pass** on the branch.

## Risks and unknowns

- I ran on SQLite through aiosqlite, not on Postgres, because I don't have
  Docker. The error is raised during SQLAlchemy's statement coercion, before
  any dialect is involved, so the fix shouldn't depend on the backend.
  Classmates' repros on the thread (rueiliu, Aniruthan-0709) show the same
  `ArgumentError` on Postgres 16 with asyncpg. Still, I haven't run `GET /health` against the Compose stack on Postgres,
  and I'll say so in the PR.
- I won't claim the endpoint returns `200` after this change. It won't
  until #62 is fixed.

## Branch

`fix/61-health-probe-text` on my fork (Bobaninja21/pathreview-ai301-fa26-s3).

## Deviations

The fix itself held as planned: one import and one changed line in
`api/routes/health.py`, nothing else in that file.

The regression test took two adjustments I hadn't planned for:

1. Importing `api.routes.health` imports `core.database`, which builds its
   engine from `DATABASE_URL` at import time. With no URL set, it tried the
   default Postgres driver and failed with `ModuleNotFoundError: No module
   named 'asyncpg'`. The test now sets `DATABASE_URL` to SQLite before the
   import.
2. My first choice, in-memory SQLite (`sqlite+aiosqlite:///:memory:`),
   failed with `TypeError: Invalid argument(s) 'pool_size','max_overflow'`.
   `core.database` passes pool arguments that the in-memory StaticPool
   rejects. The test now uses a file-backed SQLite database in the temp dir.

Both adjustments stay inside the new test file. I didn't change
`core.database` to make the test easier, because that would be scope creep.
I also had to install `pytest` into my local venv, which is not a repo
change. The posted plan's intent didn't change, so I haven't posted a
follow-up comment on the thread.
