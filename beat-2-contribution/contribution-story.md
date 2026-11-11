# Contribution: Document what each LLM provider retains and where

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** 1
**Student:** [Student name]
**GitHub Username:** Bobaninja21
**Issue:** https://github.com/DarkWorldCoder/market-mirror/issues/164
**Pull Request:** [GitHub PR link, once opened]

One repository, built across units 5 to 8, telling the whole story of your real
contribution. Fill it as you work, not at the end — the sections below ask for evidence that
is far easier to write down as it happens than to reconstruct in the last weekend.

Contribution Number stays `[1]` unless you make more than one contribution this term; it
just numbers them.

---

## Toolkit

Every story section below, from Why I Chose This Issue through Pull
Request, ends with a Toolkit block: which tool did that phase's work
and how you used it, what it produced, what difficulties or
shortcomings it presented, and what you changed or would change
because of them. Together the blocks are the thread the rubric's
Toolkit points read, and they are the raw material for your unit-9
portfolio, so keep them current as you work rather than
reconstructing them later.

A Toolkit block looks like this (copy it into each story section and
fill it there):

```text
**Toolkit**

- Tool: [which tool did this phase's work, and how you used it]
- What it produced: [described in a sentence or a short excerpt]
- Difficulties: [what fought you or fell short, or "none met"]
- What changed: [the patch, revision, or workaround, what you would
  update, or "nothing"]
```

This section also keeps one short dated entry per week of tool work,
starting below: what you built or changed that week, what broke, and
what you changed because of it. The blocks tell the story where it
happened; these entries are the toolkit's own timeline.

All four entries are graded. Keep every heading below, and fill each
one in the week it belongs to.

**Unit 5 (2026-11-10): installed issue-scout, wrote the strategy, ran two scans, found a rubric gap**

I installed the scout over my unit-1 skill, carried my fit profile forward with a new
"where I want to search" sentence, and wrote five `gh search issues` queries, each traced to
a profile sentence. `gh` was not logged in, so I ran the same queries through the
unauthenticated REST search API, whose 60-calls/hour limit forced a 15-minute wait mid-grading.
Scan 1's "fewer comments first" ordering floated issues filed minutes earlier to the top, and
3 of the 5 already had open PRs with 0 comments, so I patched the strategy to drop issues
younger than 3 days. Scan 2 then showed my rubric accepting two aws/agent-toolkit-for-aws issues
whose broken text lives in a different repo; I wrote up a `fix-is-in-this-repo` check as a
proposed patch, not yet applied.

**Unit 6 (2026-11-10): voice-guard installed and failing closed on Windows; repo memory written; the claim corrected**

I folded my three voice-guide copies into one at `~/.claude/voice-guard/voice-guide.md` (the
pr-precheck copy was a superset of the other two) and added the upstream rules. My new claims
rule uses my own posted line as its Wrong example: "I'm aiming to open a PR within about a
week" broke my unit-2 rule "I never claim a fix, a timeline, or a date", and I had posted it
unguarded. repro-check confirmed it (`claim-is-specific`: fail), so I edited the claim on GitHub
to drop the date; the re-grade passes. The guard broke twice on Windows: the hook's `python3`
is the Microsoft Store stub (I point the hook at `python -X utf8` instead), and
`subprocess.run(["claude", ...])` cannot find npm's `claude.cmd`, so I patched one line to
`shutil.which("claude")`. It then failed closed with "claude exited 1": the `claude` CLI's
OAuth token had expired. The guard log shows the wiring test stopped (`stop: no body the guard
can read | target: gh issue comment --help`) but no real pass yet, so a guarded check of the
edited claim is still pending a re-login. Repo memory: `CLAUDE.local.md` in my market-mirror
clone, 47 lines, kept out of git via `.git/info/exclude`. The repo ships its own `CLAUDE.md`,
but that file covers operating the product, so my file holds the dev conventions, the
`providers.py` map, and the Windows baseline (868/34/15, compare failures by name).

**Unit 7: [one line on the tool work]**

[2-4 sentences: the ship-gate and your gate manifest. What it produced
on your real branch, what fought you, what changed because of it.]

**Unit 8: [one line on the tool work]**

[2-4 sentences: the PR-monitor and your playbook. What it read, what it
drafted, what you did with the draft.]

---

## Why I Chose This Issue

I searched with my own issue-scout: five queries built from my fit profile (Python bugs,
Python LLM/CLI/agent tooling, FastAPI/SQLAlchemy/pytest, Python docs, scoped TypeScript bugs),
all on `good first issue`, unassigned, open. Scan 1 combined 394 unique results, ranked them,
and graded the top five: it accepted only openslop/openslop#1220 (a TypeScript prompt fix) and
rejected the rest, three of them because an open PR already existed (curvine-doc #120, repowise
#5490/#5493, yasbd-lib #426/#427) and one because its whole body was "Review attack vectors".
After patching the strategy, scan 2 surfaced this issue at rank 1 from the LLM/CLI/agent query
(signals: `documentation` label, 0 comments, updated 2026-11-06) and the scout graded it
**accept**: all six required checks pass (repo pushed 2026-11-10, no AI ban in CONTRIBUTING.md,
no assignee/comments/linked PRs, opened by the owner with named files and a "Done when" list).
`maintainer-responsive` came back `unclear` because I skipped that fetch to save API calls.

It fits because it is a Python CLI for LLM providers, the exact kind of tooling I use daily,
and the change is a docs table plus a small `privacy` field in `CATALOG` in
`backend/claude_cli/providers.py` with a pytest test, which I can verify locally (the suite mocks
the model, no API key needed). I picked it over openslop#1220 because verifying that one needs a
live LLM run. The honest cost: `CATALOG` has about 26 entries, roughly 15 of them hosted APIs whose
retention, DPA and region terms I have to research from each provider's own pages, so this is
more reading than coding. I want to learn to land a small, sourced change in someone else's repo.

On the tool: the rubric held up on claims (it caught every open PR), but the ranking misread the
wild twice. Brand-new issues get claimed by PR within hours, not by comment, so "fewer comments
first" was rewarding exactly the wrong issues; and the rubric accepted two aws issues whose broken
README text is generated by a separate CLI, which a search of 99 docs in that repo confirmed.
I patched the strategy for the first and drafted a rubric check for the second.

Claim comment: https://github.com/DarkWorldCoder/market-mirror/issues/164#issuecomment-6788636938
(posted 2026-11-10 06:16 UTC; edited 06:47 UTC to drop "I'm aiming to open a PR within about a
week", a promised date that broke my own voice guide; see the Unit 6 entry).

**Toolkit**

- Tool: issue-scout (my search strategy + my unit-1 rubric), run as two scans on 2026-11-10;
  queries went through the REST search API because `gh` was not logged in.
- What it produced: scan 1 graded 5 (1 accept: openslop#1220); scan 2 graded 5 (3 accept),
  with market-mirror#164 at rank 1, verdict accept, six required checks pass,
  `maintainer-responsive` unclear.
- Difficulties: "fewer comments first" ranked minutes-old issues highest, and 3 of 5 were
  already claimed by open PRs; my `sort=updated` in the API workaround made the 90-day filter
  a no-op; the rubric accepted aws#543/#541 although their fix lives in another repo.
- What changed: added a "drop issues created < 3 days ago" filter and went back to best-match
  order; drafted a `fix-is-in-this-repo` required check (pending a unit-1 harness regression run).

---

## Understanding the Issue

**Problem Description**

MarketMirror sends your briefs and documents to whichever LLM provider you configure, and
PRIVACY.md (line 23) only says "that provider's own privacy policy and data-retention terms
apply." Nothing in the repo tells a team, per provider, whether a data processing agreement
exists, how long prompts are kept, whether zero-retention is available, where data is processed,
or whether prompts train models by default. Teams under GDPR need exactly that to pick a
provider, and the CLI that helps you pick one (`providers.py`) has a docs link and a key link per
provider but no privacy link.

**Expected Behavior**

- The docs carry a "Provider data handling" table with one row per `CATALOG` provider: DPA link,
  default retention, zero-retention option, processing regions, trains-by-default, a
  checked-on date and a source link, all from the providers' own pages, with no legal advice.
- Every `CATALOG` entry has a `privacy` link, and `provider check` shows the configured
  provider's privacy link, with a test covering it.

**Current Behavior**

- `providers.py list` returns 25 entries and none has a `privacy` key.
- `provider check` returns only `status`, models and `inference_tested`, with no privacy link.
- The only data-handling text in the docs is the one generic sentence in PRIVACY.md:23.

**Affected Components**

- `backend/claude_cli/providers.py`: `CATALOG` (13 static entries), `API_GUIDES` (12 hosted
  APIs merged into `CATALOG` by a loop), and `check()`, which reads the configured provider from
  `.env` (`MARKET_MIRROR_PROVIDER`, written by `write_config`).
- `backend/claude_cli/tests/test_providers.py`: existing provider tests.
- `docs/PROVIDERS_AND_SCALE.md` or `PRIVACY.md` for the table (asked the maintainer which).

**Toolkit**

- Tool: Claude Code (AI assistant), used to read the issue body and `providers.py` and to
  run `providers.py list` to count the entries.
- What it produced: the issue restated as 3 deliverables, and the finding that `CATALOG` has
  25 entries (13 static + 12 merged in from `API_GUIDES`), not the "15+" the issue mentions.
- Difficulties: the issue says "15+ providers", which undersold the research load. Only by
  running `list` did I see that about 15 are hosted APIs that need real data-terms research.
- What changed: I put the per-row source and checked-on date into the plan as the main cost,
  and asked the maintainer where the table should live instead of guessing.

---

## Reproduction Process

**Environment Setup**

- OS: Windows 11 Pro (10.0.26300), Git Bash and PowerShell 7; git 2.55.
- Python 3.12.13 (installed by uv for the project; system Python is 3.14), uv 0.13.7
  (installed with `python -m pip install --user uv`, since uv was not on PATH).
- Project: DarkWorldCoder/market-mirror at commit `2ffb39f` (2026-11-10), set up per its
  CONTRIBUTING.md: `git clone`, then `cd backend && uv sync --python 3.12`. No API key needed:
  the tests mock the model and embedder.
- Baseline test run: `uv run --no-sync python -m pytest tests claude_cli/tests -q`.

Challenges (2026-11-10):

1. **The suite killed itself at ~50%.** pytest exited with code 15 and no summary line, twice.
   A `-v` run showed the last test started was
   `tests/test_production_hardening.py::test_sigterm_stops_server_chains_previous_handler_and_kills_simulations`,
   which sends SIGTERM to its own process; on Windows that terminates pytest outright.
   Workaround: `--deselect` that one test. Result: **868 passed, 34 failed, 15 skipped,
   1 deselected** in 4m51s, before touching any code.
2. **The 34 failures are Windows-only, and 5 are in the file I need to change**
   (`claude_cli/tests/test_providers.py`, 5 failed / 4 passed). Four fail at
   `providers.py:230` in `write_config` with `PermissionError: [WinError 32] The process cannot
   access the file because it is being used by another process` (it replaces a temp `.env`
   file that is still open, which POSIX allows and Windows does not); the fifth shells out to
   `bash mm.sh` and gets exit 49. The rest (gateway fake CLIs, process liveness, SIGTERM) are
   the same POSIX assumptions.
3. **Tried WSL Ubuntu for a clean Linux baseline:** uv installed fine, but `uv sync` on Linux
   pulls CUDA torch wheels, and the WSL disk went read-only mid-extraction
   (`Read-only file system (os error 30)`, then `df: Input/output error`). Status: pending repair.

**Steps to Reproduce**

1. `git clone https://github.com/DarkWorldCoder/market-mirror && cd market-mirror/backend && uv sync --python 3.12`
2. `uv run --no-sync python -c "import sys; sys.path.insert(0, 'claude_cli'); import providers as p; c = p.CATALOG; print(len(c), 'entries:', len(p.API_GUIDES), 'from API_GUIDES,', sum(bool(v.get('gateway')) for v in c.values()), 'login gateways,', sum(bool(v.get('key_required')) for v in c.values()), 'need an API key'); print('have guide:', sum('guide' in v for v in c.values()), '| have privacy:', [k for k, v in c.items() if 'privacy' in v])"`
   (counts the catalog, and as a control counts the `guide` link every entry already has
   against the `privacy` link none has)
3. `uv run --no-sync python -c "import sys, json; sys.path.insert(0, 'claude_cli'); import providers as p; print(json.dumps(p.check(None, settings={'MARKET_MIRROR_PROVIDER': 'anthropic', 'LLM_PROVIDER': 'claude', 'LLM_BASE_URL': 'http://127.0.0.1:9/v1'})))"`
   (runs `check()` with Anthropic configured but pointed at a closed local port, so it runs offline
   with no key)
4. `grep -niE "retention|DPA|data processing agreement" ../PRIVACY.md ../docs/PROVIDERS_AND_SCALE.md`
5. Observed: 25 entries, all 25 with `guide` and none with `privacy`; `check()` output has no
   privacy field even with a provider configured; one generic retention sentence in the docs.

**Reproduction Evidence**

- **Commit showing reproduction:** upstream `main` at
  https://github.com/DarkWorldCoder/market-mirror/commit/2ffb39f (no fork yet; this is a
  missing-feature issue, so the reproduction is the current state of `main`, and my fork
  branch will start from this commit).
- **Screenshots/logs:** output of steps 2-4 on 2026-11-10, Windows 11:

  ```text
  $ step 2
  25 entries: 12 from API_GUIDES, 7 login gateways, 16 need an API key
  have guide: 25 | have privacy: []
  $ step 3
  {"status": "unreachable_or_invalid_response", "inference_tested": false}
  $ step 4
  ../PRIVACY.md:23:  privacy policy and data-retention terms apply to those prompts.
  ```

  (Steps 2-3 were rewritten on 2026-11-10 after repro-check rejected my first version: step 2
  had an elided `python -c "..."` filter and step 3 was written as a pseudo-call. These are
  the commands exactly as run.)
- **My findings:** the gap reproduces exactly as the issue describes. The real surprise was
  the baseline: on Windows the suite needs one SIGTERM test deselected to finish, and it
  shows 34 pre-existing failures, 5 of them in `test_providers.py` (WinError 32 in
  `write_config`). My new test must therefore exercise `check()` through its `settings=`
  argument and not through `write_config`, or it would fail on Windows for unrelated reasons.

**Toolkit**

- Tool: Claude Code, used to run the setup, the reproduction commands and the baseline suite;
  then my repro-check skill (live mode, with the wild scope installed in unit 6), which graded my
  posted claim plus a draft repro comment against #164.
- What it produced: the before-evidence above, a baseline of 868 passed / 34 failed / 15
  skipped / 1 deselected, and a repro-check verdict of **reject** with three required fails:
  `steps-rerunnable` ("first command elided as `python -c \"...\"`"), `outcome-matches-evidence`
  ("'13 static, 12 added' ... '34 failures (868 passed)' asserted with no output shown"), and
  `claim-is-specific` ("'I'm aiming to open a PR within about a week' promises a date").
- Difficulties: pytest killed itself (exit 15) at about 50% twice before a `-v` run pinned it
  to the SIGTERM test; WSL's disk went read-only during `uv sync`. And repro-check caught me
  pasting a shortened command I had never run in that form, plus counts I had only worked out
  in my head.
- What changed: I deselect the SIGTERM test and compare failures by name. I rewrote steps 2-3
  as the exact commands run, added a `guide` vs `privacy` control, and cut the unsupported
  Windows paragraph from the draft. After I edited the claim on GitHub, the re-grade was
  **accept** (all 8 required checks pass, plus both preferred checks).

---

## Solution Approach

**Analysis**

This is missing content, not broken logic. `CATALOG` in `backend/claude_cli/providers.py`
holds, per provider, `label`, `base_url`, `key_required`, `guide`, `key_url` and `key_env`;
the 12 hosted APIs in `API_GUIDES` are copied into it by the loop over `API_GUIDES.items()`
that builds each entry from `model_pool.API_PROVIDERS`. No field carries a privacy link, so
`check()` (which returns status and models only) has nothing to show. The configured provider
is already known to `check()`: `write_config` saves the `CATALOG` key as
`MARKET_MIRROR_PROVIDER` in `.env`, which `check()` reads through `read_env(root)` or its
`settings=` argument. On the docs side, PRIVACY.md:23 defers to "that provider's own privacy
policy" without saying what those policies are.

**Proposed Solution**

Add the data and surface it in one place each:

- a `privacy` link on every `CATALOG` entry (for the 12 `API_GUIDES` providers, a small
  `PRIVACY_LINKS` dict read by the existing loop, so the loop stays the single place
  those entries are built);
- `check()` adds `"privacy": <link>` to its result for the configured provider, on success and
  on error paths, so you see it even when the endpoint is down;
- one "Provider data handling" table in the docs file the maintainer picks, every row with a
  source link and a checked-on date; gateways point to the CLI vendor's consumer terms;
  Ollama and LM Studio are marked on-device; "Another OpenAI-compatible server" gets "your
  provider's terms".

Deliberately NOT changing: the Windows-only failures in `write_config` and the test suite
(a separate issue, and one I may report); `configure()`'s flow and prompts; `model_pool.py`;
the structure of `CATALOG`; and no legal interpretation in the table: facts and links only.

**Implementation Plan**

1. `backend/claude_cli/providers.py`: add a `privacy` URL to each static `CATALOG` entry; add
   a `PRIVACY_LINKS` dict next to `API_GUIDES` and set `"privacy"` in the loop that builds
   those entries.
2. `backend/claude_cli/providers.py` `check()`: look up
   `CATALOG.get(env.get("MARKET_MIRROR_PROVIDER") or env.get("LLM_GATEWAY") or "", {}).get("privacy")`
   and include it in every returned dict.
3. `backend/claude_cli/tests/test_providers.py`: a test that every `CATALOG` entry has a
   `privacy` key (an https URL, or empty only for `compatible`), and a test that `check()` with
   `settings={"MARKET_MIRROR_PROVIDER": "anthropic", ...}` and a fake opener returns the
   Anthropic privacy link on both the reachable and the HTTP-error paths.
4. Docs: the "Provider data handling" table in `docs/PROVIDERS_AND_SCALE.md` (or `PRIVACY.md`
   if the maintainer prefers), plus a one-line pointer to it from PRIVACY.md:23.

Deviations: none yet (no code written).

**Toolkit**

- Tool: Claude Code, used to trace `check()`, `write_config()` and `status()` to find how
  `check()` can know which provider is configured.
- What it produced: the finding that `MARKET_MIRROR_PROVIDER` already holds the `CATALOG` key,
  so the lookup needs no new config, and the 4-step plan above.
- Difficulties: my claim comment said "print it in `provider check`", but `check()` returns a
  dict that `main()` prints as JSON, so the "print" is really a new field in that JSON.
- What changed: the plan states "add a field to `check()`'s result" explicitly, so the PR
  description can't overstate it as a new printed line.

---

## Testing Strategy

1. **Reproduction re-run (before/after):** run Reproduction steps 2-3 on the branch. Before:
   `have guide: 25 | have privacy: []` and
   `{"status": "unreachable_or_invalid_response", "inference_tested": false}`.
   After: `have privacy:` lists all 25 entry names, and the same step 3 output carries a
   `"privacy"` field with Anthropic's link on that offline error path.
2. **New tests:** `uv run --no-sync python -m pytest claude_cli/tests/test_providers.py -q`.
   Before: 5 failed, 4 passed (all 5 are WinError 32 or bash failures in `write_config`).
   After: the same 5 failures, unchanged, plus my new tests passing.
3. **Full suite, same command as the baseline:**
   `uv run --no-sync python -m pytest tests claude_cli/tests -q --deselect tests/test_production_hardening.py::test_sigterm_stops_server_chains_previous_handler_and_kills_simulations`.
   Baseline: 868 passed, 34 failed, 15 skipped. Pass condition: the same 34 failures by name
   (diffed from the `FAILED` lines) and the passed count up by exactly the number of new tests.
4. **Docs check:** every table row has a source link and a checked-on date; I will open each
   source link once to confirm that it resolves.

**Toolkit**

- Tool: Claude Code, used to turn the baseline run into before/after numbers and
  a named-failure diff.
- What it produced: the 4 checks above with their baseline numbers filled in.
- Difficulties: on a baseline with 34 red tests, "tests pass" means nothing, so I needed
  a comparison that a new failure couldn't hide in.
- What changed: the pass condition is "the same 34 failures by name", not a green suite, and
  the new tests deliberately avoid `write_config`.

---

## Implementation Notes

**2026-11-10: scout and selection**

Installed issue-scout, wrote the strategy, and ran scan 1 (1 accept), patched the
"created < 3 days" filter, then ran scan 2 (3 accepts). I chose market-mirror#164 over
openslop#1220 because it is testable locally with no LLM key, and over the two aws issues
because their fix lives in another repo. The claim comment is posted and asks where the table
should go.

**2026-11-10: setup and the Windows baseline**

Wrong turn: I assumed the suite was slow, but it was killing itself. A `-v` run found the SIGTERM
test. I tried WSL for a clean Linux run and the WSL disk went read-only, so I decided to keep
the Windows baseline (868/34/15) and compare failures by name. Decision: the new tests go through
`check(settings=...)` so they don't depend on `write_config`, which fails on Windows.

**2026-11-10: voice-guard, repo memory, and correcting my own claim**

Installing the voice-guard meant re-reading my voice guide, and its first rule says I never
promise a date. My posted claim did ("within about a week"). repro-check graded the package
reject on that plus two flaws in my repro draft (an elided command, counts with no output).
Decision: fix both in public rather than leave them. I rewrote the repro commands to the exact
form run (with a `guide`/`privacy` control) and edited the claim on GitHub to drop the date. I
chose not to keep an "Edited:" note, since GitHub shows its own edit marker and history, and I
also dropped the AI-disclosure sentence from the claim; the repo states no AI policy, and the
disclosure goes in the PR. Also applied the unit-6 rubric patch (`scope-feasible` + difficulty
note) to my scout. Its unit-1 regression run is pending for the same reason as the guard:
`claude -p` returns 401 until I log in again.

**Toolkit**

- Tool: Claude Code for the commands, and this log written as I worked.
- What it produced: two dated entries tied to the selection and the baseline decisions.
- Difficulties: none met with the log itself; the difficulties were in the work it records.
- What changed: nothing.

---

## Pull Request

**PR Link:** [GitHub PR URL when opened]

**PR Description:** [what the PR says; much of the content above
adapts into it]

**Maintainer Feedback:**

- [Date]: [feedback received, or "none yet"; silence is a normal
  state and costs you nothing]
- [Date]: [how you addressed it]

**Status:** [Awaiting review / Iterating / Merged / Closed. The
status is a fact you record, never a grade: the PR is graded as
opened.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Learnings and Reflections

**Technical Skills Gained**

[What you learned technically, named concretely.]

**Challenges Overcome**

[What was hard and how you solved it; point at the real events, the
Toolkit blocks above are full of them.]

**What I'd Do Differently Next Time**

[Reflection on your process.]

---

## Resources Used

[What actually helped, not a reading list. List things this story
already shows you using: the doc you followed to set the environment
up, the thread that explained the bug, the person who answered you.
One real entry beats ten plausible ones.]

- [Link to documentation that helped]
- [Discussion, issue, or post that helped]
