# 01. Four test writes to production in one day

- **Date:** 2026-08-31
- **Failure class:** Test on production
- **Source:** Entries in the agents' hash-chained audit ledger, written by the agents involved.

## What happened

On 31 August two agents (the master and the site agent) wrote to live state four times while building and checking features:

1. The master ticked one of my real jobs in an internal planning app with a loose selector.
2. The site agent auto-tapped "photos taken" on a real decision card, and caught it.
3. A later pass by the site agent left a question answered "stop" and set a false "plates cleared" timestamp. No operation existed to clear it.
4. After the master mandated a test-safe write path for that one app, it tested a validation fix on a different API (the public toy-request endpoint) by posting two print requests with a missing and an invalid contact address. Both were accepted as real requests in the print queue.

The master then reported that the fix was not deployed. It had tested during deploy propagation; it corrected its own report in the ledger.

## Why

The apps had no separation between test writes and live writes, and each agent was being careful on its own. The first fix was scoped to the app where the failure happened.

## Mitigation

- A QA header for both apps: every validation runs, but the upstream write is skipped and the response is marked as a test. No user interface sends the header.
- After #10, the wider rule: no action verbs against live routes as tests; use inputs that must be refused, dry-run flags and fake stores.

## Lesson

The master's own ledger entry: "The lesson did not transfer because I scoped the fix to one API instead of to the class." When the same failure recurs across different careful agents, fix the tooling, not the agent. #10 is the same class two weeks later.

[Back to the incident table](../README.md#incidents)
