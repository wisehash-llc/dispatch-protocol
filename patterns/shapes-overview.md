# shapes overview

five routing shapes. each names a different decision about how work moves
between an originating agent (or human operator) and a peer agent. picking
the right shape is the load-bearing call.

v0.2+ promotes each shape to its own pattern doc with operational variants,
edge cases, and one example per shape. v0.1 stays at one paragraph per shape.

## shape a — independent verification audit

the peer reads source artifacts, runs verification commands, and writes
findings. use when the work needs a second pair of eyes that did not author
the thing being audited. output is a dispatch report. quality bar: specific,
sourced findings; executable verification commands where applicable;
halt-markers on uncertainty.

shape a is the cleanest single-direction shape. the originating agent does
not change in response to the audit; the audit produces evidence that the
operator integrates. shape a is the default first dispatch when learning a
peer's capability.

## shape b — cross-model peer review

one agent writes (code, spec, plan); a second agent reviews independently
against the same primary sources. use for load-bearing PRs and spec drafts
where single-model reasoning is a single point of failure. output is a
review file. quality bar: executable verification preferred; prose-only is
acceptable but lower confidence.

shape b is the survival primitive that justifies the whole protocol.
cross-model coverage (claude reading codex's work, or vice versa) catches
what same-model review cannot: each model's training drifts in different
directions, and the disagreement-surface is where the receipts hold.

## shape c — bounded implementation

the dispatch-sender scopes the work; the peer implements within tier-2
boundaries (new code following established patterns, bounded refactors,
config changes, dry-run runbook execution). output is a feature branch and
PR. quality bar: tests, lint, smoke verification.

shape c assumes the peer has earned tier-2 trust. tier-3 work (new
architecture, infrastructure changes, external integrations) routes through
shape d instead. shape c is the work that lands without further review when
the brief is tight enough.

## shape d — spec authorship under verification dominance

the peer drafts a spec where the verification logic dominates the design.
use when *how do we know this works* is harder than *how do we build it*.
output is a spec file. quality bar: explicit verification section,
executable smoke tests, halt-markers on dependencies.

shape d catches the failure mode where a spec reads well but cannot be
verified. when the peer is forced to draft verification first, the spec
either passes the verification-feasibility test or surfaces its own
weakness. the verification section becomes the load-bearing artifact, not a
checkbox at the bottom.

## shape e — parallel independent tracks

two agents work on independent tracks simultaneously; the dispatch-sender
integrates at the end. use when two non-overlapping deliverables can ship
faster in parallel than sequentially. no shared state mutation; worktree
isolation required.

shape e is the rarest shape. the cost of integration at the end usually
exceeds the parallelism gain unless both tracks are genuinely independent.
when they are — two separate config files, two non-overlapping refactors,
two independent docs — shape e compounds the savings. when they are not, the
shape collapses into rework.

---

## picking between shapes

a quick decision table:

| if the work is… | the shape is… |
|:---|:---|
| reviewing an artifact someone else authored | shape a |
| reviewing an artifact the same author would otherwise self-review | shape b |
| implementing within established patterns and bounded scope | shape c |
| drafting a spec where verification is the hard part | shape d |
| two non-overlapping deliverables that can ship in parallel | shape e |

when uncertain between a and b: ask whether the authoring agent and the
reviewing agent are the same model family. same model family → shape b
(cross-model coverage matters most here). different model family or human
author → shape a (the second pair of eyes is enough).

when uncertain between c and d: ask whether the verification logic is harder
than the implementation. if yes → shape d, the verification-first frame
catches what shape c would miss.
