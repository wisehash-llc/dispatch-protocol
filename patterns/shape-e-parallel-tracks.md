# shape e — parallel independent tracks

two (or more) agents work on independent tracks simultaneously; the
dispatch-sender integrates at the end. use shape e when non-overlapping
deliverables ship faster in parallel than in sequence.

shape e is the rarest shape. the cost of integration at the end usually exceeds
the parallelism gain — *unless* the tracks are genuinely independent. when they
are (two separate config files, two non-overlapping refactors, two independent
docs), shape e compounds the savings. when they are not, the shape collapses
into rework at the merge.

## when to use

- two or more deliverables share no files and no mutable state.
- each track is independently briefable and independently verifiable.
- the wall-clock saving from parallelism exceeds the integration cost.

the test is **non-overlap**, not size. two large independent tracks are a good
shape e; two small tracks that both edit the same file are a bad one — sequence
them instead.

## variants

- **parallel writers, one integrator.** each agent owns a track; the
  dispatch-sender merges. the default shape.
- **fan-out / fan-in.** many short independent tasks dispatched at once, results
  collected and synthesized. use when the work decomposes cleanly into a list.
- **redundant-track (verification).** two agents do the *same* track
  independently and the results are compared. expensive; reserve for work where
  a single agent's error is unacceptable and a second independent pass is the
  cheapest insurance.

## edge cases

- **a hidden shared dependency surfaces.** two "independent" tracks both turn
  out to touch one file or one piece of state. the moment this is detected, the
  tracks are no longer parallel — halt, re-sequence, and note the false
  independence. this is the dominant shape e failure.
- **one track finishes far ahead of the other.** parallelism gave nothing if the
  slow track gates integration. shape e pays off only when track durations are
  comparable; lopsided tracks should have been sequenced.
- **integration conflicts at the merge.** if the tracks were truly
  non-overlapping this should not happen; a conflict is evidence the
  independence assumption was wrong.

## isolation is mandatory

parallel tracks that mutate files **must** run in isolated git worktrees or
separate branches so concurrent edits never collide; the dispatch-sender
integrates at the end. never run two writers against the same working tree. see
[`worktree-isolation.md`](worktree-isolation.md).

## brief skeleton

```
## Shape   — E (parallel independent tracks)
## Variant — subprocess per track; worktree isolation REQUIRED for writers
## Source  — per-track sources; the explicit non-overlap assertion
## Scope   — per track; each track names its files AND asserts no shared state
## Output  — per-track output paths; the integration plan
## QualityBar — per-track verification; integration verification at fan-in
## BLOCKED — halt ALL tracks on: discovered shared dependency / state overlap
```

## verdict

each track returns its own **GO / GO-WITH-AMENDS / REJECT** with confidence; the
integration step gets its own verdict at fan-in. vocabulary in
[`dispatch-result-format.md`](dispatch-result-format.md); worked example in
[`../examples/example-shape-e-parallel.md`](../examples/example-shape-e-parallel.md).
