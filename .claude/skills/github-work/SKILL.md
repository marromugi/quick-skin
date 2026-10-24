---
name: github-work
description: "Know-how for carrying work through GitHub in this repo: when an issue is worth writing and what it should hold, handing a piece to an agent in its own worktree, landing a PR safely, running several pieces side by side without them breaking each other, finding contradictions between open issues, and clearing out note issues. None of it is a required sequence — reach for the part the situation calls for. Use it whenever work involves issues, PRs, worktrees, or merging — 「issue にして」「#12 をやって」「できる issue を進めて」「マージして」「issue の競合を見て」「メモを片づけて」「次々やって」, an issue or PR URL."
---

# Working through GitHub

`software-design-theory` is what the work is held to. This file is only
means: what has worked for recording, handing off, and landing work here.
How to sequence the work is yours to decide each time — explore, try
something, split it, or do it in one go, whatever the situation shows.

Reports and questions to the developer are in Japanese.

## When an issue earns its place

An issue is a record, not a plan you are bound to. It helps when:

- the work will be picked up later, by another session, or by an agent;
- the work splits into pieces that land separately, and the order matters;
- a defect or idea is set aside, so it does not get lost (a note issue);
- a decision needs a place the developer can find and overturn.

Work done in one go in this session needs no issue; its PR is the record.
Either way, the record carries what `software-design-theory` asks of it: the
request, what was decided, the pass condition for the running software, and
any departure from the design. `references/record-format.md` is a shape for
that; only `## Verification` is required.

When splitting, split where a piece can be built and run on its own, and
put first the piece that shows soonest whether the direction is right — a
thin path through every layer the work touches, then the part you are least
sure of. Splitting the whole thing up front is a guess about the future;
pieces nobody has started are rewritten freely as you learn
(`software-design-theory`, A new direction carries everything before it).

## Handing a piece to an agent

An agent in its own worktree (Agent, `isolation: worktree`) keeps the
developer's checkout untouched and lets pieces be in flight together. Give
it everything it needs in the prompt, because the prompt is all it has:

- the full text of the work (and its parent's), not a pointer to it — and
  after a change of direction, the request so far and every later
  instruction together;
- to read `.claude/skills/software-design-theory/SKILL.md` before writing
  code;
- that it owns the design while building: when the design does not hold it
  redraws it and names the departure in the PR body, and when the same
  friction keeps returning it stops building on that design;
- to stop and report only for what goes to the developer;
- to run the project's tests, type check and lint, and open a PR with
  `gh pr create`, starting `Closes #<N>` when there is an issue, then a
  summary, what it decided, and its departures from the design (or
  "none").

Read the issue once (`gh issue view <N> --json body,updatedAt`) and keep
`updatedAt`; before merging, it tells you whether the issue changed under
the work.

When waiting on CI, leave the `verifier` status out of the judgement —
it is posted later and is not a CI check:

```
gh pr checks <PR> --watch --fail-fast
gh pr checks <PR> --json name,bucket --jq '[.[] | select(.name != "verifier")]'
```

A red check goes back to the same agent with the failing names and log
excerpt. If it is still red after one retry, show the developer rather than
looping.

## Landing a PR

A PR left open costs one look; a wrong merge costs a revert plus whatever
was built on top. Merge when all of these hold, and leave it open when
anything does not fit cleanly:

- Nothing in the work is waiting on the developer, and no `harm` finding
  from review is open.
- Checks are green on the final head. With no CI configured, run the checks
  the project's agent instructions name; with none named, the agent's own
  word is not enough to merge on.
- `gh pr view <PR> --json mergeable,mergeStateStatus,headRefOid` is not
  `CONFLICTING` or `DIRTY`.
- The issue's `updatedAt` is unchanged since the work read it. Check it
  immediately before merging; a new comment you cannot interpret stops the
  merge (`software-design-theory`, principle 5).
- The head commit's `verifier` status is success, read immediately before
  merging. A missing status is not success.

```
gh api "repos/{owner}/{repo}/commits/<headRefOid>/status" --jq '.statuses[] | select(.context=="verifier") | .state'
```

Departures and decided items do not hold a merge; they are reported so the
developer can overturn them afterwards.

To merge, remove the agent's worktree first, or deleting the branch fails:

```
git worktree list --porcelain
git worktree remove --force <path>
gh pr merge <PR> --merge --delete-branch
git pull --ff-only origin main
git worktree prune
git branch -d worktree-agent-<id>
```

Merge commits, not squash. `-d` refuses a branch with commits main lacks;
leave that branch and say so. After the merge, copy the PR's departures
onto the issue (`gh issue comment`) — after, because a comment of your own
before it would move `updatedAt` and stop the merge. When every child of a
parent is closed and no child PR is open, close the parent with a comment
listing each child and its PR.

## Several pieces at once

Every merge changes main, and a PR built on the old main can pass its own
checks and break after another lands. What has held up:

- A change felt by every package or every later PR — CI, git hooks, Node or
  pnpm version, root `package.json`, lockfile, lint or format config, shared
  tsconfig, workspace layout, shared skill files — goes first and alone.
- Two pieces run side by side only when it is certain they cannot touch:
  no common file, neither touches what the other protects, and no shared
  index or barrel file, README table, snapshot set, or config file. If
  deciding takes an argument, run them one after another.
- Children of one parent go in their listed order.
- Keep to about three in flight; each is an agent in this session.
- Before starting a pass, check the checkout is clean on an up-to-date main
  (`git status --short`, `git pull --ff-only origin main`). Do not stash or
  reset the developer's edits to get there.

## Contradictions between open issues

Issues written at different times can each be sound and still collide.
Look across them while they are still text and cheap to change:

| Kind | How it shows |
|---|---|
| Builds on something removed | A removes or renames what B uses or changes |
| Same thing, two directions | A and B change one interface into different shapes |
| Opposite decisions | A and B decide the same thing opposite ways |
| Order contradicts itself | Waits form a cycle, or run against a parent's order |
| Duplicate | A and B add or change the same thing |
| Collides with an open PR | A PR in flight changes what another issue removes or changes |

Pull names from each issue (what it removes, adds, changes, and relies on),
compare literally first, then read for what names miss. Confirm each
candidate against the code on main and the issues' own words before acting
— a later issue may already account for the earlier one. A resolution that
follows from the principles with both goals intact is yours; one that makes
an issue give up part of its goal, or drops an issue, goes to the developer.

## Note issues

Notes pile up between rounds. Read each against today's code — often the
problem is already gone. Then:

- **gone** (the code no longer has it) or **covered** (an open issue or PR
  does the work): close it with a comment naming the evidence;
- **waiting** on an open issue: leave it;
- **unclear what to build**, or building it costs money, cannot be undone,
  or reaches outside: ask the developer, offering to leave it as it is;
- everything else: it is work to build.

Notes touching the same role or interface are designed together, so two
pieces do not change one interface two ways. Whether a note is worth fixing
is not a question; fixing it is what it was set aside for.
