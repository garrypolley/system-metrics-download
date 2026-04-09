# System Metrics Agent Notes

This workspace contains two separate Git repositories with different purposes.

## Repositories and ownership

1. Website + distribution metadata repo (public):
   - Local path: `/Users/macmini/src/system-metrics`
   - Remote: `git@github.com:garrypolley/system-metrics-download.git`
   - Purpose:
     - Public marketing/download site (`docs/`)
     - GitHub Pages deployment
     - Public update feed (`docs/appcast.xml`)
   - Must NOT contain:
     - Proprietary Swift source code
     - Private keys, `.p8` files, notarization credentials

2. App source repo (private/local code repo):
   - Local path: `/Users/macmini/src/system-metrics/SystemMetrics`
   - Purpose:
     - Swift/SwiftUI app source, Xcode project, tests
     - Sparkle integration and app binary build/sign pipeline
   - Suggested remote:
     - Configure your private code repo remote here (none is configured right now).

## Directory intent

- `docs/` (outer/public repo): static website content only.
- `dist/` (outer repo, local build output): generated artifacts (`.app`, `.dmg`, temp files), not source.
- `SystemMetrics/` (inner/private repo): full app code and configuration.

## Release flow (high-level)

1. Build app from inner repo (`SystemMetrics`).
2. Sign app with Developer ID Application cert.
3. Create DMG with app + `/Applications` link.
4. Sign DMG container.
5. Notarize and staple DMG (and app as needed).
6. Upload DMG to GitHub release asset.
7. Regenerate/sign `docs/appcast.xml` for Sparkle.
8. Push website repo (`docs/` updates) so appcast is live on GitHub Pages.

## Sparkle update setup (current state)

- App uses Sparkle and exposes a `Check for Updates…` action.
- Feed URL is configured in app Info.plist build settings:
  - `SUFeedURL = https://system-metrics.garrypolley.com/appcast.xml`
- Public EdDSA key is configured as:
  - `SUPublicEDKey = Kqa/expqIXjFSbHHk0ueMp4PXxFvtOXFToAYgQ85ZV0=`
- Sparkle signing private key is stored in local macOS Keychain account:
  - `ed25519`

## Sparkle appcast signing notes

- `docs/appcast.xml` must include:
  - `sparkle:edSignature` on each enclosure
  - Appcast-level signature block at end of XML
- If `appcast.xml` is modified manually, it must be re-signed.

## Required local secrets and files

Do not commit real secrets. Use local files under the inner repo:

- `/Users/macmini/src/system-metrics/SystemMetrics/.secrets/notarization.env`
- `/Users/macmini/src/system-metrics/SystemMetrics/.secrets/sparkle.env`

These are intentionally gitignored by the inner repo `.gitignore`.

## Required credentials

- Apple Developer ID Application certificate in Keychain
- App Store Connect API key file (`AuthKey_*.p8`)
- App Store Connect Key ID + Issuer ID
- Sparkle private key in Keychain (`ed25519`) or exported private key file for CI

## Common pitfalls

- Outer repo accidentally includes app code (should not).
- DMG not code-signed as container (must be signed).
- Appcast updated but not re-signed.
- Sparkle key mismatch between `SUPublicEDKey` and signing private key.
- Testing updates from a pre-Sparkle build (first Sparkle-enabled release must be installed manually).
