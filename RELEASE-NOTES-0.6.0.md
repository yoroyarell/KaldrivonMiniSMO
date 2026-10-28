# Kaldrivon MiniSMO 0.6.0 — Release Notes

Windows 11 x64. Installer: `Kaldrivon-MiniSMO-0.6.0-Setup.exe` (checksum in `.sha256`), code-signed by Kaldrivon (Azure Artifact Signing). Installs over 0.5.x in place; your data, NFs and license are kept. From 0.5.2, *SYSTEM > Settings > Software updates* offers this version.
Nothing changes for you unless you turn on Team mode.

## New: Team mode (accounts and web access)

- **Local and Team mode** (*SYSTEM > Settings > Access*; accounts in *SYSTEM > Users*). Local mode is MiniSMO as before: only the desktop app on this PC, no sign-in. Team mode adds an HTTPS web UI for other people on your network, on port 9443 by default, with the PC's own certificate (its SHA-256 fingerprint is shown). Only the desktop app on the MiniSMO PC switches the mode; going back to Local mode ends every browser session.
- **Accounts and roles**: viewer (reads everything, exports), operator (alarms, monitoring, reads from NFs, backups, reports, previews), admin (everything, including NFs, configuration changes, settings, users). Writes that are not explicitly given to a lower role need admin.
- **Sign-in protection**: passwords of at least 12 characters, stored only as hashes; lockout for 15 minutes after five wrong passwords; at most ten attempts a minute per address; the same answer for a wrong user name and a wrong password; one-time passwords for resets, changed at the next sign-in; the last enabled admin cannot be removed, disabled or demoted.
- **Sessions** end after 8 hours without use or 7 days at most, and on sign-out, password change, reset, role change and when the account is disabled or removed. Browser sessions are protected against cross-site requests.
- **Web UI with the desktop app's pages**: Dashboard, Network Functions (add with the host-key check or Call Home, edit, delete, identity mapping, capabilities, monitoring with methods, PM sources, file-based PM and measurement jobs, VES), Topology & Inventory, Alarms (console and history), Configuration (read, YANG-checked changes with preview, snapshots, compare; backups, rollback, templates, desired state, bulk jobs), Performance (charts), Automation (rules, runs, approvals, schedules), Analytics, Software Campaigns, API & Webhooks, NETCONF Log, Audit Log, Demo Network, Scenarios, License, Health & Capacity, Settings, YANG Models, Users, Help, About and Notifications. Every button appears only for the roles allowed to use it. Files the desktop app opens on the PC (diagnostics, reports, exports) are downloads in the browser. The web UI loads nothing from outside MiniSMO. Works on phones too.
- Only the desktop app on the MiniSMO PC can switch Local / Team mode, run the factory reset, and install updates.
- **Audit**: every entry names who acted: the account and *WEB* for browser actions, the PC's user and *UI* for the desktop app. Sign-ins, failed sign-ins, sign-outs, account changes and mode switches are recorded.
- **Lost admin password**: `KaldrivonMiniSMO.Backend --reset-password <user>` on the MiniSMO PC prints a one-time password; `--list-users` lists the accounts.

## New: MiniSMO as a Windows service

- **RUN AS A WINDOWS SERVICE…** (*SYSTEM > Settings > Access*): MiniSMO keeps monitoring, receiving VES and PM files and serving Team mode when the window is closed, when nobody is signed in to Windows and after a restart. Administrator permission is asked once.
- The service uses the same ports and firewall rules as the app. It runs as its own account `NT SERVICE\KaldrivonMiniSMO`, without administrator rights, starts automatically and is restarted after a failure.
- Its data is in `%ProgramData%\Kaldrivon\MiniSMO`. Only that account and administrators can open it; your Windows account may read the logs, exports and the app's key, and may start and stop the service.
- Device passwords are re-encrypted for the service, with a key only the service can read. They are never written in plain text.
- **STOP RUNNING AS A SERVICE…** returns everything to your own data folder, with the passwords protected for your Windows account again.
- Updates stop and restart the service by themselves. Uninstalling removes the service and keeps its data.
- Minimizing the window was never a problem: it only pauses the screen, and collection continues. Closing the window stopped MiniSMO; the service is for that case.

## Changed

- **Software updates moved to *SYSTEM > About***, with the update channel next to it. *Check for updates: Once a day* is now on by default (it asks GitHub for the list of releases once a day; nothing is downloaded or installed without your confirmation). An installation where you chose *Only when I select CHECK NOW* keeps that choice.
- **Users** has its own entry in the navigation (*SYSTEM > Users*); *SYSTEM > Settings > Access* keeps the Local / Team mode switch.

## Fixed

- An NF that allows only one NETCONF session at a time (a second connection is refused) showed DISCONNECTED with "No existing session" while its monitoring was running. MiniSMO now notices this: when the NF advertises `:interleave`, requests (get-config, diagnostics, backups) run over the monitoring session; without `:interleave` MiniSMO keeps one session and collects FM and CM by polling. The NF overview and the Monitoring tab say so (**One session at a time**).
- An NF that reports the same fault over NETCONF and VES showed it twice. MiniSMO now takes each NF's alarms from one route: NETCONF (`ietf-alarms`, `o-ran-fm`) when that delivers them, VES otherwise; the VES copies stay in the VES event log. The Monitoring tab shows the **Alarm source**.
- `ietf-alarms` alarms used the alarm type as their title. The title is now the alarm text, and the type is the category, as for VES and O-RAN alarms.
- Long NF names were cut off in the alarm lists (Dashboard, Alarm Console, NETCONF activity). They now wrap to a second line, and the full name is in the tooltip.
- An NF added by DNS name that moved to a new address reconnected over NETCONF, but its VES events were refused from the new address. The name is now looked up again on every reconnection attempt, and the collector accepts the new address.
- A device that kept calling (Call Home) and was refused added a row to **REFUSED CALLERS** on every attempt. Repeated refusals of the same caller are now one row with a count. A key exchange the device breaks off is explained as such.
- Deleting an NF that could not be reached could hang at *Stop the NF sending VES to MiniSMO*, for a Call Home NF that did not call in, indefinitely. That step now waits at most 30 seconds, and not at all for a Call Home NF that is not connected; the NF is then deleted with a warning.
- The desktop app could crash when a background operation (for example a connection attempt) finished with an error while another window was open in the app. The error message now waits until that window closes.

## Known limitations

- MiniSMO is not certified against any security standard. Before you make Team mode reachable beyond a trusted network, an independent security test is advisable.
