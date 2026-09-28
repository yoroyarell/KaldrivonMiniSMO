# Kaldrivon MiniSMO

A small Windows SMO for lab and demo work: a simulated O-RAN demo network, plus FM / CM / PM monitoring of external NETCONF NFs, a VES 7.2.1 collector, and confirmed configuration changes on external NFs.

## Download

Get the installer from [Releases](../../releases). Check it against the `.sha256` file published alongside it:

```powershell
Get-FileHash .\Kaldrivon-MiniSMO-0.2.0-Setup.exe -Algorithm SHA256
```

## Requirements

- Windows 10 (19041) or Windows 11, x64
- The installer is self-contained (no .NET or Python needed)

## Notes

- The installer is not code-signed yet, so SmartScreen may warn on first run.
- The EULA shipped with 0.2.0 is a draft.
- The installer adds one Windows Firewall rule for the VES collector ports (TCP 8443, 8080, Domain/Private). You can untick it during setup; it is removed on uninstall.

See [RELEASE-NOTES-0.2.0.md](RELEASE-NOTES-0.2.0.md) for what's in this version.
