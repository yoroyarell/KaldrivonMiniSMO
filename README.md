# Kaldrivon MiniSMO

A small Windows SMO for lab and demo work: a simulated O-RAN demo network, plus FM / CM / PM monitoring of external NETCONF NFs (methods chosen from each NF's capabilities, with fallback), a VES 7.2.1 collector, file-based PM, and confirmed configuration changes on external NFs.

Two editions share one installer: **Community** (no license, up to 5 external NFs, every essential FM / CM / PM function and a small allowance of each Pro feature) and **Pro** (a signed license file: more NFs, automation, analytics, API & webhooks and the other Pro features without limits).

## Download

Get the installer from [Releases](../../releases). Check it against the `.sha256` file published alongside it:

```powershell
Get-FileHash .\Kaldrivon-MiniSMO-0.3.1-Setup.exe -Algorithm SHA256
```

## Documentation

- [User Guide (PDF)](docs/Kaldrivon-MiniSMO-User-Guide-0.3.1.pdf): installation, Demo Network, external NFs, FM / CM / PM, VES, Community and Pro, troubleshooting
- [Quick Start (PDF)](docs/Kaldrivon-MiniSMO-Quick-Start-0.3.1.pdf): install and first steps on four pages

The same guide is built into the app (**Help**, or F1 on any page).

## Requirements

- Windows 10 (19041) or Windows 11, x64
- The installer is self-contained (no .NET or Python needed)

## Notes

- The installer is not code-signed yet, so SmartScreen may warn on first run.
- The EULA shipped with 0.3.1 is a draft.
- 0.3.1 upgrades 0.3.0 and 0.2.0 in place; your data, NFs and license are kept (0.2.0 databases are backed up and migrated on first start).
- Open-source components and their licenses: [THIRD-PARTY-NOTICES.txt](THIRD-PARTY-NOTICES.txt) (also installed with the app).
- The installer adds one Windows Firewall rule for the VES collector ports (TCP 8443, 8080, Domain/Private). You can untick it during setup; it is removed on uninstall.

See [RELEASE-NOTES-0.3.1.md](RELEASE-NOTES-0.3.1.md) for what's in this version ([0.3.0](RELEASE-NOTES-0.3.0.md), [0.2.0](RELEASE-NOTES-0.2.0.md)).
