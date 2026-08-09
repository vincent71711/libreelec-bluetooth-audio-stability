# Publication and handoff checklist

## Completed locally

- Source implementation and live `bose25` deployment.
- Contribution branch `contrib/bluetooth-audio-stability` is pushed publicly;
  corrected head is commit `6b53538`.
- Mandatory startup recovery validation.
- Low-frequency watchdog 90-second transition validation.
- Services row, default-On state, footer text, and one-step navigation validation.
- Live watchdog Off/On validation with a connected SBC sink; the worker stopped
  and restarted without an audible event or latency change.
- Add-on ZIP build and checksum.
- Public project repository and `v0.1.0-rc1` Git tag published at
  https://github.com/vincent71711/libreelec-bluetooth-audio-stability.
- Pre-release published with the verified add-on ZIP; the public download is a
  valid archive, embeds version `12.2.1-bose24`, and matches its retained
  `SHA256SUMS` entry. RC1 is superseded by RC2 because of the initial-focus
  regression.
- Corrected `v0.1.0-rc2` source and `12.2.1-bose25` artifact published after
  live validation of immediate right-pane focus and preserved Bluetooth footer
  navigation.
- Public README, installation, rollback, testing, compatibility, changelog,
  release notes, GitHub release record, and upstream submission notes.
- Draft pull request #369 opened against LibreELEC Settings with four commits.
- Private raw traces retained outside the public bundle.

## User actions needed

1. Review maintainer feedback on draft PR #369 when it arrives.
2. Perform additional hardware testing if requested by maintainers.

## Codex can complete after that

- Address selected maintainer review feedback while preserving the tested
  community release.
- Create a sanitized evidence excerpt if requested.
- Post a concise public update to the relevant issue when explicitly approved.

## Not included publicly

- SSH credentials or target address.
- Bluetooth MAC addresses.
- Raw traces containing local identifiers.
- FROSLABS/Bose custom splash artwork is excluded from the add-on ZIP and
  upstream PR; it is reserved for the separate custom Pi 5 image.
