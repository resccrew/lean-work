---
name: efficient-work
description: Use at the start of any non-trivial coding task (multi-file change, bug hunt, refactor, feature, unclear request) to triage it, route work to the right model, and keep context lean. Skip for one-line or trivial edits.
---

# Efficient work

Goal: same or better result, fewer tokens. Cut waste, never cut information needed to decide.

## Step 0 — Triage (3 checks, ~10 seconds)

1. **Clarity.** Is there a real fork where two readings give different results and a mistake is expensive?
   - Yes: ask the user 1-3 questions, each with options and a recommended default.
   - No, or answerable by reading code / CLAUDE.md: do not ask, read instead.
2. **Size.** Small (1-2 files): do it directly. Large: write a short plan first.
3. **Risk.** Architecture, deletion, prod, security: decide yourself on the strongest model. Routine: delegate.

Trivial tasks (typo, rename, one-liner): skip triage entirely.

## Step 1 — Route by model

| Subtask | Who |
|---|---|
| Understand task, plan, architecture decisions, final review | strongest model (you) |
| Broad code search, reading many files, running tests, bulk edits to a clear plan | subagent, `model: sonnet` |
| Formatting, renames, listing, simple lookups | subagent, `model: haiku` |

Rules: executors never make architecture decisions. The strongest model always reviews the final result. Give every subagent a precise brief and ask for a short report (findings, not file dumps).

## Step 2 — Keep context lean (safe rules)

- Grep/Glob first, then Read only the needed range (`offset`/`limit`). Do not re-read what is already in context.
- Noisy output (tests, builds, logs): show only failures and the summary line. If truncated, say where the full output is.
- Wide searches go to an Explore subagent; only conclusions return to the main context.
- Plan before editing for anything touching 3+ files.
- Run tests after code changes; catching errors early is cheaper than rework.
- Prefer short answers and diffs over rewriting whole files.
- Topic change: suggest `/clear`. Context past ~60% on a long task: suggest `/compact` with a note on what to keep.

## Never do (quality guards)

- Do not refuse to read a whole file when it is the right thing to do.
- Do not use a cheaper model for ambiguous or risky decisions.
- Do not compact away details the current task still depends on.
- Do not hide errors to save tokens.
