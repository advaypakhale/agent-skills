---
name: no-crap-docs
description: Writing standards for code comments, docstrings, READMEs, help files, taglines, commit messages, and any prose. Load BEFORE writing or editing any of these, not after. Also load when asked to review or de-slop existing docs or comments.
---

# No crap docs

The reader should get the facts without noticing the writing. A sentence written for effect reads as insincere. Write as if explaining to a colleague.

Concision means more information per word. Cut filler and facts the reader does not need; do not clip sentences into fragments.

## Comments

A comment may only say what the code cannot: an external constraint, a non-obvious why, or unidiomatic code that a maintainer would otherwise "fix" back to broken. Everything else — including a summary of the lines below it — gets deleted.

A comment never excuses unclear code. If code needs a comment to explain what it does, or you cannot write the comment clearly, rewrite the code instead.

Delete this kind:

```lua
-- Reuse a live master, silently start one under existing credentials, and
-- only then fall back to interactive authentication.
if master_alive() or start_master(false) then
  return true
end
return authenticate(start_master(true))
```

The code already says it.

Keep this kind:

```lua
-- The remote `tar` uses no GNU-only options because it may be busybox.
```

```lua
-- Version-keyed, so switching versions cannot leave a half-matched install.
```

The code cannot say these.

File headers give what the file is and the one mental model needed to read it, in a few lines. They are not the place for philosophy or for narrating the file's own structure ("builds bottom-up so it reads top to bottom").

## Docstrings

A docstring says what the signature cannot: behavior and purpose, parameter constraints, what the return value means, and the errors raised. It never restates what a name or type annotation already says, and never describes internal logic.

Public modules, functions, classes, and methods get docstrings. Non-public ones get at most a one-line comment, and only when the name and signature leave something unsaid.

One terse line is often enough. "Idempotent, and free of UI so it can be driven headlessly." is complete; "Bring the target to the desired state. Idempotent, and…" opens with filler.

## Prose

State facts in the order the reader needs them. All of the following are writing for effect and are banned:

- Coined framing: "That directory is the entire footprint."
- Triadic rhythm: "no root, no packages, no daemon"
- Punchline fragments: "Delete the directory and it's gone."
- Cute phrases: "fork it and make it yours"
- Grand self-assessment: "meant to be complete", "batteries included", "blazingly fast"
- Lecturing the reader: "for new features, fork it"

The same content, written properly: "It never needs root. Deleting that one directory undoes everything it does to the target."

Taglines and descriptions say what the thing does, verb first: "Deploy Neovim and your config to remote development environments", not "Your entire Neovim setup, in one directory on any ssh host or container".

Scope and status statements stay humble: "aims to stay small … unlikely to be accepted", never "is complete".

## Sentences

These rules apply to chat replies as well as files. They apply to a reply that explains a writing fault.

Put one fact in each sentence. A second fact joined on with a comma gets its own sentence. The joining word varies: "X, and Y", "X, so Y", "X, which Y", "X, from Y".

Make the thing that acts the subject of the sentence. A clause that starts with "whose", "which" or "that" puts the actor behind another noun.

Use the literal word. Write "added", not "bolted on". Write "no core accesses the block", not "nobody reads it".

Check a claim against every case it covers. A cause that is true of one result is often false of the second result in the same sentence.

Choose the subject and the length of a sentence by the fact it states. Do not choose them to match the sentence before it or to end on a strong phrase.

Rewrite this kind:

> The new build is 24% slower on the first benchmark and 4% slower on the second, from updates sent to copies nobody reads again.

The cause is joined on with a comma. "Nobody reads" is not literal. The cause is true of the first benchmark only.

This rewrite still fails:

> On the first benchmark the updates go to caches whose cores do not access the block again.

The cores act, but "updates" is the subject.

Write this:

> The new build is 24% slower on the first benchmark and 4% slower on the second. On the first benchmark the other cores do not access these blocks again. Every update sent to them is wasted. On the second benchmark the other cores access the blocks rarely. Most updates are still wasted.

## Editing existing prose

Match the file's voice. To add a fact, extend the passage with a flat sentence in its existing cadence; do not rewrite the passage into your own register. Check the diff afterward: if the new sentences have more rhythm than the old ones, redo them.

## Commit messages

Follow Conventional Commits. The subject and body state what the change does, with no attribution footers.

## Test

Read the text aloud. If a sentence would survive in ad copy, or you notice the sentence before the fact, rewrite it until it only informs.
