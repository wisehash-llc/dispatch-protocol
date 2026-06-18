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
peer agent loads context, and returns a result in a matching format. the
protocol exists because peer review between agents is a survival primitive:
single-model reasoning is a single point of failure, and the only practical
defense is a second agent reading the same sources independently and
disagreeing in writing.

what this repo is:
- a set of markdown documents
- a brief template + a result format
- one worked example per shape

what this repo is not:
- a runtime
- an installable package
- a sales methodology
- a manifesto

the patterns make their own case through receipts.

## the five shapes

each shape names a different routing decision and now has its own doc with
variants, edge cases, a brief skeleton, and a worked example. the index and the
decision table live in [`patterns/shapes-overview.md`](patterns/shapes-overview.md).

- **[shape a — independent verification audit](patterns/shape-a-independent-audit.md)**
  — a peer reads source artifacts, runs verification, and writes findings. use
  when the work needs a second pair of eyes that did not author the thing.
- **[shape b — cross-model peer review](patterns/shape-b-cross-model-review.md)**
  — one agent writes; a different model family reviews independently. use for
  load-bearing PRs and specs where single-model reasoning is a single point of
  failure.
- **[shape c — bounded implementation](patterns/shape-c-bounded-implementation.md)**
  — the sender scopes the work; the peer implements within tier-2 bounds.
- **[shape d — spec authorship under verification dominance](patterns/shape-d-spec-under-verification.md)**
  — the peer drafts a spec where the verification logic dominates the design.
- **[shape e — parallel independent tracks](patterns/shape-e-parallel-tracks.md)**
  — non-overlapping tracks run at once; the sender integrates at the end.

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

## the return half

a brief with no agreed result format produces reports that drift. the result
format — verdict, confidence, cited findings, numbered amends, verification
evidence, halt-markers — is the brief's mirror. it defines two verdict families
(PASS / PASS-WITH-CONCERNS / FAIL for audits; GO / GO-WITH-AMENDS / REJECT for
reviews, builds, and specs) and an intentionally-coarse compute-effort receipt.
see [`patterns/dispatch-result-format.md`](patterns/dispatch-result-format.md).

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

## parallel work

when two tracks run at once, isolation is mechanical, not optional: each writer
runs in its own git worktree so concurrent edits never collide, and the sender
integrates at the end. see
[`patterns/worktree-isolation.md`](patterns/worktree-isolation.md).

## worked examples

[`examples/`](examples/) ships one fully-realized dispatch per shape — the brief
that went out and the result that came back, all sanitized teaching artifacts:

- [shape a — audit](examples/example-shape-a-audit.md)
- [shape b — review](examples/example-shape-b-review.md)
- [shape c — implementation](examples/example-shape-c-implementation.md)
- [shape d — spec](examples/example-shape-d-spec.md)
- [shape e — parallel](examples/example-shape-e-parallel.md)

## status

- **version:** v0.2.
- **license:** apache 2.0 ([LICENSE](LICENSE)).
- **origin:** patterns emerged over several months of production use across
  mixed-model agent peers. the protocol is the codification.

paired with [`wisehash-llc/mcp-patterns`](https://github.com/wisehash-llc/mcp-patterns),
the two repos compose: dispatch-protocol routes work between AI peers;
mcp-patterns wires the tools those peers reach for. either is useful on its own.

operator's broader work lives at [wisehash.io](https://wisehash.io).
