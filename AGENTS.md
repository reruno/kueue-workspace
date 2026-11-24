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

Item format. Write the `LINKS:`, `DESC:` and `REC ACTION:` labels in capitals so
they stand out from the text, and indent them so they stay inside
the list item:

```markdown
1. [ ] Do x and y (P1)
   LINKS:
   - https://github.com/kubernetes-sigs/kueue/pull/18064#discussion_r2501234567
   - `pkg/controller/jobs/sparkapplication/sparkapplication_controller.go:195`
   DESC: Short context: the current state and why it matters.
   REC ACTION: Simple summary of the action to take next.
   NOTE: Optional, written by the developer.
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
  by looking into the item in detail; mention them in `DESC:` as counts
  (for example "3 outdated CodeRabbit threads") instead.
- If there are no useful links, write `LINKS: none`.
- `NOTE:` is optional and belongs to the developer, who adds it by hand to
  leave a remark on an item (for example, that it is blocked or what to keep
  in mind). Never write, change, or remove a `NOTE:`. When carrying an item
  over to a new day, copy its `NOTE:` as it is, and take it into account
  when refreshing the item's priority, `DESC:` and `REC ACTION:`.

## Commits

The developer is the sole owner and author of all code. Never add
`Co-authored-by` trailers or any other AI attribution to commits, PRs, or code.

Commit messages are concise, simple, and meaningful: say what changed and,
when not obvious, why.

## Code quality

Apply these principles whenever you write or judge code: coding, making new
solutions or designs, fixing bugs, refactoring, reviewing existing code, and
reviewing PRs (your own changes too, before calling them done). When reviewing,
check against them in order.

Quality code is the simplest correct solution that fits naturally into the
existing code (KISS: as simple as possible, but not simpler).

1. **Simple.** Make the smallest clear change that solves the actual problem.
   Every new function, variable, branch, or special case must justify itself
   ("do we need it, and why?"). No speculative generality, no unneeded guards
   or interim variables, and no scope wider than the problem (narrow a
   generic change down to the integration that needs it).
2. **Fits the existing code.** The new code should look like it was always
   there:
   - Reuse existing helpers, wrappers, interfaces, and patterns before
     writing new ones. Search for them first.
   - Follow local conventions for naming, structure, and API symmetry
     (for example `Annotation(k, v)` like the existing `Label(k, v)`).
   - Put logic where it conceptually belongs, not wherever it is easiest to
     call.
   - Treat the same field or concept the same way in every code path.
3. **Reusable and flexible where it pays off.** Extract a helper when logic
   actually repeats (for example identical across Job integrations) or when a
   function does too much. Keep helpers as general as their names suggest,
   and no more. Extend through existing extension points (`GenericJob`,
   optional interfaces) rather than new abstractions. Some features need
   flexibility; don't add it by default.
4. **Clear and honest.** Names say exactly what a thing is or does. Comments
   describe what the code does, not what it intends to do. No spaghetti: one
   function does one job and reads top to bottom.
5. **Correct.** Simplicity never excuses missing edge cases or tests: upgrade
   safety, both feature-gate states, scheduler invariants, behaviour at scale.
6. **One concern per change.** Keep refactors and fixes in separate PRs.

The project-wide process bar (tests, API versioning, logging policy, release
notes) is in `kueue-llm-wiki/wiki/code-quality.md`; the reviewer profiles
linked from it show how maintainers apply these principles.

You may use the ponytail skills (below) for code quality: `ponytail` while
writing code, `ponytail-review` to review a change or PR, `ponytail-audit` to
audit a package or folder.

### Ponytail skills

Skills live in `.agents/skills/<name>/SKILL.md`, a vendor-neutral place: an
agent without native skill support reads the `SKILL.md` when its description
fits the task. `.claude/skills` is a symlink to it, so Claude Code finds them.

`.agents/skills/` has the ponytail skills, vendored from
https://github.com/DietrichGebert/ponytail (MIT, commit `96608e0`):
`ponytail` (smallest complete change), `ponytail-review` (review a diff),
`ponytail-audit` (audit a folder or repo), `ponytail-debt` (list `shortcut:`
markers). They back up the principles above; where they disagree, this file
wins. Never put `shortcut:` or `ponytail:` marker comments in Kueue code:
upstream reviewers ask to remove them. Say the limit in a normal comment, or
in the PR description.

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
