# Trialum Server — v47 roadmap

**Status:** planning and staging; no production changes have been made.  
**App target:** Trialum Beta v47 / versionCode 47.  
**Source baseline:** pending import from the live server.

## Product decisions

- Preserve Friends, accepted friendships, shared playgrounds, visits, and pet-to-pet gameplay.
- Keep Google Play Age Signals dormant in v47. It is not requested and is not a prerequisite for baseline social access.
- Allow only reviewed, predefined player-to-player message phrases. Reject all caller-supplied text on the server, even from older or modified clients.
- Keep offline game data, pet names, local memories, achievements, and existing profiles intact.
- Do not collect a Google email, date of birth, exact age, or location for this policy.
- A parent pairing code is not proof of legal parenthood or adult identity. Parent-management controls require a separately reviewed threat model.

## Server work stages

### 0. Import the exact live baseline

1. Retrieve the current server repository path and commit from the owner's authenticated server session.
2. Make a source-only snapshot, excluding all credentials, environment files, mounted secret material, databases, logs, and backups.
3. Record the active container image/version, schema revision, feature flags, and clean/dirty state.
4. Import the snapshot to a dedicated baseline branch without overwriting history or working files.

**Gate:** source provenance and secret scan are recorded. No server patch is based on guessed or stale code.

### 1. Social authorization and service policy

- Keep authentication and pseudonymous profile identifiers.
- Enforce current consent, profile state, friendship, block status, rate limits, room/visit consent, and account deletion at the server boundary.
- Apply the same decisions to REST, WebSocket, presence, shared-room, visit, and push-registration routes.
- Do not add an Age Signals requirement to these baseline operations.

### 2. Preset-only messages

- Accept a documented `presetId` allowlist only.
- Reject or ignore neither `text` nor unknown payload fields silently: return a stable error and do not relay or persist the message.
- Enforce over REST and WebSocket, including offline/retry queues, event replay, and older app versions.
- Do not turn pet-generated speech or a player's local AI prompt into cross-player messaging.
- Review preset vocabulary and personal-data leakage before enabling phrases.

### 3. Safety, moderation, and privacy

- Preserve player block/report controls and ensure blocked profiles cannot invite, message, visit, or share a room.
- Add bounded rate limits and abuse-safe audit events that do not log message text or secrets.
- Confirm report data access and retention from the actual database cleanup jobs before updating privacy notices.
- Keep profile deletion available with valid profile authentication and ensure related social data is deleted according to the published policy.

### 4. Parent/guardian management (later stage)

- Define roles, permission scopes, revoke flows, abuse reporting, and child-facing transparency before adding schema.
- Short-lived one-use pairing codes, attempt limits, replay protection, cooldowns, and server-side revocation are technical pairing controls only.
- Do not describe pairing as verified legal guardianship unless an independent verification method actually establishes that.
- A child's unlink/report path must not depend on the manager's approval.

### 5. Staging and release

- Apply additive migrations only after a tested backup; ship rollback scripts and document data retention effects.
- Test the server patch in staging with old and new app clients, replayed requests, malformed payloads, blocked profiles, dropped networks, and concurrent consent changes.
- Publish source and patch first. Production deployment remains a separately reviewed action.

## Current source status

The live server source has not yet been imported. The Webmin terminal inspected on 2026-11-23 showed that its terminal connection had already closed; a read-only command returned no repository path or source revision. Direct password-based SSH through the execution tool was blocked, and the credential file was not printed or copied. Therefore this repository currently contains documentation only; it does not claim to contain the current server source or a deployable patch.

The v46 patch in `Trialum-Beta/server-patch/v46` is not a v47 starting point: it mandates Age Signals and Play Integrity eligibility. Do not deploy that patch for the v47 policy.

## Release gate

Do not call the server work complete until the exact live source is imported, preset-only behavior is enforced across REST and WebSocket, social controls remain functional, migrations/rollback are reviewed, and the matching app disclosure is accurate.
