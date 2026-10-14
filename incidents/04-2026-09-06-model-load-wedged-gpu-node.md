# 04. Model loads wedged a GPU node

- **Date:** 2026-09-06 and 2026-09-22
- **Failure class:** Resource exhaustion
- **Source:** Two incident memos by the agents involved.

## What happened

On 6 September an agent loaded an image model (31 GB on disk, about 20 GB in memory) inside a live container for a benchmark, while the host had about 13 GB available.

On 22 September the same node wedged again. Several models were resident, a 44 GB image model was rendering, and two 7 GB model containers were recreated under compose. Available memory went from 53 to 26 to 16 GB and then to nothing in 15 minutes.

A DGX Spark has 121 GB of memory shared by CPU and GPU. When it runs out, the box does not kill a process; it thrashes. Ping answered, SSH did not. Both times the public toy generator and other services were down until a person pressed the power button; there is no BMC. On the second occasion the containers' restart policies would have wedged the node again on boot.

## Why

Container-level statistics do not show unified-memory GPU allocations, and the host's free memory was not checked first.

## Mitigation

- Before starting any GPU service, read the host's available memory and keep at least 20 GB headroom after the new service loads.
- Never benchmark inside a live container. Never recreate several GPU containers while another is mid-generation.
- The large image model runs on demand only, never resident.
- A watcher stops the offending containers and disables their restart the moment SSH answers again.

## Lesson

Shared physical resources need a pre-flight check that the agent must perform, better still one the scheduler enforces.

[Back to the incident table](../README.md#incidents)
