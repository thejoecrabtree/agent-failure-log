# 24. Parallel agents reverted each other's files

- **Date:** 2026-10-08
- **Failure class:** Shared-state clobber
- **Source:** The agent's incident memo and the project notes.

## What happened

In a parallel build of the same website, about 14 package agents edited one source tree. The navigation agent ran its generator in a scratch shadow copy, then copied back every file that differed. That reverted six files other agents had edited in the 17 seconds in between, and four packages lost work.

Then the lead session messaged six running workflow agents. Each message resumed a second copy of the agent with the same ID, giving duplicate writers. Stopping those IDs killed both copies, including the workflow's own agents.

## Why

Exclusive-file lists do not help if any step writes files it does not own. And the control channel itself, messaging an agent, could create a writer.

## Mitigation

- Every parallel brief says: write only your exclusive files, each by explicit name; never sync or copy a shadow, a backup or a directory back into the tree; output for files you do not own goes in a shared request list.
- The integrator checks each package's changed-files list against the tree before the full build.
- Never message agents inside a running workflow. Let the healthy ones finish, stop the run, and start a new, smaller workflow for the affected packages.

## Lesson

Shared trees need ownership enforced per file. Know what your control actions actually do before using them mid-run.

[Back to the incident table](../README.md#incidents)
