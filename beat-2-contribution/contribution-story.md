# Contribution: Document what each LLM provider retains and where

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** 1
**Student:** Bobaninja21
**GitHub Username:** Bobaninja21
**Issue:** https://github.com/DarkWorldCoder/market-mirror/issues/164
**Pull Request:** https://github.com/DarkWorldCoder/market-mirror/pull/195

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
can read | target: gh issue comment --help`) and nothing real until I logged in again. When it
finally ran (2026-11-10 23:28), it **stopped my edited claim** on "Promise only the next thing I
will look at", because the claim still promised the whole table after the first step; repro-check
had passed that same text. I left the claim as it was, since the PR now delivers what it promised,
and my PR description and review reply both went out through the guard with `pass`. The
unit-6 rubric patch's regression (unit-1 harness, with the same `claude.cmd` fix) scored 20/20.
Repo memory: `CLAUDE.local.md` in my market-mirror
clone, 47 lines, kept out of git via `.git/info/exclude`. The repo ships its own `CLAUDE.md`,
but that file covers operating the product, so my file holds the dev conventions, the
`providers.py` map, and the Windows baseline (868/34/15, compare failures by name).

**Unit 7 (2026-11-10): ship-gate installed and patched for Windows; manifest written; the gate stopped on login, my pr-precheck caught a missing template**

The install ran cleanly but shipped the same two Windows breaks as unit 6's guard (`python3`
in the hook, `claude` without `.cmd`), plus a third: `git diff` and check output are decoded as
cp1252, which crashes on the em dashes in this repo's docs. I patched all of them (4 small changes)
and pointed the hook at `python -X utf8`. My `ship-manifest.md` has 4 must-pass rows written for
cmd.exe, because the gate runs them with `shell=True` (setup, CI's syntax-only ruff, provider tests
with the 5 known Windows failures deselected by name, the manifest-version test). It has 3 dated
exclusions (full suite red on main on Windows, those 5 tests, Playwright tests I can't run). On
`docs/provider-privacy-164` all four checks passed, then the gate stopped: pr-precheck "could not
complete its run (claude exited 1 ... 401)". That was a fail-closed refusal, not a judgment. Run
in-session as a dry run, my pr-precheck rejected on `standards-met` for the missing PR-template
heading and checklist; I fixed the draft. After I logged in again, the real gate first blocked on
`diff-matches-plan [unclear]: plan.md absent`; with `plan.md` written at the clone root it passed,
and PR #195 went out through the gate hook (see Unit 8 and the Pull Request section).

**Unit 8 (2026-11-11): PR-monitor and playbook; a bot review in the first minute, a draft I rewrote**

I installed the PR-monitor with the same Windows patches as the gate. One of them first failed
with `NameError: name 'shutil' is not defined`, because the file's CRLF line endings made my
`import subprocess\n` match miss. I wrote my playbook with a 5-quiet-day nudge threshold, from the
7 latest outside-contributor PRs here, which merged in 0.1 to 2.3 days. Seconds after PR #195
opened, `--state` said `FEEDBACK-IN`, but the comment was CodeRabbit's placeholder, not a review:
the monitor counts a bot as feedback. Once the review landed (7 findings), the full run drafted
a reply in my playbook's order. It was written as if every fix were already done, and it cited a
Google Cloud DPA URL that was not the one Google's Gemini terms link. I rewrote it from what I
actually verified and fixed (5 taken in `cd9d36b`, 2 declined with reasons) and posted it through
the voice-guard hook (`[hook] pass`). The monitor now reads `QUIET`, with 2 checks passing.

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
live LLM run. The honest cost: `CATALOG` has 25 entries, roughly 15 of them hosted APIs whose
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
  order; drafted a `fix-is-in-this-repo` required check in my scan notes but have not applied it
  to the rubric yet (the rubric patch I did apply and regress, 20/20, was unit 6's `scope-feasible`).

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

Deviations (recorded 2026-11-10 while building, commit `841265b`):

1. **Step 2 drops the `LLM_GATEWAY` fallback.** `check()` reads only `MARKET_MIRROR_PROVIDER`.
   Reading `write_config`/`primary_from_pool` showed that gateway names collide with catalog keys:
   the Gemini CLI gateway writes `LLM_GATEWAY=gemini`, but `CATALOG["gemini"]` is the Gemini
   **API**, so the fallback would have printed the wrong provider's page.
2. **Step 1 uses one `PRIVACY_LINKS` dict for all 25 entries**, applied by one loop, instead of
   inline fields on 13 static entries plus a dict for the 12 `API_GUIDES` ones. One place to
   update, and `PRIVACY_LINKS[_name]` raises `KeyError` at import if a provider is ever added
   without a link.
3. **Step 3's test allows `""` for `multi` as well as `compatible`.** A multi-provider pool has
   no single vendor; its members' rows apply. Found while filling the dict; stated in the PR.
4. **Review round 1 (CodeRabbit, 2026-11-11) partly reverses deviation 1.** `mm.sh gateway start`
   (line 208) strips `MARKET_MIRROR_PROVIDER` and writes `LLM_GATEWAY`, so with deviation 1 alone
   every login-gateway user got no privacy link. `check()` now falls back to `LLM_GATEWAY` through
   an explicit `GATEWAY_CATALOG_KEY` map (`gemini` -> `gemini_cli`, `multi` unmapped), which keeps
   the collision deviation 1 was avoiding. New test:
   `test_provider_check_maps_login_gateways_to_their_catalog_entry` (fails with `KeyError: 'privacy'`
   without the fix). Commit `cd9d36b`.
5. **Review round 1, docs:** four table corrections, each re-read on the provider's own page
   2026-11-11: the Gemini paid-tier DPA (`business.safety.google/processorterms/`, which my "Not
   stated" had missed) and a new unpaid-tier row; Together's passthrough models; Qwen's storage
   region vs inference deployment scope; Claude Code's safety-review training exception.
   Declined with reasons: an "endpoint differs from catalog" label (new behaviour, not in #164) and
   CodeRabbit's docstring-coverage threshold (not a check this repo states).

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

**Results on the built change (`841265b`, 2026-11-10):**

1. Repro re-run, after:
   `have guide: 25 | have privacy: ['ollama', 'lmstudio', 'anthropic', ... 'minimax', 'compatible']`
   (all 25) and
   `{"status": "unreachable_or_invalid_response", "inference_tested": false, "privacy": "https://www.anthropic.com/legal/privacy"}`.
2. Provider tests: **6 passed, 5 failed**. Both new tests pass (they failed before the links
   existed: 7 failed / 4 passed), and the 5 failures are the same Windows ones.
3. Full suite: **869 passed, 35 failed, 15 skipped**. My pass condition was *not* met as written
   (that would be 870/34). All 34 baseline failures are present by name. The one extra,
   `tests/test_anthropic_camel.py::test_social_action_and_interview_are_cached`, turned out to be
   flaky on main: it failed 5 of 6 runs at `2ffb39f` and 6 of 6 on the branch, and it does not
   touch `providers.py`. My baseline had caught one of its rare passes. Reported in the PR as such.
   After review round 1 (`cd9d36b`, 2026-11-11): provider tests **7 passed, 5 failed** (3 new
   tests pass; the gateway test fails with `KeyError: 'privacy'` when I stash only the fix), full
   suite **870 passed, 35 failed, 15 skipped**, which is exactly 868 + 3 new − 1 flaky, with the
   same 34 failures by name plus the same flake. One run before that died at collection with a
   Windows `0xc000001d` "Illegal instruction" while importing torch; the rerun completed.
4. Links: all 44 in the table resolve; 41 return 200 to curl, and 3 (openai.com DPA, x.ai DPA,
   OpenAI help article) return 403 to scripts but loaded in a browser.
5. Also run: CI's syntax lint (`ruff check --select E9,F63,F7,F82 ...`, all checks passed) and
   `tests/test_manifest_versions.py` (OK).

**Toolkit**

- Tool: Claude Code, used to turn the baseline run into before/after numbers, run the suite on
  the branch, and diff the `FAILED` lines by name.
- What it produced: the 4 checks above with baseline numbers, then the results: one name in the
  diff (`> FAILED tests/test_anthropic_camel.py::test_social_action_and_interview_are_cached`).
- Difficulties: the by-name diff flagged a "new" failure that wasn't mine. I only knew that after
  running it 6 times on each side; a single baseline run had caught a rare pass.
- What changed: I now treat one baseline run as a sample, not a fact. Next time I would run the
  baseline twice and mark any test that flips as flaky before building anything.

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
note) to my scout. Its unit-1 regression run had to wait for the same reason as the guard
(`claude -p` returned 401); after I logged in again it scored 20/20 against the gold labels.

**2026-11-10: research split, then the build**

Decision: the table is research, not code, so I split the 22 providers that have terms of their
own across three research agents with one rule: a cell is a quote from the provider's own page or
it says "not stated". That rule produced most of the "not stated" cells (DeepSeek, MiniMax,
Z.ai training) instead of guesses from memory. Four links returned 403 to every script
(openai.com, x.ai) and I opened them in a browser before keeping them. No maintainer answer on
where the table goes, so I used `docs/PROVIDERS_AND_SCALE.md` (the issue's first option) and
said so in the PR. Built on `docs/provider-privacy-164`. Wrote the tests first and watched them
fail (7 failed / 4 passed), then added the links. Deviations 1-3 above came out of this step.

**2026-11-10: the ship-gate on the real branch**

Installed the ship-gate, wrote `ship-manifest.md` (4 must-pass checks mined from CONTRIBUTING.md
and `.github/workflows/tests.yml`, 3 dated exclusions), and ran it by hand. All four repo checks
passed; then it stopped: "your pr-precheck could not complete its run (claude exited 1: ... 401
OAuth access token has expired)". Same login as the guard. I ran my pr-precheck in-session as a
dry run instead. It rejected on `standards-met`: the repo ships `.github/pull_request_template.md`
("How I tested it" plus a 3-item checklist), and my draft had renamed the heading and dropped the
checklist. Fixed; the dry-run re-grade is accept. Not shipped: the real gate pass and the PR wait
on the `claude` and `gh` logins.

**2026-11-10/11: shipping through the gate, then review round 1**

The first real gate run blocked on `diff-matches-plan [unclear]: plan.md absent`; I wrote
`plan.md` at the clone root and it passed. My first `gh pr create` was then stopped by the gate
*hook*, which judged `codepath/ai301-unit5-starter`: the hook reads the session's working
directory, not a `cd` inside the command. I moved the session into the clone, and the PR went out
through both hooks as https://github.com/DarkWorldCoder/market-mirror/pull/195 (voice-guard
`[hook] pass`, gate pass on the same package). CodeRabbit reviewed within minutes. Decision: verify
each of its 7 findings against the code and the providers' pages before touching anything. It was
right about the gateway gap, and about the Gemini DPA I had marked "not stated"; it asked for two
things outside #164, which I declined. Fixed in `cd9d36b`, re-gated (pass), updated the PR body,
and replied through the guard.

**Toolkit**

- Tool: Claude Code for the commands, and this log written as I worked.
- What it produced: dated entries tied to each decision: selection, baseline, claim correction,
  research split, gate run, the ship and review round 1.
- Difficulties: none met with the log itself; the difficulties were in the work it records.
- What changed: nothing.

---

## Pull Request

**PR Link:** https://github.com/DarkWorldCoder/market-mirror/pull/195 (opened 2026-11-10 23:52
PT from `Bobaninja21:docs/provider-privacy-164` against `main`)

**PR Description:** The title is "Add provider privacy links to provider check and a provider
data handling table (#164)". The description opens with `Fixes #164` and follows the repo's
`.github/pull_request_template.md`:
- **What this changes:** per file, including the `GATEWAY_CATALOG_KEY` map and why `gemini`
  maps to `gemini_cli`, and a note on where I put the table, since the maintainer never answered.
- **Why:** teams under GDPR need per-provider retention, DPA, region and training facts.
- **How I tested it:** the template's 3-item checklist (pytest run, docs updated, dashboard n/a),
  the before/after output of both reproduction commands, then ruff, the manifest test, provider
  tests (7 passed / 5 failed, the same 5 as on main) and the full suite (main 868/34/15, branch
  870/35/15, the extra being the named flaky test), plus what I could not run (Linux, Playwright).
- **AI assistance:** says Claude Code wrote the code, tests and table, researched the pages and
  ran the commands, and that I'm responsible for it.

After review round 1, I updated it to describe `cd9d36b` and list what I declined.

**Maintainer Feedback:**

- 2026-11-10 23:53 PT: CodeRabbit (the repo's AI review bot, not a maintainer) posted a summary,
  then a review with 7 actionable findings: login gateways get no privacy link because
  `mm.sh gateway start` drops `MARKET_MIRROR_PROVIDER`; an "endpoint differs" label; the Claude
  Code safety-review exception; Together passthrough models; a Gemini unpaid-tier row; the Gemini
  paid-tier DPA; and Qwen region vs inference scope. It also flagged docstring coverage.
- 2026-11-11 00:23 PT: I verified each finding against `mm.sh` and the providers' pages and fixed
  5 in `cd9d36b`, with a new gateway test that fails with `KeyError: 'privacy'` without the fix. I
  declined 2 with one reason each (the endpoint label is beyond #164; docstring coverage is not a
  check this repo states), and replied in the thread:
  https://github.com/DarkWorldCoder/market-mirror/pull/195#issuecomment-6790911677
- 2026-11-11: no human maintainer has commented yet. Checks: CodeRabbit and GitGuardian Security
  Checks both pass. Silence so far is under my 5-quiet-day threshold, so no nudge.

**Status:** Awaiting maintainer review (open, mergeable, 2 checks passing; 2026-11-11).

**Toolkit**

- Tool: the ship-gate (my manifest + my pr-precheck) on the real branch and at the `gh pr create`
  door; the voice-guard hook on the PR and the reply; then the PR-monitor with my playbook.
- What it produced: gate runs blocked on `plan.md absent`, then passed; the hook stopped my
  first `gh pr create` (wrong repo), then passed PR #195; the monitor read `FEEDBACK-IN`, drafted a
  7-point review reply, and now reads `QUIET` with 2 checks passing.
- Difficulties: the gate hook judges the session's working directory, so a `cd` in the command
  sent it to the wrong repo. The monitor counted CodeRabbit's placeholder comment as review
  feedback. Its draft claimed fixes not yet made and cited a Google Cloud DPA URL that Google's
  Gemini terms do not link.
- What changed: I moved the session into the clone before shipping. I treated the monitor's draft
  as a checklist, not a reply: I verified each point, fixed 5, declined 2, and rewrote the reply
  from what actually happened. I would add a "bot vs human" distinction to the monitor's
  FEEDBACK-IN state.

---

## Learnings and Reflections

**Technical Skills Gained**

- Reading a stranger's config flow end to end before touching it: tracing `write_config` →
  `.env` → `check()` is what showed me `MARKET_MIRROR_PROVIDER` already held the catalog key, and
  that the `LLM_GATEWAY` fallback I had planned would have mapped the Gemini CLI to the Gemini
  API's privacy page (deviation 1).
- Testing on a red baseline: comparing `FAILED` lines by name instead of trusting a green/red
  summary, and re-running a suspect test 6 times on each side before calling it flaky
  (`test_social_action_and_interview_are_cached`: 5/6 failures on main).
- Writing tests that exercise the change without the platform-broken path: `check(settings=...)`
  with a fake opener avoids `write_config`, whose `os.replace` on an open temp file fails on
  Windows (WinError 32).
- Making Python tools portable to Windows: `shutil.which("claude")` for npm's `.cmd` shims,
  explicit UTF-8 decoding instead of cp1252, `python -X utf8` instead of the `python3` Store stub,
  and cmd.exe syntax for anything run with `shell=True`.

**Challenges Overcome**

- **My own claim broke my own voice guide.** "I'm aiming to open a PR within about a week"
  went up unguarded on 2026-11-10. repro-check caught it (`claim-is-specific`: fail), and I edited
  the claim. Then the voice-guard, once it could run, stopped the *edited* claim too, on "Promise
  only the next thing I will look at", because it still promised the whole table. Two tools
  reading one rule differently taught me the guide's wording, not the tool, is where the fix goes.
- **The gate blocked a good branch for a real reason.** Its first real run on
  `docs/provider-privacy-164` passed all four manifest checks, then pr-precheck returned
  `diff-matches-plan [unclear]: plan.md absent`. My plan lived only in this story. I wrote
  `plan.md` beside the code from the story's plan and deviations, and the next run passed.
- **Every course tool assumed macOS/Linux.** The voice-guard, the ship-gate, the unit-1 harness
  and the PR-monitor all failed on Windows the same way (`claude` not found, then cp1252
  decoding). The test suite killed itself on a SIGTERM test, and WSL's disk went read-only. Each
  workaround is recorded in the Toolkit thread where it happened.
- **Research I could not do from memory.** 16 hosted rows × 6 columns of legal-ish facts. I split
  it across three research agents with one rule (a quote from the provider's own page, or "not
  stated"), then checked every link: 41 return 200, and the three that block scripts (openai.com
  DPA, x.ai DPA, OpenAI help) I opened in a browser.

**What I'd Do Differently Next Time**

- Run every outgoing comment through the guard *before* posting, including the first one. The
  claim that broke my rules is the one I posted before the guard existed.
- Run the baseline suite twice before building, and mark anything that flips as flaky up front.
  One baseline run gave me a wrong "new failure" to explain later.
- Ask the location question in the claim *and* decide a default in the plan, as I eventually
  did. The maintainer never answered, and the plan already had to choose.
- Write `plan.md` at the clone root from day one rather than only in the story. The gate reads
  the clone, not my course repo.

---

## Resources Used

- market-mirror's `CONTRIBUTING.md` (setup commands, "A PR needs green tests, updated docs") and
  `.github/workflows/tests.yml` (the ruff and manifest checks my ship manifest mined), plus
  `.github/pull_request_template.md`, which my pr-precheck caught me ignoring.
- The issue itself, https://github.com/DarkWorldCoder/market-mirror/issues/164, for the column
  list and the "facts only from the providers' own pages" rule.
- The providers' own pages linked in the table's Source column, e.g. OpenAI's
  https://developers.openai.com/api/docs/guides/your-data and Groq's
  https://console.groq.com/docs/your-data, which settled the retention and training cells.
- My own toolkit: issue-scout (scans 1-2), repro-check, the voice-guard, the ship-gate wrapping
  pr-precheck, and the PR-monitor, each with its log on my machine.
- Claude Code (Anthropic), the AI assistant that did much of the reading, coding and research
  with me, as disclosed in the PR.
