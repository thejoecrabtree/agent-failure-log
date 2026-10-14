# 14. Agents dying silently

- **Date:** 2026-09-26 to 2026-09-29
- **Failure class:** Silent failure
- **Source:** The whole-stack review notes (2026-09-29) and the agent-swarm notes (2026-09-28).

## What happened

The ops agent ran Claude Code against the local 120B model with a 131,072-token window. On 26, 28 and 29 September it filled the window (122,881 prompt tokens plus 8,192 reserved for output) and stopped. On the 29th it was dead from 06:33 until a review found it at about 09:35.

Separately, on 28 September:
- The server's Claude login lost its refresh token. The CEO and master agents showed "Login expired", while the health check reported only "tick missed".
- An informational notice shown at start-up to sessions on the local model waited for Enter, and wedged three agents for 3 hours.

## Why

The health check looked for symptoms it already knew. A session that has stopped working can look like a session that is idle.

## Mitigation

- Automatic compaction well below the context window (later lowered further, see #21).
- A fresh ops session twice a day.
- The command that sends work to an agent confirms delivery and exits non-zero otherwise.
- The health check names "Claude login expired" and warns three days ahead. Agents start with a long-lived token that I generate, never an agent.
- The health check presses past the one known start-up notice, by an allowlist.

## Lesson

A process that exists is not an agent that works. The scheduled fresh session fixed this and caused #21.

[Back to the incident table](../README.md#incidents)
