# Kaldrivon MiniSMO 0.7.0 — Release Notes

Windows 11 x64: `Kaldrivon-MiniSMO-0.7.0-Setup.exe` (checksum in `.sha256`), code-signed by Kaldrivon. Installs over 0.6.x and 0.5.x in place; your data, NFs, accounts and license are kept. *SYSTEM > About > Software updates* offers this version.
Linux x86_64: `kaldrivon-minismo_0.7.0-1_amd64.deb` and `kaldrivon-minismo-0.7.0-1.x86_64.rpm` (checksums in `SHA256SUMS`, signed by Kaldrivon in `SHA256SUMS.sig`; the public key is `kaldrivon-linux-signing.pub`). From this version, MiniSMO on Linux can install later versions from the web UI and checks their signature first.

## Team mode security

- Roles with separation of duties (viewer, FM operator, CM engineer, PM analyst, SW manager, security admin, system admin), custom roles and copies, groups, and roles limited to NF groups (by list, by attributes or by DN subtree). Existing accounts keep what they could do.
- Configuration rules per role: which parameters a role may change.
- Account policy: password rules with an importable blocklist, history and ages; lockout values; session timeouts (now 15 minutes idle, 12 hours at most) and a limit per account; inactive accounts; account expiry; sign-in banner.
- Passwords stored with Argon2id.
- Second factor (authenticator app), optional or required per role.
- Forgot password by e-mailed code (needs the new e-mail server setting).
- Personal API tokens for scripts.
- Sign-in with an LDAP directory or an OpenID Connect provider; local accounts keep working when they are down.
- Export and import of accounts, groups and roles.
- Audit log: sign-in category, settings changes with before and after values, forwarding to a SIEM over syslog/TLS.
- HSTS when your own web certificate is loaded.

## NF connections

- NETCONF over TLS with client certificates; SSH strict algorithms by default, legacy per NF.
- Call Home over TLS (port 4335) and approval of refused callers from the list.
- Critical alarm when an NF presents another host key or certificate.

## Fault management

- Alarm Console: every X.733 field of the alarm (event type, repair actions, trend, backed-up status, threshold, correlation), optional columns, filter expressions and saved filters, and alarms hidden by rules on request.
- Acknowledge, unacknowledge and clear, one alarm or many, with user, system and time; sent to ietf-alarms NFs when their new "Alarm operations on the NF" write class is on, otherwise kept in MiniSMO and marked local.
- Comments on alarms, including those the NF sends.
- Alarm history and exports with who acknowledged and cleared; JSON export.
- Summary per NF and object, rolled up the topology and the object path.
- Saved filters, including Warnings and indeterminate, and Security alarms (X.736).
- Alarm rules: forward to e-mail, webhook, a VES collector or a syslog server; count / rate alarms; automatic acknowledgement; root-cause correlation with optional hiding; hiding; weekday and time schedules; the rule explorer with a test against the history.
- Repeated identical alarms are collapsed with a count; flapping alarms show how often they were raised.
- Alarm dictionary from VES Event Registration files: repair hints on matching alarms.
- Alarm reports: most alarming NFs, most frequent alarms, alarms over time.
- Alarm storm protection per NF, with a summary alarm.
- VES: the 3GPP notifications for acknowledgement, comments, correlation and alarm-list rebuild; threshold-crossing alerts as alarms; stateChange, syslog and measurement events used; per-NF VES accounts and client certificates (mutual TLS); an optional strict event check.
- Alarm lists read again on a schedule if you set one, and after the NF says its list was rebuilt.
- Clock skew warning when an NF's clock is off.
- O-RU supervision: supervision-notifications tracked; the supervision watchdog sent only when its new write class is on. SSH keepalive on NETCONF sessions.
- Notification types for MiniSMO's VES subscription on the NF.
- Syslog receiver (TLS on 6514, optional plain TCP/UDP) with search.

## Onboarding

- Discovery of NFs by address range on the NETCONF ports, without sending credentials.
- Bulk onboarding from CSV, checked first.
- NFs that announced themselves with a VES pnfRegistration.
- Onboarding history.


## Configuration management

- New Configuration Management page (web) and tab (desktop Configuration Operations).
- The NF's YANG models: discovery of modules, features and deviations; a model browser; comparing two revisions of a module.
- RPCs and YANG actions from the NF's models, with a form from their inputs, behind the NF's new "RPCs and actions" write class.
- Objects in the 3GPP NRM style with links between related objects, configured and operational values side by side, and uniform state columns with lock / unlock / shutting down.
- Cell states of all NFs, with MiniSMO alarms when a cell or carrier is disabled or degraded, cleared again when it recovers.
- Neighbour relations per cell, with the target cell, the handover and removal flags, consistency findings and the handover success rate; add, remove and change them with a preview.
- Changes are checked against the NF's YANG before sending, including must / when expressions and references to other entries.
- Confirmed commits survive a dropped session (persist id).
- On NFs without a candidate datastore, MiniSMO writes the previous values back when the read-back does not match.
- Optional settings for configuration changes: copy to startup after a change, and NMDA edit-data.
- When the NF supports it, a change locks only the entries it touches (partial lock).
- Change history with changes made outside MiniSMO, the configuration at a point in time, comparing two times, and restoring a snapshot.
- Search across the stored configuration of all NFs.
- Export and import of configurations as XML, JSON, instance data, CSV and XLSX.
- Change sets for many NFs:
  - dependency order and parallel NFs
  - stop or continue on failure
  - scheduled runs in a maintenance window
  - rollback of the whole set
  - creation from a CSV / XLSX sheet
  - an optional before / after check of KPIs and alarms
- Templates with object content and variables; assignment to NFs, NF types or NF groups, with alignment.
- Golden baseline per NF with drift, accept and correction.
- Policy rules, built-in consistency checks (duplicate NCI, PCI collision and confusion, PLMN / TAC, missing reverse neighbour) and audit runs with exceptions, a CSV report and e-mail.
- Encrypted configuration archives per NF group, on demand or scheduled, and restore from them.
- MiniSMO's syslog receiver can be set as an NF's syslog destination through the NF's standard model.
- Optional RESTCONF or 3GPP ProvMnS endpoint per NF to carry configuration changes.

## Performance management

- New Performance Mgmt page in the web UI, and KPIs, thresholds, KPI report, catalogue & quality and PM settings on the desktop Performance page.
- KPIs over TS 28.552 measurements, each labelled with its TS 28.554 clause: a starter set following TS 28.554 V18.8.0 (partial and total DRB accessibility per 5QI, accessibility per slice, throughput, handover success per neighbour relation, DRB retainability, energy efficiency, cell availability) with editable formulas; values per cell, NF or NF group, per period, hour or day, combined above the cell as each clause defines.
- Thresholds on KPIs or measurements that raise and clear quality-of-service alarms with hysteresis, with e-mail on raise and clear; ThresholdMonitor entries on NFs whose models have them, previewed and confirmed.
- Boards of panels, saved per user and shared with roles; KPI reports as CSV or Excel.
- PM job templates applied to many NFs, each checked against what the NF supports.
- Per NF: what it can measure, what it reports, data quality (duplicates, late data, missing periods with a 'PM intervals missing' alarm), live values of the last hours, and interface statistics read when nothing else delivers them.
- More O-RU measurement groups read (tx, energy / power / environmental, symbol RSSI); measurement file names in the TS 32.432 form are shown with their period.
- New settings: late data window, missing periods alarm, interface statistics interval.

## Software, files and NF security

- New web pages Software & Inventory, Files & Trace and NF Security, and a Software & Security tab on the desktop Operations page.
- Software slots per NF, a package repository with signature checking, and upgrades of one NF or many (download, install, integrity check, activate, restart, verify, roll back on failure), now or at a set time.
- NF restart, after confirmation.
- Hardware inventory with change history and CSV export; assets across NFs with build drift; synchronization state with a 'Synchronization lost' alarm.
- Files from NFs: list and fetch files, troubleshooting and trace logs, trace and MDT jobs. Trace files are encrypted, kept 7 days by default and readable only with the new Trace analyst role.
- Certificates: a lab CA, imported CAs, CSR signing, trust per NF, NF truststore and certificate mapping, CRL and OCSP revocation checks, and expiry alarms.
- NF accounts, NETCONF access control and RADIUS on the NF; password policies per NF type; rotation of MiniSMO's own NF password and SSH key with verification and rollback.
- New write classes per NF: Software management, Reset and restart, Troubleshooting and trace logs.
- New settings: synchronization read interval, file and trace retention, fetching generated logs, refusing NFs with a revoked TLS certificate.

## RAN functions, platform operations and northbound

- New web page RAN Functions: SON functions with their switches and parameters, slices and RRM policy ratios, shared cells, energy saving (including O-RU array carriers to sleep) and antenna line devices (DC power, messages to a device, over-current alarm).
- Topology graph with F1 and Xn links found in the NFs' configuration and the worst alarm on each node.
- De-onboarding an NF in one action: MiniSMO's certificates and SSH keys removed from the NF, an encrypted archive of its data, subscriptions removed, history entry.
- Pre-check before risky operations (reachable, no critical alarm, sync locked, disk space); optionally stops upgrades and restarts when it fails.
- New web page Platform and a RAN & Platform tab on the desktop Operations page: encrypted MiniSMO backups on demand or scheduled with staged restore, settings snapshots and compare, one job list for all schedules and MiniSMO's own jobs, NF traces that stop by themselves, log search, support bundle, a health check report, and DHCP values for zero-touch (including SZTP redirect options).
- Overload alarm and a request limit per account on the Team mode API.
- NF groups filter the NF and alarm lists and choose the NFs of bulk backups and health checks.
- Northbound: 3GPP Fault Supervision MnS (alarms, acknowledge, clear, comments, notifications), read-only Provisioning MnS with CM change notifications, TS 32.435 PM files with file-ready notifications and SFTP sending, inventory export with hardware (JSON, CSV), VES re-emit of PM, trouble tickets (TMF621) from alarm rules, and Team mode personal tokens on the northbound API.
- Team mode accounts and groups can be provisioned from your identity system (Entra ID, Okta and others) with SCIM 2.0: accounts are created, disabled and removed automatically; group roles stay set in MiniSMO.

## Working with MiniSMO

- Alarm details open in a window of their own at the top of the screen and stay open until you close them, also while the list refreshes and after the alarm clears; the same for alarms in the alarm history.
- Forms stay open with everything you entered when saving fails, and show the reason at the top.
- Alarm filters, rule schedules, board panels, KPI formulas, threshold NFs and measurement lists can be chosen from lists; the text is written for you and stays editable.
- Alarm rules can forward to a trouble ticket system (TMF621) from the rule form.
- Every chart can be shown as a table; pointing at a chart shows the value and time.
- Web: the Configuration Mgmt tabs are now under Configuration and the Performance Mgmt tabs under Performance.
- An NF that stops answering (for example after a restart) is reconnected automatically.
- The communication matrix lists every connection MiniSMO can make or accept, including LDAP, OpenID Connect, e-mail, SCIM, syslog, ticket systems, VES re-emit, MnS notifications and certificate revocation checks.
- The desktop app shows the same pages as the Team mode website, in Local mode without sign-in and in Team mode with your account; turning on Team mode also opens the website in your browser. What only the app on the PC does (software updates, the backend's port) is under the mode chip > This PC.
- Alarm history details show the same information as an active alarm's.
- A board's active alarms panel lists the alarms and opens them, or the Alarm Console with those alarms.
- Measurements can be typed or chosen from every measurement the NFs report.
- The web menu scrolls on its own and a page opens at its top; Health & Capacity, Storage and Reports are under Platform; Software & Inventory is now Software & Hardware.
- The Measurements choices stay when another NF is selected; the chart's hover shows the value without a repeated name.
- The app's earlier desktop pages are retired. On a PC without WebView2 the window says what is missing and links to Microsoft's free download.
- MiniSMO has an icon in the notification area: open the window or the website, see the backend's state, keep running when the window is closed, start with Windows, quit. Starting MiniSMO again opens the one already running.
- The installer sets MiniSMO up as a Windows service by default, also when upgrading: on its next start the app moves the data to the service's folder (the previous folder is kept as a backup). Untick the option to keep the previous behaviour.
- The installer can turn on Team mode (off by default: Local mode); it adds the firewall rule for the web UI and the app then asks for the first admin account.
- Fewer, clearer menus: Analytics is now an Analysis tab under Alarms and under Performance; API & Webhooks and the 3GPP northbound services are one Northbound page; Onboarding is under Network Functions; Software Campaigns and Inventory are tabs of Software & Hardware (the Topology page keeps the topology and graph); NF trace files are next to the NETCONF Log; audit forwarding is a tab of the Audit Log; Configuration Operations is split over the Configuration tabs; all schedules are under Platform > Jobs; Platform has one Health tab, one Storage & Retention tab and Health Reports. Old links still open the right place. NF certificates and the web page certificate, and the VES collector and each NF's VES tab, link to each other.
- Every page has a **? HELP** button (and F1) that opens the help for that page and tab beside it; nothing on the page is lost. A new Page Reference in the help explains every page, tab, button and field.
- Settings > General has Reset MiniSMO again (in the app on the MiniSMO PC, Local mode), with a restart button.
- The Dashboard shows what needs attention (NFs not connected, critical and major alarms, locked cells).
- Alarm Console: one EXPORT… button for CSV or JSON. Both export the active alarms the console shows (with its severity, origin, search, filter and hidden setting). Before, CSV exported every stored alarm.
- Configuration, Objects & States, Neighbours, Endpoints, NF Software, NF Accounts and the RAN tabs show the values MiniSMO last read from an NF while the NF cannot be read, with when they were read and a stale warning after 24 hours. UPDATE reads the NF again at any time.
- New page NETWORK > O-Cloud: MiniSMO reads the inventory (resource pools, servers and other resources, resource types, sites, deployment managers) and the alarm list of your O-Clouds over O-RAN O2ims, every 5 minutes, over HTTPS with mutual TLS and an OAuth 2.0 token (pasted, or fetched by MiniSMO with a client id and secret). The O-Cloud's active alarms also appear in the Alarm Console (category O-Cloud). Acknowledging and clearing them on the O-Cloud is possible when you allow it for that O-Cloud, each time after a confirmation; otherwise nothing on the O-Cloud is changed. With notifications turned on for an O-Cloud, its alarms and inventory changes reach MiniSMO as they happen (O2ims subscriptions, received on the northbound API listener over HTTPS).
- Management data analytics (3GPP TS 28.104): an OSS can ask MiniSMO, over the 3GPP Provisioning service, for a physical resource utilisation analysis of chosen NFs or sub-networks, once or periodically, and gets the NFs with high and low PRB usage as a report sent to its address.
- Intents (3GPP TS 28.312): an OSS can give MiniSMO radio network goals over the 3GPP Provisioning service (throughput, PRB load, energy) for a sub-network, and MiniSMO reports every observation period whether each goal is met, with the measured values, and notifies subscribers when the report changes. MiniSMO checks and reports; it does not change the network to meet a goal.
- Team mode: if an imported password blocklist can no longer be read, MiniSMO now refuses new passwords until the list is imported again or removed, and raises an alarm, instead of skipping the check.
- A1 enrichment information: a Near-RT RIC can now ask MiniSMO, over A1-EI on the northbound listener, for alarms, configuration changes, measurements or topology, and MiniSMO sends them to the RIC as they come in.
- R1 access tokens: an onboarded rApp gets a client secret and can ask MiniSMO for short-lived OAuth 2.0 access tokens for the APIs it is allowed (3GPP CAPIF security), instead of using an API token for every call. These tokens give read rights only.
- R1 data in the form of the O-RAN R1 application protocol: rApps can discover MiniSMO's data types and subscribe to alarms, configuration changes, measurements and topology with R1AP data jobs, delivered to them by HTTP push.
- R1 for rApps: rApps can now read and change configuration, read, acknowledge, clear and comment alarms, list and download the NFs' YANG models, and create, change and delete A1 policies on the Near-RT RICs (with status notifications) through MiniSMO. Changes need a CHANGE token and the NF's write class, as for the OSS. rApps can address the 3GPP Provisioning service with version v1, as O-RAN R1 specifies.
- A1 policies: RAN Functions > A1 Policies manages policies on a Near-RT RIC over A1 (O-RAN A1AP): read the policy types the RIC offers, create, change and delete policies after confirmation (new write class A1 policies), and see whether the RIC enforces them.
- Trace and MDT activation from an OSS: creating a TraceJob on an NF over the 3GPP Provisioning service starts trace or MDT on that NF, deleting it stops it (NFs whose models include TraceJob; same CHANGE token and write class rules as other Provisioning changes). New objects can now be created with list values.
- 3GPP streaming of measurements: a performance job can stream its values to the OSS (over a secure WebSocket, in the standard GPB format) instead of writing files.
- 3GPP performance jobs (PerfMetricJob): an OSS can ask MiniSMO, over the 3GPP Provisioning service, to produce measurement files of chosen measurements and objects every reporting period, and to be told when each file is ready.
- TM Forum TMF628 Performance Management (v5): OSS/BSS systems can define indicators, run measurement collection jobs on the values MiniSMO already collects, and read the measurements or receive them as events.
- TM Forum TMF639 Resource Inventory (v4), read only: the NFs and their hardware parts for OSS/BSS inventory systems, with an event when an NF connects or disconnects.
- TM Forum TMF642 Alarm Management (v4) on the northbound listener: OSS/BSS systems that use TM Forum APIs can list, acknowledge, clear and comment alarms, also many at a time by pattern, and subscribe to alarm events.
- 3GPP Provisioning changes: an OSS can change attributes, create and delete objects on an NF over the 3GPP Provisioning service. It needs a new token scope, CHANGE, and the NF's Configuration changes write class turned on; such changes are applied without a confirmation in MiniSMO, after the same checks as your own, and each is in the Audit Log.
- 3GPP heartbeat notifications: an OSS subscribed over the 3GPP northbound services can ask MiniSMO for a heartbeat at an interval of its choice, or for one at once, so it knows MiniSMO is still there. Northbound > 3GPP Northbound shows the interval per subscription.
- 3GPP notifications name this MiniSMO (its distinguished name) as the sending system.
- Demo Network: one step (CREATE DEMO NETWORK) now creates the whole network, and RESET and DELETE act on all of it: 8 simulated NFs (4 O-RUs instead of 10), a demo O-Cloud with inventory, alarms that come and clear by themselves, Kubernetes and ETSI NFV deployment managers and a Near-RT RIC's A1 service, and 4 NFs that MiniSMO runs on this PC as NETCONF devices (an O-CU and an O-DU hosted on the demo O-Cloud with their A1 address and an A1 policy set, an O-RU with shared cell and antenna line devices, an NTS-style O-DU with VES). Their monitoring starts by itself, they show origin SIMULATED, and nothing counts against the licence. The page lists every element in one table. CELL OUTAGE now uses DEMO-O-RU-004.
- Northbound conformance to the published standards:
  - 3GPP Fault Supervision MnS: acknowledging, unacknowledging and clearing answer 204 No Content (one alarm or several; with several, nothing changes if one alarm does not exist); a comment answers 201 with the comment and its location; an unknown alarm id answers 404; a subscription's filter is a Jex text (only an empty one is taken, as MiniSMO does not evaluate Jex).
  - 3GPP Provisioning MnS: an NtfSubscriptionControl keeps the id given in the request, so it can be read, changed and deleted with that id. An NF whose MO ID has no `=` is addressed as `ManagedElement=<MO ID>` (also in alarms and files); the DN the NF reports for itself reads the same object; the NF's own ManagedElement no longer appears again below it.
  - 3GPP file data reporting MnS at its standard address (`/3GPPManagement/fileDataReportingMnS/v1800`): list files and subscribe to notifyFileReady.
  - Error answers in the form each standard defines: 3GPP ErrorResponse, TMF630 Error, and RFC 7807 problem details on R1, CAPIF and A1 (also for 401, 403 and unknown paths); requests that are not valid answer 400.
  - CAPIF onboarding: `apiList` is an object with `serviceAPIDescriptions`.
  - R1AP data jobs also take the DME type id as an object (namespace, name, version).
  - TMF642: the alarm list includes cleared alarms unless you filter by `state`; an unknown alarm id answers 404. TMF639: `place` is one place reference. TMF hub answers no longer carry an empty `query`.
- Intents: an expectation can be narrowed with object contexts (RAT, tracking area, PLMN, downlink or uplink frequency), matched against each NF's stored configuration.
- VES collector: new installations require basic authentication by default; updated installations keep their setting. The security recommendations say when authentication is off, or when it is on but no password or NF account is set. The Demo Network's NTS-like O-DU gets its own VES account.
- VES accounts: a new installation creates a random collector user name and password on first start. Pointing an NF's VES at MiniSMO also sets the NF's own random account on NFs with the NTS VES endpoint settings, so they authenticate at once; for other NFs, NEW RANDOM VES ACCOUNT… on the NF's VES tab creates one and shows the password once. Deleting an NF removes its account. MiniSMO also reads the modules a device lists as import-only in its YANG library.
- O-Cloud page: opens with a table of all O-Clouds (status, last read, resource pools, resources, active alarms, changes allowed); select one for its details. The details page is titled with the O-Cloud's name.
- O-Cloud alarms: acknowledging or clearing also works for an alarm MiniSMO knows only from a notification; the O-Cloud's answer (for example 409 or 412 with its reason) is shown, and the alarm changes in MiniSMO only when the O-Cloud accepted it; every attempt is in the Audit Log with the address, the body and the O-Cloud's answer. Acknowledging or clearing an O-Cloud alarm in the Alarm Console now asks the O-Cloud too.
- Provisioning MnS: an object below an NF whose DN ends in its ManagedElement can now be changed at the same DN it is read at; PATCH accepts exactly the four patch media types TS 28.532 lists (others get 415) and answers 200 with the changed attributes; a refused JSON Patch operation is named in `badOp`; a change made here is notified once with its new and old values.
- Measurement file retrieval is on by default for every NF without its own setting (Settings > General, New NFs changes the default; an NF where it was turned off stays off; a file server's identity is still confirmed first).
- Pointing an NF's VES at MiniSMO by hand now re-points it after the NF restarts by default (the first time; as integration does).
- Provisioning MnS: a TraceJob can be created in the TS 28.623 form (traceDepth inside traceConfig, plmnTarget as an object) on NFs with the 3GPP trace YANG; an NF's object lists the measurement periods, reporting methods and measurement names MiniSMO offers for PerfMetricJobs (supportedPerfMetricGroups).
- The northbound API is on by default on new installations, on the MiniSMO PC's address, port 8770, HTTPS; every call still needs a token. API & Webhooks shows the address other systems use, and file URLs and O-Cloud notification addresses use the PC's address instead of 127.0.0.1. Turn it off, or keep it to this PC (127.0.0.1), in Settings > General, Operations.
- New and deleted configuration entries for any external NF (Configuration page, **NEW ENTRY…** and **DELETE ENTRY…**): add or remove an entry of any list in the NF's models (for example an O-RU's processing elements, endpoints, array carriers and links), with the fields, defaults and choices taken from the NF's own YANG.
- Measurement control for any external NF (Performance page, Measurements): turn the NF's measurements on and off as its models offer them, with 3GPP measurement jobs or O-RAN measurement objects. Only the objects the NF lists in its measurement capabilities are offered. The measurement interval and the notification interval are set in the same change (60, 300, 900 or 3600 seconds, or another value).
- POINT UPLOADS AT MINISMO…: choose the file upload interval. If the O-RU has no measurement interval yet, it is set in the same change instead of the upload being refused.
- When an NF refuses a configuration change, its reason (error tag, path and message) is shown.
- Long lists and logs show 100 rows at first, with SHOW MORE. Recent webhook deliveries, the Audit Log and Syslog read older entries a page at a time, so a large log no longer slows the app down.
- A webhook or MnS subscription that is off says why on its row, for example after how many failed deliveries and with the last error.
- Changing an existing 3GPP subscription (PUT of an NtfSubscriptionControl) now updates it in place. Its heartbeat setting stays, and notifications already waiting for it are delivered instead of dropped.
- NEW ENTRY… and Parameters offer values from the NF instead of free text. References to other objects (also those that depend on another field), enumerations, identities and true / false are lists. Ranges, lengths and patterns are shown and checked before the preview.
- NEW ENTRY… follows the NF's model more closely: a part that exists only when it is given has an Include switch, alternatives in the model are chosen one at a time with the default one first, flags are check boxes, values that point at another node of the NF are a list of its nodes, values with a default are filled in, and the model's conditions on values are checked with its own message before the preview. The preview also checks unique values, the number of entries a list allows and that only one alternative is given.
- Check on the NF: when the NF can validate a change without applying it, the change preview (NEW ENTRY…, Parameters) can ask the NF to check it and shows its answer, including its error information. Nothing is applied.
- Changes to the NF's model announced by the NF (deviations) and refinements in the models are applied to the forms.
- The NF's models include every module its YANG library lists, also those it lists only for their definitions (import-only modules), each at the listed revision. Forms now show the values such modules define (for example an O-RU endpoint's compression settings). A module the models need but cannot be read is named, with its revision, on the Models tab and in NEW ENTRY….
- NEW ENTRY… shows only what exists on the NF for that entry. Parts that need a feature the NF does not announce are left out. Parts whose condition is false (for example the flow that transport-session-type does not choose, or the LTE TDD frame of an NR carrier) are hidden and not required. A deciding value outside the entry can be changed in the same form.
- O-Cloud notifications: a read and a change that both subscribe at once no longer leave extra subscriptions on the O-Cloud. An alarm change notified while MiniSMO reads the O-Cloud (for example an acknowledgement) is no longer lost.
- Configuration > History & Files: the five export buttons are now one EXPORT… that asks for the format, and says what each one gives.
- Audit Log: deleting a northbound subscription (3GPP or TM Forum) is recorded with the API token that did it. Webhooks are recorded as created, changed or deleted.
- Recent deliveries: a delivery of a webhook or subscription deleted since is shown as "deleted (#id)" instead of a bare number.
- NEW ENTRY…: after a problem in the preview or a refused write, the form opens again with everything entered. Required values of a part that exists only when given (for example an endpoint's compression) are required only when that part is filled in. A problem the NF already has in another entry no longer blocks the change.
- Software packages: ADD PACKAGE… requires the SHA-256 the supplier published and refuses a file whose SHA-256 differs.
- Software packages: VERIFY says in full whether the stored file is intact (same SHA-256 as when it was added) and repeats the signature result.
- Automation: NEW RULE… opens again (it failed with "hooks.map is not a function"), and the rule's webhook list shows the configured webhooks.
- Dashboard: the integrated O-Clouds, with their resources, active alarms and connection state.
- NEW ENTRY…: numeric values (for example a carrier's frequency or gain) are accepted when written. Before, the write was refused before anything was sent, with no message. A write refused before anything is sent now says why.
- Intents (Northbound > 3GPP Northbound): see the intents an OSS created, with their targets, the measured values, fulfilment and feasibility (read-only).
- NF Accounts: CHANGE PASSWORD (CHG-PASSWORD) changes MiniSMO's own password on an O-RU with the o-ran-usermgmt chg-password RPC, tried before it is kept.
- Configuration checks: leafrefs with require-instance false are no longer reported as missing, and paths that start at the top of the NF's data are evaluated correctly.
- Adding an O-RU: when any change is allowed, the O-RU supervision watchdog is turned on too, so MiniSMO is the O-RU's supervising client; the Add external NF window shows it for O-RUs and lets you untick it.
- NF configuration changes: the check after a change no longer expects values the NF's model marks as not readable (nacm:default-deny-all, RFC 8341), such as an O-RU account's password, which the NF leaves out of its replies. Before, adding an account on such an O-RU was undone and reported as not written.
- RESTCONF northbound (RFC 8040): every managed NETCONF NF's data is reachable under /restconf/data/network-topology:network-topology/topology=topology-netconf/node=<NF name>/yang-ext:mount/ for reading (JSON), changing (merge), deleting and calling the NF's RPCs, with the same tokens and write permissions as the 3GPP services. The NF's own error answers are passed on.
- O-RU supervision: the supervision notification interval and guard timer overhead are set in Settings (60 s and 10 s by default). The O-RU supervision lost alarm is raised after interval + guard without a supervision-notification, also when an O-RU never sends one.
- INTEGRATION > API & Webhooks lists the rApps onboarded over O-RAN R1, and an administrator can offboard one (for example an old registration).
- Approving a Call Home caller: the window explains that MiniSMO still logs in to the NF, marks the login details that are needed for the chosen authentication, and checks them before approving.
- NETCONF Call Home is on by default on new installations (SSH 4334, TLS 4335); it still accepts only NFs you added in Call Home mode.
- NF configuration changes: nodes that need a YANG feature the NF does not announce, and nodes whose when condition is false for the values being written, are no longer reported as missing mandatory values. Values that need a feature the NF does not announce are refused.
- NF Accounts: the account type list shows only the types the NF's model offers (SSHPUBLICKEY only when the NF announces that feature; it then asks for the public key), and on NFs with NETCONF access control a new account must be put in at least one NACM group (an existing one or a new one).
- Northbound PerfMetricJobs: a GET now returns only the job's id and attributes; a failed streaming connection shows as operationalState DISABLED. The connection, the last error and the files of each job are listed on INTEGRATION > API & Webhooks.
- Streaming: streamTarget can be the OSS's streaming service address (…/StreamingDataReportingMnS/<version>); MiniSMO adds /connections. An address ending in /connections still works as before.
- Streaming: each stream in the connection request now follows the TS 28.532 data type: streamId is a string, and the object's DN, the measurement names and the job's jobId are in additionalInfo.
- R1 data jobs: failed deliveries are logged as warnings with the address and the reason.
- O-Cloud provisioning: NEW REQUEST… also works on O-Clouds that offer no template list: type the template name and version, and give template parameters as name = value lines. After a request is created, a window shows the O-Cloud's answer and follows the request's phase until it is fulfilled or failed; a refusal (for example 422 for an unknown template) is shown as the O-Cloud gave it.
- Notifications: a notification still waiting to be sent when its subscription was deleted is no longer sent to a subscription created right after (for example an intent report arriving at an MDA report address).
- Provisioning MnS: TraceJob MDT settings (measurements, reporting trigger, report interval, logging duration) are translated between the TS 28.623 form and the TS 32.422 numeric encodings an NF's 3GPP trace YANG uses, in both directions, so a TraceJob reads back as it was created. Values that cannot be translated are refused with the attribute named.
- An NF integrated while the VES collector is off is now pointed at the collector as soon as the collector is turned on; the integration says so instead of skipping the step silently.
- Fixed: the minute job stopped reading O-Clouds on schedule (and failed every minute) after an O-Cloud was added or read.
- O-Cloud alarms that arrive by notification while MiniSMO is reading the O-Cloud are no longer dropped by that read.
- The Add external NF and Add an O-Cloud windows mark required fields with a red * and have a *Changes MiniSMO may send* section with **All changes**, so permissions are set when the NF or O-Cloud is added (for NFs pre-ticked from Settings > General, New NFs).
- Settings > General > New NFs: choose the write permissions (and measurement file retrieval) every external NF gets when it is integrated, and apply them to the NFs already integrated. All off unless you choose them.
- Desktop app: SHOW IN FOLDER for an NF's measurement files (FILE-BASED PM on its Monitoring tab), OPEN EXPORTS FOLDER under Platform > Storage, and Show in folder after a diagnostics export are back.
- Network function details: a *Network function* list at the top switches to another NF on the same tab, without going back to the list.
- Intent Provisioning: an intent's `intentReportReference` is now a single DN, as TS 28.312 defines it (it was a list with one entry).
- Certificates: a CA can be imported as trusted for notification receivers, so webhooks and subscribers with their own CA get notifications while their certificate is still checked.
- VES collector: assigned sources are listed with REMOVE…, so an assignment can be undone.
- API tokens: a revoked token disappears from the list.
- O-Cloud: an O-Cloud is read as soon as it is added or changed, and a deployment manager as soon as it is connected, without READ NOW.
- O-Cloud changes: with the new switch Allow changes on this O-Cloud (off by default), MiniSMO can deploy, scale and remove NF Deployments on a deployment manager (Kubernetes: from a JSON manifest, by replicas, by deleting the workload; ETSI NFV: create and instantiate from a VNF package, scale steps, terminate and delete), and create, change and delete the O-Cloud's provisioning requests from its templates on the new Provisioning tab; moving a request to another template version changes the O-Cloud's software. Every change asks first and is in the Audit Log. The Deployment Managers tab also shows which servers each NF Deployment runs on.
- O-Cloud and NF correlation: an NF can carry the NF Deployment it runs as on an O-Cloud (its 3GPP vnfInstanceId). The NF page then shows Runs on (O-Cloud, deployment manager, NF Deployment and servers), and an O-Cloud alarm lists the NFs it impacts, on the O-Cloud page and in the alarm text.
- Alarm Console: O-Cloud alarms now show the O-Cloud as their NF (origin O-CLOUD) instead of MiniSMO, and the origin filter has O-Cloud; alarm details show where the NF runs.
- O-Cloud: MiniSMO can now read the NF Deployments of a deployment manager, shown on the Deployment Managers tab: on a Kubernetes cluster the workloads and Pods (O-RAN O2dms Kubernetes profile), on an ETSI NFV deployment manager the NF Deployments and their alarms (O2dms ETSI NFV profile). Read only.
- R1 AI/ML models: rApps can register the AI/ML models they offer and discover each other's (O-RAN R1AP AI/ML model registration and discovery).
- Analytics (MDA): the physical resource utilisation analysis can now also predict each NF's PRB usage for the next period, from the trend of its recent measurements.
- Intents: an expectation can now say when its targets must be met (time windows), and every intent report says whether the intent is feasible (it covers no NF, its time windows are over, or it conflicts with another intent).
- The browser view now opens in the dark theme unless you picked another (Theme, in the side bar).
- R1 data access: data jobs can now also be one-time requests (the current alarms, topology or measurements), and deliver by HTTP pull (with a data availability notification) or to a Kafka topic, besides HTTP push.
- R1 data from rApps: an rApp can register the data it produces and offer it; other rApps find it in data discovery, subscribe with a data job and receive the data as the producer pushes it (O-RAN R1AP data registration and data offer).
- R1 service events: an rApp can subscribe to be told when service APIs are published, changed or removed (O-RAN R1AP service events subscription, based on the 3GPP CAPIF events API).
- New page TOOLS > Standard Interfaces: every standard interface that matters for an SMO (O1, Open Fronthaul M-Plane, 3GPP management services, R1, A1, O2, TMF Open APIs and others), whether MiniSMO has it, and a plain explanation of what it is for and how to use it here. Also in the Help Center and the User Guide.
- Each MiniSMO has an instance ID and a management node name (by default the computer's name; change it, and an optional DN prefix, under Settings > General, This MiniSMO). Downloaded and exported file names start with the node name, 3GPP measurement files name MiniSMO in their header, and forwarded VES events carry it as the reporting entity. SYSTEM > About shows all three.
- NETCONF Log: one EXPORT… button that asks for CSV or JSON; the export now follows the page's NF, operation, status and direction filters as well as the search.
- Chart tables show one column per measurement; a line broken by gaps or counter resets no longer appears as many separate series.
- Performance charts no longer freeze the app on long ranges with many samples (for example 7 days of raw values in the table view): the table shows 500 rows at a time with SHOW MORE, the graph draws peaks and dips without every single point, MiniSMO sends a chart at most about 4,000 points (lowest and highest kept) instead of every sample, so a long range no longer uses up memory, and long ranges refresh every 5 minutes. EXPORT CSV still gives every value.
- The Network Functions list has a REFRESH button, shows when it was last updated, and refreshes as soon as the window is shown again.
- Configuration > Search no longer lists rows of deleted NFs (shown with a number instead of a name).
- F1 in the app opens the help for the page beside it, as the HELP button does.
- Team mode address and port are in Settings > General (in the app on the MiniSMO PC).
