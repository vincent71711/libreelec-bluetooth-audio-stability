# Published commit structure

## Community repository

```text
Initial Bluetooth audio stability patch release candidate

Document and package the LibreELEC 12.2.1 Raspberry Pi 5 community patch.
Include installation and rollback instructions, compatibility limits, test
evidence, release notes, upstream preparation, and the source diff. Keep raw
traces and local branding out of the public repository.
```

## Upstream source branch

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

The contribution branch is split into four reviewable commits:

1. Settings list/footer navigation support.
2. Bluetooth connection lifecycle and recovery.
3. Services toggle for continuous recovery.
4. Fix for an initial-focus regression introduced by footer navigation,
   restoring upstream separator skipping without removing the Bluetooth footer.
