# jpm-job-pipeline

A job scraper → evaluator → submission pipeline, built as a learning project.

## First time setup

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
playwright install chromium
cp .env.example .env   # then fill in your API keys
```

## Getting started with Claude Code

Open a terminal in this folder and run `claude`, then say:

> Read plan.md and let's start on the scraper.

Don't ask it to build the whole pipeline in one go — `plan.md` is written
to walk through each stage's design decisions with you first.

## Structure

```
plan.md          — the design/learning guide (read this first)
src/scraper/     — Stage 1: finds job postings
src/evaluator/   — Stage 2: scores postings against your resume
src/submitter/   — Stage 3: sends applications
data/            — jobs.csv lives here (not committed — it's your personal data)
```
