# 15. The approval judge published a post nobody approved

- **Date:** 2026-09-29
- **Failure class:** Misclassification
- **Source:** The whole-stack review notes and the Autopilot repository (the fix commit and its regression test).

## What happened

The Lab Autopilot gateway maps each proposed action onto a class in the constitution, then onto a verdict: do, recall, ask or block. The classifier is a set of deterministic keyword rules that an agent wrote from the design I approved.

At 09:06 on 29 September the social agent, by then running on the local 120B model, submitted a photo post. Its kind, "photo", was not one the classifier knew, so it fell through to keyword matching. It matched the printer word list and was filed as "farm planning", level 3: just do it. The gateway approved it in about 20 seconds, with no recall window. The record said approved "by joseph", the name the tool uses for the family's human approver. The social agent then published the post to the business's Instagram and Facebook.

At 07:08 the same morning a CEO request had been approved the same way, in 21 seconds. It was about checking that age and choking warning signs existed for a market. It mentioned choking only in its body, and for typed requests safety words were checked only in the title.

The day before, an agent had told me social posts needed my approval. That was true of the old path and false once the Autopilot could approve.

## Why

The authority matrix was right. The code that mapped an action onto a row of it was wrong, and it read text the agent wrote. An unknown kind fell through to a keyword path that could reach level 3.

## Mitigation

Same morning:
- Posts, and anything from the social or photo agents, are always "a post outside an approved series": ask, with "hold, not posted" as the default.
- Safety words are read in the whole body, whatever the kind.
- Unknown site kinds become "site feature" (ask).
- Level-3 farm planning needs an explicit print or schedule action from a farm actor.
- Printer keywords give a business decision (ask), never a level-3 "do".
- CEO proposals always wait for a person, with no default.
- Regression tests for each rule.

Also: the social and CEO agents were held, and the social standing approvals were disabled: every post needs a person.

## Lesson

Derive the class from who is acting and through which channel, not from words in the request. Any path that can reach "just do it" must be explicit, never a fallthrough. And an agent's statement about what is gated is not evidence.

[Back to the incident table](../README.md#incidents)
