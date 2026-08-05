---
name: no-leaks
description: Strip conversation, planning and process residue out of code, comments, documentation and repository files. Use when writing or editing any durable artifact - source comments, README, user docs, help files, config files, templates - and especially before declaring work complete, after a refactor or rewrite, after removing a feature, or when the user says documentation "reads like AI slop", references something that does not exist, or leaks internal discussion.
---

# No leaks

A durable artifact is read by people who were not in the room. They have no access to the
conversation that produced it, the plan it followed, the alternatives that were weighed, or any
earlier version of the code. Anything that only makes sense with that access is a leak, and to a
first-time reader it is worse than noise: it points at something they cannot find.

Assistant-written code does this constantly.

## The two tests

Apply both to every sentence you write into a durable artifact.

**1. The stranger test.** Would this make sense to someone who has never seen the conversation, the
plan, the issue thread, or any previous version of this code? If understanding it requires knowing
what was discussed, considered, rejected, or removed, cut it.

**2. The findability test.** Does it reference anything the reader cannot locate in the artifact in
front of them? If a doc names a flag, command, file, option or behaviour, the reader must be able to
find that thing.

## What leakage looks like

Each category is a way of forgetting that the reader cannot see the process.

**Process narration.** Text describing how the artifact came to be rather than what it is: "we
changed this to", "instead of the previous approach", "as discussed", "per the plan", "phase two",
"originally", "this used to", "now uses", "after the refactor". The code's job is to describe the
present. Its history lives in version control.

**Ghost references.** Naming something that does not exist in the shipped artifact: a removed
feature, a rejected alternative, an option that was renamed, a file that was deleted, a command that
was never released. Especially common right after a deletion, because the mentions elsewhere
survive it.

**Negative-space claims.** "There is no X." "You don't need to Y." "No Z is required." These only
parse if the reader arrived expecting X, Y or Z. Usually they are the residue of an argument the
reader never heard. State what the thing does.

**Unprompted justification.** Defending a decision to a reader who never challenged it: "this is
deliberate", "rather than the alternative", "which is why", "this is safe because", "the point is".
Reference documentation states behaviour; it does not argue.

**Internal principles as prose.** Design rules, constraints and philosophy from planning documents
restated in user-facing docs or module headers. Contributors may need these; users do not, and code
almost never does.

**Machine register.** Vocabulary that signals the text was generated rather than written. It varies
by model and era, so judge by ear rather than by blocklist: if a normal engineer writing quickly
would not reach for the phrase, do not use it. Inflated abstractions, portentous framing, and
throat-clearing openers are the usual tells.

## Where the rule flips

The distinction is what the artifact is *about*.

| Artifact | Subject | Before/after allowed? |
| --- | --- | --- |
| Source, comments, README, user docs, help files | The current state | **No** |
| Commit messages, changelogs, release notes, PR descriptions, migration guides | The change itself | **Yes** |

A commit message saying it replaces one mechanism with another is doing its job. The same sentence in
a README is a leak, because a new reader has no memory of the thing being replaced. Do not
over-correct commit messages and changelogs into uselessness; describing a transition is their entire
purpose.

Deprecation notices and migration guides are the deliberate exception in user docs: they exist to
address readers who *do* remember the old thing. Keep them scoped and dated, and remove them when the
audience they serve is gone.

## Do not over-correct

Stripping leakage is not stripping comments. These are legitimate and must survive:

- Why a non-obvious technique is used, when the obvious one is wrong.
- A constraint imposed by a dependency, platform, protocol or standard.
- A bug or quirk in something external that the code works around.
- A gotcha that will cause someone to "fix" correct code back into a broken state.

The difference: these explain the *world the code lives in*, which the reader cannot see from the
code. Leakage explains the *process that produced the code*, which the reader does not need.

If a comment restates what the line plainly does, it is noise for a different reason - delete it, but
that is a separate concern from leakage.

## Sweeping an existing artifact

When asked to clean up, or before declaring work done:

1. Grep for temporal and comparative markers: `previously`, `used to`, `no longer`, `instead of`,
   `rather than`, `originally`, `now`, `as discussed`, `per the`, `phase`, `TODO`, `we `, `our `.
2. Grep for justification markers: `deliberate`, `the point`, `which is why`, `this is safe`,
   `by design`, `note that`.
3. For every proper noun, flag, command and option named in docs, confirm it exists in the shipped
   artifact. Anything you removed this session is a prime suspect.
4. Read the opening paragraph of each document as a stranger. Introductions attract manifesto tone
   because that is where the author is still arguing for the work.
5. Check that removals were complete: deleting a feature means deleting its mentions in docs, help
   text, templates, config examples, error messages and completions.

## Independent review

On a large artifact, or when you wrote the text yourself and have lost the ability to read it cold,
dispatch a read-only reviewer with the stranger test as its brief and no knowledge of the
conversation. Authors are systematically blind to their own leakage, because to them the missing
context is still present.
