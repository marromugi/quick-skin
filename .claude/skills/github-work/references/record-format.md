# Record format

A suggested shape for an issue, or for a PR body when the work has no issue.
Only `## Verification` is required, because the confirmation and the review
read it; the rest is there when it helps whoever picks the work up. Keep the
headings exactly as written when you use them; `check-issue.mjs` and other
readers find `Verification`, `Child issues`, and the `Parent:` line by
heading.

Issues are written in English. The part above `To Implementer` is for
people, in plain wording with no file or function names; `To Implementer` is
for an implementing agent and is as technical as it needs to be.

## Work to build

```
## Request
<what should become possible, from the point of view of whoever uses it.
2–4 sentences. A bug fix says what is seen now and what should be seen.>

## Design
<a child's first line is `Parent: #<n>`, alone. Then the shape in a few
sentences: which role this piece plays and the interfaces it relies on or
adds.>

## Decided
- <a name, value, format, or wording the maker chose>: <the choice>
- <a fact settled by running something>: <what was seen, and how it was run>

## Verification
- V1: `<entry>`. <steps>. <the pass condition, with the values seen as they are>

## To Implementer
- Files / modules: ...
- Interfaces (signatures, data shapes): ...
- Behaviour on failure: ...
- Out of scope (do not touch): ...
```

`Verification` items take one of these forms:

- `- None` — the work changes no behaviour.
- `- None: <reason>` — it changes behaviour, but no entry reaches it.
- `- V<n>: ...` — one runnable check, naming its entry in backticks as a
  path from the repo root. When the entry declares itself in
  `<entry>.entry.json`, use its `command`.

Tests are not listed; they follow from the request
(`software-design-theory`, Tests).

## Parent

```
## Request
<the whole of what should become possible>

## Design
<the shape shared by every child>

## Decided
- ...

## Child issues
1. #<n> <title>
2. ...
```

A child has exactly one line `Parent: #<n>` in its `Design`, on its own and
outside code fences. A parent has no `Parent:` line and needs no
`Verification`.

## Note

A defect or idea set aside for later. The title ends in "(note)"; the body
has `## Background` (what was seen, where) and `## Handling` (what would be
done about it), and no `To Implementer`, so nobody mistakes it for work
ready to build.

## Checking

```
node .claude/skills/github-work/scripts/check-issue.mjs <body.md>
```

It checks that a body which is not a parent has `## Verification`, that its
items have one of the forms above, and that a `Parent:` line is well formed.
It prints one line per problem and exits non-zero.
