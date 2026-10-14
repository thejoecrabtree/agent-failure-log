# 13. Two automation rules fought over printer power

- **Date:** 2026-09-23
- **Failure class:** Automation conflict
- **Source:** The printer power-rule memo.

## What happened

The farm software had a sleep rule and a wake rule, both written by agents. The sleep rule cut a printer's smart plug after a quiet period. The wake rule saw a plate "ready to start" on that printer and switched the plug back on 0.2 to 1.9 seconds later. This repeated about every 30 minutes on four printers from 08:58.

On its second bounce one printer's camera failed: the printer reported its media pipeline malfunctioning, and the camera stream closed while its other services stayed up. It needed a full power cycle by a person.

## Why

Each rule was reasonable on its own. Nothing coordinated two rules that drove the same actuator.

## Mitigation

- Sleep skips any printer the wake rule would restart.
- The farm never powers a printer back on within 60 seconds of cutting it.
- The standing rule: the farm may switch a printer's plug for exactly two reasons, idle sleep with all its guards and a real fire, smoke or heat alarm. A person turns printers back on after an alarm. The air-quality sensor has no path to a plug. New automation that wants a plug asks me.

## Lesson

A physical actuator needs a single owner, or an explicit interlock between the rules that drive it.

[Back to the incident table](../README.md#incidents)
