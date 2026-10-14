# 08. A deploy landed on the wrong site

- **Date:** 2026-09-12
- **Failure class:** Wrong target
- **Source:** The deploy-pipeline and quoting-agent notes. The memo does not record which session ran the command.

## What happened

A bare production deploy command was run from a checkout of the quotes site that was not linked to its own hosting project. It landed in the main site's project and replaced texastoylab.com with a build that had no API functions. Every route ended in a 404 for about 20 minutes.

A second, older fault then stretched the outage to about an hour: the deploy allowlist excluded the package manifest, so every build shipped functions without their dependencies. They had only ever worked from a cached build.

## Why

The deploy tool picks its target from the directory it runs in. Nothing checked that the target was the intended one.

## Mitigation

- The quotes site deploys only through a script that refuses unless the checkout is linked to the quotes project.
- Main-site deploys go through an audit script that probes a live API route afterwards and expects a known error code.
- A deployment in the hosting dashboard with no matching ledger entry means something deployed outside the gate.
- Recovery is to promote the last known-good deployment.

## Lesson

Implicit targets are a blast-radius problem. Make the target explicit, and check it before the action.

[Back to the incident table](../README.md#incidents)
