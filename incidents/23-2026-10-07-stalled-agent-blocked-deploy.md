# 23. One stalled agent blocked an overnight deploy

- **Date:** 2026-10-07
- **Failure class:** Stall, no time-box
- **Source:** The agent's incident memo and the project notes.

## What happened

An overnight multi-agent workflow rebuilt a website for another company I run. I had set a hard deadline: a finished site when I woke at 06:00. One page package's agent was still "polishing" after about 6 hours. The workflow script waited for every phase-1 package to finish before integrating and deploying.

The 28 finished packages did not reach the live site until the lead session ran an emergency deploy at 05:55. The stalled package's pages were missing at launch.

## Why

No agent had a deadline of its own, and integration waited for all packages to finish.

## Mitigation

- Every agent prompt carries a wall-clock deadline: check the time, stop and report by T.
- Integrate and deploy incrementally, without waiting for stragglers.
- A lead-side watchdog at T minus 90 minutes, not just T minus 60.
- Check stragglers' progress hourly instead of waiting for notifications.

## Lesson

One agent without a deadline can hold every other agent's finished work hostage.

[Back to the incident table](../README.md#incidents)
