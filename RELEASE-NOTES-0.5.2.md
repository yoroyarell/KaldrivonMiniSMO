# Kaldrivon MiniSMO 0.5.2 — Release Notes

Windows 11 x64. Installer: `Kaldrivon-MiniSMO-0.5.2-Setup.exe` (checksum in `.sha256`), code-signed by Kaldrivon (Azure Artifact Signing). Installs over 0.5.0 and 0.5.1 in place; your data, NFs and license are kept. Database schema unchanged (30).

## New

- **Software updates** (*SYSTEM > Settings > Software updates*). **CHECK NOW** asks Kaldrivon's releases on GitHub for a newer version on your Update Channel: Stable offers releases, Preview and Internal also pre-releases. *Check for updates: Once a day* does this by itself and adds a notification; it is off by default, and nothing else is sent. **DOWNLOAD…** and **INSTALL…** each ask for confirmation. The download is accepted only over HTTPS from GitHub, with the published SHA-256 and a valid Authenticode signature by Kaldrivon. Both are checked after the download and again right before the installer starts. INSTALL… starts the usual setup wizard (administrator permission), MiniSMO closes, and the wizard keeps your data folder. API: `/api/updates`. Updating this way works from 0.5.2 on; to get 0.5.2 itself, run its installer.

## Installer

- Every Portable Executable in the installation is now signed: besides `.exe`, `.dll` and `.pyd`, also the WinUI language resource files (`.mui`), which had no signature. Files signed by their own publisher (Microsoft, Python Software Foundation, ...) keep that signature.
- A silent installation (`/VERYSILENT /SUPPRESSMSGBOXES`) never stops on a message: the notice about a missing WebView2 Runtime is skipped then.
- Uninstalling removes the fonts that MiniSMO's setup installed (IBM Plex Sans, IBM Plex Mono, Space Grotesk). A font that was already on the PC before is left alone. Your data folder (`%LOCALAPPDATA%\Kaldrivon\MiniSMO`) is still kept on uninstall; delete it yourself to remove all data.

See [RELEASE-NOTES-0.5.1.md](RELEASE-NOTES-0.5.1.md) and [RELEASE-NOTES-0.5.0.md](RELEASE-NOTES-0.5.0.md) for the earlier changes.
