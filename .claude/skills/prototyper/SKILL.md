---
name: prototyper
description: "How to settle a factual question by building a throwaway and watching what it does: what a function returns, how long a call takes, whether an outside system accepts a shape, how a library really behaves. Reach for it whenever a design question has an answer that only running code can give — before guessing, and before asking the developer something that is a fact rather than a preference. Covers keeping the throwaway out of the real work, running it in a disposable worktree, asking before it spends money or reaches outside, and reporting what was seen. Not for building the real implementation, or for confirming finished work (see verifier)."
---

# Prototyper

Some questions have only one honest answer once something has run. Asking
the developer such a question asks them to guess at a fact; deciding it by
reasoning alone builds a design on a guess. A small throwaway that runs is
usually cheaper than either (`software-design-theory`, How to use this).

## What makes a throwaway useful

- **One question.** Write down the question and what observation would
  answer it — a value, an output, a timing, an accept or reject — before
  building. The smallest code that produces that observation is enough.
- **Observation, then judgement.** Record what was seen exactly as it came
  back, separately from what you conclude. When an agent runs the
  throwaway for you, have it return the raw observations and the commands
  it ran, not a recommendation; the choice is made against the principles
  by whoever holds the design.
- **Nothing is kept.** The throwaway skips everything the real code needs
  for correctness and review. Keeping it leaves a second, unreviewed
  implementation next to the real one. Keep the fact and the question that
  produced it, as a decided item in the record of the work, and discard
  the code. Building it for real is new work.

## Where it runs

Not in the main checkout: stray files there land in the developer's tree
and in everyone else's next branch. A disposable worktree works — an Agent
with `isolation: worktree`, or `git worktree add <scratchpad>/proto-<slug>`.

An agent's own worktree clean-up only fires when the tree is left
unchanged, and a throwaway always leaves files, so remove it yourself:

```
git worktree remove --force <path>
git branch -D <branch>
```

The branch holds no commits if nothing was committed, so deleting it loses
nothing.

## Before it runs

Find out what the throwaway calls. An entry point in this repo may declare
how it runs and what it costs in a `<entry>.entry.json` file next to it
(`command`, `needs` for keys, `burdens` such as `cost`, `outside`,
`hands`); read that instead of guessing. For anything else, name the paid
services and outside machines it reaches.

A run that spends money or reaches outside this machine is asked right
before it runs (`software-design-theory`, What goes to the developer): what
would run, its burden in plain words, and the design question it answers.
Approval covers that one run. Not approved means not run; say so.

## Reporting

- what was observed, quoted;
- the commands that ran;
- what could not run, and why, when the throwaway never produced an
  observation.
