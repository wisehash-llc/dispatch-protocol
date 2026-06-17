# worktree isolation

when more than one agent mutates files at the same time, they must not share a
working tree. each writer runs in its own isolated git worktree (or its own
branch in its own checkout); the dispatch-sender integrates at the end. this is
the mechanical precondition for shape e parallel tracks
([`shape-e-parallel-tracks.md`](shape-e-parallel-tracks.md)) and for any
concurrent state mutation.

## the problem

two agents editing the same working tree at the same time is a race. agent one's
uncommitted change is visible to agent two's build; a stray `git checkout` from
one resets the other's work; two writers touching the same file produce a corrupt
interleave that neither intended. the failure is not hypothetical — it is the
default outcome of concurrency without isolation.

## the pattern

- **one worktree per writer.** `git worktree add` gives each agent a separate
  working directory backed by the same repository, on its own branch. the agents
  cannot see each other's uncommitted state.
- **branch per track.** each track commits to its own branch. nothing lands on
  the integration branch until the dispatch-sender merges it deliberately.
- **integrate at the end.** the dispatch-sender (or a follow-on review) pulls the
  tracks together, resolves any merge, and runs the integration-level
  verification. integration is an explicit step, not a side effect of the tracks
  finishing.

## the non-overlap assertion

isolation makes concurrent writing *safe*; it does not make overlapping tracks
*correct*. shape e still requires the tracks to be genuinely non-overlapping —
isolation prevents the working-tree race, but two tracks that both need to change
the same logical thing will still conflict at the merge. the brief asserts
non-overlap; the worktree enforces the mechanics. you need both.

## cleanup

a worktree left behind after a track completes is clutter and a future-confusion
surface. remove it once the track is integrated (`git worktree remove`). a
worktree that was never written to should be removed as if it never existed.

## when it isn't needed

sequential dispatches do not need worktree isolation — one writer at a time
against one tree is fine. read-only dispatches (shape a, shape b, shape d's
codebase reads) never mutate the tree and need no isolation. reach for worktrees
specifically when two or more dispatches write *at the same time*.
