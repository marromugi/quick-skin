---
name: reviewer
description: "How to get a review of a pull request or a change that is not anchored on the maker's own reading: two reviewers on different models given the same prompt, a prompt that keeps them to mismatches with the request, concrete bugs, harm to the developer, and tests that guard nothing, and how to triage and post what they find. Reach for it when a change is about to land and a second read would catch what the maker cannot see, and whenever the developer asks to review a PR — \"PR #14 をレビューして\", \"review the PR\", a PR URL, \"check what the agent built\"."
---

# Reviewer

The maker of a change reads it through what they meant to write. A reader
who did not write it sees what is there. What a review may report, and how
findings are triaged, is in `software-design-theory` (Review, Tests, What
goes to the developer); this skill is how to run one well.

## Two readers, one prompt

Two reviewers on different models (Opus and Fable), given the same prompt,
are not split by lens. Two differently trained readers looking at the same
thing is the point, and a finding both raise is the strongest signal there
is. If one model is unavailable, run that seat on the other and say so.

Reviewers who must find something inflate nits into findings. The prompt
says what not to report, allows the answer `pass`, and asks for a concrete
scenario behind every finding. Give each reviewer what the request asked
for, any departures the maker named, and how the work is run — not the
maker's opinion of it.

```
You are reviewing a pull request in this repository. You did not write it.

What the developer asked for: <the request>
PR: <url>
Departures from the design the maker named: <or "none">
How it is run: <the pass conditions recorded for the work>

Read the diff with `gh pr diff <number>`, and the code around it as far as
you need. Read .claude/skills/software-design-theory/SKILL.md, sections
Tests and Review.

Report only these four kinds:
- mismatch: it does not do what was asked. Name the input and what the
  developer would see instead.
- bug: a concrete input or state that gives a wrong result or a crash.
  Trace the call site; a value no caller can pass is not a bug.
- harm: something that costs the developer money, cannot be undone, or
  reaches outside this machine without being asked for.
- test: a test in this PR that guards nothing, by the list in "Tests that
  guard nothing".

Do not report:
- another way you would have built it
- naming, formatting, or style
- abstractions the code does not need yet
- anything you cannot tie to a concrete scenario

Do not edit files. Do not run anything that calls a paid API.

Return findings in this form, or the single line "pass":

- kind: mismatch | bug | harm | test
  where: <path:line>
  scenario: <input or state -> what happens>
  evidence: <quoted code or command output>
```

## Triage

Whoever runs the review has the context the two readers lack, and decides
each finding rather than passing them all on:

- **fix** — the scenario can happen, or the test does guard nothing. Both
  readers raising it is reason to fix unless the code proves otherwise. A
  `test` finding is fixed by deleting the test.
- **dismiss** — the scenario cannot happen (trace the caller), or it is a
  preference in disguise. One line of reason.
- **ask** — a `harm` finding that holds goes to the developer.

A finding that reopens a choice the maker already listed as decided, or a
departure it already named, is dismissed with that pointer; the developer
overturns those from the record, not the review.

## Leaving it visible

Post every finding on the PR, fixed or dismissed, so the developer can
overturn a call. Post before fixing, so each comment anchors to the commit
that was reviewed. Start the body with the kind and decision, such as
`[bug / fix]`, then the scenario, who raised it, and the reason for a
dismissal. Write comments in Japanese.

```
gh api repos/<owner>/<repo>/pulls/<PR>/comments \
  -f commit_id=<headRefOid> -f path=<file> -F line=<line> -f side=RIGHT \
  -f body='[bug / fix] ...'
gh pr comment <PR> --body '[mismatch / fix] ...'
```

After a fix is pushed, reply on its thread with the commit:

```
gh api repos/<owner>/<repo>/pulls/<PR>/comments/<id>/replies -f body='<sha> で修正しました。'
```

A fix agent spawned with `isolation: worktree` leaves its worktree and a
`worktree-agent-<id>` branch behind once it has pushed; remove both.
