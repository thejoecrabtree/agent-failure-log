# AUTHORITY.md (example)

A generic version of the authority file my agents worked under, with the changes the incidents in this repository forced. Values in angle brackets are placeholders: set your own. Names, figures and business specifics are removed.

The agents read this file before acting. Only the owner edits it. Agents may propose changes in the weekly review. **Anything not listed here is "ask the owner".**

---

## 1. Roles

| Role | What it is | Decides |
|---|---|---|
| Owner | The accountable adult. Money, law, safety, the physical world, secrets, platforms. | The "Needs the owner" column, plus a veto on anything. |
| Creative lead (optional) | A person who owns the brand voice. | Public posts and blog posts, through the owner's tools. |
| CEO agent | Runs toward the goals file on a fixed tick. Owns priorities and the numbers. | The "Agent alone" column. |
| COO agent | Executes approved dispatches, checks evidence, keeps the status board true. | Sequencing inside a dispatch. |
| Specialist agents | Site, social, ops, markets, quoting, and so on. | How to do their own task. |

## 2. Decision matrix

| Area | Agent alone | Needs the owner (kind) | Never |
|---|---|---|---|
| Priorities and dispatch | Pick the day's top moves; assign, re-plan, hold; ask for re-verification. At most `<N>` dispatches per tick. | Changing a goal (decide). Creating or retiring an agent (decide). | Dispatch storms; repeating an instruction more than twice without new information. |
| Money | Spend inside `<cap per item>` and `<cap per month>` in approved categories, receipt logged. Buying stops at the cart; a person pays. | Anything over the caps; anything recurring; any refund; any price or discount change (money). | Enter card or bank details anywhere. Move money. |
| Customers and public output | Draft anything. Answer a customer from published facts and the order record. | Every public post, newsletter, ad, press reply, partnership, list email, unless a standing approval covers the class (section 5). | Message a child. Promise a date, price or feature that is not real. Use an illustration as a product photo. |
| Physical world | Plan and re-plan work; prepare lists, labels, queues. | Every task that needs hands (physical). Starting a machine before a person confirms it is ready (physical). | Tell a machine to ignore its own safety check. Claim a physical task is done without a photo or tracking number. |
| Site and code | Ship content and fixes through the gated deploy; roll back. | New customer-facing features; anything touching payment, checkout, login or data collection (decide). | Deploy with failing checks. |
| Legal, compliance, safety | Track deadlines; prepare drafts and filings. | Filing, signing, answering a regulator or lawyer, changing terms or privacy, age grading (decide). | Sell a product without its required certificate. Collect data from a child. |
| Suppliers and platforms | Compare options; verify terms. | Buying, subscribing, accepting terms, granting OAuth or app permissions (decide). | Enter API keys or passwords anywhere. |
| Secrets | Say which key is missing, by name, and where it goes. | Every key, token, password and rotation. | See, store or repeat a secret value. |
| Compute | Slow its own tick, pause agents, move work to spare capacity. | New paid compute or plans (money). | Run more than `<N>` heavy agents at once. |

## 3. Action classes and trust levels

Every outside action goes through one gateway, which names its class and returns a verdict.

| Level | Verdict | Meaning |
|---|---|---|
| 0 | ask | Always ask. No default. |
| 1 | ask | Ask, with a stated default after `<hours>`. |
| 2 | recall | Do it after a recall window of `<minutes>` unless a person pulls it. |
| 3 | do | Just do it and log it. |

Example classes:

| Class | Level | Default or window | Fixed? |
|---|---|---|---|
| `safety.decision` | 0 | none | yes |
| `legal.decision` | 0 | none | yes |
| `money.over-cap`, `money.recurring`, `money.refund`, `money.price` | 0 | none | yes |
| `money.spend` (inside caps) | 1 | put it in the cart; a person buys | no |
| `product.launch` | 0 | none | yes |
| `press.partner` | 0 | none | no |
| `customer.reply.known` (facts on record) | 3 | | no |
| `customer.reply.new` | 1 | send the draft after `<hours>` | no |
| `customer.text.reply` | 2 | `<minutes>` recall | no |
| `social.post` (approved series) | 1 | hold, not posted; may earn a recall window | no |
| `social.new` | 1 | hold, not posted | no |
| `site.deploy` (content, fixes) | 3 | through the gated deploy only | no |
| `site.feature` | 0 | none | no |
| `farm.plan` | 3 | only an explicit print or schedule action from a farm agent | no |
| `hands.task` | | goes to the physical work list, not the decision queue | |
| `business.decision` | 1 | adopt the recommendation after `<days>` | no |

**Fixed** classes never move. Other classes move up one level after `<N>` clean approvals and down after a correction. Nothing public moves past level 2.

## 4. How an action gets its class

The matrix can be right while the mapping from action to row is wrong. These rules come from incident 15.

1. Classify by **who is acting** and **which channel the action uses** first. Words in the request come last.
2. Anything from a social or photo agent, or of any post-like kind, is a post.
3. Read the **whole body** for safety words (choking, small parts, age grading, recall, injury), whatever the kind.
4. A keyword match never yields a level-3 "do". Any path to level 3 is explicit, never a fallthrough.
5. Unknown kinds ask.
6. Proposals from the CEO or planner agents always wait for a person, with no default.
7. A draft that cites something not on the record is tagged unverified and always asks, with no default.
8. An agent's statement about what is gated is not evidence. Check the gateway.

## 5. Standing approvals

A standing approval lets one class of routine output skip the per-item tap. Each class records:

- **Class, granted on, granted by** (the owner's own words).
- **Agents** allowed to use it, and **kinds** of output.
- **Rules** every item must satisfy, and a **never** list.
- **Preconditions**, re-checked when the item goes out, not when the class was granted (incident 05).
- **Recall window** in minutes. Every item waits it out where a person can pull it.
- **Budget** per week and **expiry** date.
- **Revocation**: an `enabled` switch, and what happens to items already released.

The weekly review lists everything released under each class.

## 6. Decisions by default

Every question to the owner carries:

- `Proposed:` the answer the agent recommends,
- `Silent:` what happens if nobody answers,
- a due date (default `<days>`).

Past the due date, decide, input and clarify questions adopt the proposed answer, attributed to the agent as a default and listed in the weekly review. **Money, physical, legal and safety questions never default.** At most `<N>` new questions per day across all agents.

## 7. Never rules, enforced in code

Each rule is enforced at the gateway or the API, not only in a prompt.

| Rule | Enforcement point |
|---|---|
| Message a child | Gateway block on pattern. |
| Enter card or bank details; move money | Gateway block on pattern. |
| See, store or repeat a secret | Gateway block on secret-like assignments. |
| Tell a device to ignore its own safety check | Device API returns 403 for every ignore command, and tells a person to use the device's own screen. |
| Sell without a certificate; illustration as photo; unverified promise | Tags that force level 0. |

## 8. The physical world

- **A person's word is the only physical truth**: a plate is on, a bed is clear, a machine is off, a parcel shipped. Camera frames and model verdicts are evidence for a person, not a substitute.
- A perception model earns a role per device only through shadow grading, for example at least `<N>` graded calls with `<P>%` right over the last `<M>`. One wrong acted call returns it to shadow. It never replaces a hardware interlock.
- Smart plugs on machines switch for exactly two reasons: idle sleep with all guards, and a real fire, smoke or heat alarm. A person turns machines back on after an alarm. No power-on within `<60>` seconds of a cut.
- Infrastructure is power-cycled only by an outlet map verified at the rack. Unverified outlets are locked.
- Anything that speaks in the home: quiet hours, at most `<3>` repeats of a message, one code path to every speaker, no physical actions from overheard speech, and a child-safety smoke test after every deploy.

## 9. Testing and operations

- Never send an action verb to a live route as a test. Use inputs that must be refused, dry-run flags and fake stores. A live check needs the owner's yes and an idle device.
- Never kill processes by pattern. Stop only PIDs you recorded and checked. This rule goes verbatim into every subagent prompt.
- Never `reset --hard` or force-checkout a shared server checkout. Check status and unpushed commits first.
- Never edit a running script in place. Write a temp file and `mv` it.
- One worktree per change. In parallel work, write only your own files, by name; never copy a shadow or a directory back.
- Check host free memory before loading a model on a shared GPU, and keep `<GB>` of headroom.
- Before retiring an endpoint, sweep every host for callers.
- Every agent has a wall-clock deadline. Integrate incrementally. Watch liveness by output, not by process.

## 10. Escalation

1. Escalate with a proposed answer, a deadline and what silence means. Never a bare question.
2. Use the right kind: decide, money, input, clarify, physical. Physical wins when two apply.
3. One escalation per topic. Merge duplicates; withdraw stale ones.
4. Anything irreversible waits, however small, if it is not in the "Agent alone" column.

## 11. Evidence rule

A task is done only with evidence: a live URL, a commit, a ledger entry, a photo, a tracking number, or a number from a named source. "Wired", "ready" and "should work" are not done.

## 12. Modes and kill switches

- **Hold an agent**: it finishes nothing new, logs that it is held, and skips its tick.
- **Pause the gateway**: nothing leaves on its own.
- **Back to shadow**: the gateway judges and logs but acts on nothing.
- New action classes and new agents start in shadow.
