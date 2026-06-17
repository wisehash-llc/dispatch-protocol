# shapes overview

five routing shapes. each names a different decision about how work moves between
an originating agent (or human operator) and a peer agent. picking the right
shape is the load-bearing call.

each shape now has its own doc with operational variants, edge cases, a brief
skeleton, and a worked example. this page is the index and the decision table.

## the five shapes

- **[shape a — independent verification audit](shape-a-independent-audit.md)** —
  a peer reads source artifacts, runs verification, and writes findings. use when
  the work needs a second pair of eyes that did not author the thing being
  audited. reports PASS / PASS-WITH-CONCERNS / FAIL.
- **[shape b — cross-model peer review](shape-b-cross-model-review.md)** — one
  agent writes; a different model family reviews independently against the same
  sources. use for load-bearing PRs and specs where single-model reasoning is a
  single point of failure. reports GO / GO-WITH-AMENDS / REJECT.
- **[shape c — bounded implementation](shape-c-bounded-implementation.md)** — the
  sender scopes the work; the peer implements within tier-2 bounds. use for new
  code following established patterns, bounded refactors, config changes, dry-run
  runbooks. returns a branch + PR.
- **[shape d — spec authorship under verification dominance](shape-d-spec-under-verification.md)**
  — the peer drafts a spec where the verification logic dominates the design. use
  when *how do we know this works* is harder than *how do we build it*.
- **[shape e — parallel independent tracks](shape-e-parallel-tracks.md)** — two or
  more agents work non-overlapping tracks at once; the sender integrates at the
  end. use when independent deliverables ship faster in parallel. requires
  worktree isolation.

the result format for both verdict families (PASS/… for audits, GO/… for
reviews, builds, and specs) is defined in
[`dispatch-result-format.md`](dispatch-result-format.md).

## picking between shapes

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

when uncertain between c and d: ask whether the verification logic is harder than
the implementation. if yes → shape d, the verification-first frame catches what
shape c would miss.
