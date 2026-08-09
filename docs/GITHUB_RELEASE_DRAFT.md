# GitHub release draft

## Tag

`v0.1.0-rc2`

## Title

`v0.1.0-rc2 — Raspberry Pi 5 Bluetooth audio stability patch`

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

RC2 supersedes RC1 and restores immediate visible focus when entering ordinary
settings panes. RC1 could initially focus a non-selectable section separator;
Bluetooth list/footer navigation remains intact in RC2.

## Release asset

- `service.libreelec.settings-12.2.1-bose25.zip`
- SHA-256:
  `2a74779c8e0c2683751edcf57de84c6f87293d23d733e3a385a1ca5c719a765e`

## Publication sequence

1. The community repository and tag `v0.1.0-rc1` are already published and
   superseded.
2. Review the rendered README and links.
3. Create a GitHub Release using tag `v0.1.0-rc2`.
4. Paste the release body above.
5. Upload the exact ZIP from `/srv/dev/libreelec/release-artifacts/`.
6. Confirm the published asset hash matches `SHA256SUMS`.
7. Review `docs/ISSUE_UPDATE_DRAFT.md` before posting any public issue update.
