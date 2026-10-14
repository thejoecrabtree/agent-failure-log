# 17. A retired endpoint the robot still called

- **Date:** 2026-09-29
- **Failure class:** Hidden dependency
- **Source:** The serving-upgrade notes (the agent's "lesson" entry) and the whole-stack review.

## What happened

During a model-serving upgrade the agent retired a local 30B model endpoint and repointed its names in the gateway to another model. Its search for callers skipped the robot's install directory. The robot's gateway called the old endpoint directly by local address, not through the gateway.

The robot had no language model from about 05:45 to 07:25. Its exported health metric stayed "up" throughout, because it measured the gateway process, not the model behind it.

## Why

The caller search covered the places the agent expected callers to be. The health check measured liveness, not function.

## Mitigation

- Before retiring any endpoint, sweep the service, config, home and systemd directories on every host for its address.
- The robot now has a fallback model on the same node with tight timeouts, and a deep health check every 30 seconds: a one-token completion on each model, plus speech in, speech out and vision.
- Metrics and alerts for each of those, including "running on the backup brain".

## Lesson

Retiring something is an action on every caller. Health checks have to exercise the dependency.

[Back to the incident table](../README.md#incidents)
