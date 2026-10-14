# 07. The robot's speech loops

- **Date:** 2026-09-11 and 2026-09-19
- **Failure class:** Feedback loop
- **Source:** The robot project notes and the sound-rules memo, both written by the agents.

## What happened

On 11 September, after one "Hi buddy", the robot's gateway answered every room fragment ("Yeah.", "Oh", laughter) inside its 45-second engaged window, and each answer re-armed the window. That made 40+ turns in 26 minutes. Its short-term memory filled with those pairs, so the model answered "Hi buddy!" to everything.

On 19 September the robot repeated "plate off the bed" all night until I unplugged it at 04:00. The cause was in the farm's voice code: the check that limited repeats had ended up inside a code comment, so every open need was said again every 10 seconds.

## Why

Engagement and reminder logic had no upper bound that did not depend on the code being right, and nothing stopped a machine from speaking into the house at night.

## Mitigation

- Fillers and fragments are ignored unless the robot's name is said. The engaged window extends per answered turn but is capped at 3 minutes since the name. A loop guard ends the conversation and clears memory when the same line comes up three times. Memory skips fragments and repeats.
- Sound rules I set: no speech from 20:00 to 06:00 except critical heat, smoke or fire; any attention message said at most 3 times, 3 minutes apart, then left to the screens; a silence button on every screen.
- One function is the only path to any speaker, and the robot's gateway enforces the same night rule as a second layer.

## Lesson

Anything that speaks into a home needs rate limits and quiet hours enforced below the model, in more than one place.

[Back to the incident table](../README.md#incidents)
