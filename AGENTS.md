# Kueue workspace

```text
kueue-workspace/
  kueue-main/        # Submodule: Kueue, at upstream/main
  kueue-llm-wiki/    # Submodule: Kueue LLM wiki
  branches/          # Gitignored: Kueue copies for working on separate issues
  todo/              # Gitignored: daily TODO files, one per day
```

## Where work happens

`kueue-main/` is the main place for work: reading, researching, and changing
Kueue code happen there by default.

When the developer says to work on an issue separately (not on main), create a
copy of Kueue under `branches/` as a Git worktree of `kueue-main` based on the
latest `upstream/main`. Name it `kueue-<suffix>`, where the suffix is the issue
number it resolves (when there is one) plus a short description of the work,
for example `kueue-18415-spark-elastic-cleanups`, or `kueue-spark-partial-preemption`
without an issue. Never use only the number:

```sh
git -C kueue-main fetch upstream
git -C kueue-main worktree add ../branches/kueue-<n>-<short-name> -b issue-<n>-<short-name> upstream/main
```

Then do all work for that issue in `branches/kueue-<n>-<short-name>/`.

Keep the issue's context (requirements, scope, relevant code and wiki
pointers, how to verify) in `testbin/WORKTREE_CONTEXT.md` inside that copy.
Kueue's `.gitignore` ignores `testbin/*`, so this file is never committed.

## Daily TODO

Each day the developer asks for a new TODO file in `todo/`. Each file is
self-contained, either Markdown (the default) or a single HTML file, and is
named `yyyy-mm-dd.md` (or `.html`) for its day.

To generate a new day's file:

1. Carry over every open `[ ]` item from the latest earlier file. Leave that
   file as it is, as history. Don't carry over done `[x]` items.
2. Refresh each carried-over item: new comments, CI results, merges, and
   anything that changes its priority. Drop items that no longer apply.
3. Add new items: review requests, mentions, failing PRs, follow-ups.
4. Sort from highest to lowest priority, then renumber from 1.

Priorities:

- **P0**: critical. Drop all other priorities and finish it as soon as
  possible. P0 items always come first.
- **P1** to **P4**: from high to low.

Item format (indent `Links:`, `Desc:` and `Rec Action:` so they stay inside
the list item):

```markdown
1. [ ] Do x and y (P1)
   Links:
   - https://github.com/kubernetes-sigs/kueue/pull/18064#discussion_r2501234567
   - `pkg/controller/jobs/sparkapplication/sparkapplication_controller.go:195`
   Desc: Short context: the current state and why it matters.
   Rec Action: Simple summary of the action to take next.
```

- The developer marks an item done for the day by changing `[ ]` to `[x]`.
- Every link points straight at the point of interest, so the developer
  doesn't have to search the page: the exact review thread
  (`#discussion_r<id>`), comment (`#issuecomment-<id>`), failing test log, or
  file and line (`blob/<sha>/<path>#L<n>`). Write local code references as
  repo-relative paths with a line number, as in review maps. Link just the
  PR, issue, or page only when there is no more precise point, or when the
  link is background that might help.
- Keep links few: only the ones the developer needs to act on the item,
  usually 1 or 2, at most 3 (one per PR when an item spans several PRs).
  Leave out background, outdated threads, and links an agent can find later
  by looking into the item in detail; mention them in `Desc:` as counts
  (for example "3 outdated CodeRabbit threads") instead.
- If there are no useful links, write `Links: none`.

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
