# example — shape d spec authorship under verification dominance

a fully-realized shape d dispatch: a spec for a feature where proving correctness
is harder than building it. authored as a teaching example. the scenario is
fictional — `example-service` needs an account balance-transfer, and the money
invariants dominate the design.

---

## part 1 — the brief

```markdown
# example — balance-transfer spec

## Shape   — D (spec authorship under verification dominance)
## Variant — subprocess. read-only on the codebase; writes only the spec file.
## Source  — example-service/docs/ledger-model.md @ HEAD (the existing account
            model + the invariants it already guarantees)
## Scope   — design. WRITES: docs/balance-transfer-spec.md. IMPLEMENTS nothing.
## Output  — docs/balance-transfer-spec.md. the verification section comes FIRST,
            before the design prose.
## QualityBar — the spec opens with the invariants + the checks that prove each;
            every invariant has an executable or precisely-described check;
            halt-markers on any invariant that cannot be proven.
## BLOCKED — halt and report if: an invariant turns out unprovable (report it as
            REJECT with the reason), or ledger-model.md references a constraint
            not in Source.

## Context
a transfer moves value between two accounts. the hard part is not the code — it
is proving the money invariants hold under concurrency and failure: no value
created, no value destroyed, no partial transfer left half-applied.
```

## part 2 — the result

```markdown
## verdict
GO-WITH-AMENDS

## confidence
high. the verification section was written first and every invariant but one has
a clean check; the one exception is named as a required amend, not hidden.

## the verification section (the load-bearing artifact)
invariants and their proofs:
1. conservation — sum of both balances before == sum after. check: a property
   test over random transfer sequences asserting the sum is constant.
2. no-negative — no account goes below zero (no overdraft). check: a test that
   a transfer exceeding the source balance is rejected, balances unchanged.
3. atomicity — a transfer either fully applies or not at all; no half-state.
   check: a fault-injection test that kills the process mid-transfer and
   asserts, on restart, that the ledger shows either both legs or neither.
4. idempotency — a retried transfer with the same id does not double-apply.
   check: AMEND — the spec as drafted has no transfer-id, so a retry would
   double-apply. the design needs an idempotency key before this is provable.

## required amends
1. add an idempotency key to the transfer design so invariant 4 is checkable;
   without it, a retry double-applies value. this is a design gap the
   verification-first frame surfaced.

## design prose
[the account-pair locking order, the write-ahead sequence, and the rollback path
follow — each written to satisfy invariants 1-3, and to satisfy 4 once the
idempotency-key amend is integrated.]

## halt-markers fired
none. ledger-model.md was self-contained.
```

---

## what to notice

- the verification section is first and is the bulk of the artifact. the design
  prose is written *to satisfy the checks*, not the other way around.
- writing the proofs first surfaced a real design gap — the missing idempotency
  key — that design-first prose would likely have shipped. that is the entire
  point of shape d.
- the verdict is GO-WITH-AMENDS: the spec is sound once the one gap is closed.
  had an invariant been genuinely unprovable, the correct verdict would have
  been REJECT — and that, too, is a successful shape d outcome, caught before any
  code was written.
