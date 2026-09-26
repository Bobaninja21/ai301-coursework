# Voice guide: how I talk upstream

## Who I am in threads

I am a student making my first open-source contributions, working in
Path Review this term. I know Python, FastAPI and SQLAlchemy well
enough to read a stack trace and stand a service up; I have never been
a maintainer, and I do not know this codebase's history. What a reader
can expect from me is that everything I state as a fact I watched
happen on my own machine, that I will say which parts I am unsure of,
and that I write my own comments — an assistant helps me organise and
tighten them, and where a repo asks, I say so.

The register these rules protect is *junior and useful*. Not
deferential (deference spends a maintainer's reply on reassurance), not
breezy (breeziness makes a stranger check my work twice).

## Rules I write by

### Rule: Promise only the next thing I will look at

I claim investigation. I never claim a fix, a timeline, or a date, and
I never ask to be assigned or to have the issue held for me. If I stop
working on it, the comment I already posted should not have cost anyone
anything.

- Wrong: "I'll take this one — should have a PR up by the weekend."
- Right: "I'd like to investigate this one. My next step is reading
  `contract_repo_path` in `src/modules/directory.rs`, where the
  reporter thinks the `None` return drops the module, and I'll report
  back what I find."

### Rule: Say what I ran, not how sure I am

Certainty words are a substitute for evidence, and I reach for them
exactly when I have least. If I want the reader to believe something,
the paste goes in the comment; if I cannot paste it, I do not assert
it. "Confirmed", "definitely", "guaranteed", "100%" earn nothing an
output block would not earn better.

- Wrong: "Confirmed — this is definitely a session-binding problem,
  100% reproducible on my end."
- Right: "On SQLAlchemy 2.0.32 the probe raises before it reaches the
  database: `ArgumentError: Textual SQL expression 'SELECT 1' should
  be explicitly declared as text('SELECT 1')`. Full transcript below.
  I have not checked whether 1.4 behaves the same way."

### Rule: Report the failure to reproduce as loudly as the success

If I could not reproduce it, that is the comment I post — with the same
environment record, the same steps, the same artifacts, plus what
differed from the report. I do not quietly stop, and I do not soften it
into "having some trouble". A negative result from a stated environment
is data the maintainer does not otherwise have.

- Wrong: "Hmm, having some trouble getting this to happen on my
  machine — might just be me. Will keep trying!"
- Right: "I could not reproduce this on Linux + zsh with the report's
  exact layout and config (steps and prompt output below). The report
  is macOS + fish; my shell resolves `PWD` to the physical path for
  symlinked directories, which may be why the module still renders. A
  fish shell looks necessary to hit this."

### Rule: Name the difference before someone else finds it

Whenever my environment, version, or input differs from the one the
issue names, that difference goes in the first line of the report — not
in a footnote, and not nowhere. An undisclosed version gap turns a real
observation into false evidence, and it is the easiest way for a
well-meaning report to waste a maintainer's afternoon.

- Wrong: "Reproduced the crash, traceback below." (run on 1.5.3,
  against an issue confirmed on main)
- Right: "Reproduced on 2.3.1; the issue is filed against main, so I
  also checked that the affected call is unchanged between them.
  Traceback below."

### Rule: Write the comment to this issue, not to GitHub

Every comment I post has to fail the substitution test: cover the issue
title, and the comment should stop making sense. I do not open with
praise for the project, I do not introduce myself at length, and I do
not thank anyone for something they have not done yet. The first
sentence names the behaviour or the file.

- Wrong: "Hello! Great project, I use it every day. I'm very
  interested in contributing and this looks like a good first issue
  for me."
- Right: "The `/health` DB probe passes the bare string `SELECT 1` to
  `db.execute()` in `api/routes/health.py`, which SQLAlchemy 2.x
  rejects before it ever reaches Postgres — so the endpoint reports
  the database down while it is up. I'd like to investigate this one."

## Things I never post

- A date, a deadline, or a promised fix. Ever, in any wording,
  including "should be quick".
- "Please assign this to me", "kindly reserve this for me", or any
  request to hold an issue. In Path Review a classmate's claim does
  not block me and mine does not block them, so the ask is pure noise;
  in the wild it reads as entitlement.
- "+1", "same here", "any update on this?", or a bump of any kind.
- A cause I have not observed. Diagnosis language ("this is a race
  condition", "the regression is in X") only after I have shown the
  thing that made me think so.
- The word "just" about someone else's code — "you just need to wrap
  it in `text()`" — which makes a fix sound free and the author sound
  careless.
- A wall of AI-shaped scaffolding: bolded section headers on a
  three-sentence comment, "I hope this helps!", a summary of what I am
  about to say before I say it. If a repo asks for disclosure I give it
  in one plain sentence naming the tool and what it did, and the rest
  of the comment still reads as me.
- Anything posted before I have run it past `repro-check`. The tool
  exists so that the draft I am most pleased with is the one that gets
  checked hardest.
