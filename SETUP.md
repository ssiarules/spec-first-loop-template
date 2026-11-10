# Setup

Do these steps in order, before the first build. Most of them only need doing once per project.

## You do not have to do these steps by hand

You can instruct your Architect Agent to complete the setup steps for you. Paste this file to your Architect Agent and say: "Complete the setup steps for me. Ask me only for decisions and secrets, and show me what you changed before you finish."

The Architect Agent can do most of the work: create the branches, set up the files, check the .gitignore, split staging and production settings, and run the scrub checklist and report what it found.

The Owner still has to do a few things yourself, because only you can do them: create the accounts, choose Public or Private, approve the repo, enter secrets and passwords yourself, and give the go for anything that touches production or money. The Architect Agent will name those items clearly and wait for you, instead of guessing.

## Step 1. Create the repo

1. On GitHub, create a new repository from this template. Use the "Use this template" button so the new repo starts clean, with no history from the template repo.
2. Name it for the project, not for a person. Example: `my-project`.
3. Set it to Private while you are learning the loop. Make it Public only after you have scrubbed it, using the checklist in Step 7.
4. Add a `README.md` with one line: what the project does, and who it is for.

## Step 2. Create the two branches

1. Make `main` the production branch. Protect it if your plan allows: require a pull request, require CI green, require the Owner's approval.
2. Create a `staging` branch from `main`. This is where all work lands first.
3. Rule: no one pushes directly to `staging` or `main`. Every change goes through a pull request.

## Step 3. Pick your two AI agents

1. Pick an Architect Agent. This agent writes specs, reviews work, verifies live, and keeps the docs and logs current. It does not implement features.
2. Pick an Implementer Agent. This agent builds from the spec, on a feature branch, as a pull request. It runs on your machine or in your coding environment.
3. Give both agents access to the repo. Give the Implementer Agent a local clone or a workspace it can write to.
4. If you are starting with one agent, read `docs/ONE-AGENT-MODE.md` instead. One agent wears both hats, one at a time, and the loop and approval gates stay the same.
4. Paste `docs/AGENT-ROLES.md` to each agent at the start of its first session, and tell it which role it is. Example: "Read this file. You are the Architect Agent for this project."
5. If you change agents later, paste the same file to the new agent. The roles stay the same. The names do not matter.

## Step 4. Set up staging and production

1. Create two separate environments, for example two hosting projects or two database projects: one called Staging, one called Production.
2. Point staging deploys at the Staging environment only. Point production deploys at the Production environment only.
3. Never share one database between staging and production. Test data must never land in real user data.
4. If a deploy is missing a required setting, make it fail loudly. Never let it silently fall back to production.
5. Record the two environment names in `docs/STAGING-PROD-SEPARATION.md`. Do not record secrets, keys, or passwords in that file.

## Step 5. Set up secrets

1. Put every key, token, and password in your hosting provider or secret manager, never in the repo.
2. Check that `.gitignore` blocks `.env`, `*.pem`, `*.key`, and local credential files.
3. Give each environment its own keys. A staging key must not work in production.
4. If an agent asks for a secret in chat, stop. Hand it over only through your provider's secure flow, or paste it yourself in the place that needs it, and never store it in a chat log or a doc.

## Step 6. Start the async loop

Read `docs/ASYNC-LOOP.md` before you turn this on. In short:

1. Pick one project and one task. Name them in one line.
2. Write the spec in the repo, with build order, acceptance tests, and what is out of scope.
3. Pick a check-in cadence. The default in this template is every 10 minutes while work is active.
4. Set up the check-in runner. Use whatever scheduler your Architect Agent supports: a cron job, a scheduled task, or a recurring reminder that wakes the Architect Agent. The runner must be able to read repo commits, pull requests, and comments.
5. Tell the Architect Agent: "Async loop on for [project], [task]. Spec is at [link]. Deadline is [date and time with timezone]."
6. To stop it, say: "Async loop off for [project]." The Architect Agent confirms the loop is retired, with the last state and what is still open.

## Step 7. Scrub before you share or go public

Do this before anyone outside your team sees the repo, and again before you make it public:

1. Search the whole repo for real names, emails, phone numbers, addresses, client names, company names, money amounts, and account numbers. Remove or replace them with placeholders.
2. Search for keys, tokens, passwords, and URLs that contain secrets. Rotate any secret that was ever committed, even once.
3. Check git history, not just the current files. If a secret was ever in a commit, it is still in history. Start from a clean repo if you need to, and do not copy history over.
4. Remove real screenshots, real user data, and real customer examples. Use sample data only.
5. Check the bug log, decisions file, and comments for private details. The bug log is append-only, so scrub it before it grows, not after.
6. Only then flip the repo to Public, or share the link.

## Step 8. Run one small item end to end

1. Pick the smallest real item you can: one bug fix, or one small feature.
2. Run it through the full loop: Discover, Architect, Direct, Audit, Deploy to staging, Owner tests it, then Owner approves production.
3. If any step feels missing or confusing, fix the template files first, then run the next item. The template should get better with each item.
4. Do not invite other people to use the template until you have run at least one item end to end yourself.
