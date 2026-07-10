# Job Pipeline — Plan & Learning Guide

This doc is written for **Claude**, not just for you (the human reading this).
If you're the human: open Claude Code in this repo and say something like
*"Read plan.md and let's start on the scraper."* Don't skip ahead and just
ask Claude to build the whole thing — the point of this project is to learn
by making the decisions yourself, with Claude as a guide, not a code vending
machine.

## Instructions to Claude

The human using this repo is new to programming and to Claude Code. For each
stage below:

1. **Ask before building.** Use a brainstorming/interview approach (if the
   `superpowers` plugin is installed, use its `brainstorming` skill) — walk
   through the open questions for that stage with the human, one at a time,
   before writing any code. Don't assume an answer just because it's the
   common default.
2. **Explain the "why," not just the "what."** When you make a technical
   recommendation, give the one-sentence reason, especially where a simpler
   option (stdlib, no dependency, plain CSV) is being passed over.
3. **Small, working steps.** Get one thing running end-to-end before adding
   the next. A scraper that finds 3 real jobs beats a framework for finding
   any job.
4. **Let the human write some of the code.** Don't just generate the whole
   file. Especially in Stage 1, pair — you can scaffold, but leave the parts
   that teach a concept (a loop, a conditional, a function signature) for
   the human to attempt first.

## Project Overview

A three-stage pipeline:
1. **Scraper** — finds job postings from a handful of sources.
2. **Evaluator** — scores each posting against the human's resume/preferences.
3. **Submitter** — sends an application (email + resume/cover letter) for
   postings that pass the bar.

v1 target: runs manually from the command line, stores state in `data/jobs.csv`,
no hosted database, no scheduling yet. Scheduling and a real DB are v2+
problems — don't build them now.

## Stage 1: Scraper

Open questions to resolve with the human before writing code:
- Which job sites/sources specifically? (Affects whether Playwright alone is
  enough or whether a source needs Firecrawl / has an API.)
- How does the scraper know it's already seen a posting? (Dedup key —
  probably URL, or company+title+posted-date hash.)
- What fields matter enough to extract? (title, company, location, comp,
  remote/onsite, posting URL, raw description — probably not more than this
  for v1.)

Default technical shape (confirm/adjust with the human, don't just apply):
- **Playwright** for sources that need real browser rendering / login.
- **Firecrawl** as a fallback for sites that are annoying to scrape directly
  (heavy anti-bot, complex pagination) — costs money per call, so only
  reach for it where Playwright is genuinely painful.
- Raw scraped text → one Claude API call to extract structured fields into
  the CSV row. Don't hand-write a parser for every site's HTML.
- Append to `data/jobs.csv`, keyed by dedup hash, skip if already present.

## Stage 2: Evaluator

Open questions:
- What does "good fit" mean, concretely? (Get this from the human's actual
  resume/preferences, not a generic rubric.)
- Score as a number, a pass/fail, or a category? Keep it simple for v1.
- Where does the resume/preference data live? (A text file the human edits
  is enough — no need for a settings UI.)

Default shape: one Claude API call per posting, given the resume text and
the posting fields, returns a score + one-line reasoning. Write both back to
the CSV row.

## Stage 3: Submitter

Open questions:
- What does "submit" mean for the sources chosen in Stage 1? (Email the
  employer directly? A platform form? This determines almost everything
  about this stage — don't build a generic emailer if none of the sources
  actually work by email.)
- Does every passing job get auto-submitted, or does the human review first?
  (Strongly recommend a review step for v1 — auto-sending applications
  without a human check is asking for a bad email to go out.)

Default shape (if email-based): `smtplib` + an app password from `.env`,
resume/cover letter generated or filled from a template (the `docx`/`pdf`
Claude Code skills can help here), draft shown to the human before sending.
`humanizer` (https://github.com/blader/humanizer) can be added later to make
the email copy read less like a template — not needed for v1.

## Explicitly Deferred (don't build until v1 works end-to-end)

- Scheduling (cron / GitHub Actions)
- A real database (Supabase, SQLite, etc.) — CSV is fine at this scale
- Multi-account / multi-resume support
- Retry/error-recovery logic beyond "print the error and skip"
