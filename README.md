# lean-work

> Get the same (or better) results from Claude Code while burning fewer usage limits.

[Русская версия](README.ru.md)

**Status: v0.1 (experimental).** The rules are based on how Claude Code spends tokens, but the savings have **not been benchmarked yet**. The A/B protocol is in [`evals/tasks.md`](evals/tasks.md). Numbers will be published here once measured; until then, treat this as a hypothesis you can try.

## The problem

Every message in a Claude Code session re-sends the whole conversation. Limits are mostly burned by *context bloat*, not by the useful work:

- reading whole files when a few lines were needed
- pasting 500 lines of passing tests to find one failure
- doing bulk reading and editing on the most expensive model
- one endless session carrying stale context from earlier tasks
- misunderstanding the request, then redoing the work

## The idea

Cut waste, never cut information needed to decide. `lean-work` adds one skill, `efficient-work`, that Claude loads at the start of non-trivial tasks.

### 1. Triage (step 0)

Three quick checks before working:

| Check | Decision |
|---|---|
| **Clarity**: is there a real fork where a wrong guess is expensive? | Ask 1-3 questions with options and a recommended default. Otherwise read the code instead of asking. |
| **Size** | Small: just do it. Large: short plan first. |
| **Risk**: architecture, deletion, prod, security? | Decide on the strongest model. Routine work can be delegated. |

Trivial tasks (typo, rename, one-liner) skip triage entirely, so the skill adds no overhead there.

### 2. Route work by model

| Subtask | Model |
|---|---|
| Understanding, planning, architecture, final review | strongest model |
| Broad search, reading many files, running tests, bulk edits to a clear plan | `sonnet` subagent |
| Formatting, renames, simple lookups | `haiku` subagent |

Executors never make architecture decisions, and the strongest model always reviews the result.

### 3. Keep context lean (safe rules only)

- Grep/Glob first, then read only the needed range.
- Don't re-read what is already in context.
- Noisy output: surface failures and the summary line, and say where the full output is.
- Wide searches go to a subagent; only conclusions return.
- Plan before touching 3+ files; run tests after code changes.
- Suggest `/clear` on topic change and `/compact` on long tasks.

### Quality guards

The skill explicitly forbids the optimizations that risk quality: refusing to read whole files, using cheap models for ambiguous or risky decisions, compacting away details the task still needs, hiding errors to save tokens.

## Install

```
/plugin marketplace add resccrew/lean-work
/plugin install lean-work@lean-work
/reload-plugins
```

Try it without installing: `claude --plugin-dir <path-to-clone>`.

## How a rule earns its place

Each rule must pass two gates on the tasks in [`evals/tasks.md`](evals/tasks.md):

1. **Quality**: success is equal or better (tests green, acceptance met).
2. **Savings**: fewer tokens than the baseline run.

Rules that fail either gate are removed. Rules that improve quality without saving tokens (like "plan first") stay.

## Roadmap

- [ ] Run the A/B benchmark and publish results
- [ ] Hooks that enforce the rules (e.g. cap huge tool output with a way to get the full text)
- [ ] Status line showing context usage
- [ ] Tuning the triage threshold from real data

## Contributing

Issues and PRs welcome. If you change or add a rule, include an eval task showing quality and token impact.

## License

MIT, see [LICENSE](LICENSE).
