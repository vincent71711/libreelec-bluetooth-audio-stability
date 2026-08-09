# 0.1.0-rc1 release notes

This release candidate is an unofficial LibreELEC 12.2.1 Raspberry Pi 5 patch
for Bluetooth A2DP instability, initially diagnosed with Bose QC Ultra
headphones and regression-tested with SteelSeries Arctis Nova Pro Wireless.

The primary fix prevents LibreELEC Settings from continuously scanning while an
audio sink is connected. It also adds reliable audio-device classification,
ordered reconnect fallback, a manual profile reset, bounded startup recovery,
and an optional default-on lifetime watchdog.

## User-visible changes

- **Scan for devices** is a visible footer action.
- Connected audio receives a warning before scanning, with Cancel selected.
- **Reset audio connection** is available in connected-audio context menus.
- **Continuous audio auto-recovery** appears under **Services → Bluetooth** and
  defaults to On. Turning it Off does not disable the mandatory first 90 seconds
  of recovery.

## Warning

Confirming a manual scan may degrade or disconnect active Bluetooth audio. Bose
QC Ultra may reboot because of headset firmware behavior observed during
discovery. Automatic reconnect recovered the test system, but playback is
interrupted.

## Validation

The release candidate completed clean power-on, power-off, manual reconnect,
Kodi restart, full Pi reboot, paused/stopped playback, and second-headset tests.
One captured cold-start fault was automatically detected and recovered without
disconnecting the underlying BlueZ link.

See [compatibility.md](compatibility.md), [TESTING.md](TESTING.md), and
[INSTALL.md](INSTALL.md) before installation.
