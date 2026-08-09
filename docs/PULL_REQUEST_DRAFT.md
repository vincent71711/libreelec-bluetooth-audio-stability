# LibreELEC pull request draft

## Suggested title

`bluetooth: avoid discovery interference with connected audio`

## Suggested body

### Summary

The observed failure is not limited to the Bluetooth page. After boot, powering
on Bose QC Ultra from the Kodi home screen could connect normally or enter four
or five headset reboot/reconnect cycles before becoming usable, without opening
Bluetooth settings. A successful BlueZ connection could also initially have no
audio or a degraded/delayed audio path.

LibreELEC Settings currently starts Bluetooth discovery whenever the Bluetooth
page opens. On the tested Raspberry Pi 5 this interferes with a connected A2DP
stream. Bose QC Ultra is especially sensitive: audio degrades immediately and
longer discovery can trigger a headset reboot/reconnect loop.

Normal discovery behavior is preserved when no Bluetooth audio sink is
connected: opening the Bluetooth page still starts the standard automatic
discovery cycle. Discovery is suppressed only after an audio sink is connected;
the explicit scan action remains available when the user intentionally wants to
search for another device.

This series:

- suppresses automatic discovery only while connected audio exists;
- provides an explicit, time-limited manual scan action;
- warns before scanning with active Bluetooth audio;
- recognizes audio sinks by BlueZ icon or standard Audio Sink UUID;
- filters inactive unpaired scan objects after discovery;
- adds ordered paired-audio reconnect fallback;
- adds manual and automatic PulseAudio profile recovery;
- provides a default-on, user-toggleable low-frequency watchdog after the
  mandatory startup recovery window.

The contribution is organized as five reviewable commits:

1. Settings list/footer navigation support.
2. Bluetooth discovery, reconnect, manual reset, startup recovery, and watchdog
   backend.
3. Services toggle for continuous recovery.
4. Fix an initial-focus regression introduced by the footer-navigation work:
   restore upstream separator skipping in ordinary settings panes while
   retaining the new Bluetooth footer navigation.
5. Shorten the scan-warning question so it remains visible without waiting for
   the dialog text to scroll.

Commit 4 is a required follow-up whenever commit 1 is used; it does not depend
on the optional Services toggle in commit 3. Maintainers may prefer to squash
commit 4 into commit 1 and commit 5 into commit 2 before merging, eliminating
the intermediate regression and wording correction from the final history. I
have left the commits separate to preserve the development and testing history.

### Interface

Device names and hardware addresses are obscured in the Bluetooth-page image.

| Explicit scan action | Scan warning |
| --- | --- |
| ![Scan for devices](https://raw.githubusercontent.com/vincent71711/libreelec-bluetooth-audio-stability/main/docs/images/scan-for-devices.png) | ![Warning before scanning](https://raw.githubusercontent.com/vincent71711/libreelec-bluetooth-audio-stability/main/docs/images/scan-warning.png) |

| Manual audio recovery | Optional continuous recovery |
| --- | --- |
| ![Reset audio connection](https://raw.githubusercontent.com/vincent71711/libreelec-bluetooth-audio-stability/main/docs/images/reset-audio-connection.png) | ![Continuous audio auto-recovery](https://raw.githubusercontent.com/vincent71711/libreelec-bluetooth-audio-stability/main/docs/images/continuous-audio-auto-recovery.png) |

### Evidence

Cold-start and power-cycle testing reproduced the home-screen auto-connect
failure independently of visiting Bluetooth settings. Outcomes ranged from an
immediate clean connection to four or five headset reboot/reconnect cycles
before stable audio.

Controlled A/B/A testing changed only discovery state. Playback was stable with
discovery off, degraded immediately when discovery started, and recovered when
discovery stopped in runs where the headset did not reboot. The Bluetooth link
remained connected during the shorter trials.

With no audio sink connected, opening the Bluetooth page continued to start
automatic discovery normally. After an audio sink connected, discovery stopped
and transient results faded from the list as intended.

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

The full test record, compatibility limits, installable release, and source
patch are published at:

https://github.com/vincent71711/libreelec-bluetooth-audio-stability

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
