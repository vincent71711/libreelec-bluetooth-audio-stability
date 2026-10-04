# LibreELEC 13 Raspberry Pi 5 Bluetooth test results

On October 4, 2026, stock LibreELEC 13 reproduced severe discovery-triggered audio degradation with Bose QC Ultra headphones. Heavy Wi-Fi traffic alone did not cause an audible change. The listener also reproduced the discovery failure with wireless networking disabled. Automatic audio reconnection after a headset power cycle failed; an explicit A2DP connection restored it. After adapting the local fixes to LE13, one normal headphone power cycle restored audio automatically.

## Setup

- Raspberry Pi 5; onboard Broadcom BCM4345C0/BCM43455 Bluetooth, UART.
- LibreELEC RPi5.aarch64 nightly 20261002-1bc4065, Kodi 22 RC1, kernel 6.18.54, BlueZ 5.87, PulseAudio 17.0.
- Stock Settings 13.0, source revision 43b834fa64cb4901c86c96275b417e728d34dda7; the previous writable Bluetooth add-on override was removed before testing.
- Bose QC Ultra, SBC/44.1 kHz; local MP3 playback from the Pi, not network-streamed media.
- Pi-to-server TCP upload using iperf3 3.22 on the Pi and 3.20 on the server. Receiver throughput reported below, decimal Mbit/s, 30-second runs.
- Initial Wi-Fi: 5200 MHz, channel 40. Later: 2432 MHz, channel 5.

## Results and evidence

| Condition | Listener observation | Instrumented evidence |
|---|---|---|
| Baseline, discovery off | Clean audio | Running SBC sink |
| Discovery alone, 5 GHz Wi-Fi associated | Horrible chopping, degraded quality, recovery to normal after stopping | `logs/discovery-only-20261004/`: six running-sink samples; configured latency includes 68.537 ms |
| 5 GHz upload, discovery off | Zero audible change | `logs/wifi-only-20261004/iperf.json`: 215.17 Mbit/s received; all six adapter samples show discovery off |
| Wi-Fi disconnected; discovery exercised manually | Same issue | Listener report; subsequent network journal retained |
| Wireless Networks disabled; discovery exercised manually | Equally bad, possibly worse | `logs/wifi-disabled-20261004/`: listener report plus later-collected interface-down journal; no live transport trace during offline interval |
| 2.4 GHz upload, discovery off | Zero audible change | `logs/wifi-24ghz-only-20261004/iperf.json`: 19.64 Mbit/s received |
| 2.4 GHz upload plus discovery | Severe failure; listener directly observed headphones rebooting | `logs/wifi-24ghz-discovery-20261004/`: 11.03 Mbit/s received; audio sink subsequently absent; BlueZ AVDTP connection abort logged |

The 2.4 GHz tests used channel 5, with a 30-second iperf3 upload in each run. With discovery off, throughput averaged 19.64 Mbps and audio was clean. With discovery on, throughput averaged 11.03 Mbps, audio degraded severely, and the headphones rebooted. Throughput was approximately 44% lower with discovery on than with discovery off. The initial mixed off/on experiment was interrupted after audible failure and is excluded from this controlled comparison; its raw data remain locally retained.

## Reconnection failure

At initial connection, BlueZ reported connected while PulseAudio had profile Off and Kodi was feeding `auto_null`. Profile activation failed with transport acquisition errors. Reconnecting restored audio.

After the discovery/load-induced headset reboot, BlueZ again reported connected but PulseAudio had no headphone card or sink; Kodi continued feeding `auto_null`. One explicit `bluetoothctl connect <headset> a2dp-sink` recreated the SBC sink and restored audible playback. See `logs/le13-power-cycle-no-audio-20261004/` and `logs/wifi-24ghz-discovery-20261004/post-failure-no-audio.txt`.

A subsequent ordinary headphone power cycle reproduced unsuccessful automatic recovery and repeated disconnect/reconnect announcements. The passive trace in `logs/le13-normal-power-cycle-20261004/btmon.txt` records BR/EDR SMP Security Request, LE Start Encryption returning Unknown Connection Identifier, and a local disconnect requested for Authentication Failure. Five-second adapter samples showed discovery off. Explicit A2DP connection again restored audio, confirmed by the listener. This is a separate captured failure path; it is not proof that active inquiry caused those reconnect failures. HCI Page Scan entries must not be confused with device discovery/inquiry.

## Wi-Fi reconnection observation

After wireless networking was toggled during testing, the user experienced difficulty reconnecting. The retained network journal shows repeated 5 GHz connection attempts, four-way handshake failures with reason 15, and ConnMan reporting `invalid-key`; a 2.4 GHz connection subsequently succeeded. See `logs/wifi-disabled-20261004/journal.txt`. The user confirms the password was correct on every attempt, despite the `invalid-key` error. These logs do not establish whether the issue is a LE13 regression, AP/security interoperability, or related to Bluetooth. This also means the manual Wi-Fi-off sequence should not be interpreted as one perfectly controlled offline interval.

In a subsequent manual test with Bluetooth discovery off, the user turned Wi-Fi off, turned it back on, reconnected, disconnected, and reconnected again, reporting zero issues throughout. This successful sequence is a user-reported comparison; no dedicated trace was collected for it, and discovery state was not continuously logged during the earlier failures.

## Local recovery changes and validation

The prior local patch was reapplied to the exact LE13 settings source as version 13.0-bose26, retaining LE13's other changes. It restores automatic discovery suppression for connected audio devices, explicit bounded scanning, reconnect handling, and existing manual/automatic recovery controls. A small LE13 adaptation requests A2DP once from the per-connection recovery worker when the headphone sink is missing, using the command path verified above.

The adapter passed local behavior checks, on-Pi Python compilation, and service startup. After installation, the listener performed one normal headphone power cycle and confirmed normal audio recovery without manual intervention. `logs/post-fix-power-cycle.txt` records “Restored missing A2DP connection”, a running SBC sink, and discovery off. This is one successful functional check, not a long-term reliability claim or proof that the authentication failure itself is fixed.

## Scope and interpretation

Discovery is a demonstrated trigger on this Pi/headset combination; heavy Wi-Fi load is not necessary. The wireless-disabled observation further argues against Wi-Fi activity being a necessary condition, although exact offline transport/radio state was not continuously instrumented. Controller firmware, headset behavior, and stack ownership remain unresolved.

The user's broader experience is that default Bluetooth behavior is effectively unusable with their headphones on both Raspberry Pi 5 and Raspberry Pi 4 B, across old and new LibreELEC versions. Attribute that as user experience: this bundle directly documents Pi 5/LE13/Bose testing. Earlier project records separately document Pi 5/LE12.2.1 testing with Bose QC Ultra and SteelSeries Arctis Nova Pro Wireless.

## Time and privacy notes

ISO timestamps ending in +00:00 and `date -u` output are UTC. Kodi, journal, and btmon wall-clock timestamps in these captures are America/New_York (UTC−4); e.g. 01:58:29 local is 05:58:29 UTC. Listening reports are associated with phases, not sample-accurate event timestamps.

This bundle contains sanitized copies, not byte-identical originals. Network addresses, device addresses, SSIDs, local domain, cookies, and raw packet hex payloads (including Bluetooth key bytes) were removed or replaced. Replacement device labels are consistent across the bundle. Event/status fields and timing remain. Original captures remain private in the local workspace. SHA256SUMS verifies the shareable files, not the private originals.
