# Kaldrivon MiniSMO 0.3.1 — Release Notes

Windows 11 x64. Installer: `Kaldrivon-MiniSMO-0.3.1-Setup.exe` (checksum in `.sha256`). Upgrades 0.3.0 (and 0.2.0) in place; your data, NFs and license are kept. No database schema change (schema 16).

The installer is **not code-signed** yet, so Windows SmartScreen may warn on first run. The EULA is still a **draft for legal review**.

## New

- **Reset MiniSMO** (*SYSTEM > Settings*, bottom): returns MiniSMO to a fresh installation. You confirm three times by typing **RESET**, **DELETE ALL DATA** and **MINISMO**, and choose whether to keep the installed license. MiniSMO restarts and deletes the data folder before it opens anything. A pending reset can be cancelled. Nothing is sent to any NF.
- **Deleting an NF**:
  - The confirmation has the option **Stop this NF sending VES to MiniSMO** (on by default). MiniSMO restores the NF's previous VES destination, or, when that is not known, sets it to the NF's own loopback address (127.0.0.1).
  - A progress window shows each step (VES destination, monitoring stopped, history deleted with row count, NF removed) and closes only when the deletion has finished. A second delete of the same NF is refused while one runs.
  - Events that still arrive from a deleted NF are dropped, not stored, and listed on the *VES Collector* tab under **Deleted NFs still sending**.
- **PURGE DATA…** (*Capacity & Storage > Storage*) has three more groups: **BACKUPS** (configuration backups of all NFs, including ones marked KEEP), **OPS** (audit log, notifications, automation runs, jobs, webhook deliveries, approvals) and **FILES** (exports, temporary files, database backup copies). Each group shows how many rows or files it would delete.
- **Storage tab**: PM from external NFs, file-based PM, VES events, configuration snapshots and backups, and operations data are counted in their own categories (new: *NETCONF & VES Logs*, *Operations & Audit*), so *Inventory & Other* is only the remainder. The tab updates by itself within about 5 seconds when files are added or deleted in the data folder (for example backups deleted in File Explorer); storage is measured again only when something changed.

## Fixes

- **"An error occurred while sending the request"** after deleting NFs: deleting an NF with a long history could hold the database for a long time. NF data is now deleted in small batches, VES events of an NF being deleted no longer fail, app and backend keep-alive times are aligned, and a failed read is retried once.
- **"Unspecified alarm"** entries after adding an NF: 3GPP AlarmList records that carry only an alarmId and a severity appeared when the first list read ran before VES was set up. VES is now pointed at MiniSMO before monitoring starts, an NF pointed at the collector counts as reporting its faults by VES, and such entries already shown are removed at the next list read. An NF that has only an AlarmList still shows them (they are its only fault data).
- **Dialogs**: wider (up to 960 px); long titles, check box and toggle labels, field headers and texts wrap instead of being cut off, and tall dialogs scroll.

## Known limitations

Unchanged from 0.3.0 (see the User Guide, *About MiniSMO > Known limitations*).
