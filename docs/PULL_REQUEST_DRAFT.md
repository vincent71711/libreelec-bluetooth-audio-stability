# LibreELEC pull request draft

## Suggested title

`bluetooth: avoid discovery interference with connected audio`

## Suggested body

### Summary

LibreELEC Settings currently starts Bluetooth discovery whenever the Bluetooth
page opens. On the tested Raspberry Pi 5 this interferes with a connected A2DP
stream. Bose QC Ultra is especially sensitive: audio degrades immediately and
longer discovery can trigger a headset reboot/reconnect loop.

This series:

- suppresses automatic discovery while connected audio exists;
- provides an explicit, time-limited manual scan action;
- warns before scanning with active Bluetooth audio;
- recognizes audio sinks by BlueZ icon or standard Audio Sink UUID;
- filters inactive unpaired scan objects after discovery;
- adds ordered paired-audio reconnect fallback;
- adds manual and automatic PulseAudio profile recovery;
- provides a default-on, user-toggleable low-frequency watchdog after the
  mandatory startup recovery window.

### Evidence

Controlled A/B/A testing changed only discovery state. Playback was stable with
discovery off, degraded immediately when discovery started, and recovered when
discovery stopped in runs where the headset did not reboot. The Bluetooth link
remained connected during the shorter trials.

A separate intermittent startup delay was reproduced on Bose and SteelSeries.
Cycling only the PulseAudio A2DP card profile cleared it while BlueZ remained
connected. Early resets could worsen SBC adaptation, so automatic recovery
latches the elevated configured-latency signature and waits for stable baseline
samples before acting.

### Testing

- Raspberry Pi 5 Model B Rev 1.0
- LibreELEC 12.2.1 RPi5.aarch64
- Kodi 21.3
- BlueZ 5.83
- PulseAudio 17.0
- Bose QC Ultra
- SteelSeries Arctis Nova Pro Wireless
- NVIDIA Shield Remote remained connected

Validated pairing, manual connect/disconnect, headset power cycles, Kodi restart,
full Pi reboot with headset on/off, last-used fallback, music playback,
pause/stop, manual profile reset, automatic startup recovery, watchdog handoff,
and scan-warning cancellation.

### Caveats

- Confirming manual discovery may still interrupt audio; Bose QC Ultra may
  reboot. The dialog makes that risk explicit.
- Automatic telemetry detection currently evaluates SBC sinks.
- Formal hardware validation is limited to Raspberry Pi 5.
- Raw traces contain hardware identifiers and are not attached publicly;
  sanitized excerpts can be supplied on request.

## Review strategy

Consider submitting discovery suppression/manual scanning first. Reconnect and
recovery policy can be split into follow-up commits if maintainers prefer a
smaller initial change.
