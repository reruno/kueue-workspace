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

## Manual code review for important features

manual review is labor intensive action, that evaluates approach/code style and etc etc, it is done by me manually so manual review must be done only for core logic and most important code. (Tests documentation and etc is not considered core logic)

Review map - review document that splitted like book chapters, from start to finish

When reviewing important features and code I would like to have a map, like a good book have start -> finish, the same TOC I want to have for files with explanation, I want to start manual review from entrypoint and have a good understanding and good story around the feature/code that I am manually reviewing. 

In some cases I would like to have this review map in some file formats like txt, md or even HTML if visualization is needed

The file name of review map should have this format yyyy-mm-dd_name-of-review-map.extension 

In the review map, write every code reference as a repo-relative path with a line
number (for example `pkg/controller/jobs/sparkapplication/sparkapplication_webhook.go:140`),
never a bare `:line` or a file name without its directory, so it can be pasted into
Ctrl+P in VSCodium to jump straight to that line.
