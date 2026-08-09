# Upstream submission

Draft pull request:

- https://github.com/LibreELEC/service.libreelec.settings/pull/369

## Likely owner

The proven trigger is LibreELEC Settings' discovery policy, so the primary
upstream target is:

- https://github.com/LibreELEC/service.libreelec.settings

No BlueZ or PulseAudio source modification is required for the tested fix.
BlueZ issue 1266 remains useful historical comparison, but this work should not
be represented as a general BlueZ or Bose firmware fix:

- https://github.com/bluez/bluez/issues/1266

## Contribution split

The submitted branch contains logically separable changes organized for
upstream review:

1. Stop automatic discovery while connected audio exists; add explicit timed
   scan and UI focus behavior.
2. Classify audio sinks by UUID/icon and filter inactive scan results.
3. Add bounded, ordered paired-audio reconnect fallback.
4. Add manual A2DP profile reset.
5. Add startup recovery and optional low-frequency watchdog.

The first change is the smallest evidence-backed root-cause fix. Recovery and
reconnect policy are useful follow-ons but may attract separate design review.

## Evidence summary

- Discovery-off playback is stable.
- Starting discovery alone causes dropouts and configured-latency escalation.
- Stopping discovery alone restores audio in non-rebooting runs.
- Long scans can reboot Bose QC Ultra firmware.
- A PulseAudio-only profile cycle is the smallest proven lag recovery.
- Bose and SteelSeries regression paths pass on the target Pi 5.

Raw btsnoop, btmon, journal, and PulseAudio traces remain private because they
contain hardware identifiers. Prepare sanitized excerpts only when an upstream
maintainer requests specific evidence.

## Current review state

- Draft PR #369 targets current upstream `master` from the public contribution
  branch.
- Python compilation, XML parsing, patch application, and live Pi 5 validation
  passed.
- The source branch contains no local `boseNN` packaging identifier or custom
  splash artwork.
- The fourth commit restores ordinary settings-list separator skipping after a
  live-tested RC1 focus regression while preserving Bluetooth footer behavior.
- This community project is linked as unofficial test evidence.
