# Kueue workspace

```text
kueue-workspace/
  kueue-main/        # Submodule: Kueue, at upstream/main
  kueue-llm-wiki/    # Submodule: Kueue LLM wiki
  branches/          # Gitignored: Kueue copies for working on separate issues
```

## Where work happens

`kueue-main/` is the main place for work: reading, researching, and changing
Kueue code happen there by default.

When the developer says to work on an issue separately (not on main), create a
copy of Kueue under `branches/`, named after the issue, as a Git worktree of
`kueue-main` based on the latest `upstream/main`:

```sh
git -C kueue-main fetch upstream
git -C kueue-main worktree add ../branches/kueue-issue-<n> -b issue-<n>-<short-name> upstream/main
```

Then do all work for that issue in `branches/kueue-issue-<n>/`.

Keep the issue's context (requirements, scope, relevant code and wiki
pointers, how to verify) in `testbin/WORKTREE_CONTEXT.md` inside that copy.
Kueue's `.gitignore` ignores `testbin/*`, so this file is never committed.

## Commits

The developer is the sole owner and author of all code. Never add
`Co-authored-by` trailers or any other AI attribution to commits, PRs, or code.

Commit messages are concise, simple, and meaningful: say what changed and,
when not obvious, why.

## Approach to solutions

Keep it simple (KISS): the solution should be as simple as possible, but not
simpler. Solve the actual problem with the smallest clear change, prefer
existing patterns and helpers over new abstractions, and avoid speculative
generality. Don't cut corners on correctness, edge cases, or tests to get there.

## Answering questions

When a question about Kueue comes up (architecture, scheduling, preemption,
quotas, integrations, history, why something is the way it is), read the wiki
first: start at `kueue-llm-wiki/wiki/index.md`, then the relevant pages. Check
against the code when it matters, and say when the wiki has no answer.
