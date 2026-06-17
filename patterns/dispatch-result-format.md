# the dispatch result

the eight-element brief ([`../templates/dispatch-brief.md`](../templates/dispatch-brief.md))
is the outbound half of a dispatch. this is the return half: the shape of the
report a peer sends back. a brief with no agreed result format produces reports
that drift — verdicts buried in prose, findings without citations, no clear
signal the operator can act on. the result format is the brief's mirror.

## the structure

a dispatch result carries, in order:

1. **verdict** — one line, from the vocabulary below.
2. **confidence** — high / med / low, with the basis named (not a bare label).
3. **findings** — one section per audit dimension or review axis, each with the
   finding, the evidence (cited by file:line or SHA), and a severity.
4. **required amends** — a numbered, actionable list of what must change. empty
   on a clean pass; the load-bearing section on anything else.
5. **verification evidence** — the commands run and their output verbatim, or an
   explicit statement that no executable check was available.
6. **halt-markers fired** — every BLOCKED condition the peer hit, named.

## the two verdict families

different shapes answer different questions, so they use different verdicts.

**audits (shape a)** answer *does this hold up?* — they report:

- **PASS** — holds up; no concerns.
- **PASS-WITH-CONCERNS** — holds up, but named issues the operator should weigh.
- **FAIL** — does not hold up; the finding section says why.

**reviews, builds, and specs (shapes b / c / d / e)** answer *should this
proceed?* — they report:

- **GO** — proceed as-is.
- **GO-WITH-AMENDS** — proceed *after* the numbered amends are integrated. the
  amends are required, not optional; integrating them in the same work session
  is the discipline (see below).
- **REJECT** — do not proceed; the result names what would have to change for a
  re-dispatch.

both families carry the three-state nuance deliberately. a binary pass/fail
throws away the most useful signal — the *qualified* yes.

## confidence is not optional

confidence names how much weight the verdict can bear and why. "high — both
sources read cleanly and the verification command passed" is a confidence
statement; "high" alone is not. low confidence with a clear basis ("the
verification command named in the brief was not provided, so the verdict rests
on prose cross-reading only") is more useful than false high confidence.

## GO-WITH-AMENDS and same-session integration

GO-WITH-AMENDS is the most common non-trivial verdict, and its discipline is:
the required amends are integrated *in the same work session*, within the scope
already approved, before the work is considered done. amends that are deferred
silently become amends that are forgotten. if an amend needs new authority it
could not have had (a wider scope, a tier-3 sign-off), that is the one case it
waits — and the result says so explicitly.

## the compute-effort receipt is intentionally coarse

a result may carry a one-line effort receipt so the operator can see what the
dispatch cost in rough terms — for example `effort: high, wall: minutes-scale`.
keep it coarse on purpose. a public or shared result does **not** carry raw
token counts, exact model name-and-version, or anything from which operating
economics could be reconstructed. the receipt signals magnitude, not a meter
reading. coarseness here is a feature, not an omission.
