---
name: save-progress
description: >
  Capture a whole working session's learnings into durable memory without overwriting what is
  already there. Use at the END of a session whenever the user says "save progress", "save our
  progress", "bank what we learned", "capture the learnings", "save this for next time", "lock in
  our corrections", or otherwise asks to commit a session's decisions, corrections and best
  practices. Sorts every learning by HOW LONG IT SHOULD LAST (durable rule / one-off override /
  project state / lesson), guards against a one-time choice becoming a permanent rule, writes
  additively so nothing is clobbered, and prints a receipt of exactly what changed. Works in
  Claude Code (writes to your memory and project files) and in Claude Chat (commits to Memory and
  gives you a block to paste into Project instructions). Do NOT use it to save a single isolated
  fact — write that directly.
---

# Save Progress

Your AI forgets every correction you give it. You explain the same preference on Monday, correct
the same mistake on Thursday, and start from zero again next week. The one moment it most needs to
learn from you — you telling it it got something wrong — is the moment nothing captures.

This skill runs at the end of a session. It reads the conversation already in context, works out
what is worth keeping, sorts each item by how long it should last, and writes it into the right
place without overwriting anything that is already there.

## The four buckets

Every learning sorts into exactly one of these. Getting the bucket right is the whole job, because
it decides both where the learning goes and whether it becomes permanent.

Most memory systems sort by *topic*. This one sorts by **durability** — how long the thing should
stay true. That is the distinction that stops a memory file turning into landfill.

| Bucket | What it is | How long it lasts |
|---|---|---|
| **A durable rule** | A preference that should apply to every future job | Forever, until you change it |
| **A one-off override** | A choice that is true for this one piece only | This piece only. Never becomes a rule |
| **Project state** | What shipped, what is open, what you are waiting on | Until the project ships |
| **A lesson** | A mistake that should not repeat | Permanent, as a warning |

### The safeguard

This is the most valuable line in the skill. Before anything is filed as a durable rule, ask:

> **"Would I want this applied to every future job?"**

If yes, it is a rule. If no, it is an override — record it as project state and never promote it.

Promoting a one-time choice into a global rule is the failure that quietly poisons a memory system.
It is invisible for weeks, then suddenly every output carries a preference you expressed once, about
one thing, months ago. Nothing else guards against this, so do not skip the question.

**Worked example.** In one session a user asked for a cream-coloured footer on a single graphic, and
also established that a sidebar costs no vertical space while a bottom bar costs about 50 pixels.
Two learnings, same session, different buckets: the cream footer is an override (that one graphic),
the layout fact is a durable rule (true of every build). Same conversation, opposite destinations.

## Step 1 — Work out where this session's memory lives

Before writing anything, establish the target:

- **Which project was this?** Infer it from the working directory, the files edited this session,
  and which instruction file governed the work.
- **Where does its memory live?** Typically a memory folder of one-fact-per-file notes, an index
  that lists them, and per-project notes for state and lessons.
- **Is there a dedicated owner for any of it?** If another skill already owns a particular file,
  hand the learning to that skill instead of editing the file directly.

If you cannot tell which project it was, ask before writing anything. Guessing the project means
writing to the wrong memory, which is worse than not writing at all.

## Step 2 — Scan and classify

Read back over the conversation and pull out every candidate learning. Look for:

- **Corrections** — "no", "actually", "roll that back", "stop doing X", "always do Y".
- **Decisions** — an approach chosen, a spec confirmed, a final version named.
- **Practices that worked** — a build trick, an ordering, a verification step that saved time.
- **Gotchas** — the wrong folder, a broken path, a workaround for an outage.

Drop anything transient: one-shot answers, small talk, intermediate states that the final version
already superseded. You are banking durable value, not transcribing the chat.

Then assign each item a bucket, applying the safeguard question to every rule candidate.

## Step 3 — Write additively (the no-clobber protocol)

This protocol is what makes the skill safe to run without supervision. Follow it for every write:

1. **Read the target file first.** Always. You cannot reconcile against something you have not read.
2. **Look for an existing entry on the same topic.** Match by subject, not by exact string.
3. **If it exists, update it in place.** Add the new nuance to the entry that is already there. Never
   create a second entry on the same topic.
4. **If it does not exist, add it**, following the format the file already uses.
5. **Never delete an existing rule.** If a new learning appears to contradict an old one, do not
   silently overwrite it. Surface the conflict and let the user decide.
6. **Skip what is already saved.** If something was written earlier in this session, or already
   lives in memory, mark it "already saved" rather than writing it twice.
7. **Sweep for parallel writes.** If the user runs several sessions at once, another one may have
   written the same thing minutes ago. List the most recently modified memory files and skim any
   that overlap your topics before writing.
8. **Defer to a dedicated owner.** If another skill owns a particular file, hand the learning to
   that skill rather than editing the file yourself.

### Retire, do not stack

A session that adds five rules and retires none has made the memory worse, not better. Rule count is
not the goal.

When a new rule supersedes an old one, **mark the old one retired in place rather than deleting it**,
so it can still be read back and you can see how the thinking changed. Where two rules can coexist,
give each an explicit scope so they stop fighting.

Every durable rule is worth tagging with:

- **Scope** — what it applies to. A rule with no scope will eventually be applied somewhere it
  should not be.
- **Status** — live, or retired and superseded by which rule.

## Step 4 — Write, then print a receipt

Write each item according to the protocol above, then print a receipt showing exactly what changed:

```
SAVE PROGRESS — receipt

  Your rules       1 updated   "ask one question before assuming" replaced the older three-question rule
  Project notes    2 added     cream footer (this piece only), client naming for this deliverable
  Lessons          1 added     do not promise a date without checking

  Nothing left open.
```

The receipt is the proof of work. The user should be able to see what moved without opening a single
file.

If any learning conflicted with an existing rule, name it here and say what you did about it.

### Confirm first, or run straight through?

Default to **showing the routing table and waiting** the first few times, so the user can see how you
are bucketing things:

```
 #  Learning                              Bucket      → Destination        Action
 1  cream footer on this graphic          override    → project notes      add
 2  sidebar free, bottom bar ~50px        rule        → your rules         add
 3  use the faster model when one is down rule        → your rules         already saved
 4  rendered into the wrong folder        lesson      → lessons            add

Approve, or edit any row?
```

Once the user trusts the bucketing, they can tell you to skip the table and write directly. Keep one
exception permanently: **if a learning contradicts an existing rule, always surface it**, however
much autonomy you have been given.

## Step 5 — Leave nothing open

Banking the lessons is only half the job. Before printing the receipt, sweep the session for anything
flagged and not done — anything described as "worth fixing", "a separate job", "carried forward",
"still open", "not blocking".

Then fix each one, to the same standard as the main work: confirm it actually landed, do not just
assume it did.

Escalate instead of fixing only when the item is genuinely irreversible, is a real change of scope,
or needs a judgement only the user can make. When you escalate, do the mechanical part first so what
reaches them is one decision rather than a to-do list.

The receipt then states explicitly that nothing is open. If something is still open, name it and say
why it could not be closed.

## Running this in Claude Chat

Everything above works in Chat. The classifier and the safeguard are the portable part — only the
destinations change, because Chat cannot write files to your disk.

| Bucket | Claude Code | Claude Chat |
|---|---|---|
| Durable rule | Your memory / instruction file | **Commit to Memory**, and emit a block to paste into Project instructions |
| One-off override | Project notes | **Keep out of Memory.** Report it in the receipt only |
| Project state | Project file | A paste block for the Project, or the receipt |
| Lesson | Lessons file | **Commit to Memory**, phrased as a warning |

The safeguard matters *more* in Chat, not less. Memory there is a single shared pool with no folder
structure, so a one-off promoted by mistake follows you into every future conversation with no
obvious way to trace where it came from.

Two Chat-specific rules:

- **Read your existing Memory before adding to it.** The no-clobber protocol applies exactly as
  written. Update the entry that is already there instead of adding a near-duplicate.
- **Overrides never go in Memory.** They belong to one piece of work. Say so in the receipt and let
  them expire.

## What this skill does not do

Deliberately kept out, to stay lean and predictable:

- It does not touch code or run git commits.
- It does not run automatically on every turn. You invoke it, so a quiet session writes nothing.
- It does not re-summarise the conversation — only durable learnings and project state.
- It does not invent rules you never expressed. A practice is banked only if it actually proved out.
