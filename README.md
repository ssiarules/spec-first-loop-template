# Spec-First Loop Template

A reusable way for one person and two AI agents to build a project without losing track, overwriting each other, or shipping unverified work.

This template is AI agnostic. Use any two AI agents that can read and write files, open pull requests, and leave comments. One agent architects. One agent implements. The person owns every decision.

## The loop

Discover, Architect, Direct, Audit, Deploy.

1. **Discover.** Find the real problem. Watch the user, read the current workflow, name the bottleneck in plain words.
2. **Architect.** Write a spec before anyone builds. No spec, no build.
3. **Direct.** The Owner assigns one item at a time and gives a separate go for each step.
4. **Audit.** Verify the work live against the spec. A commit alone is not proof.
5. **Deploy.** Staging first. Production only on the Owner's explicit go.

The pilot loop inside a build is Problem, Number, Scope, Prove, Hand off. Name the problem, name the number that proves it, set the scope, prove it live, then hand it off.

## The three roles

| Role | Who | Owns |
|---|---|---|
| Owner | The person | What gets built, in what order, and every production go. |
| Architect Agent | AI agent 1 | Specs, research, reviews, verification, docs, bug log. |
| Implementer Agent | AI agent 2 | Building from the spec, on a branch, as a pull request. |

The Owner decides. The Architect Agent specs and checks. The Implementer Agent builds. No agent changes direction, merges to production, deploys, spends money, or contacts anyone outside the project without the Owner's separate go for that exact action.

## What is in this template

- `SETUP.md`: the steps the Owner must do before the first build.
- `docs/AGENT-ROLES.md`: role cards to paste to any AI agent so it knows its job in one read.
- `docs/WORKING-ALONGSIDE-AGENTS.md`: the rules that stop two agents from overwriting each other.
- `docs/PR-WORKFLOW.md`: branches, pull requests, CI, and the comment channel between agents.
- `docs/ASYNC-LOOP.md`: how agents keep working while the Owner is away, with check-ins, nudges, and escalation.
- `docs/ONE-AGENT-MODE.md`: how to run the same loop with one agent, wearing the Architect and Implementer hats one at a time. Start here if you are building solo.
- `docs/STAGING-PROD-SEPARATION.md`: staging and production kept apart, with a migration log.
- `docs/BUG-LOG.md`: append-only bug log, blank and ready to use.
- `docs/DECISIONS.md`: locked decisions, blank and ready to use.
- `.gitignore`: keeps secrets and local files out of the repo.

## The one rule that matters most

Pushed is not verified. A change is not done until it is built, tested, deployed to staging, and checked live, with the bug log and migration log updated if they apply.

## How to use this template

Read `SETUP.md` first. Do the setup steps in order. Then pick one small project, run one item through the full loop, and only then invite a second person or a second project to use it.
