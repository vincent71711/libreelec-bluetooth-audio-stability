# Compatibility

| Field | Validated value |
|---|---|
| LibreELEC version | 12.2.1 official, RPi5.aarch64 |
| LibreELEC build ID | 6209f5a3b067c45fbf527286dd84ee483294b46a |
| Settings add-on base commit | 9cf5f9868c48878a31ee9f97d290af889dd1c879 |
| BlueZ | 5.83 |
| PulseAudio | 17.0 |
| Kodi | 21.3 (Omega) |
| Kernel | 6.12.56 aarch64 |
| Hardware | Raspberry Pi 5 Model B Rev 1.0 |
| Patch revision | bose24 / 0.1.0 release candidate |
| Last validated | 2026-08-09 |
| Test result | Pass on Bose QC Ultra and SteelSeries Arctis Nova Pro Wireless |

## Known caveats

- This record does not claim Raspberry Pi 3/4, x86, Amlogic, Allwinner, Rockchip,
  or other controller compatibility.
- Discovery suppression and manual recovery are generic Bluetooth-audio policy.
  Automatic telemetry recovery currently evaluates SBC sinks only.
- Manual discovery remains inherently disruptive on the tested radio/headset
  combination and can reboot Bose QC Ultra firmware.
- The add-on override must match the installed LibreELEC release.

## Artifact

- Release: https://github.com/vincent71711/libreelec-bluetooth-audio-stability/releases/tag/v0.1.0-rc1
- File: `service.libreelec.settings-12.2.1-bose24.zip`
- SHA-256:
  `7b1ba0876704e37eb14a915720e5bc70d21afc0ddabbdfe5a6856373f85d2c84`
- Source patch SHA-256:
  `621bceeeb190ca48196a0c5901e1fd586783c2e228255c91ab146900652592ad`
