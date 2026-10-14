# 03. The self-approval guard misread who was calling

- **Date:** 2026-09-02
- **Failure class:** Control misfire (failed closed)
- **Source:** The console's project notes, written by the agent that fixed it.

## What happened

The approval tool refuses to let an agent approve its own request. It decided who was calling by asking the terminal multiplexer (tmux) for the current session name. When the console's API called the tool from outside tmux, tmux still answered with the server's current session, which was the master agent's. My approvals from the console were refused as master self-approvals. The card said "failed" and the item stayed pending.

## Why

Identity was inferred from ambient context instead of being passed explicitly.

## Mitigation

- Trust the session name only when the process really runs inside tmux.
- The console shows the tool's output when a decision fails.
- Test the decide path with a throwaway item that is submitted, decided and removed.

## Lesson

Failing closed was the right direction. But a separation-of-duties check is only as good as its source of identity, and that source should be explicit.

[Back to the incident table](../README.md#incidents)
