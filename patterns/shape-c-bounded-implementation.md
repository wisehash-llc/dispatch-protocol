# shape c — bounded implementation

the dispatch-sender scopes the work; the peer implements within tier-2
boundaries — new code following established patterns, bounded refactors, config
changes, dry-run runbook execution. output is a feature branch and a PR.

shape c assumes the peer has earned tier-2 trust. tier-3 work (new architecture,
infrastructure, external integrations, anything touching money / credentials /
production state) does not route through shape c — it routes through shape d
(spec first) or stays with the operator. shape c is the work that lands when the
brief is tight enough that the implementation is mechanical.

## when to use

- the change follows an existing pattern the peer can see in the codebase.
- the scope is bounded and nameable: these files, this behavior, these tests.
- the work is reversible (a branch + PR the operator reviews before merge).

if you cannot write down exactly what the peer touches and what it does **not**
touch, the work is not yet shape-c-ready — tighten the brief or drop to a spec
(shape d) first.

## variants

- **pattern-following feature.** new code that mirrors an existing module. the
  brief points at the reference implementation; the peer matches it.
- **bounded refactor.** a mechanical transformation across a named set of files
  (rename, extract, inline). the test suite is the safety net.
- **dry-run runbook.** the peer executes a runbook in `--dry-run` mode and
  reports what *would* change. nothing mutates; the output is the plan.

## edge cases

- **the scope creeps mid-implementation.** the peer hits an adjacent problem and
  wants to fix it too. the BLOCKED instruction must say: stop, report, do not
  expand. scope creep in shape c is the most common drift.
- **a dependency is missing or the pattern doesn't actually exist.** halt and
  report rather than inventing a pattern. a fabricated convention is worse than
  a blocked dispatch.
- **the change turns out to be tier-3.** if implementation reveals the work
  touches architecture or a protected surface, the peer stops and re-routes to
  the operator. trust tier is re-evaluated, not assumed-forward.

## brief skeleton

```
## Shape   — C (bounded implementation)
## Variant — subprocess; worktree isolation if run alongside other writers
## Source  — the reference pattern + every file in scope
## Scope   — Tier 2 (bounded code). enumerate touched files AND off-limits ones.
## Output  — feature branch + PR; title convention if any
## QualityBar — tests pass, lint clean, smoke verification; report before/after
## BLOCKED — halt on: scope creep, missing pattern, dropped dep, tier-3 reveal
```

## verdict

a shape c dispatch returns the PR plus a **GO / GO-WITH-AMENDS / REJECT** self-
report with confidence and the verification evidence (test output, lint status).
the operator (or a follow-on shape b review) makes the merge call. vocabulary in
[`dispatch-result-format.md`](dispatch-result-format.md); worked example in
[`../examples/example-shape-c-implementation.md`](../examples/example-shape-c-implementation.md).
