# agent-cli-writer

**The command is the manual.** A skill for designing, writing and reviewing
command-line tools that AI agents operate.

An agent reaches for the command, not the manual. It reads to confirm a plan it
already made, forgets the tool between sessions, sends calls in parallel, pays
again for every character printed, and cannot wait cheaply. A rule that lives
only in a document is followed when the agent happens to read it.

`agent-cli-writer` turns that into twelve checks for the tool itself: what each
command prints, what it refuses, how long work is started and reported, and how
to prove the result on an agent that has never seen the tool.

## What you get

A tool that teaches at the moment of the mistake:

```
$ tool machine new
created m-41 (stops by itself in 60 min)
note: work started by hand in this machine keeps no run record.
      for a job you need to follow:  tool job start --from <dir> -- <cmd>
```

and a refusal that is a lesson:

```
tool: refused: `job wait j-7` would hold this turn for up to 40 min.
      next:  tool job show j-7          (how it stands now)
             tool job start … --notify  (be told when it ends)
```

## The twelve checks

1. **Teach at the point of use** — the answer names the cost of the path taken.
2. **A refusal is a lesson** — what, why, and the exact next command.
3. **Carry the manual in the tool** — the tool prints its rules and asks for
   them before a hand-driven call.
4. **Make the right path the easy path** — consequences, not preferences.
5. **One command answers "where do I stand, and why".**
6. **Long work: start, return, tell** — no blocking, no polling, no nagging.
7. **Print little** — one screen by default.
8. **Two readers: a person and a program** — text, JSON lines, exit codes.
9. **Safe to repeat, safe at once.**
10. **Bound it and leave a record.**
11. **One name for one thing.**
12. **Secrets stay out.**

Then: how to write the tool's own manual so an agent cannot lose half of it,
and the test that counts, a fresh agent with a real task and nothing but the
command's name.

## Where it comes from

One week of nine agents building a product around the clock: 51,813 model
requests and 17,380 calls of one command-line tool, read from the transcripts.
A third of those calls were raw queries of the tool's own record, because its
status did not answer. Three quarters of the periodic messages sent to agents
were followed by nothing; three quarters of the "your run has ended" messages
were followed by work. An agent opened a tool's manual, lost the two lines it
needed to a display that cut long lines, and went around the rule it had read.

The figures, and which rules are measured and which are designs not yet
proven, are in
[`skills/agent-cli-writer/references/evidence.md`](skills/agent-cli-writer/references/evidence.md).

## Install

**Claude Code**

```
/plugin marketplace add panbergco/agent-cli-writer-skill
/plugin install agent-cli-writer
```

**Codex**

```
git clone https://github.com/panbergco/agent-cli-writer-skill ~/.codex/plugins/agent-cli-writer
```

**pi**

```
git clone https://github.com/panbergco/agent-cli-writer-skill ~/.pi/agent/skills-src/agent-cli-writer
ln -s ~/.pi/agent/skills-src/agent-cli-writer/skills/agent-cli-writer ~/.pi/agent/skills/agent-cli-writer
```

**Any other agent** — copy `skills/agent-cli-writer/` wherever your harness
reads skills from, or point your `AGENTS.md` at its `SKILL.md`.

## Use it

Say `/agent-cli-writer`, or describe the job: "design the CLI for this",
"review this tool for agents", "why do agents keep polling it".

## It's working if

- A fresh agent finishes a real task with no help lookup and no failed call it
  had to guess its way out of.
- Nobody reads the tool's storage or logs directly.
- No request is spent asking whether something has finished.
- The tool's share of the conversation's text goes down.

## Licence

MIT
