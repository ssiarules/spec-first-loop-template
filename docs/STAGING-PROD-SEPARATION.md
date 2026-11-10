# Staging and Production Separation

## The rule

Staging and production are two separate environments, with two separate data stores. Never share one database between them. Test data must never land in real user data.

## How it works

| | Production | Staging, previews, local |
|---|---|---|
| Host | `[your production URL]` | `[your staging URL]`, preview URLs, `localhost` |
| Data store | `[production project name]` | `[staging project name]` |
| Deploy branch | `main` | `staging` and feature branches |

**Client side:** route on the hostname. Only the production hostname resolves to the production data store. Every other host resolves to staging. The fail-safe direction is deliberate: an unrecognized environment errs toward the data store that is safe to write test data into.

**Server side:** read connection settings from per-environment variables. Never hardcode a fallback to the production data store. A missing setting must fail loudly, not silently reuse production.

## Schema parity

Staging has the same tables and the same access rules as production, and no real data. When you add a migration, run it on both environments and log it below. There is no automatic sync.

## Applied-migrations log

There is no migration tracking table. Migrations are run by hand in each environment, so this table is the only record of what has been applied where. Update it in the same commit that adds a migration.

| File | Production | Staging | Probe |
|---|---|---|---|
| `001-initial-schema.sql` | not applied | not applied | |
| `002-example.sql` | not applied | not applied | |

To pick the next migration number, read this log. Do not list the folder. Numbers collide when two agents pick from the folder at the same time.

## Verification

After a migration, probe the live database for the new column or table. A column that exists returns an empty result under access rules. A column that does not exist returns an error. Do not trust the migration file alone. Verify both environments.
