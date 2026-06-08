# Cycle OSPuter — Architecture Notes

Running record of decisions made and questions open.
Newest entries at the top.

---

## 2026-06-07 — Project Scaffolding

### Decisions Made

**Heading source: GPS course over ground, not compass**
The phone compass reports which way the device is pointing — useless
if the phone is clipped at an angle on the handlebars. GPS `course`
reports the direction of actual travel, which is what matters for
relative wind calculation. Core Location `CLLocation.course` is the
correct property. Compass heading is explicitly rejected.

**Hardcode before live data**
First UI milestone used hardcoded 307° rather than live GPS.
Reasoning: prove the display works as a known-good baseline before
introducing Core Location. One variable at a time — same principle
as aviation instrument checks.

**Privacy by design from day one**
No user accounts, no backend, no ride storage, no analytics SDKs.
App Store privacy label target: no data collected. Architecture
earns the label honestly — not a marketing decision.

**iCloud Drive project location friction**
Project lives in com~apple~CloudDocs/Xcode — known friction with
Xcode's git integration (pull hangs on iCloud file locks). Terminal
git pull used as reliable alternative. Consider moving to plain
~/Developer/ path when Swift resumes post-cert season.

---

## Open Questions

- **Weather API:** OpenWeather vs Apple WeatherKit — WeatherKit is
  native (no third-party dependency, free for low volume) but
  requires paid Apple Developer account. OpenWeather is free tier
  friendly. Decide before implementing wind data feature.

- **Wind dial design:** Does the arrow rotate to show wind direction
  and the dial stays fixed? Or does the whole dial rotate with
  heading and the arrow stays pointing up? UX decision — test both.

- **Units:** mph vs km/h — user preference toggle or system locale?
  Consider both for international App Store reach.

- **Screen layout:** SE has a small display. Grid of data fields
  around the wind dial — how many fields fit readably at cycling
  speed and glance distance? Real-world ride test needed.

- **Background location:** "When in use" only for now (least
  privilege). If the app needs to log a ride, background location
  required — revisit with explicit user consent flow.

---

## Cert Season Note

Active Swift development paused June 2026 during CompTIA cert prep:
Network+ (June 22), Security+ (July 13), CySA+ (August 3).
Core Location and weather API implementation resume after certs.
Repo and README remain live as portfolio during pause.
