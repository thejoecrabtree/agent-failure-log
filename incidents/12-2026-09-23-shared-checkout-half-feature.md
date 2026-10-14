# 12. One session shipped another session's half-finished feature

- **Date:** 2026-09-23
- **Failure class:** Shared-state clobber
- **Source:** Two incident memos written by sessions involved.

## What happened

Several Claude Code sessions were open on the same checkout of the farm repository. One made a six-line fix and ran `git add` on the server file. That file also held another session's uncommitted half of a feature, which called a function in a second file that was not committed. The commit shipped 33 lines and was deployed.

The board route then threw "is not a function" 365 times in 10 minutes. All five lab screens and the phone app read that route. The server's deploy gate was blocked for everyone for about 8 minutes. Tests had passed on the Mac because its working tree had the missing file. The half feature went live with no known author, and the two sessions then built conflicting versions of it.

## Why

`git add <file>` commits whatever is in the file, including other agents' uncommitted work. Four or five sessions often shared one checkout and committed with broad adds.

## Mitigation

- Any change in a repository other sessions touch goes in its own git worktree off `origin/main` and merges in one commit.
- A change that calls a new library function ships in the same commit as the function.
- Check recent commits for the same feature before building it.
- After deploying, read the server's test result, not the laptop's.
- If committing in place is unavoidable, stage hunks and read the cached diff first.

## Lesson

A shared working tree is shared mutable state. Each agent needs its own.

[Back to the incident table](../README.md#incidents)
