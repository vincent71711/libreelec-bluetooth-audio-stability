# Changelog

## 0.1.0-rc2 — 2026-08-09

- Restore immediate visible focus when entering ordinary settings lists.
- Preserve the Bluetooth list/footer navigation introduced in RC1.
- Supersede RC1 because its initial right-pane focus could land on a
  non-selectable section separator.

## 0.1.0-rc1 — 2026-08-09

- Suppress continuous Bluetooth discovery while connected audio exists.
- Add visible 15-second manual scan action and connected-audio warning.
- Fix footer/list focus navigation.
- Filter inactive, unpaired scan results after discovery.
- Classify audio sinks by icon or standard UUID.
- Add ordered last-used/paired audio reconnect fallback.
- Add connected-audio manual PulseAudio profile reset.
- Add mandatory per-connection 90-second relative-latency recovery.
- Add default-on, 10-second continuous watchdog with Services toggle, safe-state
  deferral, cooldown, reset serialization, and per-connection limit.
- Validate Bose QC Ultra and SteelSeries Arctis Nova Pro Wireless on Raspberry
  Pi 5 / LibreELEC 12.2.1.
- Add manual-scan Bose firmware warning.

## Local-only branding

- A custom FROSLABS/Bose boot splash was tested successfully but is not included
  in the community or upstream patch.
