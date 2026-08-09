# GitHub release draft

## Tag

`v0.1.0-rc1`

## Title

`v0.1.0-rc1 — Raspberry Pi 5 Bluetooth audio stability patch`

## Release body

This is the first unofficial community release candidate of the LibreELEC
Bluetooth audio stability patch validated on LibreELEC 12.2.1 / Raspberry Pi 5.

The primary fix stops LibreELEC Settings from continuously running Bluetooth
discovery while an audio sink is connected. The release also adds an explicit
15-second scan action with a connected-audio warning, ordered reconnect fallback,
a manual PulseAudio profile reset, mandatory 90-second startup recovery, and a
default-on low-frequency watchdog that can be disabled under
**Services → Bluetooth**.

Validated headsets:

- Bose QC Ultra
- SteelSeries Arctis Nova Pro Wireless

Important: confirming a manual device scan can interrupt active Bluetooth audio.
Bose QC Ultra was especially sensitive during testing and could reboot. This is
an unofficial patch and is not merged into or supported by LibreELEC, BlueZ,
PulseAudio, Bose, or SteelSeries.

Read `INSTALL.md`, `TESTING.md`, and `compatibility.md` before installing.

## Release asset

- `service.libreelec.settings-12.2.1-bose24.zip`
- SHA-256:
  `7b1ba0876704e37eb14a915720e5bc70d21afc0ddabbdfe5a6856373f85d2c84`

## Publication sequence

1. Push the staged community repository to the user-owned GitHub project.
2. Review the rendered README and links.
3. Create tag/release `v0.1.0-rc1` from that commit.
4. Paste the release body above.
5. Upload the exact ZIP from `/srv/dev/libreelec/release-artifacts/`.
6. Confirm the published asset hash matches `SHA256SUMS`.
7. Replace `<PROJECT_URL>` in `docs/ISSUE_UPDATE_DRAFT.md` before posting any
   public issue update.
