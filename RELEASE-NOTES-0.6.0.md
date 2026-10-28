# Kaldrivon MiniSMO 0.6.0 — Release Notes

Windows 11 x64. Installer: `Kaldrivon-MiniSMO-0.6.0-Setup.exe` (checksum in `.sha256`), code-signed by Kaldrivon (Azure Artifact Signing). Installs over 0.5.x in place; your data, NFs and license are kept. Database schema 31 (upgraded automatically). From 0.5.2, *SYSTEM > Settings > Software updates* offers this version.
Nothing changes for you unless you turn on Team mode.

## New: Team mode (accounts and web access)

- **Local and Team mode** (*SYSTEM > Settings > Access*; accounts in *SYSTEM > Users*). Local mode is MiniSMO as before: only the desktop app on this PC, no sign-in. Team mode adds an HTTPS web UI for other people on your network, on port 9443 by default, with the PC's own certificate (its SHA-256 fingerprint is shown). Only the desktop app on the MiniSMO PC switches the mode; going back to Local mode ends every browser session.
- **Accounts and roles**: viewer (reads everything, exports), operator (alarms, monitoring, reads from NFs, backups, reports, previews), admin (everything, including NFs, configuration changes, settings, users). Writes that are not explicitly given to a lower role need admin.
- **Sign-in protection**: passwords of at least 12 characters, stored as salted scrypt hashes; lockout for 15 minutes after five wrong passwords; at most ten attempts a minute per address; the same answer for a wrong user name and a wrong password; one-time passwords for resets, changed at the next sign-in; the last enabled admin cannot be removed, disabled or demoted.
- **Sessions**: an HttpOnly, Secure, SameSite=Strict cookie (only its hash is stored), a separate token against cross-site requests, same-origin checks, idle timeout (8 h) and maximum lifetime (7 days), ended on sign-out, password change, reset, role change and when the account is disabled or removed.
- **Web UI with the desktop app's pages**: Dashboard, Network Functions (add with the host-key check or Call Home, edit, delete, identity mapping, capabilities, monitoring with methods, PM sources, file-based PM and measurement jobs, VES), Topology & Inventory, Alarms (console and history), Configuration (read, YANG-checked changes with preview, snapshots, compare; backups, rollback, templates, desired state, bulk jobs), Performance (charts), Automation (rules, runs, approvals, schedules), Analytics, Software Campaigns, API & Webhooks, NETCONF Log, Audit Log, Demo Network, Scenarios, License, Health & Capacity, Settings, YANG Models, Users, Help, About and Notifications. Every button appears only for roles the server allows (the role table comes from the server). Files the desktop app opens on the PC (diagnostics, reports, exports) are downloads in the browser. Plain JavaScript served by MiniSMO itself, no outside resources, strict content security policy. Works on phones too.
- Only the desktop app on the MiniSMO PC can switch Local / Team mode, run the factory reset, and install updates.
- **Audit**: every entry names who acted: the account and *WEB* for browser actions, the PC's user and *UI* for the desktop app. Sign-ins, failed sign-ins, sign-outs, account changes and mode switches are recorded.
- **Lost admin password**: `KaldrivonMiniSMO.Backend --reset-password <user>` on the MiniSMO PC prints a one-time password; `--list-users` lists the accounts.

## New: MiniSMO as a Windows service

- **RUN AS A WINDOWS SERVICE…** (*SYSTEM > Settings > Access*): MiniSMO keeps monitoring, receiving VES and PM files and serving Team mode when the window is closed, when nobody is signed in to Windows and after a restart. Administrator permission is asked once.
- The service is the same backend program (`KaldrivonMiniSMO.Backend.exe --service`), so the ports and firewall rules stay the same. It runs as its own account `NT SERVICE\KaldrivonMiniSMO`, without administrator rights, starts automatically and is restarted after a failure.
- Its data is in `%ProgramData%\Kaldrivon\MiniSMO`. Only that account and administrators can open it; your Windows account may read the logs, exports and the app's key, and may start and stop the service.
- Device passwords are re-encrypted for the service, with a key only the service can read. They are never written in plain text.
- **STOP RUNNING AS A SERVICE…** returns everything to your own data folder, with the passwords protected for your Windows account again.
- Updates stop and restart the service by themselves. Uninstalling removes the service and keeps its data.
- Minimizing the window was never a problem: it only pauses the screen, and collection continues. Closing the window stopped MiniSMO; the service is for that case.

## New: ready for services and, later, containers

- Configuration from the environment next to the stored settings: data folder, log folder, local API address, and any setting as `MINISMO_SETTING_<KEY>` (read-only in the app while set). Secrets from the environment or from files (`*_FILE`), never logged.
- Headless Team mode: the backend runs without the desktop app; the first admin can come from `MINISMO_ADMIN_USER` / `MINISMO_ADMIN_PASSWORD`.
- `/health/live` and `/health/ready` for health checks, without revealing anything about the installation.
- Logs to standard output on request (`MINISMO_LOG_STDOUT=1`), with the same masking as the log file.
- A clean stop on SIGTERM: NETCONF sessions, monitors and listeners are closed first.
- `team_public_url` for the address users see behind a port mapping, NAT or a reverse proxy.
- The firewall command shown for each listener matches the operating system (`/api/platform`).
- No Docker images or Linux packages yet; see `docs/DOCKER_READINESS.md` in the source.

## Changed

- **Software updates moved to *SYSTEM > About***, with the update channel next to it. *Check for updates: Once a day* is now on by default (it asks GitHub for the list of releases once a day; nothing is downloaded or installed without your confirmation). An installation where you chose *Only when I select CHECK NOW* keeps that choice.
- **Users** has its own entry in the navigation (*SYSTEM > Users*); *SYSTEM > Settings > Access* keeps the Local / Team mode switch.

## Fixed

- The packaged backend's Windows file version said 0.2.0; it now follows the app version, and the release build checks that every version number agrees.
- An NF that allows only one NETCONF session at a time (a second connection is refused) showed DISCONNECTED with "No existing session" while its monitoring was running. MiniSMO now notices this: when the NF advertises `:interleave`, requests (get-config, diagnostics, backups) run over the monitoring session; without `:interleave` MiniSMO keeps one session and collects FM and CM by polling. The NF overview and the Monitoring tab say so (**One session at a time**).
- An NF that reports the same fault over NETCONF and VES showed it twice. MiniSMO now takes each NF's alarms from one route: NETCONF (`ietf-alarms`, `o-ran-fm`) when that delivers them, VES otherwise; the VES copies stay in the VES event log. The Monitoring tab shows the **Alarm source**.
- `ietf-alarms` alarms used the alarm type as their title. The title is now the alarm text, and the type is the category, as for VES and O-RAN alarms.
- Long NF names were cut off in the alarm lists (Dashboard, Alarm Console, NETCONF activity). They now wrap to a second line, and the full name is in the tooltip.
- An NF added by DNS name that moved to a new address reconnected over NETCONF, but its VES events were refused from the new address. The name is now looked up again on every reconnection attempt, and the collector accepts the new address.
- A device that kept calling (Call Home) and was refused added a row to **REFUSED CALLERS** on every attempt. Repeated refusals of the same caller are now one row with a count, and the log notes the first and then every 50th. A key exchange the device breaks off is explained as such.
- Deleting an NF that could not be reached could hang at *Stop the NF sending VES to MiniSMO*, for a Call Home NF that did not call in, indefinitely. That step now waits at most 30 seconds, and not at all for a Call Home NF that is not connected; the NF is then deleted with a warning.
- The desktop app could show an error (for example "Only a single ContentDialog can be open at any time") when a background operation finished with an error while another dialog was open. Such a message now waits until the open dialog closes.
- Linux preparation: the disk type of NVMe and eMMC partitions was not recognised; the data folder is owner-only on POSIX systems.

## Known limitations

- MiniSMO is not certified against any security standard. Team mode was checked internally with tests and free, open-source scanners (security gate G7); an independent test is advisable before exposing it beyond a trusted network.
