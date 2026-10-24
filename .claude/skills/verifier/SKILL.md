---
name: verifier
description: "How to confirm that a change works on the running software, judged by a fresh agent that neither wrote nor reviewed it, and how to record the verdict on the PR's head commit as pass, fail, not-needed, or unverifiable. Reach for it whenever work that changes behaviour is about to be called done or about to land — the line 'done means seen running' in software-design-theory is what it serves. Covers pinning the checked commit, preparing a disposable worktree, reading what an entry point costs, asking before cost or outside effects, and why a check that could not run is never a pass."
---

# Verifier

`software-design-theory` holds the line: work that changes behaviour is done
only when its behaviour has been seen running, by an agent that did not
write or review it, against a pass condition decided before the diff was
read. This skill is how to do that without fooling yourself.

## Why a fresh agent judges

The agent that wrote the code and the one that reviewed it both already
believe it works; asking either to confirm it has them agree with
themselves. The judge is spawned only for this check (Agent,
`general-purpose`, `sonnet`). It sees the pass conditions and what the
commands printed — never the implementation or the review. It runs only the
commands it is given and edits nothing.

## Why a check that could not run is not a pass

A command that could not run — a missing declaration, a missing key, a check
only a person can judge, an approval refused — says nothing about the
software. Calling it a pass lets work land on a check nobody performed.
It is `unverifiable`, with the fact that stopped it as the reason.

## Why the checked commit is pinned

A push can land while the check runs. Read the head once,
`gh pr view <PR> --json headRefOid`, and pin the worktree, the judge and the
posted status to that commit, so the status describes exactly what ran.

## Preparing the run

- **Worktree.** `git worktree add <scratchpad>/verify-<PR> <headRefOid>`.
  It is a fresh checkout: no packages, no build output, no `.env`. Run the
  fresh-checkout setup the project's agent instructions (`CLAUDE.md`) name,
  from its root.
- **Keys.** Link the main checkout's `.env` in only when it exists — never
  copy the values:
  `ln -s <main checkout>/.env <scratchpad>/verify-<PR>/.env`. A dangling
  link makes Node print a warning on every run, and that extra line breaks
  any pass condition that compares output exactly. Without it, keys come
  from this session's environment.
- **What each entry costs.** An entry point may declare how it runs next to
  itself in `<entry>.entry.json`: `command`, `needs` (keys), and `burdens`
  (`cost`, `outside`, `hands`). Read it rather than guessing. No
  declaration, a needed key that is nowhere, or `hands` (a person must
  look) makes that item unverifiable without running it; keep checking the
  others.
- **Cost and outside effects.** A run with `cost` or `outside` is asked
  right before it runs, for that one run (`software-design-theory`, What
  goes to the developer). Not approved: unverifiable, 「承認されませんでした」.
- **Outside network.** A spawned agent may not reach the local network.
  Run `outside` commands in this session from the worktree root, save each
  output and exit code to a file, and hand the judge the files instead,
  telling it not to run anything. The judge still decides.
- **Other machines.** When a check needs something running elsewhere (a
  browser, a server), look at what already runs there and how much memory
  is free first. Start only what is needed, keep the process ids, and stop
  them afterwards. A small machine that runs out of room stops answering,
  and then neither the check nor the clean-up can finish.

## Deciding and recording

Any item failing makes the result `fail`; otherwise any unverifiable item
makes it `unverifiable`; otherwise `pass`. Work with no behaviour change is
`not-needed`, with the reason when behaviour changed but no entry reaches it.

Every result — including one that stopped early — is recorded the same way:

```
node .claude/skills/verifier/scripts/verdict.mjs <PR> <result> [--reason <text>] --sha <commit>
gh pr comment <PR> --body '...'
```

`verdict.mjs` posts a `verifier` commit status; it refuses an unknown result
or a `fail`/`unverifiable` without a reason, and exits non-zero with gh's
error when GitHub rejects the write. The comment, in Japanese, gives the
result and, per item, the command that ran (or why not), what was observed,
and the judgement against its pass condition.

Then remove the worktree (`git worktree remove <scratchpad>/verify-<PR>`,
which also removes the `.env` link) and anything started on other machines.
A worktree left behind blocks the next run's `git worktree add`.
