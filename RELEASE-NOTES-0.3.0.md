# Kaldrivon MiniSMO 0.3.0 — Release Notes

Windows 11 x64. Installer: `Kaldrivon-MiniSMO-0.3.0-Setup.exe` (checksum in `.sha256`). Upgrades 0.2.0 in place. The database is backed up and migrated on first start (schema 9 → 16). The only data removed is the "Unspecified alarm" duplicates described under Fixes.

The installer is **not code-signed** yet, so Windows SmartScreen may warn on first run. The EULA is still a **draft for legal review**.

## Editions: Community and Pro

- **Kaldrivon Mini SMO Community** is MiniSMO without a license. It manages up to **5 external NFs** and includes all essential FM, PM and CM. Everything that was free in 0.2.0 stays free.
- **Kaldrivon Mini SMO Pro** is MiniSMO with any valid signed `.klicense` (the existing licensing system, unchanged). It adds the licensed NF capacity and removes the limits of the Pro features listed below.
- **Pro features without a license**: Community includes a small allowance of every Pro feature (for example 1 READ API token with 1,000 requests a day, 1 webhook, 2 automation rules, 2 templates, 10 correlation or KPI analyses a day, bulk jobs on up to 3 NFs). The allowance and today's use are listed on *SYSTEM > License*; going past it shows what Community includes. Pro only: NFs above 5, retention settings, listening for the API beyond this PC, flapping suppression, automation changes without approval.
- The edition is shown in the title bar chip, on the Dashboard (`Community | Managed NFs 3/5`) and on *SYSTEM > License*. Pro features carry a small **PRO** marker. There are no pop-ups or reminders.
- Availability has two parts that are shown separately: whether your edition includes a feature (**PRO FEATURE**) and whether the NF supports it technically (**NOT SUPPORTED BY NF**, with the reason).
- **Downgrade is non-destructive.** When a license is removed, expires or becomes invalid, all data, NFs, backups, templates, rules and schedules are kept. The oldest items within the Community allowance keep running; the rest are paused, not deleted, and resume when a valid license is installed again. NFs already onboarded above 5 stay visible; you cannot add more until you are within the limit or on Pro.
- Adding a 6th external NF on Community is refused with a clear message; nothing partial is stored.
- License changes are recorded in the audit log and raise an in-app notification.

## New in Community

- **Topology & Inventory**: one page with the topology and the site / region / NF / cell tree. External O-RUs and O-DUs can be placed under their O-DU / O-CU (stored in MiniSMO only).
- **NF identity**: vendor, software version, NF type, model, serial number and hardware version, read with `<get>` from the 3GPP `ManagedElement/attributes` (the standard O1 location), then o-ran-software-management and ietf-hardware when advertised, with VES pnfRegistration and the VES event header as fallback. Read automatically on connect and on pnfRegistration; shown as unknown only when no source reports it.
- **NF capabilities**: a vendor-neutral view of what each NF supports (FM notifications, YANG-Push, PM pull, configuration writes, software management), with "Not discovered yet" instead of guesses.
- **Global search** in the title bar across NFs (name, host, vendor, site, region), alarms and cells.
- **Configuration backups**: manual backups, compare a backup with the current configuration of the same NF, export.
- **Audit log** of every change made in MiniSMO, with the Windows user name. Secrets are never logged.
- **Notifications** behind the bell at the top right, and a **Needs attention** card and system state on the Dashboard.
- Alarm list **EXPORT CSV**.
- **O-RU performance data (O-RAN WG4)**: `performance-measurement-objects` read with `<get>` every 60 s: rx-window counters per object unit (RU hardware component, transport flow, eAxC) shown as the change between polls, and transceiver min / max / first / latest. Measurement names are readable (`Rx on time (RX_ON_TIME)`); the O-RU's measurement settings are shown read-only.
- **Standards-based method choice with fallback**: FM, CM and PM methods are chosen from what each NF announces (hello, YANG library, streams), in a fixed standards order. Reads use the namespaces the NF announced. All PM sources the NF offers run side by side (YANG-Push, the NF's PM model, O-RAN PM notifications, VES perf3gpp, measurement files). When a method stops working (stream refused, YANG-Push refused, repeated read failures, notifications not arriving), MiniSMO switches to the next method the NF offers and lists what changed and why on the Monitoring tab (METHODS & FALLBACKS) and in the notifications. You can choose the FM / CM method, turn PM sources off and turn automatic fallback off per NF.
- O-RU measurements use one naming scheme whether they come from notifications or `<get>` (for example `Rx power (RX_POWER) latest` on `transceiver 0`; window counts from notifications are `… per interval`).
- **File-based PM**: 3GPP measurement files (`measCollecFile`) signalled by VES fileReady, 3GPP notifyFileReady or the O-RAN `file-upload-notification` are retrieved over SFTP, FTPES or HTTPS with per-NF credentials (or the NETCONF ones) from allowed hosts only, parsed and stored per object and period. Each file is ingested once. FILES ON NF lists files (`retrieve-file-list`) and can request an upload (`file-upload`, after confirmation). IMPORT FILE stores a file from disk.

## New in Pro

- **Northbound REST API** (`/api/v1`): its own listener (default `127.0.0.1:8770`, HTTPS), bearer tokens with READ or OPERATE scope, stored as hashes and shown once.
- **Webhooks** with event and severity filters, HMAC-SHA256 signing, retries with back-off and automatic pause after repeated failures.
- **Automation**: event-driven rules and scheduled jobs; actions that change the network always create an **approval** first (human in the loop), with expiry.
- **Fault**: alarm correlation (potential parent alarm and likely related alarms from timing and topology; never presented as a root cause), flapping detection and suppression controls.
- **Performance**: KPI rankings, most-alarmed NFs, biggest changes.
- **Configuration**: cross-NF and template comparisons with ignore rules, desired state and drift detection (every 15 minutes), templates with pattern matching, bulk backup / health check / template apply as tracked jobs, scheduled backups and rollback orchestration (backup, preview, apply).
- **Software campaigns**: planning and pre-checks (target version, per-NF readiness). Execution on NFs is not part of 0.3.0.
- **Reports**: health and operations reports (HTML, JSON, CSV), on demand or scheduled.
- **Troubleshooting**: Investigate (one view of what was observed around an NF or alarm: alarms, configuration changes, KPI trends, likely related alarms and recommended checks, worded as possibilities) and diagnostic ZIP collection without credentials.
- **Retention and audit**: retention for audit, backups and automation history; audit search and CSV export.

All network changes made by Pro features use the same per-NF write enablement, preview and confirmation as manual configuration changes, and a backup is taken first.

## Menu

OVERVIEW Dashboard · NETWORK Network Functions (per-NF tabs: Overview, Capabilities, Monitoring), Topology & Inventory · FCAPS Alarms, Configuration (Configuration, Configuration Operations; NETCONF read tab), Performance · TOOLS Automation, Analytics, Software Campaigns, API & Webhooks · LOG NETCONF Log, Audit Log · SIMULATION Demo Network, Scenarios · SYSTEM License, Health & Capacity (Capacity & Storage, Reports), Settings (General, Storage & Retention, VES Collector, YANG Models).

Pages with several sections show them as tabs across the top. The Network Functions toolbar shows as many actions as fit and moves the rest to "…". Notifications are behind the bell at the top right.

## Fixes

- NFs that describe their alarms over VES and also keep a 3GPP AlarmList with only `alarmId` and severity (the O-RAN-SC NTS simulators) no longer produce "Unspecified alarm" entries. The AlarmList record now only updates or clears the matching VES alarm, and the stored duplicates are removed by the migration.

- Monitoring no longer drops and reopens the session every 30 s when a device never answers one initial read (seen with the O-RAN active alarm list on O-RAN-SC NTS O-RU simulators). That function is reported as *initial sync incomplete*; notifications and the other functions keep working.
- Each NF's Monitoring tab shows what the VES collector actually receives from it (destination, events, heartbeat, pnfRegistration).
- The Topology page no longer draws the "not under an O-DU" group as if it were a simulated O-RU.
- Audit entries open with all their fields; adding an NF is recorded with the new NF's name, address and type.
- Text dialogs (identity, audit, …) no longer show only their first line.
- The keyboard-shortcut hint (for example "F1") no longer appears over the window.
- SQLite waits up to 30 s for a busy database instead of 5 s, avoiding "database is locked" on large databases.

- **Stop monitoring now stops everything**: VES events from an NF whose monitoring is stopped are logged but create no alarms or PM. A MONITORING column in Network Functions shows the state per NF.
- **Supervision**: internal alarm *Loss of supervision* while an NF's monitoring session is down (cleared on reconnect), *VES heartbeat missed*, and health alarms (storage, memory, CPU, queues, event bus, failed jobs / webhooks, VES listener) raised and cleared from the System health checks.
- **Alarm resync** after every reconnection and on request (**RESYNC ALARMS**); for NFs that report faults by VES with a 3GPP AlarmList, alarms no longer in the list are cleared. When an NF's list cannot be read, a confirmed local clear is offered.
- **O-RAN FM alarms** are titled from `fault-text` and keyed by NF + `fault-id` + `fault-source` (stored alarms are re-keyed by the migration).
- **VES attribution by sender address** no longer gives events of one NF to another NF on the same host (containers). Events whose sourceName belonged to another (possibly deleted) NF are listed as unattributed.
- **PURGE DATA…** (Health & Capacity > Storage) deletes all FM, PM, CM or log data after typing CONFIRMED.
- About and audit texts wrap instead of being cut off.
- Buttons that run something (exports, diagnostics, reports, snapshots, running a schedule, testing a webhook, analyses) ask for confirmation first; exports then show where the file is, with OPEN FILE / SHOW IN FOLDER.
- Software versions moved to *TOOLS > Software Campaigns* (with campaign planning). Inventory shows the network tree.
- In the network tree, an NF with its own site or region is shown there (naming its topology parent) instead of only under its parent.
- Community daily limits reset at local midnight; KPI analysis and alarm correlation are counted separately.

## Known limitations

- No user accounts or role-based access control; one Windows user. API tokens have READ or OPERATE scope only.
- Software campaigns are planned, not executed.
- Webhook delivery is at least once; receivers should tolerate duplicates.
- Scheduled jobs and automation run only while MiniSMO is running.
- Correlation and KPI analytics use the data MiniSMO has collected; they never estimate missing data.
- Limitations from 0.2.0 still apply (no persistent NETCONF subscriptions, no A1 / O2 / E2, single PC).
- File-based PM: MiniSMO does not run its own file server; for SMO-pull uploads point the NF at a server you provide. Measurement and upload configuration on the NF is not changed by MiniSMO.
- Installer unsigned; EULA draft.
