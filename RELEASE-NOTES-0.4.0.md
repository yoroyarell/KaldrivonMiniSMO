# Kaldrivon MiniSMO 0.4.0 — Release Notes

Windows 11 x64. Installer: `Kaldrivon-MiniSMO-0.4.0-Setup.exe` (checksum in `.sha256`). Upgrades 0.3.x in place; your data, NFs and license are kept. Database schema 16 → 19, migrated automatically at the first start (a database backup is taken first).

The installer is **not code-signed** yet, so Windows SmartScreen may warn on first run. The EULA is still a **draft for legal review**.

## New

- **VES destination set by standard models only.** MiniSMO no longer uses product-specific models (O-RAN-SC NTS `simulation/ves-endpoint`, SiteWatch `/probe/ves-endpoint`).
  - 3GPP NFs: one `NtfSubscriptionControl` entry (TS 28.623) named `MiniSMO` with the collector URL and a heartbeat period (new setting `ves_heartbeat_period_s`, default 60 s).
  - O-RUs: the O-RAN WG4 way, a configured subscription (RFC 8639) with an `o-ran-ves-subscribed-notifications` receiver at the collector's server root, plus the `o-ran-supervision` heartbeat recipient when the O-RU supports non-persistent M-Plane. The collector also accepts events at its root path.
  - Deleting an NF removes MiniSMO's own entry. Entries of other managers are never touched. NFs with neither model are explained; set their destination with the vendor's tools.
- **O-RAN R1 interface (Pro)** on the northbound listener, with its API tokens:
  - SME: the CAPIF core functions of 3GPP TS 29.222 (rApp onboarding, API provider registration, service API publish and discovery). MiniSMO's own data API and REST API are discoverable right after onboarding.
  - DME: the O-RAN-SC ICS data consumer API. Information types `fm-alarm-events`, `cm-change-events`, `pm-measurements` and `nf-topology` (MiniSMO-defined, with JSON Schemas), information jobs delivered by HTTP POST to the job's result URI, and type subscriptions.
  - *TOOLS > API & Webhooks > API access* shows the onboarded rApps, data jobs and published APIs.
- **NETCONF Call Home (RFC 8071).** An external NF can dial in; MiniSMO stays the SSH / NETCONF client. Off by default (*SYSTEM > Settings > NETCONF Call Home*). Only registered NFs are accepted (SSH host key fingerprint, optional serial number / hardware ID and source address); unknown callers are closed before any credential is sent and listed with **ADD AS NF…**. *Add External NF* has a Connection choice. Installer option **Allow NFs to call MiniSMO** adds a Windows Firewall rule for inbound TCP 4334 (Domain and Private networks, removed on uninstall).
- **PM models discovered from the NF's own YANG**: every container with counter types or a measurement name is offered as a PM source, with units from the YANG. The NTS cell PM preset is gone; cell PM is found this way.
- **Reset de-integrates the NFs first** (on by default): MiniSMO removes its own VES subscription from every external NF it can reach, stops monitoring and closes the sessions, with a progress window; NFs that could not be reached are listed.
- **Dashboard**: when the system state is DEGRADED or CRITICAL, *What needs attention* lists the checks behind it (for example memory).
- *Health & Capacity > MiniSMO now* refreshes every 5 seconds.

## Fixes

- PM showed configuration and state values (for example 3GPP NRM `attributes`, O-RAN delay profiles, sync status) as measurements. Only measurement containers qualify now, and only the leaves declared in them are read.
- VES perf3gpp PM showed "no samples yet" / "events 0" in the NF's PM status although the values were stored and plotted.
- **PM values dated in the future are refused** (more than 5 minutes, or one period, ahead of this PC's clock). They sat on the chart days ahead and made later correct values look out of order. The VES log and the NF's PM source say how far ahead the NF's timestamps are; values of this kind already stored are removed by the upgrade.
- The O-RU PM source showed "expected every 60 s" from a fixed MiniSMO value. The expected interval now comes only from the NF: for O-RUs the shortest `*-measurement-interval` in their `o-ran-performance-management` configuration (results are read once per that interval); without one there is no expectation and results are read every 300 s. A YANG-Push period counts as expected only once the NF accepted the subscription.
- CPU on *Health & Capacity* and in the system health check always read 0 %. CPU is now sampled every 2 seconds; process CPU is a share of the whole machine.
- Help: links between topics did nothing; they now open the topic.
- *Edit NF* sends only changed values, so saving a description no longer forgets a pinned host key.

## Known limitations

See the User Guide, *About MiniSMO > Known limitations*. New in this release: R1 does not provide CAPIF security (per-API OAuth) or CAPIF events, access is by API token; the DME information types are MiniSMO's own (no standardised R1 data type ids are used); MiniSMO is not a Non-RT RIC and runs no rApps.
