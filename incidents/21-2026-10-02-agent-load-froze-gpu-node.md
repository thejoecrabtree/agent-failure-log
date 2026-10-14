# 21. Agent load bursts froze a GPU node

- **Date:** 2026-10-02, 2026-10-05 and 2026-10-13
- **Failure class:** Resource exhaustion
- **Source:** The serving notes and the vision-offload notes, with the agent's cause analysis.

## What happened

After #14 the ops agent got a fresh session at 06:20 and 16:50 every day. It ran Claude Code against the local 120B model on one DGX Spark node. A fresh session sent prompts of 45k to 120k tokens every 6 to 15 seconds, on two streams.

- **2 October, 17:23:** the node stopped answering ping on both networks. Metrics a minute earlier looked normal. With no BMC or smart plug it stayed down until I pressed the power button at 05:17 the next morning. Gateway fallbacks moved its traffic to another model.
- **5 October, about 17:15:** it froze the same way, a minute after a burst of 120B load (see #22 for what the power-cycle did).
- **13 October, 17:24:** the inference engine wedged. A watchdog restarted it at 17:41. Prompts reached 79k tokens despite a 60k compaction window.

All three followed the 16:50 burst.

## Why

Agents on shared inference are noisy neighbours. The fix for #14 synchronised the heaviest load to two fixed times a day.

## Mitigation

- Lower automatic compaction for agents on the local model (60k, then 45k tokens) and cap shell output in their context.
- The engine on that node serves one sequence at a time.
- A timer power-cycles the node through the switched PDU if it freezes, at most once every 2 hours, and texts me if the cycle fails.
- Held in reserve: move the ops agent to a smaller model on another node, or drop the 16:50 restart.

## Lesson

Test mitigations for second-order effects on shared resources.

[Back to the incident table](../README.md#incidents)
