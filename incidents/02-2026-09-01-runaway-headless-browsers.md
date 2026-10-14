# 02. Runaway headless browsers

- **Date:** 2026-09-01
- **Failure class:** Resource exhaustion
- **Source:** Lab state notes written by the agent that investigated.

## What happened

Agents doing browser automation for the website left two headless WebKit browsers running on the rack server that hosted the agent fleet. They ran for about 6 hours at about 1000% CPU and the processor reached 87 °C. Fan noise was the first sign. The fleet's legitimate peak over the previous week was about 10.7 threads; the incident reached about 30.8.

## Why

Nothing bounded the agents' resource use. Capacity was never the constraint (the fleet normally used about 5% of the machine); isolation was.

## Mitigation

- systemd slice limits on the agents' user: 16 of 48 CPU threads, soft and hard memory caps and a task cap, load-tested with 24 CPU burners.
- A cron job that reaps headless browsers older than 2 hours.
- A runaway-process check in the 15-minute health check (CPU over lifetime, age, memory against the soft cap, throttling).

## Lesson

Agents that start processes need a resource boundary that does not depend on them cleaning up.

[Back to the incident table](../README.md#incidents)
