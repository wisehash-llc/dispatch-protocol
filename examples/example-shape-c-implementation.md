# example — shape c bounded implementation

a fully-realized shape c dispatch: a bounded feature implemented by following an
existing pattern. authored as a teaching example. the scenario is fictional —
`example-service`'s CLI already supports `--format csv`, and this dispatch adds
`--format json` by mirroring the existing path.

---

## part 1 — the brief

```markdown
# example — add --format json to the CLI

## Shape   — C (bounded implementation)
## Variant — subprocess. single writer; no other dispatch touches this tree.
## Source  — example-service/cli/export.py (the existing --format csv path is the
            reference pattern, lines 40-88)
            example-service/tests/test_export.py (the csv test is the test pattern)
## Scope   — Tier 2 (bounded code). TOUCH: cli/export.py, tests/test_export.py.
            DO NOT touch: the data layer, other CLI commands, config.
## Output  — feature branch `feat/export-json` + PR.
## QualityBar — the new path mirrors the csv path; a json test mirrors the csv
            test; full suite passes; `--format json` smoke-run output included.
## BLOCKED — halt and report if: the csv path is not actually a reusable pattern,
            a json serializer dependency is missing, or the change needs a data-
            layer edit (that would be out of scope → re-route).

## Context
the export command supports csv today. users want json. the csv implementation is
a clean reference: an isolated formatter selected by the --format flag. this is
mechanical pattern-following within tier-2 bounds.
```

## part 2 — the result

```markdown
## verdict
GO

## confidence
high. the json path mirrors the csv path one-to-one; the suite passes including
the new test; the smoke run produced valid json.

## findings
- implemented `_format_json` in cli/export.py (lines 90-108) mirroring
  `_format_csv` (lines 40-58); wired it into the --format dispatch at line 33.
- added `test_export_json` mirroring `test_export_csv`.
- no data-layer or config change was needed — scope held.

## required amends
none.

## verification evidence
`pytest example-service/tests/test_export.py` → 9 passed in 0.8s (verbatim).
smoke: `example-service export --format json` → well-formed json array, 3 records
(first record quoted verbatim in the PR body).

## halt-markers fired
none. the csv path was a clean reusable pattern as the brief assumed.
```

---

## what to notice

- the scope was nameable up front — exactly two files touched, the rest declared
  off-limits — which is what made the work shape-c-ready.
- the result reports `scope held`: the peer confirms it did not drift into the
  data layer, the most common shape c failure.
- the verdict is a clean GO with no amends *because* the brief was tight. a vague
  brief produces GO-WITH-AMENDS or worse.
- the operator (or a follow-on shape b review) still makes the merge call; shape
  c produces the PR, not the merge.
