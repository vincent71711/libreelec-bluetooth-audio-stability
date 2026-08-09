# Publication and handoff checklist

## Completed locally

- Source implementation and live `bose24` deployment.
- Contribution-ready source commit `fbdc958` on local branch
  `fix/pause-discovery-while-connected`.
- Mandatory startup recovery validation.
- Low-frequency watchdog 90-second transition validation.
- Services row, default-On state, footer text, and one-step navigation validation.
- Live watchdog Off/On validation with a connected SBC sink; the worker stopped
  and restarted without an audible event or latency change.
- Add-on ZIP build and checksum.
- Public project repository and `v0.1.0-rc1` Git tag published at
  https://github.com/vincent71711/libreelec-bluetooth-audio-stability.
- Public README, installation, rollback, testing, compatibility, changelog,
  release notes, GitHub release draft, and upstream plan.
- Private raw traces retained outside the public bundle.

## User actions needed

1. Create the GitHub Release from existing tag `v0.1.0-rc1` and upload the
   prepared ZIP asset.
2. Review the public README and release warning.
3. Fork `LibreELEC/service.libreelec.settings` when ready to begin the upstream
   pull-request process.

## Codex can complete after that

- Verify the completed GitHub Release and downloadable ZIP checksum.
- Add the user's LibreELEC Settings fork as a writable remote and push the
  prepared source branch.
- Create a sanitized evidence excerpt if requested.
- Draft or open the LibreELEC pull request after final user review.
- Post a concise public update to the relevant issue when explicitly approved.

## Not included publicly

- SSH credentials or target address.
- Bluetooth MAC addresses.
- Raw traces containing local identifiers.
- FROSLABS/Bose custom splash artwork.
