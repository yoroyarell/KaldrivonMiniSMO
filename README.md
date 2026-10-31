# Kaldrivon MiniSMO

A small SMO for lab and demo work, for Windows and Linux: a simulated O-RAN demo network, plus FM / CM / PM monitoring of external NETCONF NFs (methods chosen from each NF's capabilities, with fallback; NETCONF Call Home), a VES 7.2.1 collector (HTTPS) with VES destinations set through standard models (3GPP `NtfSubscriptionControl`, O-RAN WG4 for O-RUs), file-based PM, confirmed configuration changes on external NFs, and an O-RAN R1 interface for rApps.

From 0.6.0, **Team mode** adds an HTTPS web UI with accounts and roles (viewer, operator, admin) for other people on your network, and MiniSMO can run as a **Windows service**, so monitoring continues when the window is closed or nobody is signed in. From 0.6.1, the web UI can use your own certificate, so browsers no longer warn, and MiniSMO is also available for **Linux** (`.deb` and `.rpm`), used in a browser.

Licensing is by capacity only: every feature is included, and MiniSMO is free for up to 5 external NFs (**Community**). A signed license file raises the external NF limit (**Small Deployment**, up to 25, or **Unlimited**).

## Download

Everything is on [Releases](../../releases).

### Windows

Get the installer from the release. It is code-signed by Kaldrivon (Azure Artifact Signing). Check it against the `.sha256` file published alongside it:

```powershell
Get-FileHash .\Kaldrivon-MiniSMO-0.6.1-Setup.exe -Algorithm SHA256
Get-AuthenticodeSignature .\Kaldrivon-MiniSMO-0.6.1-Setup.exe
```

From 0.5.2 on, MiniSMO can also check for, download and install new versions itself (from 0.6.0 in *SYSTEM > About*; it checks once a day by default, every download and install is confirmed, and the installer is accepted only with a valid Kaldrivon signature).

### Linux

`kaldrivon-minismo_<version>_amd64.deb` (Ubuntu, Linux Mint, Debian) and `kaldrivon-minismo-<version>-1.x86_64.rpm` (RHEL, Rocky, Alma, Fedora), x86_64, with `SHA256SUMS`. On Linux, MiniSMO runs as a service and is used in a browser (Team mode); there is no desktop app. The packages bring their own Python runtime and need nothing else.

```bash
sha256sum -c SHA256SUMS --ignore-missing
sudo apt install ./kaldrivon-minismo_0.6.1-1_amd64.deb        # or: sudo dnf install ./kaldrivon-minismo-0.6.1-1.x86_64.rpm
sudo kaldrivon-minismo create-admin <name>                     # the first admin account (asks for the password)
sudo kaldrivon-minismo status                                  # service state and the web address
```

Then open `https://<this machine>:9443/` and sign in. Settings are in `/etc/kaldrivon-minismo/minismo.env`, data in `/var/lib/kaldrivon-minismo`, logs in `journalctl -u kaldrivon-minismo`. Open the ports NFs and browsers need (*Settings > Communication Matrix* lists them), for example `sudo ufw allow 9443/tcp` and `sudo ufw allow 8443/tcp`. Upgrading installs over the old version and keeps the data; `sudo apt purge kaldrivon-minismo` removes everything.

## Documentation

- [User Guide (PDF)](docs/Kaldrivon-MiniSMO-User-Guide-0.6.1.pdf): installation, Demo Network, external NFs, FM / CM / PM, VES, licensing, troubleshooting
- [Quick Start (PDF)](docs/Kaldrivon-MiniSMO-Quick-Start-0.6.1.pdf): install and first steps

The same guide is built into the app (**Help**, or F1 on any page).

## Requirements

- Windows 11, x64; the installer is self-contained (no .NET or Python needed)
- Linux x86_64 with systemd and glibc 2.28 or newer (for example Ubuntu 20.04+, Linux Mint 20+, Debian 10+, RHEL / Rocky / Alma 8+); a current browser to use it

## Notes

- 0.5.0 was the first public release; later versions install over it and keep your data. Earlier builds were previews; there is no upgrade path from them.
- 0.5.x includes security hardening (local API key, SSH host key and file server confirmation, VES sender check, HTTPS-only VES). Some of it changes behaviour; see the release notes. MiniSMO is not certified against any security standard.
- Open-source components and their licenses: [THIRD-PARTY-NOTICES.txt](THIRD-PARTY-NOTICES.txt) (also installed with the app).
- The installer offers three Windows Firewall rules, each scoped to the MiniSMO backend and to Domain/Private networks, and each can be unticked during setup: VES collector (TCP 8443), PM file receiver (TCP 2222, SFTP) and NETCONF Call Home (TCP 4334; Call Home itself stays off until you turn it on in Settings). They are removed on uninstall. Team mode's web port (TCP 9443 by default) is not opened by the installer; *SYSTEM > Settings > Access* shows the firewall command when you turn Team mode on.

See [RELEASE-NOTES-0.6.1.md](RELEASE-NOTES-0.6.1.md) for what's in this version ([0.6.0](RELEASE-NOTES-0.6.0.md), [0.5.2](RELEASE-NOTES-0.5.2.md), [0.5.1](RELEASE-NOTES-0.5.1.md), [0.5.0](RELEASE-NOTES-0.5.0.md)).
