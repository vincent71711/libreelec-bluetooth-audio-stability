# Commit drafts

## Community repository

```text
Initial Bluetooth audio stability patch release candidate

Document and package the LibreELEC 12.2.1 Raspberry Pi 5 community patch.
Include installation and rollback instructions, compatibility limits, test
evidence, release notes, upstream preparation, and the source diff. Keep raw
traces and local branding out of the public repository.
```

## Source branch

```text
bluetooth: stabilize connected audio devices

Avoid continuous discovery while Bluetooth audio is connected and expose an
explicit timed scan with a connected-audio warning.

Classify audio sinks by icon or standard UUID, filter transient inactive scan
objects, and reconnect paired/trusted sinks in bounded preference order.

Add a PulseAudio-only manual reset, per-connection startup recovery, and an
optional low-frequency watchdog that defers recovery during active playback.

Validated on LibreELEC 12.2.1/Raspberry Pi 5 with Bose QC Ultra and SteelSeries
Arctis Nova Pro Wireless.
```
