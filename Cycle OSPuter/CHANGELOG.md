# Changelog

All notable changes to Cycle OSPuter will be documented here.
Format based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).
Versioning follows [Semantic Versioning](https://semver.org/) — Major.Minor.Patch.

---

## [Unreleased]

> Everything here is pre-release. When the app ships on the App Store,
> this block becomes v1.0.0 and a new [Unreleased] block opens above it.

### Added
- SwiftUI project scaffold — Xcode project, Swift language, SwiftUI interface
- `.gitignore` covering macOS, Xcode user data, build output, Swift Package Manager
- `README.md` — project description, roadmap, security & privacy section, built-with
- Hardcoded heading display (307°) — first UI element rendered on iPhone SE simulator
- Security & privacy section in README:
  - Least privilege: "when in use" location only
  - Data minimization: on-device processing, nothing stored or transmitted
  - Secure credentials: weather API key in iOS Keychain, never in source
  - Secure comms: HTTPS with ATS enforced, API responses validated
  - Privacy by design: no user accounts, no ride storage, no analytics SDKs
- `CHANGELOG.md` — this file
- `NOTES.md` — architecture decisions and open questions

---

## Version Guide

| Symbol | Meaning |
|---|---|
| Added | New feature or file |
| Changed | Change to existing feature |
| Fixed | Bug fix |
| Removed | Removed feature or file |
| Security | Security fix or improvement |
