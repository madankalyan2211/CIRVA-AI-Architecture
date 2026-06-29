# CIRVA Release History & Google Play Console Evidence

This document tracks the release dates, version history, compilation profiles, and deployment statuses of the CIRVA mobile application across its 15 builds.

---

## Testing & Deployment Status

* **Internal Testing**: Completed (April 2026 - June 2026).
* **Production Release Track**: Active (Android App Bundle format, signed with release credentials).
* **Google Play Console Release Target**: v1.0.2 (Build Code 15).

---

## Version History (Builds 1 - 15)

### Build 15 (v1.0.2)
* **Date**: June 28, 2026
* **Profile**: `production` (Release AAB)
* **Archive URL**: [Artifact Link](https://expo.dev/artifacts/eas/uDZq8Pe2CXDhGvNL7psUrYj7lGKFQyxzXioaukL9S0c.aab)
* **Key Changes**:
  * Added type-safe UUID text-casting across PostgreSQL database tables.
  * Resolved conversation routing duplicate checks in `ConversationScreen.tsx` to restore chat history for existing users.
  * Patched RLS updates for database mapping of legacy profiles.

### Build 14 (v1.0.1)
* **Date**: June 26, 2026
* **Profile**: `production` (Release AAB)
* **Archive URL**: [Artifact Link](https://expo.dev/artifacts/eas/X2dzyTiQtJw3H-ObO5dKUi6krZWcIo2HzLSMFyPj7BA.aab)
* **Key Changes**:
  * Implemented client feedback category selection UI screen.
  * Connected feedback database schema (`public.feedback`) to log user bug reports and feature requests.

### Build 11 - 13 (v1.0.0)
* **Date**: June 26, 2026
* **Profile**: `production` (Release AAB)
* **Archive URL**: [Artifact Link](https://expo.dev/artifacts/eas/j330scdEhL6pMLFntNUdW11IwShkEsXq2nNN6LaHvqU.aab)
* **Key Changes**:
  * Integrated search engine mapping using recipients' `authUserId` (Auth UUID) instead of local database primary keys.
  * Restructured RLS rules on `friend_requests` for self-request prevention and request state updates.

### Build 8 - 10 (v1.0.0)
* **Date**: June 26, 2026
* **Profile**: `production` (Release AAB)
* **Archive URL**: [Artifact Link](https://expo.dev/artifacts/eas/jdGIfXKtj_5EHE9nD6rjsByydPoAA8ORUo3Sx2iT1HA.aab)
* **Key Changes**:
  * Implemented peer-to-peer WebRTC calling capability.
  * Refined calling signals schemas using explicit direction checks (`is_offer` and `is_from_caller` indicators).
  * Restored `IncomingCallContext.tsx` background event listener for call popups.

### Build 7 (v1.0.0)
* **Date**: June 25, 2026
* **Profile**: `production` (Release AAB)
* **Key Changes**:
  * Introduced telemetry tracking for active users, classification latency, and feature usage event metrics.

### Build 6 (v1.0.0)
* **Date**: June 25, 2026
* **Profile**: `production` (Release AAB)
* **Key Changes**:
  * First production build test. Configured custom keystore signing (`@madan30__cirva.jks`).

### Build 2 - 5 (v1.0.0)
* **Date**: April 19, 2026 - June 25, 2026
* **Profile**: `development` / `preview`
* **Key Changes**:
  * Incremental user interface fixes, tablet landscape configurations, and bottom nav alignment improvements.

### Build 1 (v1.0.0)
* **Date**: March 15, 2026
* **Profile**: `development` (Initial APK)
* **Archive URL**: [Artifact Link](https://expo.dev/artifacts/eas/uLbxCcVtuBXx9gUkmBMHU2.apk)
* **Key Changes**:
  * Initial React Native app bundle setup.
  * Connected SQLite for localized data caching and MiniLM ONNX runtime for on-device natural language classification.
