# Evidence guide: where proof lives in a reproduction package

The rubric names evidence; this file says where to stand to see it. One
section per proof family. Each says **where it lives** — in an eval
bundle and, separately, in live mode — and **what good looks like**, as
conditions someone else could apply and land where I would.

One instruction governs all five: **read the issue before you read the
candidate.** Every family below is comparative. The environment is
sufficient *relative to the axis the issue names*; the artifact is the
right artifact *relative to the trigger the issue names*. A grader who
reads the report first has already been told what to see by the person
being graded, and will end up grading the writing. Read the issue,
write down what the trigger and the symptom actually are, then open the
report.

---

## Environment

**Where it lives.** In an eval bundle: the first line or block of the
`## Candidate repro report`, usually beginning `Environment:`. It is
sometimes distributed instead — a version named inside a command
(`npm install prettier@3.9.6`), a platform named in a path, a browser
setting named in a step (`chrome://settings/languages`). Count those;
the check asks whether the facts are recorded, not whether they sit
under a heading. The comparison side lives in two places: the `Issue`
section (the reporter's own `Version:` / `Operating system:` lines) and
the `bug reports` line in the `Repo facts` block, which states what
this repo asks reporters for.

In live mode: the draft file the student wrote, against the issue's
opening comment on GitHub and the repo's `.github/ISSUE_TEMPLATE/`
bug-report form.

**What good looks like.** The version of the software under test and
the platform it ran on are both findable, and any axis the issue itself
names as decisive carries a value. The decisive axis is the one to hunt
for, because it is the one a rushed report drops: a minikube tunnel
issue is Windows-and-driver-specific, so a report with no driver is
unplaceable no matter how good its log is; hyperfine's failure mode
changes with the build profile; p5.js turns on browser language order;
starship turns on the shell. Install method counts as part of the
platform when the repo's own template asks for it. A one-line record —
`yq 4.53.3 (Homebrew), macOS 15.5 (arm64)` — is complete. Prose that
names no values is not a record: "my machine", "a recent version",
"the current setup". For a cannot-reproduce the environment record is
the *most* important part of the report, because the difference between
environments is the finding.

---

## Steps

**Where it lives.** The `Steps:` block of the candidate repro report,
plus everything it points at: fenced commands, file contents written by
`printf` or `cat`, config files quoted inline, playground links,
`repro.mjs`-style scripts. Read it against the issue's own
reproduction, which in a bundle sits in the `Issue` section and is
often a numbered list the candidate can simply re-run.

In live mode: the draft, plus whatever the issue thread supplies, plus
anything in the repo a stranger could clone.

**What good looks like.** A reader who has the named environment can
start from a stated beginning and arrive at the trigger without asking
the author a question. The test is literal: for each input the run
consumes, can I obtain it from this page? Pasted inline, yes. Quoted
from the issue, yes. Produced by a command that is given
(`printf 'foo: bar\n' > some.yml`), yes. A named public artifact — a
CDN URL, a playground, a release tag — yes. "Our internal
`.golangci.yml`, which I cannot share" — no, and that single word ends
it, because the report is then a claim about a repository nobody else
can enter. The other way steps fail is by omission at the trigger: the
sequence runs but skips the step the issue says is required, so a
reader who follows it exactly will not see the bug. Length is not the
measure. Four lines that each name a command beat a page of narration,
and "created `test.txt` with the exact 12 lines from the issue" is a
complete step, because the issue is right there.

---

## Behavior shown

**Where it lives.** The fenced blocks inside the candidate repro
report: command-plus-output transcripts, log excerpts, stack traces,
compiled or rendered output, a described screenshot. Compare them
against the `Issue` section: the reporter's own pasted output, their
expected/actual pair, and the trigger they name. Thread highlights
matter here too, because a maintainer often narrows the trigger after
the fact — ripgrep's owner adds that `--replace` is also required, and
a report whose control drops `-r` is answering that comment.

In live mode: the same, with the issue's artifacts read live from the
thread.

**What good looks like.** Two questions, in this order.

*Did the run use the issue's trigger?* Put the issue's command and the
report's command next to each other, character by character. This is
where the expensive failures live, and they are quiet: a bat issue
about an offset-from-end range met with a run of a prefix range; a jq
issue about `Invalid path expression` met with an edited expression
that left `$b` unbound, so what ran was a compile error; a yq trap
whose input used a colon where the issue used `=`. In each case the
report is long, confident and well formatted, and the artifact is a
different bug. A reader who grades formatting passes all three.

*Does the output show the issue's symptom?* Not an error — *that*
error. An exit-1 argument-validation message is not an exit-101
capacity-overflow panic. Garbled escape sequences with the terminal
still alive are not a crash. And an artifact that merely proves the
software started — a version banner, a session list, a screenshot of a
window that loaded — shows nothing about the reported behaviour at all.

For a cannot-reproduce, invert it: the artifact must be the issue's
trigger producing the *correct* behaviour, shown. The marker log coming
out in order; the prompt rendering where the report says it vanishes.
That is a real observation, and it is what makes the report worth
reading.

---

## Honesty

**Where it lives.** At the seam between two things that are usually a
few lines apart: the report's own `Actual:` / `Result:` statement and
the summary sentences around it, against the fenced output directly
above them. The claim comment carries the other half — its certainty
language ("guaranteed reproducible", "I verified", "confirmed on") is a
claim about evidence and is graded against the same artifacts.

In live mode: the draft's summary lines against the draft's own pasted
output. Do not go to GitHub for this one; the question is entirely
internal to the package.

**What good looks like.** The confidence is bounded by what is shown,
in one direction only — under-claiming is free, over-claiming is the
failure. Three shapes recur:

- **Assertion with no artifact.** "I can confirm this, it's guaranteed
  reproducible", "I verified this race condition", "definitely a
  debounce issue". A diagnosis is not an observation and enthusiasm is
  not a measurement.
- **Narration over the wrong artifact.** The output is pasted honestly
  and then described as something else. This is the dangerous one,
  because the report looks maximally credible: it has evidence, and the
  evidence is misread confidently. "Two machines, same result", said
  over a compile error.
- **Scope inflation.** A real reproduction on one build, reported as
  covering a build the maintainers specifically could not reproduce on.

And the shape that is *not* a failure, which is the point of this
section: "Result: I could NOT reproduce scenario 2", followed by the
attempt, the artifacts, what differed, and a hypothesis about what a
triggering setup needs. That report is more useful to a maintainer than
a vague confirmation, and it passes.

---

## Comms

**Where it lives.** Two candidate texts against two repo facts. The
texts: `## Candidate claim comment` and `## Candidate repro report`.
The facts: the `contribution policy` line and the `bug reports` line in
the `Repo facts` block. Read the policy line to the end — the operative
clause is often the last one, and it often distinguishes between what a
*pull request* must carry and what a *comment* must carry.

In live mode: `CONTRIBUTING.md`, `.github/CONTRIBUTING.md`, any
`AI_POLICY.md` or `AI_USAGE_POLICY.md` they link, the PR template, and
the issue template — plus `scope.md`'s house rules, which override the
wild's defaults inside Path Review.

**What good looks like, on the claim.** Substitution is the test: cover
the issue title and ask whether this comment would still make sense
under a different issue. "Hello sir! Great project, this issue looks
like a good one for me, kindly assign it to me, I will fix it within 2
days guaranteed" survives substitution completely, which is exactly
what is wrong with it — and it adds the two promises nobody can keep, a
fix and a date. A claim that fails substitution names the behaviour,
the version, the file, or the thread pointer it is following, and
promises only to look and report back.

**What good looks like, on the policy.** Sort the requirement by what
it lands on, then check only that. Four live shapes:

- *Silent.* No AI policy at all. Nothing extra is required; the
  comments stand on the proof checks alone.
- *Permissive-with-responsibility.* "Generative AI tools welcome; you
  are responsible for all contributions and must review and understand
  them." A condition on the contributor, not a disclosure ask on the
  comment. Passes without a disclosure line.
- *Own-words.* "Comments to maintainers must be written by humans in
  their own words; AI-generated comments may be hidden." This asks for
  voice, not for a declaration. A comment that reads as one person
  writing about their own run satisfies it, and adding a disclosure
  line was never required.
- *Disclose-all.* "All AI usage in any form must be disclosed, stating
  the tool used and the extent of the assistance." This lands on the
  comment, and this is the one that catches people, because a package
  can be excellent on every proof check and still be non-compliant on
  the one line it does not contain. Assume the comments were
  AI-assisted — for coursework that is simply true — so the obligation
  is live and silence fails it. Compliance looks like: "I used an AI
  assistant to help me organize this report; I ran and verified every
  step myself."

The asymmetry is deliberate and worth internalising: a disclosure
requirement is the only convention that can reject a package whose
proof is perfect, and it is invisible unless you read the policy line
before you read the comments.
