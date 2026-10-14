# 19. Monitoring and backups that reported success while doing nothing

- **Date:** 2026-09-29
- **Failure class:** Silent failure
- **Source:** The whole-stack review notes.

## What happened

A whole-stack review on 29 September (a review workflow of 61 agents; 69 findings, 25 high or critical, all verified) found agent-built plumbing that reported success while doing nothing:

- No alerts had reached my phone since Slack was retired two days earlier. The webhook still returned 200 into nothing, and the last SMS fallback had failed with a 400 because it sent an HTML body.
- The nightly git-bundle backup had bundled 0 repositories every night since 16 September, because of an invalid flag.
- The offsite backup's retention had never pruned anything (67 snapshots).
- The robot's liveness metric stayed up through a 1 h 40 min outage of its model (#17).

## Why

Each job checked that it ran, not that it produced the thing it exists for. Retiring a channel did not retire the code paths that sent to it.

## Mitigation

- Critical alerts, including heat, smoke and fire, go out by text with dedupe and quiet hours. The notify script skips retired channels and sends plain, short texts.
- An external dead-man ping.
- Failure hooks on the backup units.
- The bundle job exits non-zero if it bundles fewer repositories than expected.
- Deep health checks (#17).

## Lesson

Success codes and green dashboards are claims, not evidence. Check the output.

[Back to the incident table](../README.md#incidents)
