# Kaldrivon MiniSMO

A small SMO for lab and demo work, for Windows and Linux: a built-in O-RAN Demo Network, plus FM / CM / PM monitoring of external NETCONF NFs (methods chosen from each NF's capabilities, with fallback; NETCONF Call Home), a VES 7.2.1 collector (HTTPS) with VES destinations set through standard models (3GPP `NtfSubscriptionControl`, O-RAN WG4 for O-RUs), file-based PM, confirmed configuration changes on external NFs, and an O-RAN R1 interface for rApps.

From 0.6.0, **Team mode** adds an HTTPS web UI with accounts and roles (viewer, operator, admin) for other people on your network, and MiniSMO can run as a **Windows service**, so monitoring continues when the window is closed or nobody is signed in. From 0.6.1, the web UI can use your own certificate, so browsers no longer warn, and MiniSMO is also available for **Linux** (`.deb` and `.rpm`), used in a browser.

0.7.0 adds stronger Team mode security (roles with separation of duties, second factor, LDAP and OpenID Connect sign-in, a forwardable audit log), much fuller fault, configuration and performance management of external NFs (alarm operations and rules, configuration forms built from each NF's own YANG models, measurement control), onboarding by discovery and CSV, software and file management, and more northbound interfaces. On Linux, MiniSMO can now install new versions from the web UI and checks Kaldrivon's signature first.

0.7.1 makes the Demo Network a set of demo devices that MiniSMO manages over the same standard interfaces as real equipment (NETCONF, including Call Home over SSH and TLS, VES, O2 with a demo O-Cloud, A1 and R1 with a demo Near-RT RIC and rApp), adds resource and VNF performance charts for O-Clouds, more RAN, A1, R1 and intent functions, and deleting the Demo Network now removes all of it.

Licensing is by capacity only: every feature is included, and MiniSMO is free for up to 5 external NFs (**Community**). A signed license file raises the external NF limit (**Small Deployment**, up to 25, or **Unlimited**).

## Download

Everything is on [Releases](../../releases).

### Windows

Get the installer from the release. It is code-signed by Kaldrivon (Azure Artifact Signing). Check it against the `.sha256` file published alongside it:

```powershell
Get-FileHash .\Kaldrivon-MiniSMO-0.7.1-Setup.exe -Algorithm SHA256
Get-AuthenticodeSignature .\Kaldrivon-MiniSMO-0.7.1-Setup.exe
```

From 0.5.2 on, MiniSMO can also check for, download and install new versions itself (from 0.6.0 in *SYSTEM > About*; it checks once a day by default, every download and install is confirmed, and the installer is accepted only with a valid Kaldrivon signature).

### Linux

`kaldrivon-minismo_<version>-1_amd64.deb` (Ubuntu, Linux Mint, Debian) and `kaldrivon-minismo-<version>-1.x86_64.rpm` (RHEL, Rocky, Alma, Fedora), x86_64, with `SHA256SUMS`, `SHA256SUMS.sig` (Kaldrivon's signature over it, from 0.7.0) and `kaldrivon-linux-signing.pub`. On Linux, MiniSMO runs as a service and is used in a browser (Team mode); there is no desktop app. The packages bring their own Python runtime and need nothing else.

**Check the download** (from 0.7.0): the signature over `SHA256SUMS` with Kaldrivon's Linux signing key (Ed25519; OpenSSL 3), then the packages against `SHA256SUMS`:

```bash
openssl pkeyutl -verify -pubin -inkey kaldrivon-linux-signing.pub -rawin -in SHA256SUMS -sigfile SHA256SUMS.sig
sha256sum -c SHA256SUMS --ignore-missing
```

The first command must print `Signature Verified Successfully`. Compare `kaldrivon-linux-signing.pub` with the key published here:

```
-----BEGIN PUBLIC KEY-----
MCowBQYDK2VwAyEAVdXd7mnA6dicPxp0GX3hCNlXdcSJ0WCez4kyCoySed0=
-----END PUBLIC KEY-----
```

**Ubuntu, Linux Mint, Debian (.deb)**

```bash
sha256sum -c SHA256SUMS --ignore-missing
sudo apt install ./kaldrivon-minismo_0.7.1-1_amd64.deb
sudo kaldrivon-minismo create-admin <name>      # the first admin account (asks for the password twice)
sudo kaldrivon-minismo status                   # service state and the web address
sudo ufw allow 9443/tcp && sudo ufw allow 8443/tcp   # if ufw is on: web UI and VES collector
```

Upgrade: `sudo apt install ./kaldrivon-minismo_<new version>-1_amd64.deb` (keeps the data). Remove: `sudo apt remove kaldrivon-minismo` keeps the data and the account; `sudo apt purge kaldrivon-minismo` removes everything.

**RHEL, Rocky, Alma, Fedora (.rpm)**

```bash
sha256sum -c SHA256SUMS --ignore-missing
sudo dnf install ./kaldrivon-minismo-0.7.1-1.x86_64.rpm
sudo kaldrivon-minismo create-admin <name>      # the first admin account (asks for the password twice)
sudo kaldrivon-minismo status                   # service state and the web address
sudo firewall-cmd --permanent --add-port=9443/tcp --add-port=8443/tcp && sudo firewall-cmd --reload
```

Upgrade: `sudo dnf install ./kaldrivon-minismo-<new version>-1.x86_64.rpm` (keeps the data). Remove: `sudo dnf remove kaldrivon-minismo` keeps the data and the account; to remove them too, then run `sudo rm -rf /var/lib/kaldrivon-minismo /var/log/kaldrivon-minismo /etc/kaldrivon-minismo && sudo userdel minismo`.

**Both**

Open `https://<this machine>:9443/` and sign in. The browser warns about MiniSMO's own certificate until you load yours in *Settings > Access*. Other ports NFs may need (PM file receiver 2222, NETCONF Call Home 4334 and, over TLS, 4335) are listed in *Settings > Communication Matrix*.

Updates (from 0.7.0): *About > Software updates* in the web UI; admins can **DOWNLOAD…** a newer version, which MiniSMO accepts only with a valid Kaldrivon signature, and **INSTALL…** it (the web UI is unavailable for about half a minute). A 0.6.1 installation has no update service: install 0.7.1 on it once with `apt` or `dnf` as above.

| | |
|---|---|
| Settings | `/etc/kaldrivon-minismo/minismo.env` (then `sudo systemctl restart kaldrivon-minismo`) |
| Data | `/var/lib/kaldrivon-minismo` |
| Logs | `journalctl -u kaldrivon-minismo` or `/var/log/kaldrivon-minismo` |
| Accounts | `sudo kaldrivon-minismo create-admin <name>`, `reset-password <name>`, `list-users` |
| Service | `sudo systemctl start` / `stop` / `restart` / `status kaldrivon-minismo` |

## Documentation

- [User Guide (PDF)](docs/Kaldrivon-MiniSMO-User-Guide-0.7.1.pdf): installation, Demo Network, external NFs, FM / CM / PM, VES, licensing, troubleshooting
- [Quick Start (PDF)](docs/Kaldrivon-MiniSMO-Quick-Start-0.7.1.pdf): install and first steps

The same guide is built into the app (**Help**, or F1 on any page).

## Requirements

- Windows 11, x64; the installer is self-contained (no .NET or Python needed)
- Linux x86_64 with systemd and glibc 2.28 or newer (for example Ubuntu 20.04+, Linux Mint 20+, Debian 10+, RHEL / Rocky / Alma 8+); a current browser to use it

## Notes

- 0.5.0 was the first public release; later versions install over it and keep your data. Earlier builds were previews; there is no upgrade path from them.
- 0.5.x includes security hardening (local API key, SSH host key and file server confirmation, VES sender check, HTTPS-only VES). Some of it changes behaviour; see the release notes. MiniSMO is not certified against any security standard.
- Open-source components and their licenses: [THIRD-PARTY-NOTICES.txt](THIRD-PARTY-NOTICES.txt) (also installed with the app).
- The installer offers Windows Firewall rules, each scoped to the MiniSMO backend and to Domain/Private networks, and each can be unticked during setup: VES collector (TCP 8443), PM file receiver (TCP 2222, SFTP), northbound API (TCP 8770, every call needs a token made in MiniSMO) and NETCONF Call Home (TCP 4334; it accepts only NFs you added in Call Home mode). When you choose Team mode in the installer it also opens the web port (TCP 9443). The rules are removed on uninstall. *SYSTEM > Settings > Communication Matrix* lists every port.

See [RELEASE-NOTES-0.7.1.md](RELEASE-NOTES-0.7.1.md) for what's in this version ([0.7.0](RELEASE-NOTES-0.7.0.md), [0.6.1](RELEASE-NOTES-0.6.1.md), [0.6.0](RELEASE-NOTES-0.6.0.md), [0.5.2](RELEASE-NOTES-0.5.2.md), [0.5.1](RELEASE-NOTES-0.5.1.md), [0.5.0](RELEASE-NOTES-0.5.0.md)).
