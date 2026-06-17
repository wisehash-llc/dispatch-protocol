# example — shape e parallel independent tracks

a fully-realized shape e dispatch: two non-overlapping tracks run at once and the
dispatch-sender integrates at the end. authored as a teaching example. the
scenario is fictional — `example-service` needs two unrelated changes that share
no files: a docs refresh and an isolated config-file update.

---

## part 1 — the brief

```markdown
# example — parallel: docs refresh + config update

## Shape   — E (parallel independent tracks)
## Variant — subprocess per track. worktree isolation REQUIRED (both write).
## Non-overlap assertion — track 1 touches ONLY docs/; track 2 touches ONLY
            config/limits.toml. no shared file, no shared state. asserted by the
            dispatch-sender before dispatch.

### track 1 — docs refresh
## Source  — docs/getting-started.md @ HEAD
## Scope   — Tier 2. TOUCH: docs/getting-started.md only.
## Output  — worktree `wt-docs`, branch `track/docs-refresh`.

### track 2 — config update
## Source  — config/limits.toml @ HEAD
## Scope   — Tier 2. TOUCH: config/limits.toml only.
## Output  — worktree `wt-config`, branch `track/config-limits`.

## QualityBar — each track verifies independently; the integrator runs the full
            suite after merging both branches.
## BLOCKED — halt BOTH tracks immediately if either discovers it must touch a
            file the other owns (the non-overlap assumption would be false).

## Context
two unrelated deliverables. sequencing them wastes wall-clock; they share nothing,
so they parallelize cleanly. each runs in its own worktree so the writes cannot
race.
```

## part 2 — the result

```markdown
## track 1 — docs refresh
verdict: GO. confidence: high.
- rewrote docs/getting-started.md install section; no other file touched.
- verification: markdown link-check clean (output in PR).

## track 2 — config update
verdict: GO. confidence: high.
- raised the burst limit in config/limits.toml (line 14); no other file touched.
- verification: config-schema validation passed (output in PR).

## integration (dispatch-sender)
verdict: GO. confidence: high.
- merged track/docs-refresh and track/config-limits; no conflict (disjoint files,
  as asserted).
- full suite after integration: 41 passed (verbatim in the integration PR).
- worktrees `wt-docs` and `wt-config` removed after merge.

## halt-markers fired
none. the non-overlap assertion held; neither track needed a file the other owned.
```

---

## what to notice

- the brief states the non-overlap assertion explicitly and makes "discovered
  overlap" a halt-both-tracks BLOCKED condition. that is the shape e safety
  catch — the moment independence is false, parallelism is unsafe.
- each track ran in its own worktree, so the concurrent writes could not race
  ([`../patterns/worktree-isolation.md`](../patterns/worktree-isolation.md)).
- integration is its own step with its own verdict and its own verification —
  the tracks finishing is not the same as the work being done.
- the worktrees were cleaned up after merge. a left-behind worktree is clutter
  and a future-confusion surface.
