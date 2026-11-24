# Trialum server cloud migration and compatibility gate

**Date:** 2026-11-24  
**Status:** planning only; no live server source has been imported and no production change has been made.  
**Required milestone:** finish migration and app-version compatibility before Trialum exits Closed Testing.

## Current source blocker

The exact live server repository path, commit, running image/schema, and production configuration have not yet been imported into this repository. Therefore this plan is not a patch or deployment authorization. Import the live source from an authenticated owner-controlled session first, record its immutable source SHA, and exclude credentials, .env files, keys, databases, logs, and backups.

## Goal

Move the social API/database from the private/home host to a reviewed cloud environment before Closed Testing ends. Old and new app builds must remain usable during adoption, while there is exactly one authoritative writable data plane.

## Required topology

- Cloud API/database becomes the canonical production authority after cutover.
- New app builds use the reviewed cloud HTTPS endpoint.
- During the migration window, the old app endpoint must either route through a narrow compatibility gateway to the same cloud services, or be served by a restricted gateway that forwards requests to the cloud authority.
- Do not run independent writable home and cloud profile databases in parallel. Avoid split-brain writes, duplicate messages, missed deletions, and conflicting consent state.
- If a gateway cannot safely preserve the old client contract, return a clear update-required response and keep the old service available only until the owner-approved adoption window ends.
- Keep the home origin private where possible. Prefer cloud routing or a restricted outbound tunnel over leaving broad home WAN ports open for legacy clients.

## Staged migration

### 0. Exact-source and data inventory

Record the live server source SHA, container image, schema revision, routes, current endpoint, DNS/TLS, FCM behavior, data tables, backups, retention/deletion jobs, active clients, and secrets location. Export encrypted backups and test a restore in an isolated environment.

**Gate:** provenance, data map, backup, and rollback plan are reviewed; production remains unchanged.

### 1. Cloud staging

Choose the provider/region after cost, data location, access controls, and backup review. Deploy separate staging API/database with synthetic test profiles. Set HTTPS, private database networking, least privilege, secret management, patching, health checks, rate limits, minimized logs, encrypted backups, and tested restore.

**Gate:** staging passes API, security, deletion, consent, and recovery checks.

### 2. Client and legacy compatibility

Map each app version to its endpoint. Build a new app candidate with the cloud endpoint and test old and new clients simultaneously against the same canonical test state. Cover friends, preset messages, shared spaces, block/report, deletion, consent/revocation, FCM, retries, and duplicate-event handling.

No endpoint migration may rely on exposing a secret in the app bundle. Any endpoint configuration must be authenticated, versioned, TLS-protected, and fail safely.

**Gate:** both versions work without duplicate or split-brain state; rollback was rehearsed.

### 3. Production cutover

Take a final verified backup, freeze writes if needed, migrate only required data, validate record counts and relationships, and switch one cloud database to canonical. Route the new app to cloud and old app traffic through the compatibility gateway to that same authority. Observe availability, errors, retries, storage, backup health, and aggregate app-version adoption.

If rollback is needed, pick one authority and reconcile writes deliberately; never make both databases independently writable.

**Gate:** stable soak period, owner device verification, deletion/recovery checks, and explicit owner approval.

### 4. Retire the home public API

After supported clients migrate or have a safe update-required route, remove public Caddy/WAN routes and port forwards, revoke/rotate old exposed credentials, and retain only approved private backups under a defined retention policy. Verify offline play and local saves still work when the cloud is unavailable.

**Gate:** no supported client depends on the home API; no public home origin remains; restore and incident-recovery steps are documented.

## Acceptance checklist before Closed Testing ends

- [ ] Exact production source SHA and schema imported without secrets.
- [ ] Cloud provider/region, operating cost, access model, and retention approved.
- [ ] Staging backup restore and migration rehearsal pass.
- [ ] New app build uses the cloud endpoint.
- [ ] Legacy app endpoint is safely proxied to the same cloud authority, or an update-required plan is approved.
- [ ] Old and new builds tested concurrently for all online functions and deletion/revocation.
- [ ] Monitoring, cost ceiling, incident contact, backup, restore, and rollback are documented.
- [ ] Home server exposure is removed only after compatibility/adoption gate is met.
- [ ] Owner approves cutover and final home-server shutdown.

## Prohibited shortcuts

Do not copy credentials or production DB files into GitHub. Do not expose the home database to the Internet. Do not perform unreviewed dual writes or automatic production migrations. Do not claim migration complete because staging works. Do not mark Closed Testing complete until the new build and legacy compatibility path are verified.

## Change log

- 2026-11-24: captured the owner's requirement to migrate to cloud before Closed Testing ends, with a temporary old/new client adoption window and one authoritative backend.