# Pull Request Workflow

How the Architect Agent and the Implementer Agent work in one repo without stepping on each other.

## The rule

**No direct pushes to `staging` or `main`.** All changes go through pull requests.

## Branch naming

- `fix/<short-description>`: bug fixes. Example: `fix/archive-menu-race`
- `feature/<short-description>`: new features. Example: `feature/bulk-archive`
- `docs/<short-description>`: docs only. Example: `docs/archive-faq`

## Workflow

### 1. Start from fresh staging

Before creating a branch, pull the latest `staging`:

```
git fetch origin && git checkout staging && git pull
```

### 2. Create a feature branch

```
git checkout -b fix/archive-menu-race
```

### 3. Make changes, push to the branch

Push only to your feature branch. CI runs automatically on every push.

### 4. Open a pull request to `staging`

- Title: clear, imperative. Example: `Fix archive menu race condition`
- Body: what changed, what bug it fixes (link bug number), how it was tested.
- If the pull request touches a shared file that both agents edit often, note it explicitly in the body.

### 5. Check CI before merging

This is the honor-system gate. Do not skip it.

- If CI is red or still running: do not merge. Fix the issue, push again, wait for green.
- If CI is green: check mergeability next.

**Mergeability check:** a conflicting pull request can silently skip CI, so no red mark appears and CI just never runs. "No red CI" is not enough. Before merging, verify the pull request is actually mergeable. In the GitHub web UI, look for the mergeable state. With the API, read the `mergeable` and `mergeable_state` fields.

- If `mergeable` is false or `mergeable_state` is dirty: do not merge. Resolve conflicts first, then CI will run.
- If `mergeable` is true and CI is green: merge.

### 6. Check for conflicts with open pull requests

Before merging, check if any other open pull request touches the same files. If there is overlap in a shared file, coordinate with the other author in comments before either merges.

### 7. Merge to staging

Use squash merge to keep history clean. Delete the branch after merging.

### 8. Verify the deploy

After merging to `staging`, verify the staging deploy: check the deploy status, then fetch the staging URL and look for a new code string or check the version marker.

## Production (`main`)

Same pull request process, with one addition: **the Owner approves every merge to `main`.** No exceptions.

- Open a pull request from `staging` to `main`.
- The Owner reviews and merges.
- After merge, verify the production deploy (deploy status plus a live-site check) and report to the Owner unprompted.

## Cache-busters

Every JavaScript or CSS change must bump the cache-buster if your app uses one. Include it in the pull request.

## What CI checks

Your CI workflow should at least run a syntax check on the main code files and run the test suite. If CI does not cover your change, say so in the pull request body.

## When this becomes enforced

This workflow runs on discipline until you turn on branch protection. When you do, set:

- `staging`: require pull request plus CI green. No approval required.
- `main`: require pull request plus CI green plus the Owner's approval.
- Admin bypass stays on for the Owner.

Until then: the honor system is the system. Follow it every time.

## Cross-agent communication: pull request comments are the channel

This is how the two agents talk to each other when they are not in the same session, and how work continues without the Owner relaying messages.

**Rule: no relay via the Owner.** Any substantive reasoning, decision, or request shared with the Owner in chat about a pull request must also be posted as a pull request comment. The Owner should never have to copy-paste between chats. When in doubt, post it.

### The problem

Both agents may post to GitHub as the same account. GitHub shows no difference between a comment from one agent and a comment from the other. Without a convention, comments are unattributable and notifications do not reach the right agent.

### Attribution

Every cross-agent comment opens with a tag on the first line. Use role names, not product names, so the tags work with any AI agents:

- `[Architect → Implementer]`: written by the Architect Agent, for the Implementer Agent.
- `[Implementer → Architect]`: written by the Implementer Agent, for the Architect Agent.

Additional tags on the first line:

- `[blocking]` or `[non-blocking]`: whether this gates a merge or go-live.
- `[needs: Owner]`: the Owner's decision is required. This implies blocking until the Owner weighs in.

Example:

```
[Implementer → Architect] · [blocking]

The archive fix touches the same lines I am editing for the task dialog. I will hold my push until you confirm which version wins.
```

Keep tags minimal. If the header names the author, do not repeat it in a sign-off.

### How an agent learns a comment arrived

Use these layers, in order:

1. **Primary: session-start sweep.** At the start of every session touching this repo, check open pull requests for new comments addressed to you. This is the most reliable low-tech solution.
2. **Secondary: notification triage.** Whoever's session is running picks up notifications on the shared account.
3. **Fallback: Owner relays.** The Owner says "check pull request number N" in chat. Reliable, but it burns the Owner's attention. Reserve it for urgent items.
4. **Not recommended as primary:** direct agent-to-agent messaging. It only works when both sessions are live at the same time, which is the exception.

If the session-start sweep starts missing urgent items in practice, the next step is a lightweight watcher that polls pull request comments for your tags. Do not build it until the pain is real. See `docs/ASYNC-LOOP.md` for the check-in version of this.

### Scope

This protocol is required on any pull request where both agents are participants (author, reviewer, or requested). Everywhere else it is optional, but the format stays identical.

Use GitHub's review feature for line-specific code feedback (Approve or Request changes). Use issue comments for protocol, coordination, and meta discussion. This keeps the signal separated.

### Shared files

List the files both agents touch regularly in your repo here, and replace this placeholder list:

- `src/app.js`: frontend
- `src/server.js`: backend
- `src/index.html`: version marker lives here

Before opening a pull request that touches a shared file, check open pull requests for overlap. If another agent has an open pull request on the same file, coordinate in comments before either merges.

## Bug screenshots

Every bug gets visual evidence. No exceptions.

- When the Owner sends a bug screenshot: save it to `docs/bug-screenshots/bug-<number>-<short-desc>.jpg` and reference the path in the bug log row. The screenshot is the ground truth of what the Owner saw.
- When an agent finds a bug during testing: take a screenshot, annotate it (circle the broken element, arrow to the cause if known), save to the same directory, reference it in the bug log.
- Naming: `bug-<number>-<short-desc>.jpg` or `.png`. One file per bug. Append `-2`, `-3` for follow-ups.
- Why: if you revisit a bug weeks later, the screenshot shows exactly what was wrong without re-reading the whole thread.
