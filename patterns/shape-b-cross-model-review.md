# shape b — cross-model peer review

one agent writes (code, spec, plan); a second agent reviews it independently
against the same primary sources. use shape b for load-bearing PRs and spec
drafts where single-model reasoning is a single point of failure.

shape b is the survival primitive that justifies the whole protocol. cross-model
coverage — one model family reading another's work — catches what same-model
review cannot: each model's training drifts in different directions, and the
disagreement-surface is where the receipts hold.

## when to use

- the artifact is load-bearing: it merges to a protected branch, ships to
  production, or anchors a downstream decision.
- the author is a model, and you want a *different* model family to review (the
  cross-model axis is the point).
- the cost of a missed regression exceeds the cost of a second reading.

shape b differs from shape a in intent: shape a audits something the reviewer is
neutral about; shape b is adversarial-by-design against single-model drift, and
the reviewer is told to disagree in writing.

## variants

- **pre-merge review.** the review gates a merge. the verdict must be explicit
  (GO / GO-WITH-AMENDS / REJECT) because a human acts on it directly.
- **design review.** the artifact is a plan or spec, not code. the reviewer
  checks internal consistency, missing cases, and unstated assumptions.
- **adversarial refute.** the reviewer is instructed to *try to break* the
  claim — default to REJECT unless the evidence forces GO. use for the highest-
  stakes artifacts where a false GO is expensive.

## edge cases

- **the reviewer agrees with everything.** treat performative agreement as a
  smell. a shape b review that finds nothing should say what it checked and why
  it is confident, or it has not done the job.
- **the reviewer rewrites instead of reviewing.** shape b produces findings, not
  a competing implementation. if the reviewer wants to rewrite, that is a new
  shape c dispatch, not this one.
- **author and reviewer are the same model family.** the cross-model property is
  lost; flag it. a same-family review is still useful but should be labeled as
  lower-coverage.

## brief skeleton

```
## Shape   — B (cross-model peer review)
## Variant — subprocess; name the reviewer's model family explicitly
## Source  — the artifact under review + every primary source it rests on
## Scope   — Tier 1 (read+propose). reviewer does NOT modify the artifact.
## Output  — review file path + format
## QualityBar — disagree in writing; cite lines; executable verification preferred
## BLOCKED — halt on: source mismatch, unreadable artifact, scope ambiguity
```

## verdict

shape b reports **GO / GO-WITH-AMENDS / REJECT** with a confidence level and, on
anything but a clean GO, a numbered list of required amends. the vocabulary is
defined in [`dispatch-result-format.md`](dispatch-result-format.md); a worked
example is in
[`../examples/example-shape-b-review.md`](../examples/example-shape-b-review.md).
