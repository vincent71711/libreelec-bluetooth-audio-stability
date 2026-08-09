# Public issue update draft

I reproduced the Bluetooth audio problem on LibreELEC 12.2.1 with a Raspberry Pi
5 and Bose QC Ultra, then regression-tested with SteelSeries Arctis Nova Pro
Wireless.

The strongest trigger in this environment was continuous discovery requested by
LibreELEC Settings while the Bluetooth page was open. With A2DP playback stable,
starting discovery alone caused immediate degradation/dropouts. Stopping
discovery alone restored audio in shorter runs. Longer scans could trigger Bose
firmware reboot/reconnect behavior.

I have an unofficial LibreELEC Settings patch that suppresses automatic
discovery while audio is connected and exposes an explicit timed scan with a
warning. It also contains paired-audio reconnect fallback and a conservative
PulseAudio profile recovery for a separate intermittent startup-latency state.

Validated environment:

- Raspberry Pi 5
- LibreELEC 12.2.1 / Kodi 21.3
- BlueZ 5.83 / PulseAudio 17.0
- Bose QC Ultra and SteelSeries Arctis Nova Pro Wireless

This is not yet merged or supported upstream. Patch, build/apply instructions,
test results, compatibility notes, and known caveats are available here:

https://github.com/vincent71711/libreelec-bluetooth-audio-stability

Additional hardware/version testing would be welcome. In particular, please
report the LibreELEC version, board/controller, headset model, codec, whether
discovery was active, and whether the issue was degradation, lag, disconnect, or
headset reboot.

Raw traces are not posted because they contain local hardware identifiers, but
sanitized excerpts can be prepared if maintainers request them.
