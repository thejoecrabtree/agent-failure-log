# 20. Credentials that were too broad, and copies of them

- **Date:** 2026-09-29 and 2026-10-13
- **Failure class:** Over-broad credentials
- **Source:** The whole-stack review notes (2026-09-29) and a read-only security review (2026-10-13).

## What happened

Three findings:

- **29 September:** while rewiring an alert hook, an agent printed part of the hook's token in its output.
- **29 September:** the SSH key used by a separate machine, which ran a different vendor's assistant (part of the setup, not a Claude agent), gave passwordless root on a lab host. It had logged in 413 times in 7 days.
- **13 October:** the model gateway's master (admin) key had also been used as the services' API key. Copies sat in shell history, training scripts, several env files, a log store's write-ahead log, 193 config backups and an agent transcript.

## Why

Convenience: one powerful key worked everywhere, so it was used everywhere. Agent transcripts and backups keep whatever passes through them.

## Mitigation

- The hook token was rotated.
- The SSH key was restricted to one source address with forwarding disabled, and that assistant's session on the rack was retired.
- The master key was rotated. Services moved to a scoped key that gets 403 on admin endpoints. The old key was verified to fail locally and from the internet.
- The authority file already said secrets are the owner's: agents may say which key is missing and where it goes, never see, store or repeat a value. The Autopilot's "never" list matches secret-looking assignments.

## Lesson

Scope keys so that a copy in a transcript or a backup is worth little. Assume any value an agent touches will be copied.

[Back to the incident table](../README.md#incidents)
