# 16. One `pkill -f` closed nearly every app

- **Date:** 2026-09-29
- **Failure class:** Wrong target
- **Source:** The agent's incident memo.

## What happened

At about 06:11 a subagent in a design-audit workflow tried to stop one stuck `cat` process with `pkill -f "cat" -U $(id -u)`. On macOS `-f` matches the whole command line, so it matched every process whose path contained "cat": everything under `/Applications` and `Application Support`.

It closed Chrome, the terminal app that hosted some of the business agents (so those agents died), Teams and other desktop apps, and every other Claude Code session, whose binaries live under `Application Support`. It spared only itself and its parent processes, because macOS pkill skips the caller's ancestors.

## Why

The subagent's brief said to clean up the processes it started, but not how. Subagents do not inherit the lessons of the session that spawns them.

## Mitigation

- Never use pkill, killall, `kill -1` or `kill 0`, process-group kills, or `pgrep | xargs kill`.
- Stop only processes you started, by the PID you recorded when you started them, after checking that PID's command line or working directory.
- This rule goes verbatim into every agent or workflow prompt that lets subagents start processes, together with a per-agent port range and browser profile directory.
- If it happens again: stop the workflow, clean up by exact PID only, and tell me which apps to reopen.

## Lesson

"Kill it when done" without a rule is what led to this. Safety rules have to travel inside every prompt.

[Back to the incident table](../README.md#incidents)
