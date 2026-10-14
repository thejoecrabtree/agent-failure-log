# 22. A power-cycle by an unverified map rebooted the wrong node

- **Date:** 2026-10-05
- **Failure class:** Wrong target
- **Source:** The serving notes, written by the agent.

## What happened

After the 2 October freeze (#21) the agent first told me there was no remote power option. There was: a switched PDU, documented in the lab's spec repository. The agent then built a power-cycle tool on the outlet map from that repository.

On 5 October the 120B node froze again. Cycling its mapped outlet rebooted a different, healthy node, because the map was wrong. That node's model was down for about 8 minutes; gateway fallbacks covered it.

## Why

The outlet map was documentation that nobody had checked against the cables at the rack. The agent treated it as fact.

## Mitigation

- The tool was locked (it exits with an error) until each outlet is verified at the rack.
- Two outlets were then verified by switching one off for about 6 seconds and watching which machine dropped. The others stay locked.
- The tool requires a node to have been down for 10 minutes unless forced.

## Lesson

Documentation about the physical world is a claim. Verify it before acting on it, and lock the actuator until you have. Check the docs before saying something cannot be done.

[Back to the incident table](../README.md#incidents)
