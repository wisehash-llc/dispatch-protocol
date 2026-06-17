# example — shape b cross-model peer review

a fully-realized shape b dispatch: a load-bearing PR reviewed by a different
model family before merge. authored as a teaching example, not lifted from a real
dispatch. the scenario is fictional — `example-service` adds an auth middleware
in a PR, and the change gates on a cross-model review.

---

## part 1 — the brief

```markdown
# example — auth-middleware PR review

## Shape   — B (cross-model peer review)
## Variant — subprocess. author: model family X. reviewer: model family Y.
## Source  — PR branch `feat/auth-middleware` diff @ HEAD
            example-service/middleware/auth.py @ branch HEAD
            example-service/docs/auth-spec.md @ HEAD (the spec the PR claims to implement)
## Scope   — Tier 1 (read+propose). reviewer reads the diff + spec, does NOT
            modify the branch, does NOT merge.
## Output  — example-service/reviews/auth-middleware-review.md (Markdown)
## QualityBar — disagree in writing; every finding cites a diff line or spec line;
            run the test suite on the branch and quote the result verbatim.
## BLOCKED — halt and report if: the branch doesn't build, the spec references a
            file not in Source, or the diff touches files outside middleware/.

## Context
the auth middleware is load-bearing — it sits in front of every request. it was
authored by model family X in one session. single-model drift on an auth path is
unacceptable, so a different model family reads it independently before merge.

## Review axes
1. spec coverage — every rule in auth-spec.md is implemented in the diff.
2. failure-open check — no code path lets an unauthenticated request through.
3. token handling — no token value is logged, cached to disk, or echoed.
4. test adequacy — the tests actually exercise the deny paths, not just allow.
```

## part 2 — the result

```markdown
## verdict
GO-WITH-AMENDS

## confidence
high. the branch built, the suite ran clean (output below), and all four axes
were checked against both the diff and the spec.

## findings
1. spec coverage — PASS. all six rules in auth-spec.md (lines 12-44) are present
   in auth.py (lines 20-91).
2. failure-open check — CONCERN. auth.py line 73 returns `None` on an unknown
   token type and the caller at line 102 treats `None` as "skip auth". that is a
   failure-open path. AMEND required.
3. token handling — PASS. no token value reaches a log call; line 55 logs the
   token *type*, not the value.
4. test adequacy — CONCERN. tests cover allow + expired-token deny, but no test
   exercises the unknown-token-type path from finding 2. AMEND required.

## required amends
1. auth.py:73 — return an explicit deny on unknown token type, not `None`; or
   make the caller treat `None` as deny. (failure-open → fail-closed.)
2. add a test for the unknown-token-type path asserting a 401, not a pass-through.

## verification evidence
`pytest example-service/tests/test_auth.py` → 14 passed in 1.2s (verbatim).

## halt-markers fired
none.
```

---

## what to notice

- the verdict is GO-WITH-AMENDS, not GO. the change is sound *after* two named
  fixes — the qualified yes carries more signal than a binary.
- the reviewer ran the suite and quoted it, then still found a gap the suite did
  not cover (the untested failure-open path). green tests are not a clean bill.
- every finding cites a line. the amends are numbered and directly actionable.
- the failure-open catch is exactly what cross-model review buys: a second model
  family reading the deny paths the authoring model was confident about.
