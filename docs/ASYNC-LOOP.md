# Async Loop: How Work Continues Without the Owner

This is the part that lets the two agents keep working while the Owner is away, without the Owner having to follow up by hand. The Architect Agent runs the loop. The Implementer Agent reports in the loop. The Owner only steps in for decisions that need their call.

This file is written for any pair of AI agents. Replace "Architect Agent" and "Implementer Agent" with your agents' names if you like, but keep the roles fixed.

## Trigger phrases

- Start: "Async loop on for [project], [task]. Spec is at [link]. Deadline is [date and time with timezone]."
- Stop: "Async loop off for [project]."

When the Owner says the stop phrase, the Architect Agent retires the check-in and confirms in chat: check-in retired, last state, last commit, last pull request, and what, if anything, is still open.

## Roles, fixed

- **Owner:** architect and approver in the human sense. Calls the project, approves the spec, gives a separate build go, gives a separate pull request, merge, deploy, or submit go, and makes spec calls when a conflict appears.
- **Architect Agent:** spec, review, verification, and check-in runner. Does not implement, and does not post, merge, deploy, or submit without a separate go.
- **Implementer Agent:** builder, in the Owner's coding environment. Builds from the spec, in a branch, and reports per acceptance check.
- No other person, agent, or public thread is a decision maker in this loop.

## Single source of truth

- One repo issue, or one spec file set, per project, named at the start. That is the loop channel.
- Every Architect Agent message to the Implementer Agent starts with `[Architect → Implementer]`. Every Implementer Agent report starts with `[Implementer → Architect]`, so the thread is scannable.
- A project log holds the watermark: last commit, last pull request, last comment id, last test result, and last decision. No work is re-reported. No quiet run is treated as done.
- Nothing from a private loop goes to a public repo, public issue, public pull request, or social post, unless the Owner gives a separate go for that exact public item.

## Start pack, required before the loop starts

The Architect Agent prepares it, the Owner approves it, then the Owner pastes the short prompt into the Implementer Agent's session, because the Architect Agent has no direct line to the Implementer Agent:

1. Project and goal, in one line.
2. Spec file, in the repo, with build order, acceptance tests, and out of scope list.
3. Branch and repo: which repo to build in, which branch to use, never `main` for a new build unless the Owner says so.
4. Deadline, with timezone, and what happens at that deadline.
5. Test and proof gate: unit tests, integration tests, and the local or staging live check that must pass before a pull request.
6. What the Implementer Agent must report, per check, pass or fail, with branch link, commit list, and test output.
7. What is out of scope, and what needs a separate go.

## Check-in, default

- Every 10 minutes while work is active, unless the Owner names a different cadence at the start. For overnight work, every 10 to 15 minutes is the default. For a slow project, every 30 minutes can be enough. Pick one cadence per loop and write it in the start pack.
- Check, in order: the repo for new commits, issues, and comments; the target repo or pull request for new status; any competing or related pull request for status only, never copy its code.
- Stay silent in the Owner's chat on routine runs. Message the Owner only on the triggers in the "When to message the Owner" section.
- Log every run in the project log, with ids, so a morning recap can be built from it without asking the Implementer Agent again.

## How to set up the check-in runner

The check-in is a scheduled job that wakes the Architect Agent. The exact tool depends on your setup. Pick one:

**Option A: Scheduled task or cron job.** Use your agent platform's scheduler, or a system cron job that calls your agent with a fixed prompt: "Run the async loop check-in for [project]. Read the project log, check the repo for new commits and comments, answer any waiting question, and log the run." Set the interval to match your cadence, for example every 10 minutes.

**Option B: Recurring reminder.** If your agent platform has no scheduler, set a recurring reminder on your phone or calendar that says: "Check the async loop for [project]." When it fires, paste that prompt to the Architect Agent yourself. This is manual, but it works, and it is the right first version if you have no scheduler yet.

**Option C: A watcher script.** A small script that polls the repo for new comments addressed to the Architect Agent and wakes it when one appears. Build this only after the session-start sweep in `docs/PR-WORKFLOW.md` starts missing urgent items in practice.

Whichever option you pick, write down in the project log: which option you use, the cadence, the exact prompt the runner uses, and how to stop it. A loop no one can stop is a bug.

## Silence and waiting detection

This is the core of the protocol. Every run, look at the last turn.

- **If the last unanswered message is from the Implementer Agent,** asking the Architect Agent a question, asking for a spec call, or waiting on a review, then the Implementer Agent is waiting on the Architect Agent. The Architect Agent answers on that same run, with a clear spec call or next step. Do not just log that it is quiet.
- **If the Architect Agent asked the Implementer Agent something and there has been no reply for 30 minutes,** post one short nudge in the loop channel, naming the exact stuck item and the exact next step. Do not repeat the nudge every run.
- **If the Implementer Agent shows no report or push for more than 2 hours,** message the Owner once, not every run, so the Owner can check the coding session directly.
- **If the Implementer Agent is blocked,** the Architect Agent gives the next concrete step within scope, or a narrowed question, on that run. Do not wait for the Owner to ask why nothing moved.

## Nudges, in plain words

A nudge is one short comment in the loop channel. It names the stuck item, the last thing that was tried, and the exact next step. It does not repeat the whole spec, and it does not assign new scope.

Example:

```
[Architect → Implementer] · [non-blocking]

Nudge: the archive menu fix is still waiting on the CI run from commit a1b2c3d. Next step: re-run CI, then report pass or fail per acceptance check. No new scope.
```

Rules:

- One nudge per silence window. Do not nudge every 10 minutes.
- Nudge after 30 minutes of no reply to a direct question.
- If the Implementer Agent replies, the nudge count resets.
- If the Implementer Agent is actively pushing commits or tests, do not nudge. Activity is not silence.

## Stuck twice escalation

- If the same blocker fails twice, with the same fix tried, the Architect Agent does not loop the same nudge a third time.
- Step 1: the Architect Agent proposes an alternative approach in the loop channel, with the exact change to try, and the exact test that will prove it.
- Step 2: if that alternative also fails, or if the Implementer Agent cannot try it without a spec call, the Architect Agent escalates to the Owner in chat, with three things only: the blocker in plain words, what was tried twice, and the alternative plus the decision needed from the Owner.
- Step 3: the item stays held until the Owner calls it. The Architect Agent does not assign a different scope to work around a held item, and does not silently change the spec to make the blocker go away.
- A stuck count resets only when a test passes, or when the Owner makes a new spec call. A new error on the same item is a new blocker, not a reset of the old one.

## End of day recap

- Time: 9:00 PM in the Owner's timezone, in the project chat, on days when the loop was active.
- Send a recap only if something changed that day: new work, a test result, a blocker, a decision, or a held item. If nothing changed, stay silent. Do not send a "no change" recap.
- Recap format, short, in plain words:
  1. What moved today, with branch, commit, and test result.
  2. What is still open, with the exact next step and who owns it.
  3. What is held for the Owner, with the exact decision needed.
  4. Deadline and what is missing for the proof gate.
- Build the recap from the project log and watermark, not by asking the Implementer Agent again. If the Implementer Agent has not reported by recap time, the recap says so plainly, with the silence time, instead of guessing progress.

## Proactive work the Architect Agent can do, without a new go

- Review new work against the spec, per test.
- Point out a moved file path, a failed test, or a rule conflict, with the fix to try.
- Prepare the next spec, the next test list, or the next review comment, as a draft in the repo.
- Prepare the submission, README, or demo checklist, as a draft only.
- Keep the log, watermark, and open items list current, so nothing slips silently.

## Work no agent does without a separate go

- Open a pull request, merge, deploy, submit, publish, post as the Owner, pay, send a real email, or change a production setting.
- Change the spec silently. If a file path, test, or rule conflicts with the spec, stop that item, report it to the Owner for their call, and tell the Implementer Agent the item is held.
- Expand scope, add a new track, add a new platform, or add a new approval layer, without the Owner's go.
- Share private data, real names, keys, passwords, or build chat outside the private loop.

## Report format, Implementer Agent to Architect Agent, every report

- Branch and pull request link, or local branch name if not pushed yet.
- Commit list, in order.
- Test output, per acceptance check, pass or fail, not just "done".
- Demo link or file, after tests pass, if a demo is part of the proof.
- Blockers, with the exact error or conflict.
- Questions for the Architect Agent, listed separately, so they can be answered one by one.
- Next step the Implementer Agent will take, if unblocked.

## Review gate, Architect Agent to Implementer Agent, every review

- Spec match, test by test.
- Scrub: no private data, no real person data, no keys, no passwords, no internal build comments in a public copy.
- Live proof: for a staging or production bound item, check the live site or live flow, not just the commit. A commit alone is not proof.
- Verdict, in plain words: approved, needs fix with exact fix list, or held pending the Owner's call, with the held item named.

## When to message the Owner

The Architect Agent messages the Owner in chat only when:

1. New work is ready for a separate go: pull request, merge, deploy, submit, publish.
2. A blocker needs the Owner's action, password, account, or spec call.
3. A test or live check fails, and the Architect Agent cannot fix it within scope.
4. A private data or key risk is found.
5. Silence over 2 hours, once, as above.
6. Deadline risk: 24 hours before deadline, if the proof gate is not yet passed, with the exact missing items and the exact go needed.
7. A spec conflict needs the Owner's call.

Otherwise, routine runs stay silent, and the log carries the detail.

## One item at a time

- One open build item at a time, unless the Owner names a second item.
- One approval per item: spec go, build go, pull request go, merge go, deploy go, submit go, each separate.
- When the Owner holds an item back, that item stays held, and the Architect Agent does not re-litigate it in the next run.
- When a new project starts, the Owner names the active project. Content stays in its own project, except for a one line pointer.

## Stop and handover

- Stop when the Owner says "Async loop off for [project]", or when the pull request is opened, reviewed, merged, or submitted and the Owner confirms retirement.
- On stop, the Architect Agent confirms in chat: check-in retired, last state, last commit, last pull request, and what, if anything, is still open.
- On handover to a new chat, copy only the trigger, project, spec link, deadline, watermark, and open items, not the full build chat, unless the Owner asks for it.

## Plain language and copy rules, always on

- No em dashes in user-facing copy.
- Short declarative sentences, plain words, recommendation first, then numbered steps for any approval: what the Owner approves, what changes, what does not change, and why it matters.
- A diagram or image when the item has moving parts, before deeper technical detail, only when the Owner asks for deeper detail after that.
