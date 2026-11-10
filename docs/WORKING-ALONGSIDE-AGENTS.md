# Working on this repo alongside another agent

Two agents commit to the same repo concurrently. Stale bases cause silent damage: a commit can restore deleted lines and report zero conflicts. These rules exist to stop that.

## 1. Fetch before you edit, not just before you push

Start every work session with a fetch, and rebase or merge onto the current tip before opening a file. A branch that was current when you started reading is not current when you start writing.

## 2. Check the diffstat against your intent before you commit

If you meant to change two lines, the diff must say two lines. A number far larger than your intent means you are carrying someone else's deletions or a stale file body.

Rule of thumb: a one-line fix that touches hundreds of lines is not a one-line fix. Stop and re-fetch.

## 3. Verify the deployed site, not git

Confirming your commit landed is not the same as confirming it survived. After every merge:

1. Fetch the live page.
2. Read its version marker or cache-buster.
3. Fetch the changed assets at that version.
4. Grep for a marker of the work you just did, and for a marker of recent work by the other agent.

## 4. Bump the cache-buster on every JavaScript or CSS change

If your app uses a cache-buster in its HTML, bump it on every JavaScript or CSS change. Without a bump, browsers serve the cached bundle and your fix never reaches anyone.

## 5. Fix the file that runs, not the file that looks right

Check which module is actually wired before patching it. A file can look like the right one and hold no logic. Verify the import, the route, or the wiring first.

## 6. Migrations: check the log, run both environments

`docs/STAGING-PROD-SEPARATION.md` holds the applied-migrations log. There is no tracking table and no automatic sync between staging and production.

- Read that log to pick the next number. Do not list the folder. Numbers collide.
- Run every new migration on both staging and production.
- Update the log in the same commit that adds the migration.
- Verify by probing the live database, never by reading the migration file.

## 7. Write commit messages that survive your shell

Quote your heredocs. Escape dollar signs and other shell characters in commit messages, or a variable will expand into something you did not write.

## 8. Team structure and who does what

| Role | Who | Owns |
|---|---|---|
| Owner | The person | What gets built, in what order. Backlog priority. Production go or no-go. |
| Architect Agent | AI agent 1 | How it gets built. Specs, research, audits, deploy verification. Docs, bug log, migration log. |
| Implementer Agent | AI agent 2 | Building it. Local dev, tests, pushes. |

**The verification rule:** whoever can verify should do the work.

- Docs, logs, specs, research: Architect Agent, directly. No routing through the Implementer Agent.
- Migrations and ops: either agent, but only with the Owner's explicit authorization each time.
- Small code fixes the Architect Agent can verify (push to staging, check live): the Architect Agent may do them. The Implementer Agent reviews after.
- Anything touching logic: the Implementer Agent. It has the dev loop and test suite.

Anything the Architect Agent touches in code gets an Implementer Agent review after. Docs need no review.

**As the team grows:** specialist agents become domain developers. The Architect Agent shifts to tech lead and orchestrator. The Owner stays Owner. That never changes.

## 9. Definition of done

A change is not done until all of these are true:

1. Built and tested (staging for experiments).
2. Deployed and verified live. Deploy status plus a live check for the change marker.
3. Cache-buster bumped if JavaScript or CSS changed.
4. Migration log updated if a migration was involved.
5. Bug log updated if the change fixes or introduces a bug (append-only, never rewrite).
6. Production merge only with the Owner's explicit go.
