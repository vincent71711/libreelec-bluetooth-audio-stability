# LibreELEC Bluetooth Audio Stability Patch

> **UNOFFICIAL COMMUNITY PATCH**
>
> Not merged into or supported by LibreELEC, BlueZ, PulseAudio, Bose, or
> SteelSeries. Use at your own risk.

This project fixes Bluetooth audio instability observed on a Raspberry Pi 5
running LibreELEC 12.2.1. The original failure was especially severe with Bose
QC Ultra headphones: opening LibreELEC's Bluetooth page continuously requested
device discovery, degrading A2DP audio and sometimes triggering headset firmware
reboot loops.

The tested fix belongs in `service.libreelec.settings`. It does not modify BlueZ,
PulseAudio, the Linux kernel, or headset firmware.

## Why this exists

I use Bose QC Ultra headphones with LibreELEC and repeatedly ran into degraded
audio, missing audio, disconnects, and headset reboot loops. I could not find an
existing fix online, and I had the time to investigate the behavior, test the
failure modes, and turn what fixed my own setup into a documented community
patch that others can try.

## Status

- Patch revision: `bose24` / community release candidate `0.1.0`
- Release: [`v0.1.0-rc1`](https://github.com/vincent71711/libreelec-bluetooth-audio-stability/releases/tag/v0.1.0-rc1)
- Last validated: 2026-08-09
- Hardware: Raspberry Pi 5 Model B Rev 1.0
- OS: LibreELEC 12.2.1 (`RPi5.aarch64`)
- Kodi: 21.3
- BlueZ: 5.83
- PulseAudio: 17.0
- Kernel: 6.12.56
- Headsets tested: Bose QC Ultra and SteelSeries Arctis Nova Pro Wireless
- Result: discovery-induced instability is avoided; automatic reconnect,
  startup recovery, manual recovery, and the steady-state watchdog are working
  on the test system.

See [compatibility.md](compatibility.md) for the complete compatibility record
and [TESTING.md](TESTING.md) for the evidence and test matrix.

## What changes

- Stops automatic discovery while a connected Bluetooth audio sink exists.
- Adds a visible, time-limited **Scan for devices** footer action.
- Warns before scanning when Bluetooth audio is connected; Cancel is the
  default.
- Filters transient, unpaired scan results once discovery has ended.
- Recognizes audio devices from either BlueZ's `audio-*` icon or Audio Sink UUID.
- Remembers the last-used audio sink and tries other paired/trusted sinks in a
  bounded, serial fallback order.
- Rearms recovery after every real disconnect/reconnect cycle.
- Adds **Reset audio connection** to the connected-audio context menu. This
  cycles only the PulseAudio A2DP profile and does not unpair the device.
- Monitors the first 90 seconds of every SBC audio connection for a relative
  latency fault, waits for transport stability, and recovers only while media is
  paused, stopped, or absent.
- Adds default-on **Continuous audio auto-recovery** under
  **Services → Bluetooth**. After the mandatory startup window it performs a
  read-only check every 10 seconds, uses a 10-minute recovery cooldown, and
  allows at most three watchdog resets per connection.

## Important manual-scan warning

Manual Bluetooth discovery can degrade or disconnect active Bluetooth audio.
Bose QC Ultra headphones were particularly sensitive in testing and sometimes
rebooted. Other Bluetooth audio devices may also be affected. The patch warns
before starting discovery, but an intentional scan can still interrupt audio.

## Installation

Use the tested add-on ZIP from the
[`v0.1.0-rc1` release](https://github.com/vincent71711/libreelec-bluetooth-audio-stability/releases/tag/v0.1.0-rc1)
or apply the source patch. Full backup, installation, verification, and rollback
commands are in [INSTALL.md](INSTALL.md).
Published artifact hashes are recorded in [SHA256SUMS](SHA256SUMS).

## Scope and limitations

- Validated only on the Raspberry Pi 5 environment above. Raspberry Pi 4/3 or
  other Bluetooth controllers may behave similarly but are not claimed tested.
- Automatic latency detection is deliberately limited to SBC, the codec used by
  both tested headsets. Manual reset and discovery suppression are not SBC-only.
- PulseAudio's reported configured latency is an early fault signature; it does
  not prove that perceived downstream lag persists. The selected policy accepts
  an occasional unnecessary two-second reset during a safe playback state.
- Continuous monitoring is low-frequency and optional. The mandatory first
  90-second recovery remains active when the toggle is Off.
- LibreELEC updates may replace the `/storage` add-on override. Revalidate after
  system updates.

## Upstream status

The patch is now available to LibreELEC maintainers as
[draft pull request #369](https://github.com/LibreELEC/service.libreelec.settings/pull/369).
It remains an unofficial community patch unless and until the maintainers review
and merge it. See [docs/UPSTREAM.md](docs/UPSTREAM.md) for the contribution split
and review notes.

## License

The modified source retains its original SPDX headers and LibreELEC licensing.
See [COPYING](COPYING) and [LICENSES](LICENSES/).
