# One-Agent Mode

Use this when one person and one AI agent are building the project. The loop stays the same: Discover, Architect, Direct, Audit, Deploy. The agent just wears both hats, one at a time, in order. It never wears both at once.

This mode exists because starting with one agent is the right call for most projects. Add a second agent only when the work gets big enough that one agent is the bottleneck, or when you want a separate reviewer who did not write the code.

## How the hats work

The agent switches hats explicitly, out loud, at each step. Write the hat change in the chat or in the pull request, so the Owner can see which job the agent is doing.

1. **Architect hat.** The agent researches, writes the spec, and lists the acceptance tests. It does not write implementation code yet.
2. **Owner review.** The Owner reads the spec and gives a build go, or asks for changes. No build without this go.
3. **Implementer hat.** The agent builds from the spec, on a feature branch, as a pull request. It follows the spec exactly. If the spec is ambiguous, it stops and asks, in the Architect hat, instead of guessing.
4. **Auditor hat.** The agent reviews its own pull request against the spec, test by test, and verifies it live on staging. It lists what passed, what failed, and what it did not check.
5. **Owner test.** The Owner tests on their phone or machine, then gives a production go, or asks for fixes. The agent switches back to the Implementer hat for fixes, then back to the Auditor hat to re-check them.

## Rules that keep one agent honest

- **Spec first, always.** The spec is written and approved before any implementation code. If code appears before the spec, stop and write the spec.
- **Write the spec in a file, not in chat.** The spec lives in the repo, so the Implementer hat reads the same words the Owner approved.
- **Self-review is a checklist, not a feeling.** The Auditor hat checks each acceptance test and reports pass or fail per test. "Looks good" is not a review.
- **Verify live, not just in the diff.** The Auditor hat checks the staging deploy, not just the commit. A commit alone is not proof.
- **One item at a time.** One open build item, unless the Owner names a second. One approval per step: spec go, build go, pull request go, merge go, deploy go, each separate.
- **No silent spec changes.** If the Implementer hat finds the spec is wrong, it stops, switches to the Architect hat, and proposes a spec change to the Owner. It does not quietly change the spec to make the code pass.
- **No production without the Owner.** The agent never merges to `main`, deploys to production, spends money, or contacts anyone outside the project without the Owner's separate go for that exact action.

## Check-ins and nudges, one agent

The async loop in `docs/ASYNC-LOOP.md` still applies, with one change: there is no second agent to nudge. The check-in runner watches the agent's own work instead.

- **Check-in cadence.** Every 10 minutes while work is active, unless the Owner names a different cadence.
- **What the runner checks.** New commits, pull request status, CI status, and whether the last step was logged. It logs every run in the project log, with ids.
- **Stuck detection.** If the agent has made no commit, no test result, and no report for 30 minutes after starting a step, the runner posts one nudge in the project log: the exact stuck item and the exact next step. One nudge per silence window, not one per run.
- **Escalation.** If the same blocker fails twice, the agent proposes an alternative approach, with the exact change to try and the exact test that will prove it. If that also fails, it escalates to the Owner with three things only: the blocker in plain words, what was tried twice, and the alternative plus the decision needed.
- **Silence over 2 hours.** Message the Owner once, not every run, so the Owner can check the coding session directly.
- **End of day recap.** 9:00 PM in the Owner's timezone, only on days something changed. What moved, what is still open and who owns it, what is held for the Owner, and what is missing for the proof gate.

## When to add a second agent

Add an Implementer Agent, and move the Architect Agent to review only, when any of these are true:

- The Owner is spending more time reviewing than deciding.
- The agent keeps missing its own bugs, and a fresh reviewer would catch them.
- Two items need to move in parallel, and one agent cannot do both.
- The Owner wants the code reviewed by an agent that did not write it.

When you add the second agent, paste `docs/AGENT-ROLES.md` to each agent, assign the Architect and Implementer roles, and switch to the two-agent comment tags in `docs/PR-WORKFLOW.md`. The loop, the spec, and the approval gates do not change.
