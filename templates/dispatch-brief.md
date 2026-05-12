# dispatch brief template

every dispatch in this protocol carries an eight-element header before the
body. the header is a pre-flight checklist. it prevents drift between brief,
work, and report.

## Shape       — A | B | C | D | E (see patterns/shapes-overview.md)
## Variant     — 1 (subprocess) | 2 (fork-within-harness) | 3 (cross-host SSH)
## Profile     — model + reasoning effort (exact name from your harness config)
## Source      — exact file paths or commit SHAs of every read-only artifact
## Scope       — Tier 1 (read+propose) | Tier 2 (bounded code) | Tier 3 (propose-architecture-but-wait); name what the peer touches AND what it does NOT touch
## Output      — full path to the dispatch report + format (Markdown, PR, memory note)
## QualityBar  — verification commands required; halt-and-report discipline; evidence-cite expectations
## BLOCKED     — explicit halt-and-report instructions for conflicts, dep drops, scope ambiguity, or sandbox boundaries
## Sandbox     — workspace-write | read-only; cwd shift if needed

## Context
[the why — what the work is for, what just happened, what the peer needs to know to start cold]

## Inputs to read
[explicit list of read-only sources by path or URL]

## Audit dimensions / Tasks
[the actual work — bulleted, scoped, ordered]

## Output structure
[exactly what shape the report takes — sections, headers, expected length]

## Discipline
[explicit reminders: halt protocol, evidence-cite requirement, profile-name discipline,
worktree isolation for any state mutation]

---

## per-element notes

each element earns its slot. notes below explain what catches drift when the
element is wrong.

- **shape** — picking the wrong shape is the most common drift source. an
  audit (shape a) framed as a review (shape b) reads as adversarial; a review
  framed as an audit produces unfocused findings. cross-link to
  `patterns/shapes-overview.md` when uncertain.

- **variant** — subprocess, fork-within-harness, or cross-host. the variant
  decides how the peer's output gets back to the dispatch-sender and what
  sandbox boundary applies. name it explicitly; never let the peer infer it.

- **profile** — model name + reasoning effort, named exactly as the harness
  config names them. a brief that says `gpt-4 high` when the harness profile
  is `gpt-5.5-high` lets the peer load the wrong profile silently. write the
  string the harness uses.

- **source** — exact file paths or commit SHAs, not paraphrases. prose
  summaries of source content drift. when the peer reads the actual file,
  drift collapses. cite the SHA when the file is likely to change before the
  peer reads it.

- **scope** — name what the peer touches AND what it does not touch. the
  three-tier vocabulary (read+propose / bounded code / propose-architecture-
  but-wait) gives the peer a clean boundary. ambiguity here is where unbounded
  refactor scope creeps in.

- **output** — full path to the dispatch report file + format. when the peer
  knows the exact output path, the report lands where the integration step
  expects it.

- **qualitybar** — what verification is required to claim the work is done.
  *reads dominate cost regardless of conceptual scope; estimate the token
  budget from expected reads, not from scope language.* prefer executable
  verification commands; prose-only acceptable but lower confidence.

- **blocked** — explicit halt-and-report instructions. the survival-primitive
  part. when the peer hits a conflict, a dropped dependency, scope ambiguity,
  or a sandbox boundary, the BLOCKED marker fires and the peer reports
  upward rather than guessing forward. write the conditions explicitly.

- **sandbox** — workspace-write vs read-only. universal across harnesses.
  when the peer needs a cwd shift to enter the workspace, name it here.

---

## the body sections

after the eight-element header, the body of the brief covers five fixed
sections:

- **context** — the why. what the work is for, what just happened, what the
  peer needs to know to start cold.
- **inputs to read** — explicit list of read-only sources by path or URL.
- **audit dimensions / tasks** — the actual work, bulleted and ordered.
- **output structure** — exactly what shape the report takes. sections,
  headers, expected length.
- **discipline** — explicit reminders. halt protocol, evidence-cite
  requirement, profile-name discipline, worktree isolation for any state
  mutation.

the discipline section repeats what the header already named. repetition is
deliberate. when the peer is mid-context-load and skimming, the second
reminder catches what the header missed.
