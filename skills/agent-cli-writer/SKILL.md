---
name: agent-cli-writer
description: >
  Design, write or review a command-line tool that AI agents will operate.
  Use when building a new CLI or adding a command, flag, output or error
  message; when writing a tool's help text or its manual for agents; when
  deciding how long-running work is started, followed and reported; when
  reviewing an existing CLI for agent use; and when agents misuse a tool, skip
  its manual, poll it, keep asking where things stand, or spend tokens on its
  output.
---

# Agent CLI writer

An agent reaches for the command, not the manual. A rule that lives only in a
document is followed when the agent happens to read it. The measurements
behind this are in `references/evidence.md`.

**The rule:** every rule of the tool is stated by the tool, at the moment it
applies. What it prints, what it refuses and what it makes easy are the manual.

## What an agent does with a tool

- Reaches for the command first; opens the manual only when unsure, and is
  often sure and wrong.
- Reads to confirm a plan already made: it looks for syntax, not rules.
- Forgets everything between sessions and learns the tool again each time.
- Sends several calls at once.
- Pays again for every character the tool printed, on every later request.
- Cannot wait cheaply: it polls or sleeps, and each poll is a paid request.
- Does what the answer in front of it says, more reliably than what a
  document said earlier.

## Method

Twelve checks, each with how to verify it. `tool` is a made-up command.

1. **Teach at the point of use.** The answer to a command says what the path
   just taken costs, and names the better path.
   ```
   $ tool machine new
   created m-41 (stops by itself in 60 min)
   note: work started by hand in this machine keeps no run record.
         for a job you need to follow:  tool job start --from <dir> -- <cmd>
   ```
   Verify: every verb with a known wrong use says so in its own output.
2. **A refusal is a lesson.** Say what was refused, why, and the exact next
   command with the caller's own arguments filled in. Give every problem in
   one answer. Never exit non-zero without words. A usage error prints the
   right form of that one subcommand only.
   ```
   tool: refused: `job wait j-7` would hold this turn for up to 40 min.
         next:  tool job show j-7          (how it stands now)
                tool job start … --notify  (be told when it ends)
   ```
   Verify: no exit path without a message; every refusal carries a `next:`.
3. **Carry the manual in the tool.** `tool rules` prints the operating rules
   whole, ending in a version code. Verbs an agent drives by hand refuse a
   non-interactive caller that has not set the current code
   (`TOOL_RULES=<code>`), in one line. Verbs that are the machine interface
   stay open. The code changes when the rules change.
   Verify: a fresh agent given only the command name has the rules in its
   context before its first call that creates, changes or deletes something.
4. **Make the right path the easy path.** Before writing "prefer X", make X
   do the whole job: take the input agents really have, return at once, keep
   a record. Agents go around a recommended command that blocks, drops
   history or cannot take their input. State consequences, not preferences:
   "Y keeps no record" works where "prefer X" does not.
   Verify: for each "prefer" in the documentation, name what the avoided path
   offers that the preferred one lacks, and close that gap.
5. **One command answers "where do I stand, and why".** `tool status`, in a
   few lines: what is mine, what is running, how the last thing ended and
   why, what to do next. Agents reading the tool's storage or logs directly
   means status is missing a fact.
   Verify: count such reads in real transcripts; the three most common
   questions belong in status.
6. **Long work: start, return, tell.** Over about a minute: `start` returns a
   handle at once; `show <handle>` gives timeline, exit code and last lines,
   also after the worker is gone or the caller has died; the end is delivered
   to the caller with outcome and reason. A message qualifies only when it
   carries something new the agent can act on now: no periodic "carry on".
   Verify: nothing blocks over 60 seconds by default; every job has a record
   and an end notice with outcome, reason and next step.
7. **Print little.** Default output fits one screen: about 40 lines or 2,000
   characters. More goes to a file whose path is printed, or behind `--full`.
   No banners, no progress animation; standing instructions once a session.
   Output that is cut says so and says where the rest is.
   Verify: measure characters returned per command over a real week and give
   the three largest totals a budget.
8. **Two readers: a person and a program.** Text on a terminal. Piped, or
   with `--json`: JSON lines only, stable field names, one final summary.
   The exit code is the outcome: 0 done, and separate codes for refused,
   wrong usage, timed out, never ran. Diagnostics on standard error.
   Verify: `tool <verb> | cat` parses; `--help` lists every exit code.
9. **Safe to repeat, safe at once.** Running a command twice does not do the
   thing twice, or says it was already done. Two calls at one moment from one
   agent do not lose each other's writes: lock, or refuse the second with
   words. Destructive verbs need an explicit flag and offer `--dry-run`.
   Verify: a test runs every writing verb twice at once.
10. **Bound it and leave a record.** Default time limits; what the tool
    creates expires by itself; `list` shows leftovers; `cleanup` removes what
    this caller made; the cost of one thing is one command. Every action is
    recorded with who, when and the tool's build; `tool version` names it.
    Verify: kill the caller mid-run; nothing runs on unbounded, and the record
    says what happened.
11. **One name for one thing.** The same noun in the command, its output, its
    errors and its documentation; one verb for one action across nouns.
    Unknown flags and extra arguments are refused, never ignored.
    Verify: list the nouns the tool prints; each concept has exactly one.
12. **Secrets stay out.** Never on a command line, never echoed, never in a
    record: from a file, standard input or the environment, masked in output.

## Writing the tool's manual

The manual explains; the tool enforces. Nothing binding lives in it alone.

- **Description:** what the tool does and when to load it, in the words a
  task would use, naming the verbs.
- **Order:** the binding rules first, each with its consequence; then one
  short table of goal and command; then failure and what to do.
- **Size:** at most 150 lines, no line over 100 characters. Agents print files
  with long lines cut and lose the end of every wide table row.
- **Tables:** nothing binding inside a table cell.

## Prove it on a fresh agent

A new session that has never seen the tool or its manual, given a real task
and the command's name:

1. Record every call, its output and its exit code.
2. Count: help lookups, failed calls, repeats of a failed call, polls, direct
   reads of storage or logs, characters returned.
3. Every count above zero is a defect of the tool, not of the agent. Fix the
   message or the path, then run again in another fresh session.
4. Run once more with the manual loaded: nothing may get worse, and the
   manual may not contradict the tool.

Done when a fresh agent finishes the task with no help lookup, no failed call
it had to guess its way out of, no poll and no direct read of storage.

## Failure modes

**Manual-only rule** (the tool never states it) · **Mute failure** (non-zero
exit, no words) · **Why without next** (a reason and no command) ·
**Whole-help dump** · **Preferred but worse** (the recommended path lacks
what the other has) · **Status gap** (agents read the storage) · **Blocker**
(a command holds the turn) · **Nag** (a periodic message with nothing new) ·
**Go-look notice** (says something happened, not what) · **Wall of text** ·
**Silent cut** · **Lost write** (two calls at once, one result) · **Two
names** · **Leftovers** (nothing expires).
