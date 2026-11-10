# What the rules rest on

One fleet, one week: nine AI agents building a product around the clock, 51,813
model requests, read from their transcripts and from the record of the tools
they used. Two command-line tools were studied: a delivery tool that runs the
agents' work, and a tool that hands out disposable test machines.

Figures are counts from that week. Dollar figures are list prices of tokens.
One fleet is not a law. Treat each number as a reason to measure your own.

## Measured

| What was seen | Figure | Rule |
|---|---|---|
| Calls of the delivery tool's commands | 17,380 | |
| of them, raw queries of the tool's own record | 5,806, about $710 | 5 |
| Help lookups | about 330 | 2, 3 |
| Requests spent waiting on a run (sleep, process list, log tail) | 4,384 | 6 |
| Calls of the tool's own "wait" verb that were refused | 36% of 188 | 4, 6 |
| Periodic "carry on" messages sent to agents | 3,781 | 6 |
| after which the agent did nothing | 75% | 6 |
| that arrived while the agent's own run was still going | 60% | 6 |
| "Your run has ended" messages | 675 | 6 |
| after which the agent wrote code or started a run | 77% | 6 |
| Ends of a long job that no message announced | 120 | 6 |
| learned from another agent's message, minutes later | 69% | 6 |
| Characters in each periodic message, the same text each time | 1,790 | 7 |
| Deliveries of it cut off at 2,000 characters, to one agent | 208 of 705 | 7 |
| Average size of the status answer, called 617 times | 3,121 characters | 7 |
| Tokens an average request read again | 293,000 | 7 |
| Failed calls answered by a usage line | 208 | 2 |
| of them, right at the next call of that subcommand | 77% | 2 |
| Failed calls with an exit code and no words | 40 | 2 |
| Requests using the machine tool's commands | 1,579 | 3 |
| Times its manual was opened | 28 | 3 |
| Manual descriptions carried in every request | 81, about 24,600 tokens | 3, 7 |
| of those manuals, opened at least once in the week | 30 | 3 |
| Single actions the delivery tool refused with a message | 2,713 | 2 |

## Seen once, in detail

- **An agent opened the manual and still went around it.** It printed the
  file with lines cut at 240 characters; 12 of the manual's 149 lines were
  longer, and the two rows it needed were among them. The manual said to
  prefer one command; the agent had already planned another, because the
  preferred one blocked for the whole job and could not take its input. Five
  calls of the tool followed, and none of their answers mentioned the
  consequence. (Rules 1, 3, 4, and the line-length rule for manuals.)
- **Two calls at once, one write.** Agents send independent calls together.
  Two calls that each rewrote one file from the copy they had read at the
  start lost one of the two changes. (Rule 9.)
- **An edit and a commit in one message.** Both ran at once; the commit read
  the file before the edit landed. Four wrong commits in a day. (Rule 9.)
- **The same test, 17 times.** A job's result was kept only while the whole
  repository was unchanged, so another agent's unrelated commit made the tool
  run it again. Half of all runs repeated an earlier one on unchanged work.
  (Rule 9: repeating is safe only if it is also cheap.)

## Designed in response, not yet proven

- The gate of rule 3 (`tool rules`, a version code, hand-driven verbs
  refusing a caller without it) is a design. It has not been built or run.
- The note of rule 1 in the example is a design for the machine tool. The
  pattern itself is proven by the delivery tool's refusals, which agents
  follow; the note has not been tried.
- The budgets of rule 7 (40 lines, 2,000 characters) and of the manual (150
  lines, 100 characters a line) are chosen, not measured optima.

## How to measure your own tool

From transcripts of real sessions, count per week:

1. Help lookups.
2. Failed calls, and failed calls with no words.
3. Calls repeated after a failure before one succeeded.
4. Requests spent waiting: sleeps, process lists, log tails.
5. Direct reads of the tool's storage or logs.
6. Characters returned, by command.
7. Messages sent to agents, and the share after which nothing was done.
