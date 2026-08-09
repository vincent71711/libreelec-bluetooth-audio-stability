# Installation and rollback

These instructions install the patched LibreELEC Settings add-on as a writable
`/storage` override. They do not replace the read-only system image.

## Requirements

- LibreELEC 12.2.1 on Raspberry Pi 5
- SSH enabled
- The release file `service.libreelec.settings-12.2.1-bose25.zip`
- A current backup

Replace `<LIBREELEC_IP>` in the examples with the target's address.

## Install from Windows PowerShell

### 1. Download the patch

Download `service.libreelec.settings-12.2.1-bose25.zip` from the
[`v0.1.0-rc2` release](https://github.com/vincent71711/libreelec-bluetooth-audio-stability/releases/tag/v0.1.0-rc2).
Leave the file in your normal Windows **Downloads** folder.

### 2. Find the LibreELEC address

In Kodi, open **System information → Network** and note the IP address. The
examples below use `<LIBREELEC_IP>` as a placeholder; replace it with that
address, for example `192.168.1.50`.

### 3. Open PowerShell

Open the Start menu, type **PowerShell**, and launch Windows PowerShell. Change
to your Downloads folder and confirm that the ZIP is present:

```powershell
cd "$env:USERPROFILE\Downloads"
Get-Item .\service.libreelec.settings-12.2.1-bose25.zip
```

### 4. Copy the ZIP and connect

```powershell
scp .\service.libreelec.settings-12.2.1-bose25.zip `
  root@<LIBREELEC_IP>:/storage/
ssh root@<LIBREELEC_IP>
```

The first connection may ask whether to trust the host fingerprint; type `yes`.
Enter the LibreELEC SSH password when prompted. If you normally authenticate
with an SSH key, add `-i C:\path\to\private-key` to both commands.

If Windows reports that `scp` or `ssh` is not recognized, install **OpenSSH
Client** from **Windows Settings → System → Optional features**, reopen
PowerShell, and retry.

## Back up and install on LibreELEC

Run these commands from the LibreELEC SSH shell:

```sh
mkdir -p /storage/bluetooth-audio-patch/backups
systemctl stop kodi
if [ -d /storage/.kodi/addons/service.libreelec.settings ]; then
  cp -a /storage/.kodi/addons/service.libreelec.settings \
    /storage/bluetooth-audio-patch/backups/service.libreelec.settings-before-bose25
fi
unzip -oq /storage/service.libreelec.settings-12.2.1-bose25.zip \
  -d /storage/.kodi/addons
systemctl start kodi
```

The release ZIP contains the complete add-on. If no writable override existed,
the command creates one; LibreELEC's read-only stock copy remains untouched.

## Verify

```sh
grep -n 'version=' \
  /storage/.kodi/addons/service.libreelec.settings/addon.xml | head
grep -E 'SETTINGS:.*(Started initial|watchdog|Unhealthy|Resetting)' \
  /storage/.kodi/temp/kodi.log | tail -n 30
bluetoothctl show | grep 'Discovering:'
```

Expected results:

- add-on version `12.2.1-bose25`;
- a fresh connected audio sink logs the initial monitor;
- after 90 seconds, the default-on low-frequency watchdog logs its start;
- discovery is `no` while audio is connected unless the user explicitly confirms
  **Scan for devices**.

In Kodi, verify:

1. **LibreELEC Settings → Services → Bluetooth → Continuous audio auto-recovery**
   exists and defaults to On.
2. Its footer explains that the first 90-second recovery remains mandatory.
3. **LibreELEC Settings → Bluetooth → Scan for devices** displays a warning when
   an audio device is connected.
4. Cancel leaves discovery off.
5. A connected audio device's context menu contains **Reset audio connection**.

## Roll back

The following keeps the patched copy rather than deleting it:

```sh
systemctl stop kodi
mv /storage/.kodi/addons/service.libreelec.settings \
  /storage/bluetooth-audio-patch/service.libreelec.settings-bose25-disabled
if [ -d /storage/bluetooth-audio-patch/backups/service.libreelec.settings-before-bose25 ]; then
  cp -a \
    /storage/bluetooth-audio-patch/backups/service.libreelec.settings-before-bose25 \
    /storage/.kodi/addons/service.libreelec.settings
fi
systemctl start kodi
```

If there was no earlier writable override, Kodi automatically falls back to the
stock read-only add-on after the patched directory is moved aside. If paths
differ, stop and resolve the exact backup path before moving anything.

## Build from source

The source patch targets base commit
`9cf5f9868c48878a31ee9f97d290af889dd1c879` of
`LibreELEC/service.libreelec.settings`.

```sh
git clone https://github.com/LibreELEC/service.libreelec.settings.git
cd service.libreelec.settings
git checkout 9cf5f9868c48878a31ee9f97d290af889dd1c879
git apply ../patches/0001-bluetooth-stabilize-audio-connections.patch
make addon ADDON_VERSION=12.2.1-bose25
```

The Makefile requires `zip`. The resulting archive is placed under `build/`.
