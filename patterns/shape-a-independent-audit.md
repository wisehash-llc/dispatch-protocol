# shape a — independent verification audit

a peer reads source artifacts, runs verification commands where they exist, and
writes findings. use shape a when the work needs a second pair of eyes that did
**not** author the thing being audited.

shape a is the cleanest single-direction shape. the originating agent does not
change in response to the audit; the audit produces evidence the operator
integrates. it is the default first dispatch when learning a new peer's
capability — low blast radius, read-only, a clear artifact at the end.

## when to use

- a config, spec, or diff authored by someone (or some model) other than the
  reviewer needs an independent check.
- you want evidence, not a fix — the integration step stays with the operator.
- you are calibrating a peer and want a bounded, read-only first task.

do **not** use shape a when the author and reviewer are the same model family
and the goal is to catch single-model drift — that is shape b. shape a's second
reader is *different enough* that a single pass is enough.

## variants

- **read-only audit.** the peer reads and reasons; no commands run. lowest
  cost, lowest confidence. acceptable when no executable check exists.
- **audit-with-repro.** the peer runs the verification commands named in the
  brief and quotes their output verbatim. higher confidence; the standard when
  a check exists.
- **multi-source audit.** the peer cross-reads two or more artifacts against
  each other (spec vs. implementation, claim vs. evidence). the most common
  real shape; the brief must list *every* source so the peer halts on an
  unlisted reference rather than guessing.

## edge cases

- **a source is missing or unreadable.** halt that finding and report; do not
  infer the missing file's contents. continue the other dimensions.
- **the brief names a verification command that wasn't actually provided.**
  report the gap instead of inventing a substitute — an invented check is worse
  than an acknowledged absence.
- **the audit surfaces a third artifact not in scope.** name it, recommend a
  follow-up dispatch, do not silently expand scope to read it.

## brief skeleton

```
## Shape   — A (independent verification audit)
## Variant — subprocess, read-only
## Source  — every read-only artifact, by path or SHA
## Scope   — Tier 1 (read+propose). names what is NOT touched.
## Output  — full path to the report + format
## QualityBar — every finding cites a source line; verification output verbatim
## BLOCKED — halt on: missing source, failing non-target command, unlisted ref
```

## verdict

shape a reports a **PASS / PASS-WITH-CONCERNS / FAIL** verdict with a confidence
level. the three-state outcome carries more signal than a binary. verdict
families and confidence are defined in
[`dispatch-result-format.md`](dispatch-result-format.md). a fully-realized shape
a brief and report live in
[`../examples/example-shape-a-audit.md`](../examples/example-shape-a-audit.md).
