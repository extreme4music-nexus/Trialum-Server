# Trialum Server

Repository for source snapshots and reviewed server-side patches for the Trialum game.

## Safety rules

- Import server source only from an owner-authorized live snapshot and record its commit/hash.
- Never commit credentials, `.env` files, private keys, service-account JSON, database dumps, logs, or backup archives.
- Keep production deployment separate from source changes. Every schema change needs a backup, migration plan, and rollback procedure.
- Keep v46 Age Signals / Play Integrity eligibility patch separate from the v47 13+ policy.
- v47 keeps Age Signals dormant, preserves social play, and requires server enforcement of preset-only player messaging.

See [`ROADMAP.md`](ROADMAP.md) for the v47 plan and source-import status.
