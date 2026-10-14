# 05. A standing approval and a cancelled market

- **Date:** 2026-09-09
- **Failure class:** Rule gap
- **Source:** The standing-approvals file (the rule was amended with the reason), the audit ledger, and the Autopilot shadow-run report.

## What happened

The market-comms standing approval lets countdown posts and thank-you emails for a market that is booked and paid go out after a 15-minute recall window, with no per-item tap. Its "never" clause said: nothing about markets not yet booked.

On 6 September two items (countdown posts and day-after thank-you emails covering two markets) went through the window and were released without a tap: the first use of the mechanism. On 9 September I cancelled one of those markets by email. A cancelled market is neither booked nor "not yet booked". The released items still named it, and nothing re-checked.

The notes show no post going out for the cancelled market. When the Autopilot later replayed past items, 2 of 5 market countdown items had been rejected by a person because that market was cancelled.

## Why

The class checked its precondition once. "Booked and paid" was true when the items were written and false three days later.

## Mitigation

- The class now excludes cancelled or withdrawn markets by name.
- The booking is to be re-checked at submit time, not grant time. That alone would not have caught this case, because the items were submitted before the cancellation.
- So in the Autopilot the class starts at "ask" (with a default) until the gateway checks the booking itself when an item goes out, and the chief-of-staff layer flags cancelled markets.

## Lesson

A standing approval is a promise about the world at one moment. Re-check its preconditions when the action happens.

[Back to the incident table](../README.md#incidents)
