# How I documented what every LLM provider retains, and shipped it through a gate whose rules I wrote

## Overview

MarketMirror is an open-source command-line tool that sends your documents to whichever LLM provider you configure. Its privacy note said only "that provider's own privacy policy and data-retention terms apply" — no per-provider retention, DPA, region, or training facts, which is what a team under GDPR needs before it picks a provider. I added a `privacy` link to every provider in the tool's catalog, made `provider check` return that link, and wrote a sourced "Provider data handling" table covering the 15 hosted providers, each row checked against the provider's own pages. The pull request is open at https://github.com/DarkWorldCoder/market-mirror/pull/195 (issue https://github.com/DarkWorldCoder/market-mirror/issues/164), mergeable, with the repository's CodeRabbit and GitGuardian checks passing; no maintainer has replied yet. The tools that checked my work are the ship-gate, whose `ship-manifest.md` I wrote (four must-pass checks and three dated exclusions), and the voice-guard, whose `voice-guide.md` rules my comments and PR text are held against.

## What happened

**Finding the issue.** My issue-scout's first scan ranked 394 unique search results. Its "fewer comments first" ordering floated issues filed minutes earlier to the top, and 3 of the top 5 already had open pull requests. I patched the strategy — drop issues younger than three days, back to best-match order — and scan 2 dropped 277 of 438 results and surfaced market-mirror#164 at rank 1.

**The claim that broke my own rule.** My first claim comment said "I'm aiming to open a PR within about a week" — a date my voice guide forbids. repro-check rejected it (`claim-is-specific`: fail), and I edited the claim on GitHub to drop the date. The guide's Wrong example is now that exact line:

> Wrong: "I'm aiming to open a PR within about a week."

**Reproduction.** The repo's test suite killed itself at about 50% on Windows (a SIGTERM test ends the pytest process), so I deselected it and recorded a baseline: 868 passed, 34 failed, 15 skipped, with 5 of the failures in the file I needed to change. The gap reproduced exactly: 25 catalog entries, all 25 with a `guide` link, none with a `privacy` link.

**The build.** I added the links, the `check()` field, four tests, and the table. The before/after through the project's own CLI:

```text
$ python backend/claude_cli/providers.py check --root <folder with MARKET_MIRROR_PROVIDER=anthropic>
before: {"status": "unreachable_or_invalid_response", "inference_tested": false}
after:  {"status": "unreachable_or_invalid_response", "inference_tested": false, "privacy": "https://www.anthropic.com/legal/privacy"}

$ python backend/claude_cli/providers.py list
before: 0 of 25
after:  25 of 25
```

**The gate blocked me twice, for real reasons.** I wrote `ship-manifest.md` with four must-pass checks. The first run BLOCKed on a 401: pr-precheck could not run because the `claude` CLI's OAuth token had expired — a fail-closed refusal, not a judgment. The second run BLOCKed on a real gap:

```text
2026-11-10 23:37 [manual] verdict: BLOCK (block)
  diff-matches-plan [unclear]: plan.md absent from the working copy (ship gate confirmed);
  the hunk-vs-plan pairing cannot be built without it
2026-11-10 23:43 [manual] verdict: PASS
```

My plan lived only in my course repo, not in the clone. I wrote `plan.md` at the clone root and the gate passed. The voice-guard then stopped my PR body on the disclosure rule — "one plain sentence naming the tool and what it did" — and I cut the disclosure to one sentence:

```text
2026-11-11 12:36 [manual] stop: rule broken: "If a repo asks for disclosure I give it in one plain sentence..."
2026-11-11 12:39 [manual] pass
```

**The PR and the review that changed my fix.** PR #195 went out through both hooks. CodeRabbit reviewed within minutes and raised 7 findings. I verified each against the code and the providers' pages before touching anything. It was right about the gateway gap I had deliberately dropped:

> Line 296: Update the privacy lookup in check() to resolve recognized login gateways from LLM_GATEWAY when MARKET_MIRROR_PROVIDER is absent, including mapping gemini to gemini_cli...

I had dropped the `LLM_GATEWAY` fallback because gateway names collide with catalog keys (`gemini` is both the Gemini CLI gateway and the Gemini API). CodeRabbit showed that `mm.sh gateway start` drops `MARKET_MIRROR_PROVIDER`, so every login-gateway user got no link. I fixed it in `cd9d36b` with an explicit `GATEWAY_CATALOG_KEY` map, plus four table corrections; I declined two findings with reasons. My reply:

> providers.py, gateway privacy links: fixed. You're right that `mm.sh gateway start` drops `MARKET_MIRROR_PROVIDER` (line 208), so gateway users got no link... New test `test_provider_check_maps_login_gateways_to_their_catalog_entry` fails with `KeyError: 'privacy'` without the fix.

**The refactor.** My instructor's feedback asked me to move each link into its provider's own record instead of a parallel `PRIVACY_LINKS` dict that could drift, and to name the lookup. I did it in `8f70394`; the whole `CATALOG` compares equal before and after. CodeRabbit's follow-up review on the refactor raised three more points, which I have not yet addressed.

## Limitations and what's next

The PR is still open. No maintainer has replied, and CodeRabbit's follow-up review has three unaddressed points — provider-resolution fallbacks, pool disclosure, and two API privacy links. That is the next step. The table is research, not legal advice: where a provider's page says nothing, the cell says "not stated", and I did not interpret terms. The Windows baseline is the other honest limit: the full suite is red on main on Windows, so I compare failures by name, deselect one SIGTERM test, and the manifest excludes the full suite — CI runs it on Linux. On the tool side, the gate's hook judges the session's working directory rather than a `cd` in the command, which sent my first `gh pr create` to the wrong repo until I moved the session into the clone. Next I would address the three follow-ups, apply the `fix-is-in-this-repo` check to issue-scout, and add a bot-versus-human distinction to the PR-monitor's feedback state.
