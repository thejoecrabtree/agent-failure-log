# 10. A live "test" stopped a real print

- **Date:** 2026-09-17
- **Failure class:** Test on production
- **Source:** The agent's incident memo.

## What happened

The agent built a new endpoint that lets a text reply act on the farm's open alert, for example "FINE" or "STOP <printer>". To test it, it sent "FINE" and "STOP printer B" to the live farm.

An AI-watch alert happened to be open on printer B. "FINE" graded the farm's own decision. "STOP" stopped a real print at 5%, put the printer into "failed" with the bed to clear, and queued a retry. It also mis-graded the autonomy ledger that decides when the farm may act on its own verdicts. The ledger was corrected by hand.

## Why

The route acts on the newest open alert, and on a busy farm there is always one. The agent used a real verb as a test input. The lesson from #01 had been applied to two apps, not to the class.

## Mitigation

- Never send action verbs (stop, resume, off, start, decide, fill) to the live API as a test.
- Verify with inputs that must be refused (`hello` must come back "not handled"), a dry-run or preview flag, unit tests against a fake store, or read-only views.
- If a live check is truly needed, ask me first and pick an idle printer.

## Lesson

The same class as #01, two weeks later, on a different system. Rules about testing need to be written about the class of action, not the endpoint.

[Back to the incident table](../README.md#incidents)
