# Kaldrivon MiniSMO

A small Windows SMO for lab and demo work: a simulated O-RAN demo network, plus FM / CM / PM monitoring of external NETCONF NFs (methods chosen from each NF's capabilities, with fallback; NETCONF Call Home), a VES 7.2.1 collector (HTTPS) with VES destinations set through standard models (3GPP `NtfSubscriptionControl`, O-RAN WG4 for O-RUs), file-based PM, confirmed configuration changes on external NFs, and an O-RAN R1 interface for rApps.

Licensing is by capacity only: every feature is included, and MiniSMO is free for up to 5 external NFs (**Community**). A signed license file raises the external NF limit (**Small Deployment**, up to 25, or **Unlimited**).

## Download

Get the installer from [Releases](../../releases). It is code-signed by Kaldrivon (Azure Artifact Signing). Check it against the `.sha256` file published alongside it:

```powershell
Get-FileHash .\Kaldrivon-MiniSMO-0.5.1-Setup.exe -Algorithm SHA256
Get-AuthenticodeSignature .\Kaldrivon-MiniSMO-0.5.1-Setup.exe
```

## Documentation

- [User Guide (PDF)](docs/Kaldrivon-MiniSMO-User-Guide-0.5.1.pdf): installation, Demo Network, external NFs, FM / CM / PM, VES, licensing, troubleshooting
- [Quick Start (PDF)](docs/Kaldrivon-MiniSMO-Quick-Start-0.5.1.pdf): install and first steps

The same guide is built into the app (**Help**, or F1 on any page).

## Requirements

- Windows 11, x64
- The installer is self-contained (no .NET or Python needed)

## Notes

- 0.5.0 was the first public release; 0.5.1 installs over it and keeps your data. Earlier builds were previews; there is no upgrade path from them.
- 0.5.x includes security hardening (local API key, SSH host key and file server confirmation, VES sender check, HTTPS-only VES). Some of it changes behaviour; see the release notes. MiniSMO is not certified against any security standard.
- Open-source components and their licenses: [THIRD-PARTY-NOTICES.txt](THIRD-PARTY-NOTICES.txt) (also installed with the app).
- The installer offers three Windows Firewall rules, each scoped to the MiniSMO backend and to Domain/Private networks, and each can be unticked during setup: VES collector (TCP 8443), PM file receiver (TCP 2222, SFTP) and NETCONF Call Home (TCP 4334; Call Home itself stays off until you turn it on in Settings). They are removed on uninstall.

See [RELEASE-NOTES-0.5.1.md](RELEASE-NOTES-0.5.1.md) for what's in this version ([0.5.0](RELEASE-NOTES-0.5.0.md)).
