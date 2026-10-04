@heitbaum I moved the Pi 5 to LE13 nightly `20261002-1bc4065` and repeated the tests with our Bluetooth override removed, so this was stock Settings 13.0. The system is Kodi 22 RC1, BlueZ 5.87, PulseAudio 17.0 and kernel 6.18.54. Bose QC Ultra was using SBC at 44.1 kHz. Playback was from MP3s stored locally on the Pi.

The issue definitely reproduces here on LE13:

| Test | Result |
|---|---|
| Discovery alone, Wi-Fi associated on 5 GHz | Severe chopping and degraded quality, then recovery after discovery stopped |
| Heavy 5 GHz Wi-Fi upload, discovery off | No audible change; 215.17 Mbit/s received |
| Discovery with wireless networks disabled in LibreELEC Settings | Same severe problem, subjectively equally bad or worse |
| Heavy 2.4 GHz Wi-Fi upload, discovery off | No audible change; 19.64 Mbit/s received |
| 2.4 GHz upload plus discovery | Severe audio failure; I directly observed the headphones rebooting; 11.03 Mbit/s received |

The 2.4 GHz tests used channel 5, with a 30-second iperf3 upload in each run. With discovery off, throughput averaged 19.64 Mbps and audio was clean. With discovery on, throughput averaged 11.03 Mbps, audio degraded severely, and the headphones rebooted. Throughput was approximately 44% lower with discovery on than with discovery off. The important distinction is that Wi-Fi load alone was clean, whereas discovery alone was not. Disabling wireless networking did not prevent the audio failure either.

There were also Wi-Fi reconnection problems after toggling wireless networking: repeated attempts to rejoin 5 GHz failed, with four-way handshake failures (reason 15) and ConnMan reporting `invalid-key`, before I successfully connected on 2.4 GHz. Those logs are included. The password was correct on every attempt, despite that error. I then did a follow-up test with Bluetooth discovery off: Wi-Fi off, Wi-Fi back on, reconnect, disconnect, and reconnect again. That entire sequence worked with zero issues. This provides a successful comparison with discovery off; discovery state was not continuously logged during the earlier Wi-Fi failures.

There is also a reproducible Bluetooth reconnection problem. After the failure, BlueZ could say Connected=yes while PulseAudio had no headphone card/sink and Kodi was feeding the dummy sink. A normal headphone power cycle then produced repeated connection/disconnection announcements without restoring audio. During that observation our adapter samples showed discovery off; a short btmon capture records BR/EDR SMP Security Request, LE Start Encryption returning Unknown Connection Identifier, and a local disconnect requested for Authentication Failure.

Explicitly connecting the A2DP profile restored audio twice. After the unmodified tests, I reapplied our local fixes against the LE13 source, including targeted A2DP recovery when a connected headphone has no audio sink. One subsequent normal headphone power cycle then recovered audio automatically, confirmed both by listening and the “Restored missing A2DP connection” log. Longer-term validation is still needed.

In everyday use, the default behavior has been effectively unusable for me with these headphones on both Raspberry Pi 5 and Raspberry Pi 4 B, with the old and new LibreELEC versions. To keep the evidence scope clear, this new instrumented bundle covers Pi 5/LE13/Bose; the broader hardware/version statement is my experience, and our earlier documented Pi 5/LE12.2.1 tests also included SteelSeries Arctis Nova Pro Wireless.

I have prepared a sanitized evidence bundle containing the full iperf3 JSON results, adapter/PulseAudio samples, service logs, the reconnect btmon text trace, listening observations, and the post-fix power-cycle check. The wireless-disabled test has my listening report and subsequently collected network logs, but no live transport capture during the offline interval. The summary maps each conclusion to its files and explains the UTC/local timestamps and redactions.

The navigation changes and configuration toggle remain separate review concerns; these results support reviewing discovery suppression and audio lifecycle handling on their own merits.
