# Kaldrivon MiniSMO 0.2.0 — Release Notes

Windows 11 x64. Installer: `Kaldrivon-MiniSMO-0.2.0-Setup.exe` (checksum in `.sha256`). Upgrades 0.1.0 in place. The database is backed up and migrated on first start (schema 4 → 9).

The installer is **not code-signed** yet, so Windows SmartScreen may warn on first run. The EULA is still a **draft for legal review**.

## What's new

**External NF monitoring (FM, CM, PM)**
- A dedicated notification session per NF. Capability discovery from the server `<hello>`, the RFC 5277 stream list and the YANG library.
- States Connected / Subscribed / Receiving / Stale / Reconnecting / Polling / Unsupported / VES. Connection health and data freshness are tracked separately.
- Reconnect with exponential backoff and jitter, replay from the last event (RFC 5277 / 8639), dedup of replayed events, and recorded gaps when replay is not available.
- FM:
  - NETCONF alarm notifications for `o-ran-fm` and `ietf-alarms` (RFC 8632), plus the 3GPP `AlarmList` (`_3gpp-common-fm`, including devices that hold it in the running configuration and signal changes with `netconf-config-change`).
  - Initial inventory sync, then raise / update / clear.
  - Labels come from the alarm itself: title `specificProblem` (else the category), category `alarmType` / VES `alarmCondition`, faulty object `objectInstance` / `alarmInterfaceA`. The alarm ID is shown as an ID, never as a title.
  - Identity is NF + object DN + `alarmId`. The same alarm seen as a VES fault and in the NETCONF AlarmList is one alarm. An AlarmList entry without `objectInstance` is merged with the VES alarm of the same `alarmId`, and an AlarmList read never clears alarms that came by VES.
  - `netconf-config-change` is never an alarm. It is classified by target path: AlarmList subtree → re-read the AlarmList; PM counter container → dropped; anything else → CM. AlarmList `administrativeState` LOCKED is shown as maintenance.
  - Immutable event history; out-of-order and unmatched events are recorded, not applied.
  - Local-only notes and acknowledgements.
- CM:
  - Versioned `<get-config>` snapshots with secrets masked.
  - RFC 6470 change timeline, net diffs between versions, export.
  - Change-triggered snapshots are coalesced (at most one per 30 s).
  - A 3GPP AlarmList (FM) and PM counter containers (PM) are left out of snapshots.
- PM:
  - YANG-Push periodic (RFC 8641), O-RAN `measurement-result-stats`, model-driven pull (O-RAN-SC `nts-cell-pm`) and VES perf3gpp.
  - Pull reads by data path, not module root: the path to an augmenting PM container (e.g. `ManagedElement/GNBDUFunction/NRCellDU/pm-counters`) is resolved from the NF's YANG augment targets or the instance tree, with the namespace of each level.
  - Metric and managed object are chosen separately; objects are grouped under their parent (e.g. cells under their O-DU) in a collapsible tree.
  - Counter resets are handled and missing intervals are shown as gaps; charts include compare and CSV export.

**VES collector (VES Event Listener 7.2.1)**
- `POST /eventListener/v7` and `/eventListener/v7/eventBatch` over HTTPS (self-signed certificate created on first start, fingerprint shown) and HTTP.
- Default ports 8443 / 8080. Optional HTTP Basic authentication.
- Runs on its own listener; the MiniSMO API stays local-only.
- Domains:
  - `fault` becomes alarms, keyed by NF + `alarmInterfaceA` + `alarmAdditionalInformation.alarmId` (the condition only when there is no alarm ID). `NORMAL` clears.
  - `perf3gpp` (TS 28.550) becomes PM series per `measObjInstId` and measurement name. Each `measInfo`'s own `sMeasTypesList` names the values by their 1-based `p`; several `measInfo` per event are supported; `suspectFlag` marks values as suspect.
  - `heartbeat` drives the heartbeat state.
  - `pnfRegistration` triggers resubscribe and optional re-provisioning.
  - `stndDefined` 3GPP FaultSupervision becomes alarms.
  - Other domains are stored as not interpreted.
- Events are attributed by `sourceName` / `sourceId` / reporting entity against the NF name and per-NF VES source names. Unattributed sources are listed and can be assigned.
- The installer adds one Windows Firewall rule (inbound TCP 8443, 8080, backend executable only, Domain and Private profiles). It can be unticked during setup and is removed on uninstall.

**Configuration changes on external NFs (off until enabled per NF)**
- In *Configuration*, **ENABLE CHANGES…** for one NF, edit values, **REVIEW CHANGES…**, then **APPLY TO NF**.
- The parameters are the NF's running configuration. Types and constraints (ranges, lengths, enumerations, units) come from its YANG modules, read with `<get-schema>` or imported, with groupings, typedefs and augments resolved. List keys, `config false` data, secrets, leaf-lists and the alarm list are not editable.
- The preview re-reads the NF, checks every value and refuses values that changed on the NF in the meantime, and shows the exact `edit-config`.
- The transaction: lock candidate → `edit-config` (merge) → `validate` → confirmed `commit` → read back → confirming `commit` → unlock. A mismatch cancels the commit, or the NF rolls back at the timeout; a failed step discards the candidate. For writable-running NFs: lock → `edit-config` with test-then-set → read back → unlock.
- The NF's `rpc-error` (tag, path, message) is shown as returned. Each change is recorded in History with a new configuration version.
- The payload is checked on the XML actually sent: only the confirmed leaves and the list keys that address them.

**Adding an NF integrates it automatically**
- After **SAVE**, MiniSMO connects, discovers, starts FM / CM / PM monitoring and points the NF's VES destination at the collector. A dialog shows each step and its result; an unreachable NF is saved and can be connected later. *Settings > Integration* turns monitoring start and VES pointing off separately.
- VES pointing is model-driven. In this release MiniSMO recognises the O-RAN-SC NTS VES settings (`nts-network-function`, `simulation/ves-endpoint`). The collector address is computed per NF (the local address that routes to it), or set in Settings for NAT or DNS. The values are read back and verified.
- The NETCONF client checks the payload it actually sends: only leaves of that one container, no delete/replace operations, no second subtree. Everything else stays blocked, including through the API (`/api/nfs/{id}/netconf/edit-config` still returns 403).
- MiniSMO keeps the NF pointed here: it re-applies after restart or registration, or after a collector address change, and writes only when the values differ.

**NF onboarding**
- SSH authentication: password (with keyboard-interactive fallback), keyboard-interactive (non-standard prompts supported), or SSH private key (Ed25519, ECDSA, RSA; OpenSSH or PEM; optional passphrase).
- Keys and passwords are stored with Windows DPAPI and masked in all logs.
- YANG library discovery (RFC 8525, RFC 7895 fallback). Modules found only in the library are listed with the NF's capabilities. A library change triggers rediscovery.

**Demo Network and UI**
- Simulated NFs now raise, escalate and clear alarms on their own (Settings: off / low / normal / high).
- Delete Demo Network, and restore only the missing NFs when some were deleted.
- New visual design (IBM Plex Sans / Mono, Space Grotesk under the SIL Open Font License, installed into Windows by the installer; the app falls back to Segoe UI / Cascadia Mono without them).
- New Subscriptions and VES Collector pages (under System). External configuration uses the same parameter view as simulated NFs, plus XML tree and raw XML.
- Lists that refresh on their own (VES events, alarms, subscriptions, network functions, dashboard, topology) keep their scroll position and selection.
- Raising a test alarm is a dialog behind **RAISE TEST ALARM…** instead of a permanent panel.
- Fixed:
  - Help Center blank on installed builds.
  - Truncated menu and license text.
  - Empty simulated configuration values.
  - Saving Settings could point the app at a different backend port than the one in use.
  - Configuration review listed parameters you had not changed (multi-line values such as keystore entries, or choice fields such as `mount-point-addressing-method`). Only parameters you edit are reviewed now.
  - Gap above the first entries in the dashboard's Recent NETCONF activity.
  - NETCONF Log refreshes on its own, keeping the open entry and scroll position.
  - Capacity & Storage shows the frontend memory again.
- New installations use the MINIMAL retention preset (raw PM 6 h, 5-minute 7 d, hourly 30 d, FM 30 d, NETCONF 7 d). Retention values you set yourself are kept.

**Security fixes**
- Configuration snapshots and NETCONF payloads now also mask secret leaves with a prefix, such as `ves-endpoint-password`, `controller-password` and `userPassword`. Snapshots taken by 0.1.x dev builds may still contain such values until they age out (50 versions per NF).

## Known limitations
- Only one VES settings model is known for provisioning (O-RAN-SC NTS). For other NFs, set the VES destination with the vendor's tools; the collector accepts their events.
- File-based PM (fileReady / file download) is detected but not fetched.
- No configured (persistent) NETCONF subscriptions; subscriptions end with the session.
- 3GPP AlarmList records that change several times between two reads are seen in their latest state only; every change notification is still counted.
- Configuration changes cover leaves of existing entries (merge). Creating or deleting list entries, leaf-lists and `when` / `must` constraints are left to the NF's validation.
- No A1 / O2 / E2; single user, single PC.
- Installer unsigned; EULA draft.
