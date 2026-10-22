# Kaldrivon MiniSMO 0.5.1 — Release Notes

Windows 11 x64. Installer: `Kaldrivon-MiniSMO-0.5.1-Setup.exe` (checksum in `.sha256`), code-signed by Kaldrivon (Azure Artifact Signing). Installs over 0.5.0 in place; your data, NFs and license are kept. Database schema unchanged (30).

## Fixed

- **O-RU measurement files did not arrive after a new installation on the same PC.** When the O-RU's `remote-file-uploads` already pointed at MiniSMO's PM file receiver on this PC, but with an entry written by an earlier installation or a test build, MiniSMO took the upload as provisioned and did not switch its receiver on; the O-RU's uploads had nowhere to go, and the password in that entry was not known to the new installation anyway. MiniSMO now treats an entry for this PC's receiver that it did not write itself as not provisioned: it switches the receiver on and writes the entry again with its own password, as for an O-RU with no upload entry. Entries that point at another PC are never touched.

## New

- **Alarm history: EXPORT CSV…** writes the alarms of the last search (same date and time range, search text, state and severity, all matches up to 100,000) to a CSV file in the exports folder, after confirmation in the window, with OPEN FILE and SHOW IN FOLDER. Times are UTC (ISO 8601); cells that a spreadsheet would run as a formula are written as text. API: `GET /api/alarms/history/export`.

## Also

- Release build: the packaged backend is signed before its smoke test (Smart App Control blocks an unsigned executable); batches of files are passed to the signing script in a list file.
- Tests: the VES TLS limit test connects to 127.0.0.1 (on Windows, "localhost" tries IPv6 first and cost 2 s); the cross-language fixture generator releases its log file before its temporary folder is removed.

See [RELEASE-NOTES-0.5.0.md](RELEASE-NOTES-0.5.0.md) for everything in 0.5.0, including the security changes.
