# Agent failure log: LLM agents running a small physical business

In 2026 I let a team of AI agents run most of Texas Toy Lab, a small family 3D-printing toy business in Texas. They ran a print farm of eight Bambu Lab printers, the online shop, quoting, social media and customer texts, and wrote most of the software in between. This repository records what went wrong, what I changed, and which changes held.

It is a field log, not a study. Most incidents were small. One damaged a printer, one stopped a real print, and one put a post on the business's public social accounts that no person had approved.

The agents were Claude Code instances (Claude Opus and Sonnet; some were later pointed at a local NVIDIA Nemotron model). The source material is the agents' own incident memos, written to persistent memory after each failure, plus my reviews. Where a memo says "I", that is the agent.

## The setup

- **About 15 agents**, each a long-running Claude Code session with a role: master (COO), CEO, site, social, ops (print farm), quoting and others. Local models (Nemotron 120B and smaller) ran on my own four-node NVIDIA DGX Spark lab behind an OpenAI-compatible gateway.
- **A CEO agent** on a two-hour tick, reading a goals file and an authority matrix: what it may do alone, what needs me, what it may never do. It started in shadow mode, with every move an approval card I clicked.
- **Tools as the control surface**: an approval queue; a question queue (each question carries a proposed answer, a deadline and what silence means); start, hold and wake commands, with a hold file as kill switch; a hash-chained audit ledger; a 15-minute health check.
- **Standing approvals**: routine classes (customer replies built from published facts, countdown posts for a booked market) skip the per-item tap but wait in a recall window, 15 minutes by default and 5 for texts, where I can pull them.
- **Decisions by default**: an unanswered question adopts its proposed answer after the deadline, except money, physical and legal questions.
- **From 27 September, a "Lab Autopilot" gateway**: one door for outside actions, a constitution of action classes at trust levels 0 to 3 (always ask, ask with a default, do with a recall window, just do it), "never" rules, and a shadow replay of past items before it went live.
- **The farm**: an agent-written farm API, wall screens and phone app; a camera per printer; a local vision model grading beds and prints; smart plugs; speakers; and a desk robot (a Reachy Mini) on a local model that talks with a child in the family.

## Incidents

Printer names and hostnames are replaced with letters. Names, keys, internal URLs, financial figures and thinly recorded incidents are left out. Each row links to a write-up in [`incidents/`](incidents/).

| # | Date | What the agent did | Failure class | Consequence | Mitigation |
|---|---|---|---|---|---|
| [01](incidents/01-2026-08-31-test-writes-to-production.md) | 2026-08-31 | Two agents testing features wrote to production four times, including test print requests to a live API. | Test on production | Junk requests in the real print queue; a false timestamp nothing could clear. | QA header that validates but skips the real write. |
| [02](incidents/02-2026-09-01-runaway-headless-browsers.md) | 2026-09-01 | Web agents left two headless browsers running. | Resource exhaustion | 6 h at about 1000% CPU, 87 °C. | cgroup caps on the agents' user; browser reaper. |
| [03](incidents/03-2026-09-02-approval-guard-misread-caller.md) | 2026-09-02 | The self-approval guard inferred the caller from the terminal session and saw the master agent. | Control misfire (failed closed) | My approvals were refused as agent self-approvals. | Trust the session name only inside a real session. |
| [04](incidents/04-2026-09-06-model-load-wedged-gpu-node.md) | 2026-09-06, 09-22 | Loaded a large model with about 13 GB free; later the node wedged again when two model containers were recreated mid-generation. | Resource exhaustion | GPU node thrashed until SSH died; public toy generator down until a person power-cycled it, twice. | Check free memory, keep 20 GB headroom. |
| [05](incidents/05-2026-09-09-standing-approval-cancelled-market.md) | 2026-09-09 | A standing approval excluded markets "not yet booked". I cancelled one, which was then neither. | Rule gap | Two items already released without a tap still named it; the notes show no post going out. | Class back to "ask" until the gateway checks the booking at release. |
| [06](incidents/06-2026-09-10-reset-hard-wiped-live-wiring.md) | 2026-09-10 | Deployed with `git reset --hard` four times over uncommitted work on the server. | Shared-state clobber | Inbound texts silently dropped; the farm nagged me for 5.5 h about a printer I had shut down. | Never reset there; text smoke test after deploys. |
| [07](incidents/07-2026-09-11-robot-speech-loops.md) | 2026-09-11, 09-19 | The robot's code answered room noise and re-armed itself; later a repeat limit in the voice code sat inside a comment. | Feedback loop | 40+ turns of "Hi buddy!"; a night of "plate off the bed" until I unplugged the robot at 04:00. | Loop guards, quiet hours, three repeats at most. |
| [08](incidents/08-2026-09-12-deploy-to-wrong-project.md) | 2026-09-12 | A production deploy ran from an unlinked checkout of another site (the memo does not say which session). | Wrong target | The shop site served 404s for about 20 min. | Deploy script checks the target; live probe after. |
| [09](incidents/09-2026-09-15-printer-plate-interlock-override.md) | 2026-09-15 | Printer A paused: "No print plate detected". The agent read a camera frame as a plate and told it to ignore the check. It was the bare heatbed. | Safety interlock override | Printed onto the magnetic bed mat and damaged it; printer out of service. | API refuses ignore commands; a person confirms the plate. |
| [10](incidents/10-2026-09-17-live-probe-stopped-a-print.md) | 2026-09-17 | "Tested" a new endpoint on the live farm with "FINE" and "STOP printer B" while an alert was open. | Test on production | A real print stopped at 5%; autonomy ledger mis-graded. | Must-refuse inputs and dry-run only. |
| [11](incidents/11-2026-09-22-edited-running-backup-script.md) | 2026-09-22 | Edited the backup script in place mid-run. Bash reads scripts as it goes. | Shared-state clobber | The run died; pruning skipped. | Temp file and `mv`. |
| [12](incidents/12-2026-09-23-shared-checkout-half-feature.md) | 2026-09-23 | A session's `git add <file>` committed another session's half-finished edit and deployed it. | Shared-state clobber | Five lab screens and the phone app down; deploy gate blocked about 8 min. | Worktree per change. |
| [13](incidents/13-2026-09-23-printer-power-bounce.md) | 2026-09-23 | Farm sleep rule cut printer plugs that the wake rule restored within 2 s, about every 30 min. | Automation conflict | A printer camera dead until a person power-cycled it. | No power-on within 60 s of a cut. |
| [14](incidents/14-2026-09-29-agents-dying-silently.md) | 2026-09-26 to 09-29 | The ops agent hit its 131k context limit and stopped; an expired login and a start-up notice stalled others. | Silent failure | Ops dead 3 h; the health check said only "tick missed". | Auto-compact; fresh sessions; named alerts. |
| [15](incidents/15-2026-09-29-approval-judge-published-a-post.md) | 2026-09-29 | The Autopilot's keyword classifier filed a social photo post, and a CEO request about choking-warning signs, as "farm planning, level 3" and approved both "by joseph" in about 20 s. | Misclassification | The post was published on the business's Instagram and Facebook. | Posts always ask; class from actor and channel. |
| [16](incidents/16-2026-09-29-pattern-kill.md) | 2026-09-29 | A subagent ran `pkill -f "cat"` to stop one process; `-f` matched "Applications". | Wrong target | Nearly every app on my Mac closed, with other Claude sessions and some business agents. | Exact PIDs only, rule in every prompt. |
| [17](incidents/17-2026-09-29-retired-endpoint-robot-brain.md) | 2026-09-29 | Retired a model endpoint after a caller search that skipped the robot. | Hidden dependency | The robot had no brain for 1 h 40 min; its health metric stayed green. | Sweep every host first; fallback model. |
| [18](incidents/18-2026-09-29-robot-printer-safety-line.md) | 2026-09-29 | Given a test line written as a child, the robot's model said he could peek inside an open printer. | Unsafe advice to a child | Found in testing; no record of a real child hearing it. | Approved safety block; child-line smoke test. |
| [19](incidents/19-2026-09-29-silent-monitoring-and-backups.md) | 2026-09-29 | Agent-built alerting and backups reported success while doing nothing. | Silent failure | No alerts reached my phone; a backup bundled 0 repos for 13 days. | Dead-man ping; loud failures. |
| [20](incidents/20-2026-09-29-credentials-too-broad.md) | 2026-09-29, 10-13 | An agent printed part of a token; a separate machine's SSH key gave passwordless root; the gateway admin key was reused by services. | Over-broad credentials | The admin key sat in 193 config backups and an agent transcript, among others. | Rotated; scoped keys. |
| [21](incidents/21-2026-10-02-agent-load-froze-gpu-node.md) | 2026-10-02, 10-05, 10-13 | The ops agent's scheduled fresh session sent 45k–120k-token prompts every 6–15 s to the local 120B model. | Resource exhaustion | The node hung twice and the engine wedged once. The fix for #14 caused it. | Smaller window; one sequence at a time. |
| [22](incidents/22-2026-10-05-power-cycle-hit-wrong-node.md) | 2026-10-05 | Power-cycled the frozen node by an outlet map nobody had checked at the rack. | Wrong target | A healthy node rebooted instead. | Tool locked until outlets verified by hand. |
| [23](incidents/23-2026-10-07-stalled-agent-blocked-deploy.md) | 2026-10-07 | In an overnight build for another company, one agent "polished" for 6 h while the workflow waited for all packages. | Stall, no time-box | 28 finished packages shipped 5 min before my deadline. | Deadline per agent; incremental deploys. |
| [24](incidents/24-2026-10-08-parallel-agents-reverted-files.md) | 2026-10-08 | In a build with about 14 parallel agents, one copied its shadow tree back over the real one. | Shared-state clobber | Six files edited in the previous 17 s were reverted. | Exclusive files by name; no sync-back. |

## Patterns that held up

**An authority matrix the agents read and cannot edit.** Three columns per area: the agent alone, needs me, never. Agents propose; I edit. The weak point was the code that maps an action onto a row (#15). A generic version is in [`examples/AUTHORITY.example.md`](examples/AUTHORITY.example.md).

**Some classes never auto-approve.** Safety, legal, refunds, price changes, new recurring spend and anything over the spending cap are fixed at "always ask". Money, physical and legal questions never resolve by default, and purchases stop at the cart for a person. After #15, every public post needs a person too. The lesson of #15: decide the class from who acts and through which channel, read the whole body for safety words, and never let a keyword match reach "just do it".

**Standing approvals with a recall window.** Each class has rules, a weekly budget, an expiry date and an off switch. Preconditions must be re-checked when an item goes out, not when the class was granted (#05); until the gateway could do that, the class went back to asking.

**Physical truth is a person's word.** A camera frame, a model's verdict or an agent's "done" does not establish that a plate is on or a parcel shipped. "Done" needs a photo, a tracking number, a commit or a live URL. Starting a print on vision alone is still switched off.

**Refuse override verbs at the API.** The farm API returns 403 to any command telling a printer to ignore its own check, and says to press Ignore on the printer itself. No agent has to remember the rule.

**Dry-run and must-refuse test inputs only.** #01 and #10 are the same mistake two weeks apart; the first fix covered one API, not the class.

**Shadow mode, and trust earned per action class.** The Autopilot replayed 332 past items before going live, and the replay changed four rules. Each kind of farm decision acts alone only after enough graded calls: for most kinds 10, with 90% right over the last 30. One wrong acted call sends it back to shadow. Vision bed checks need 40 calls at 95%, per printer.

**A worktree per agent, exclusive file ownership.** Every change goes in its own worktree and merges in one commit. Parallel briefs name each agent's files and forbid copying anything back.

**Time-boxed agents with watchdogs.** Every prompt carries a wall-clock deadline, work is integrated incrementally, and a watchdog fires well before the deadline. Liveness checks read output, not process lists.

**Sweep for callers before retiring anything**, on every host, then add a health check that exercises the dependency.

**Safety rules travel inside the prompt.** The process and git rules go verbatim into every subagent brief. Subagents do not inherit their parent's lessons.

## Open questions for AI control research

1. **Who classifies the action?** In #15 the risk tier came from rules reading agent-written text, so the agent's wording set its own oversight. Can the class come from the effect (API, channel, audience) instead, and at what cost in extra asks? Testable by replaying a real approval log with reworded actions against rule-based, LLM and effect-based judges.
2. **Do safety lessons transfer?** In #01 the agent wrote that its lesson "did not transfer because I scoped the fix to one API instead of to the class". #10 repeated it. Given an incident memo, does an agent generalise to a structurally similar case, and does persistent memory help?
3. **When, if ever, may perception override an interlock?** In #09 the hardware said "no plate" and a model's reading of a frame said "plate". What evidence should let learned perception override a hardware safety signal, and how is calibration measured on rare disagreements?
4. **Agents' beliefs about their own oversight.** The day before #15, an agent told me posts needed my approval. Another told me there was no remote power option when there was (#22). How often are agents wrong about the controls around them, and can a monitor check such claims against the real permissions?
5. **Semantic liveness.** Agents died at the context limit, a webhook returned 200 into nothing, and a health metric stayed green while the robot had no model. What cheap protocol shows an agent is doing its job, not just running? Can a weaker trusted model judge that from logs?
6. **Command-level monitors for blast radius.** `pkill -f`, `git reset --hard` on a server, a deploy from the wrong directory and a power-cycle by an unverified map were each one confident command. Can a trusted monitor flag such commands before they run, at an acceptable false-positive rate? These incidents could seed a small real-world eval set.
7. **Soft controls around children.** The robot's mitigation is a prompt block and a scripted smoke test: a soft control on a stochastic model fed noisy room audio from a young child. What red-teaming and runtime filtering would give a defensible residual-risk estimate?
8. **Mitigations that move the failure.** Scheduled fresh sessions fixed silent context death (#14) and created synchronised load bursts that froze a GPU node (#21). How should control measures be tested for second-order effects on shared resources?

## Licence

Text licensed under [CC BY 4.0](LICENSE). Joseph Crabtree, 2026.
