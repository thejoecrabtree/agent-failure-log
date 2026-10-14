# 06. `git reset --hard` on the server wiped live wiring

- **Date:** 2026-09-10
- **Failure class:** Shared-state clobber
- **Source:** The agent's incident memo (it called the incident its own fault).

## What happened

The agent deployed the console's API to the rack server four times with `git reset --hard origin/main`. The server's checkout was on its own lineage and held uncommitted work from that day: the wiring for inbound text messages. Each reset deleted it.

From about 13:50, inbound texts silently stopped being processed. My text "We shut down the printer" was never seen, so the farm kept nagging me every 30 minutes for 5.5 hours.

## Why

Agents worked directly on the server and did not always push. The deploying agent assumed the checkout matched GitHub.

## Mitigation

- On the server, run `git status` and `git log origin/main..HEAD` before touching anything; if it is dirty or ahead, commit and push from there first.
- Never `reset --hard` there. Fast-forward only, or copy the single changed file.
- After every API deploy, a text smoke test: a simulated inbound text must get an answer.

## Lesson

Destructive git commands on a machine other agents use are a shared-state hazard. A deploy check should exercise the path that matters most, here a person telling the system something, not just `/health`.

[Back to the incident table](../README.md#incidents)
