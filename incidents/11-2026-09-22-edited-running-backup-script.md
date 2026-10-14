# 11. Editing a script while it ran

- **Date:** 2026-09-22
- **Failure class:** Shared-state clobber
- **Source:** The agent's incident memo.

## What happened

The agent edited the offsite backup script in place to add an archive, while that night's run was 2.5 hours into a 4-hour upload. Bash reads a script by offset as it runs. After the snapshot was saved it read a shifted line and died with a syntax error. `bash -n` on the edited file was clean.

The snapshot was fine. The retention step was skipped and had to be run by hand.

## Why

The agent did not check whether the script was running, and did not know that bash reads scripts incrementally.

## Mitigation

- Never edit a long-running script in place. Write the new version to a temp file in the same directory and `mv` it over the old one; the running shell keeps the old file.
- Check whether the unit or script is running first.
- The same applies to files copied over with scp: copy to a new name, then `mv`.

## Lesson

Static checks can pass on code that a running process will misread.

[Back to the incident table](../README.md#incidents)
