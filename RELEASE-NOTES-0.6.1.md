# Kaldrivon MiniSMO 0.6.1 — Release Notes

Windows 11 x64: `Kaldrivon-MiniSMO-0.6.1-Setup.exe` (checksum in `.sha256`), code-signed by Kaldrivon. Installs over 0.6.0 and 0.5.x in place; your data, NFs, accounts and license are kept. *SYSTEM > About > Software updates* offers this version.
Linux x86_64: `kaldrivon-minismo_0.6.1-1_amd64.deb` and `kaldrivon-minismo-0.6.1-1.x86_64.rpm` (checksums in `SHA256SUMS`).

## New: Linux packages

- **MiniSMO for Linux** (x86_64): `kaldrivon-minismo_0.6.1-1_amd64.deb` for Ubuntu, Linux Mint and Debian, `kaldrivon-minismo-0.6.1-1.x86_64.rpm` for RHEL, Rocky, Alma and Fedora (glibc 2.28 or newer, systemd). They bring their own Python runtime and need nothing else.
- On Linux, MiniSMO runs as the service `kaldrivon-minismo` under its own unprivileged account, starts at boot, and is used in a browser (Team mode, `https://<machine>:9443/`). There is no desktop app.
- `sudo kaldrivon-minismo create-admin <name>` creates the first admin; `reset-password`, `list-users`, `status` and `logs` are there too. Settings: `/etc/kaldrivon-minismo/minismo.env`; data: `/var/lib/kaldrivon-minismo`.
- Upgrading keeps the data; removing the package keeps it too, `apt purge` removes everything.

## Changed

- **Your own certificate for the web UI** (*SYSTEM > Settings > Access*, **LOAD CERTIFICATE…**; also in the web UI for admins), so browsers no longer warn "Not secure": a `.pfx` with its password, or a PEM certificate with its chain and key, from your organization's CA or a public CA. It is checked before use (key, validity, made for web servers, names, key strength), stored with the key encrypted, and supervised: an internal alarm warns 30 days before it expires. **USE SELF-SIGNED…** goes back. The VES collector keeps its own certificate.
- **Communication matrix** (*SYSTEM > Settings*, and *Settings > Communication Matrix* in the web UI): every connection MiniSMO makes or accepts, with local and remote system and port, who opens it, protocol, service, authentication, encryption, whether it is in use and whether a firewall rule is needed, built from your current settings and NFs. **EXPORT CSV** saves it for firewall requests.
- Turning on Team mode or the Windows service, and going back, shows progress until it is done.
- *Performance*: **SHOW IN FOLDER** next to the PM status when the NF's PM comes from measurement files.
- **VES Collector**: **CLEAR LIST** on *Refused senders*, *Unattributed sources* and *Deleted NFs still sending*. The lists only show what arrived; clearing them accepts or blocks nothing.
- **Web UI**:
  - *Performance* shows six boxes like the desktop app; right-click a box to choose its measurement (remembered per NF in that browser).
  - *Topology & Inventory*: right-click a node (or an NF in the Inventory tree) to open it, place it in the topology, set its site and region, or investigate it; the Inventory details have **PLACE IN TOPOLOGY…**.
  - *Audit Log*: select an entry to see its details (with Previous / Next), and **EXPORT CSV**.
  - *NETCONF Log*: the selected message opens over the list instead of far below it, with Previous / Next.
  - *Alarms*: the details have **CLOSE**, and close by themselves when the alarm clears.
  - *Configuration*: the parameter table keeps fixed columns; long values (keys, certificates) fold and open on click, and each object's group is named once.
  - *Performance*: the measurement list shows the selected managed object (or all, with **All objects**), with a search and at most 200 rows; **MEASUREMENT FILES** opens the NF's files when PM comes from measurement files.
  - **Theme**: System, Dark or Light, in the side bar, like the desktop app (remembered in that browser).
  - Dialogs with long content (a NETCONF message) open at the top.
- The desktop app's header shows whether MiniSMO is in **LOCAL** or **TEAM** mode (with the web addresses, accounts and service mode in its tooltip).
- **PM aggregates for external NFs**: hourly, daily, weekly (from Monday) and monthly, in UTC, with count, sum, average, minimum, maximum, last value and a counter's increase per period. *Performance* has an **Aggregation** selector (Raw, Hourly, Daily, Weekly, Monthly) with ranges up to 5 years, and **EXPORT CSV** exports the chosen level. Daily and weekly / monthly retention are new in *Storage & Retention* (presets: 365 / 730 days, 730 / 1825, 1825 / 3650); an installation on the STANDARD or EXTENDED preset gets that preset's values.
- New internal alarm **PM data not received** (MINOR, on the NF): PM is monitored and should be arriving (its chosen PM path is provisioned on the NF with a known period, or its counters are read with `<get>` as the fallback), but none arrived for three periods (at least 10 minutes). Cleared when PM data arrives again.
- **Back to the preferred method**: while FM or CM monitoring of an NF runs on a fallback method, MiniSMO checks from time to time whether the preferred method works again (after 15 minutes, then up to every 4 hours) and returns to it when it does. The Monitoring tab shows when the next check is due.
- **When an NF's VES heartbeats stop**, MiniSMO uses NETCONF for what VES carried: the NF's alarms come from its alarm list (read at once), and its counters are read with `<get>`. VES takes over again when the heartbeats return.

## Fixed

- Turning on Team mode with a password shorter than 12 characters closed the dialog without saving anything. The dialog now stays open and says what is missing.
- Web UI, adding an external NF: after you confirmed the NF's SSH host key the form came back and the test had to be run again, and when the NF could not be added (for example without a Managed Object ID or label) the reason was hidden behind the form. The test now continues after the confirmation, the form checks the Managed Object ID and label before testing, and a refusal is shown at the top of the form.
- Web UI *Analytics*: KPI rankings, biggest changes and flapping alarms showed "–" instead of the values.
- Web UI *Topology & Inventory*: a cell whose state the NF does not report now says *not reported by the NF*, as in the desktop app, with when the state was read.
- With MiniSMO running as a Windows service, **Reset MiniSMO** left the app unable to connect to the service ("port in use by another program"): the reset removed the folders whose permissions let your Windows account read the service's key, logs and exports. They are now emptied and kept. The app also says so plainly when it cannot read the service's key, and installing the new version restores the permissions of an installation where this happened.
- 3GPP AlarmList alarms (for example from O-RAN-SC NTS O-DUs) came without title, object and category when MiniSMO read the list itself: it read the configuration only, while these attributes are read-only state. The list is now read with configuration and state.
- With MiniSMO running as a Windows service, **SHOW IN FOLDER** for an NF's measurement files did nothing: the service keeps them in `%ProgramData%\Kaldrivon\MiniSMO\pm-files`, which your Windows account could not open. Your account can now read that folder. For a service set up with 0.6.0, installing the new version adds the permission. A folder the app cannot open now shows a message instead of failing silently.
