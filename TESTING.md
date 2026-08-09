# Testing and evidence

## Initial symptom scope

The pre-patch problem was not limited to opening LibreELEC's Bluetooth page.
After a fresh Pi boot, Bose QC Ultra could be powered on from the Kodi home
screen and either connect normally or reboot/reconnect four or five times before
becoming usable. This path required no visit to Bluetooth settings. Successful
BlueZ connection could also initially produce no audio, degraded audio, or a
delayed audio path.

The discovery A/B/A test below established one direct and avoidable trigger.
The reconnect and PulseAudio recovery portions of the patch address the separate
startup and connection-state symptoms observed outside the Bluetooth page.

## Root-cause result

The LibreELEC Bluetooth page requested continuous BlueZ discovery. Controlled
A/B/A tests established discovery as the direct trigger:

1. Stable A2DP playback with discovery off.
2. Start discovery without changing the Bluetooth link or playback.
3. Immediate dropouts/degradation; longer exposure could produce Bose silence,
   supervision timeout, and headset reboot/reconnect loops.
4. Stop discovery without reconnecting.
5. Audio immediately returned to normal in the non-rebooting runs.

This supports an integration-policy fix: do not continuously discover while a
Bluetooth audio sink is connected. Controller/headset robustness—especially
Bose firmware behavior—can amplify the failure but is not required to avoid the
trigger in LibreELEC.

## Headset matrix

| Device | Pair/connect | Discovery suppression | Power-cycle reconnect | Reboot reconnect | Manual PA reset | Automatic recovery |
|---|---:|---:|---:|---:|---:|---:|
| Bose QC Ultra | Pass | Pass | Pass | Pass | Pass | Pass |
| SteelSeries Arctis Nova Pro Wireless | Pass | Pass | Pass | Pass | Pass | Fault reproduced; manual recovery passed |

The NVIDIA Shield Remote remained connected and usable during audio-device
testing.

## Key measured evidence

- Clean SBC/44.1 kHz baseline on both tested headsets: 45.317 ms configured
  PulseAudio latency.
- Fault signature: quantized rise through SBC steps to 68.537 ms.
- Discovery-induced rise: 45.317 → 68.537 ms, directly correlated with the
  manual scan window.
- A persistent SteelSeries click delay could remain even after configured
  latency returned to baseline; sending continuous digital silence did not clear
  it.
- Cycling only the PulseAudio card profile `a2dp_sink → off → a2dp_sink`, with a
  two-second pause, cleared the delay while BlueZ remained connected.
- Resetting too early—while SBC was still adapting—could recreate or worsen the
  fault. Recovery therefore latches the rise and waits for stable baseline
  samples.
- Final Bose cold-start validation detected 45.317 → 68.537 ms, waited for four
  stable startup samples, performed exactly one two-second profile cycle, and
  restored clean menu audio without disconnecting BlueZ.

## Recovery policy tested

- Learn a per-connection baseline rather than assuming a universal latency.
- Startup phase: sample every 0.5 seconds for 90 seconds; latch after two samples
  at least 9 ms above baseline; require four samples within 1 ms of baseline.
- Continuous phase: default-on and user-toggleable; sample every 10 seconds;
  require two elevated and two stable samples.
- Defer reset while media is actively playing.
- Permit reset with no loaded media or explicitly paused media.
- Serialize manual and automatic resets.
- Apply a 10-minute watchdog cooldown and cap watchdog resets at three per
  physical connection.
- Rearm on a real BlueZ disconnect/reconnect event.

## Regression tests completed

- Entering System, Services, Network, and other ordinary settings panes selects
  and highlights the first actionable row immediately; separator rows remain
  non-selectable. Bluetooth footer navigation continues to pass.
- Pairing a previously forgotten SteelSeries headset, including a transient
  passkey prompt.
- Connected-device display using late BlueZ properties and Audio Sink UUID.
- Last-used headset unavailable; serial fallback to another paired/trusted sink.
- Both headsets unavailable; bounded failed attempts without connection loops.
- Headset off during Pi boot, then powered on after Kodi starts.
- Headset on during full Pi reboot.
- Kodi-only restart while Bluetooth remains connected.
- Playback, pause, stop, menu-click, context reset, disconnect, reconnect, and
  full power-cycle paths.
- Discovery cancellation warning leaves discovery off and audio connected.
- Clean connection control: monitoring caused no reset or audible interruption.
- Live watchdog-toggle control: with SBC audio running at a stable 45.317 ms,
  turning continuous recovery Off stopped the worker, turning it back On
  restarted it immediately, and the user heard no interruption, degradation,
  disconnect, or latency change. Five post-toggle samples remained at baseline.

## Known caveats

- Confirming manual discovery can still disrupt audio or reboot Bose QC Ultra.
- Automatic telemetry detection is SBC-specific in this revision.
- Only Raspberry Pi 5 was formally validated.
- Raw traces contain local hardware identifiers and are intentionally not part
  of the public bundle. Sanitized trace excerpts can be prepared for maintainer
  review if requested.
