# shape d — spec authorship under verification dominance

the peer drafts a spec where the verification logic dominates the design. use
shape d when *how do we know this works* is harder than *how do we build it*.

shape d catches the failure mode where a spec reads well but cannot be verified.
when the peer is forced to draft the verification first, the spec either passes
the verification-feasibility test or surfaces its own weakness. the verification
section becomes the load-bearing artifact, not a checkbox at the bottom.

## when to use

- the hard part of the work is *proving* correctness, not writing the code
  (concurrency, money movement, data migrations, anything with subtle invariants).
- the work is tier-3 and needs a written, reviewable design before any code.
- a downstream shape c implementation will follow, and you want its acceptance
  criteria fixed in advance.

shape d is the front half of risky work. its output is a spec the operator
reviews; the implementation is a separate, later dispatch (often shape c, gated
by a shape b review of the spec).

## variants

- **verification-first spec.** the peer writes the acceptance tests / invariants
  before the design prose, then designs to satisfy them.
- **migration spec.** the spec centers on the cut-over and rollback proof — how
  do we know the migration is reversible and lossless — more than on the
  transformation itself.
- **invariant spec.** the spec enumerates the properties that must hold and the
  checks that prove each, for systems where the rules matter more than the flow.

## edge cases

- **the verification turns out infeasible.** that is a *successful* shape d
  outcome, not a failure — the dispatch surfaced that the design can't be
  proven, before code was written. report it as REJECT with the reason.
- **the peer writes design prose and bolts verification on at the end.** that is
  the exact anti-pattern shape d exists to prevent. the brief must require the
  verification section first; a spec that inverts the order has missed the point.
- **scope balloons into implementation.** shape d stops at the spec. if the peer
  starts implementing, it has drifted into shape c without the bounded brief.

## brief skeleton

```
## Shape   — D (spec authorship under verification dominance)
## Variant — subprocess; read-only on the codebase, writes only the spec file
## Source  — the problem statement + every constraint the spec must satisfy
## Scope   — Tier 1/Tier 3 design. writes the spec; implements nothing.
## Output  — spec file path + format; verification section required FIRST
## QualityBar — explicit verification section; executable smoke tests; halt-markers
## BLOCKED — halt on: infeasible verification (report it), missing constraint
```

## verdict

shape d returns the spec plus a **GO / GO-WITH-AMENDS / REJECT** on the design's
verifiability, with confidence. a REJECT that names an unprovable invariant is a
high-value outcome. vocabulary in
[`dispatch-result-format.md`](dispatch-result-format.md); worked example in
[`../examples/example-shape-d-spec.md`](../examples/example-shape-d-spec.md).
