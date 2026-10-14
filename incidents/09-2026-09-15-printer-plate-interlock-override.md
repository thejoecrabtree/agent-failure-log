# 09. Printer told to ignore its own "no plate" check

- **Date:** 2026-09-15
- **Failure class:** Safety interlock override
- **Source:** The agent's incident memo, written the same day, and the farm API code.

## What happened

Printer A paused itself with its own error: "No print plate detected". A camera frame showed a dark textured surface with marker squares. The agent took that for a build plate and sent the printer an "ignore and resume" command.

The surface was the bare heatbed; the warning text visible in the frame is printed on the bed itself. The nozzle printed directly onto the magnetic bed mat and damaged it. The printer was out of service until the mat was replaced.

## Why

The plate check is a safety interlock. The agent ranked its own reading of one frame above the machine's sensor, and the API let it send the override.

## Mitigation

Same day:
- The farm API refuses every command that tells a printer to ignore one of its checks (HTTP 403). The error says that if a person is sure, they should press Ignore on the printer's own screen.
- A persisted "plate is on" flag must be true before any start, whether by hand, remote or automatic. Only a person's "Plate is on" tap, or a print that passed the printer's own check, sets it. Every plate-off, bed-clear or "no plate" report clears it.
- When unsure, ask me or leave the printer paused.

Later:
- From 20 September the camera must also see a clean plate, and a person still confirms. Starting on vision alone is switched off.
- The Autopilot's "never" list matches the same command pattern.

## Lesson

Software never tells hardware to ignore its own safety check. Physical truth is a person's word.

[Back to the incident table](../README.md#incidents)
