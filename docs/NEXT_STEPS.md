# Publication and handoff checklist

## Completed locally

- Source implementation and live `bose24` deployment.
- Mandatory startup recovery validation.
- Low-frequency watchdog 90-second transition validation.
- Services row, default-On state, footer text, and one-step navigation validation.
- Live watchdog Off/On validation with a connected SBC sink; the worker stopped
  and restarted without an audible event or latency change.
- Add-on ZIP build and checksum.
- Public README, installation, rollback, testing, compatibility, changelog,
  release notes, GitHub release draft, and upstream plan.
- Private raw traces retained outside the public bundle.

## User actions needed

1. Provide the GitHub project repository URL, or connect a GitHub account/repo to
   the Codex GitHub connector.
2. Provide the Git author name and email to use for local/public commits. A
   GitHub `noreply` address is fine.
3. Review the public README and release warning before publication.

## Codex can complete after that

- Add the project Git remote and push the community repository.
- Publish `service.libreelec.settings-12.2.1-bose24.zip` as a release asset.
- Create the contribution-ready source commit and format-patch.
- Create a sanitized evidence excerpt if requested.
- Draft or open the LibreELEC pull request after final user review.
- Post a concise public update to the relevant issue when explicitly approved.

## Not included publicly

- SSH credentials or target address.
- Bluetooth MAC addresses.
- Raw traces containing local identifiers.
- FROSLABS/Bose custom splash artwork.
