# example — shape a independent verification audit

a fully-realized dispatch brief and the report that came back. authored as a
teaching example, not lifted from a real production dispatch.

the scenario is fictional: a small service called `example-service` has a
rate-limit implementation in `config.toml` that needs an independent audit
against its own spec doc. the originating agent (the operator's primary
coding agent) wrote both files; the audit goes to a cross-model reviewer
for the second reading.

---

## part 1 — the brief

```markdown
# dispatch-001 — example-service rate-limit audit

## Shape       — A (independent verification audit)
## Variant     — 1 (subprocess — single peer, single output file)
## Profile     — gpt-5.5 high (reasoning effort high; from harness profile `frontier`)
## Source      — example-service/config.toml @ HEAD
                 example-service/docs/rate-limit-spec.md @ HEAD
## Scope       — Tier 1 (read+propose). The reviewer reads both source files,
                 runs the verification command in §6, writes findings. The
                 reviewer does NOT modify config.toml, does NOT modify the
                 spec, does NOT touch any other file under example-service/.
## Output      — example-service/audit-reports/dispatch-001-rate-limit-audit-report.md
                 (Markdown; ~150-250 lines expected)
## QualityBar  — every finding cites a specific line in config.toml or the
                 spec doc by line number; verification command output included
                 verbatim; halt-and-report fires on any input the reviewer
                 cannot read.
## BLOCKED     — halt and report upward if: (a) either source file is missing
                 or returns a permission error, (b) the verification command
                 in §6 fails with a non-rate-limit error (e.g., import error,
                 syntax error in config.toml), (c) the spec doc references a
                 third file not listed in Source.
## Sandbox     — read-only. No cwd shift needed.

## Context

example-service is a small bookkeeping service with a single rate-limit
boundary on its public API. the spec doc was authored last week to document
the intended behavior. the config.toml implementation followed. both
artifacts were authored by the same agent in the same session, which means
single-model drift is a real risk: the implementation may quietly disagree
with the spec without the authoring agent noticing.

the audit is the second reading. cross-model coverage matters because the
authoring agent and the reviewing agent come from different model families,
which catches drift the same-family review would miss.

## Inputs to read

- example-service/config.toml (lines 1-87, the full rate-limit block lives at lines 34-62)
- example-service/docs/rate-limit-spec.md (full doc; ~120 lines)

do NOT read any other file under example-service/. the audit scope is
strictly the rate-limit boundary.

## Audit dimensions

work through these dimensions in order. each one produces a section in the
report.

1. **spec → implementation coverage.** every rule named in the spec doc has a
   corresponding setting in config.toml. flag any spec rule that is not
   implemented and any config setting that has no spec counterpart.

2. **threshold values match.** every numeric threshold in the spec (requests
   per minute, burst capacity, retry-after seconds, backoff multiplier) has
   the same value in config.toml. flag any divergence with both values and
   the source lines.

3. **edge case behavior.** the spec names three edge cases: (a) burst-then-
   idle, (b) sustained-just-under-threshold, (c) clock-skew handling. verify
   the config implements each one. flag any missing edge case.

4. **default-deny posture.** the spec mandates default-deny: any unknown
   client classification falls through to the strictest rate limit. verify
   the config implements this. flag if default-allow is configured anywhere.

5. **observability.** the spec requires that every rate-limit decision emits
   a structured log line with the client classification, the limit applied,
   and whether the request was allowed or rejected. verify the config
   enables this. flag if any rate-limit decision path can run silently.

## Output structure

the report is one Markdown file at the Output path. structure:

```
# dispatch-001 — example-service rate-limit audit (report)

## verdict
[one-line: PASS | PASS-WITH-CONCERNS | FAIL]

## confidence
[high | medium | low — name the basis]

## findings
[one section per audit dimension, each with: dimension name, finding,
 evidence (line references), severity]

## verification commands run
[verbatim command + verbatim output]

## halt-markers fired
[list of BLOCKED conditions hit during the audit, if any]
```

expected length 150-250 lines.

## Discipline

- halt protocol fires per BLOCKED conditions above. when in doubt, halt and
  report; the operator integrates the partial finding rather than the
  reviewer guessing.
- evidence-cite requirement: every finding cites a specific source line. no
  paraphrased findings.
- profile-name discipline: the report verbatim-quotes the profile string in
  use (`gpt-5.5 high`); not a paraphrase like `frontier high reasoning`.
- this is a read-only dispatch. no file mutation, no commits, no PR.
```

---

## part 2 — the report

```markdown
# dispatch-001 — example-service rate-limit audit (report)

## verdict

PASS-WITH-CONCERNS

## confidence

medium. the audit ran against both source files cleanly; the verification
command in §6 of the brief was not provided in the dispatch (see halt-markers
below). prose findings stand; the missing verification evidence is the basis
for medium rather than high.

## findings

### 1. spec → implementation coverage

PASS. every spec rule has a corresponding config setting. the spec defines
five rules (per-minute, per-hour, burst, retry-after, backoff) at lines
22-44 of rate-limit-spec.md. all five are present in config.toml at lines
35-49.

one observation, not a finding: the config defines a sixth setting
(`circuit_breaker_after_failures`, line 61) that the spec does not name.
this is a superset, not a divergence; the spec doc could be updated to
document it.

### 2. threshold values match

PASS. all five numeric thresholds match between spec and config:

| rule | spec value (line) | config value (line) |
|:---|:---|:---|
| per-minute | 60 (line 24) | 60 (line 36) |
| per-hour | 1000 (line 28) | 1000 (line 40) |
| burst capacity | 10 (line 32) | 10 (line 43) |
| retry-after seconds | 30 (line 36) | 30 (line 46) |
| backoff multiplier | 2.0 (line 40) | 2.0 (line 49) |

### 3. edge case behavior

PASS-WITH-CONCERNS. two of three edge cases are implemented:

- burst-then-idle (config.toml line 52, `burst_window_decay = "linear"`) — PASS.
- sustained-just-under-threshold (config.toml line 54, `sustained_grace = 5`) — PASS.
- clock-skew handling — **CONCERN**. the spec describes clock-skew handling
  at lines 92-104 of rate-limit-spec.md (`a request within 200ms of the
  window boundary uses the more permissive window`). config.toml has no
  setting that maps to this behavior. either the spec rule is unimplemented
  or the implementation lives outside config.toml. the brief scope limits
  the reviewer to config.toml; this finding flags the gap without resolving
  it.

severity: medium. clock-skew handling matters under load when window
boundaries become contested; the spec named it explicitly.

### 4. default-deny posture

PASS. config.toml line 58 sets `unknown_classification_fallback = "strict"`
which the spec defines (line 78) as the most restrictive rate limit. no
default-allow path found.

### 5. observability

PASS. config.toml line 60 sets `decision_logging = "structured"`. the spec
requires structured logs at lines 110-118 of rate-limit-spec.md. emission of
the three required fields (classification, limit, decision) is configured
at lines 63-65 of config.toml.

## verification commands run

the brief named a verification command in §6 of the rate-limit-spec.md
("`example-service --check-rate-limit-config config.toml`"). this command
was referenced in the spec but no equivalent runnable command was named in
the dispatch brief itself. see halt-markers below.

prose verification proceeded by cross-reading the two source files line by
line. no executable verification was performed.

## halt-markers fired

one BLOCKED condition fired:

**condition (c) — the spec doc references a third file not listed in
Source.** rate-limit-spec.md line 87 references `rate-limit-recipes.md`
("see rate-limit-recipes.md for worked examples of each edge case"). this
file is not in the dispatch brief's Source list. per the BLOCKED instruction,
the reviewer halted on this reference and continued without reading the
third file. the finding in audit dimension 3 (clock-skew handling) may be
clarified by rate-limit-recipes.md; the reviewer cannot confirm without it.

recommendation: a follow-up dispatch with rate-limit-recipes.md added to
Source would close the clock-skew finding.
```

---

## what to notice in this example

- the brief names eight elements before the body opens. each element prevents
  a specific drift mode.
- the BLOCKED instructions are explicit and named by condition. the reviewer
  knows exactly when to halt.
- the report's verdict is `PASS-WITH-CONCERNS`, not `PASS` or `FAIL`. the
  three-state outcome carries more signal than a binary.
- one halt-marker fired (the unlisted third file) and the report names it
  explicitly. the operator integrating the report knows what additional
  dispatch closes the gap.
- the report cites line numbers throughout. no paraphrased findings.
- the verification command was named in the spec but not in the brief.
  the reviewer reported this gap rather than inventing a substitute. that is
  the halt-and-report discipline in action.

a real dispatch would route the follow-up (with rate-limit-recipes.md added
to Source) back to the same reviewer or to a different one. shape a does not
self-loop; the operator drives the integration step.
