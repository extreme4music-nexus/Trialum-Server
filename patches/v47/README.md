# v47 patch — not deployable yet

This directory is a staging record only. The live server source and commit have not been retrieved, so no application code or SQL migration is included.

## Required behavior

- Age Signals stays dormant and is not used to gate baseline social play.
- Friends, accepted invitations, shared playgrounds, visits, and pet interaction remain available under current consent and server authorization.
- Player-to-player messages accept reviewed preset IDs only. Caller-provided text must be rejected before persistence or relay across every REST and WebSocket path.
- Block/report, removal, deletion, rate limiting, and room/visit authorization are server-enforced.
- Keep player email, birth date, exact age, and location out of the schema for this feature.

## Do not deploy

Import the exact live server source first. Then produce a minimal patch against that snapshot, write a backup/rollback plan for any schema changes, and validate the REST and WebSocket paths in staging. Never copy secrets or production database files into this public repository.
