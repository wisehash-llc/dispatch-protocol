# dispatch-protocol

a pattern library for routing work between AI peers. shapes, templates, and the
discipline that keeps multi-agent dispatch from collapsing into noise.

> this repo builds on nate b. jones's work on agent infrastructure — the
> four-layer thinking stack, the open-brain thesis, the argument that the
> agent stack is real. alan shurafa's contributions to the openclaw / ob1
> ecosystem informed several of the patterns documented here. i am ashton
> papi; i operate wisehash, and i started codifying these shapes because
> the agents i run weekly stopped colliding with each other once the
> routing layer was named.

wisehash routes work between AI peers in five shapes — independent audit,
cross-model peer review, bounded implementation, spec authorship under
verification dominance, and parallel independent tracks. every dispatch
carries an eight-element brief header that pre-flights the work before the
peer agent loads context. the protocol exists because peer review between
agents is a survival primitive: single-model reasoning is a single point of
failure, and the only practical defense is a second agent reading the same
sources independently and disagreeing in writing.

what this repo is:
- a set of markdown documents
- a brief template
- one sanitized worked example

what this repo is not:
- a runtime
- an installable package
- a sales methodology
- a manifesto

the patterns make their own case through receipts.

## the five shapes

each shape names a different routing decision. full descriptions live in
[`patterns/shapes-overview.md`](patterns/shapes-overview.md); v0.2+ promotes
each shape to its own pattern doc with variants and edge cases.

- **shape a — independent verification audit.** a peer reads source artifacts,
  runs verification commands, and writes findings. use when the work needs a
  second pair of eyes that did not author the thing being audited.
- **shape b — cross-model peer review.** one agent writes; a second agent
  reviews independently against the same primary sources. use for load-bearing
  PRs and spec drafts where single-model reasoning is a single point of failure.
- **shape c — bounded implementation.** the dispatch-sender scopes the work;
  the peer implements within tier-2 boundaries. use for new code following
  established patterns, bounded refactors, config changes, dry-run runbooks.
- **shape d — spec authorship under verification dominance.** the peer drafts a
  spec where the verification logic dominates the design. use when *how do we
  know this works* is harder than *how do we build it*.
- **shape e — parallel independent tracks.** two agents work on independent
  tracks simultaneously; the dispatch-sender integrates at the end. use when
  two non-overlapping deliverables ship faster in parallel than sequentially.

## the eight-element brief

every dispatch ships with a pre-flight header. the header prevents drift
between brief, work, and report. the full template lives in
[`templates/dispatch-brief.md`](templates/dispatch-brief.md). the eight
elements are:

1. **shape** — A / B / C / D / E
2. **variant** — subprocess / fork-within-harness / cross-host
3. **profile** — model + reasoning effort (exact name from the harness config)
4. **source** — exact file paths or commit SHAs of every read-only artifact
5. **scope** — tier 1 / tier 2 / tier 3; what the peer touches AND what it does not
6. **output** — full path to the dispatch report + format
7. **qualitybar** — verification commands required; halt-and-report discipline; evidence-cite expectations
8. **blocked** — explicit halt-and-report instructions for conflicts, dep drops, scope ambiguity, or sandbox boundaries

picking the wrong shape is the most common drift source. the brief catches it
before the peer loads context.

## why peer review between agents

a single agent reasoning alone is a single point of failure. context drift,
hallucinated dependencies, misread file paths, missed regressions — none of
these are bugs in one model that better prompting fixes. they are failure modes
of single-agent reasoning. the only practical defense is a second agent reading
the same sources independently and disagreeing in writing. cross-model peer
review (claude reading codex's work, or vice versa) catches what same-model
review cannot: each model's training drifts in different directions, and the
overlap is where the receipts hold.

this protocol exists because the operator running these agents needed a way to
route the second reading without re-explaining context every time. the brief
template is that interface.

## one worked example

[`examples/example-shape-a-audit.md`](examples/example-shape-a-audit.md) ships
a single fully-realized shape a dispatch — the brief that went out and the
report that came back. v0.2+ adds one example per shape.

## status

- **version:** v0.1 — minimal-but-credible.
- **license:** apache 2.0 ([LICENSE](LICENSE)).
- **origin:** patterns emerged over several months of production use across
  mixed-model agent peers. the protocol is the codification.

operator's broader work lives at [wisehash.io](https://wisehash.io).
