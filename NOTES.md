# Cycle OSPuter — Architecture Notes

Running record of decisions made and questions open.
Newest entries at the top.

---

## 2026-06-07 — Git Workflow, Security, and Project Scaffolding

### Decisions Made

**Xcode security organization — one credential per purpose**
Per-purpose security credential setup confirmed this session: separate
credentials for GitHub git operations, remote access, scheduled
workflows, and general use. Each credential scoped to one job —
if one is compromised or revoked, nothing else breaks. This is
least-privilege applied to authentication infrastructure.
Xcode auth fixed via PAT retrieved from secure macOS credential
storage. One clean verified GitHub account remains in Xcode.

**CHANGELOG and NOTES workflow established**
Both files are now part of the CRUD toolkit. Not every session
requires updates to both — only when architecture decisions are made
or notable changes land. CHANGELOG tracks what changed; NOTES tracks
why. README roadmap tracks what is next.

**Heading source: GPS course over ground, not compass**
The phone compass reports which way the device is physically pointing —
useless if the phone is clipped at an angle on the handlebars. GPS `course`
reports the direction of actual travel over the ground, which is the correct
input for relative wind calculation. Core Location `CLLocation.course` is the
confirmed property. Compass heading explicitly rejected for this use case.

**Privacy by design from day one**
No user accounts, no backend, no ride storage, no analytics SDKs. App Store
privacy label target: no data collected. This was Carlos’s instinct — starting
from the user-facing promise (“what does the App Store label say?”) and working
backward to the architecture that earns it honestly. Not a marketing decision.
The architecture makes the claim true.

**CHANGELOG + NOTES instead of a flat TODO file**
README roadmap checkboxes already serve as the task list — visible on the repo
front page, honest about current state. A separate TODO.md goes stale fast.
CHANGELOG tracks what changed and when (source for future App Store release
notes). NOTES captures the why behind decisions. Three documents covering:
what it does (README), what changed (CHANGELOG), why we decided things (NOTES).

**iCloud Drive project location friction**
Project lives in com~apple~CloudDocs/Xcode — creates friction with Xcode’s git
integration. Xcode pull hangs indefinitely on iCloud file locks. Terminal git
pull bypasses this cleanly. Consider moving to ~/Developer/ when Swift resumes
post-cert season. Related open bug report: Desktop Commander iCloud folder
access conflict (#388).

---

## 2026-05-24 — Project Inception

### The Idea

**Why this app exists**
Carlos is a heavy cyclist. Every commercial bike computer (Garmin, Wahoo,
CYCPLUS, and all App Store equivalents) shows speed, distance, and maybe a map.
None show wind relative to the rider’s direction of travel. Wind is the single
biggest factor in how a ride actually feels — a 15 mph headwind is a completely
different experience than a 15 mph tailwind at the same speed — yet no app
surfaces this information in a usable way. The gap was identified by a real
cyclist with a real need, not invented as a project. Best first apps scratch a
personal itch.

**Why wind orientation, not just wind speed**
Raw wind data (direction + speed from a weather service) is available in many
apps. The missing piece is wind *relative to the rider* — is it hitting you from
the front, the back, or the side? That requires combining the wind’s absolute
direction with the rider’s direction of travel. A number alone (“NW at 12 mph”)
is useless on a bike. An oriented indicator — headwind, tailwind, crosswind — is
actionable at a glance. That combination is the unique feature.

**The core computation**
Relative wind angle = wind direction − course over ground.
Simple subtraction. The hard part is not the math — it is getting both inputs
correctly. Carlos’s general aviation Private Pilot License training provided the
conceptual foundation: heading, wind correction angle, relative wind, and the
critical distinction between where you are *pointing* vs where you are *going*.
That aviation mental model maps directly onto this app.

**Why the app is also a cybersecurity portfolio piece**
Per Symoné B. Tech’s validated 2026 cybersecurity roadmap: after certs, the
differentiator is an AI-powered project portfolio documented on GitHub — “99% of
candidates don’t do this.” Cycle OSPuter, built security-first with secure
credential storage, on-device data minimization, and ATS-enforced networking,
documented from commit one, is exactly that differentiator. The tricycle has a
career job to do beyond the rides.

---

### Platform Decisions

**Why iOS, not macOS**
Carlos wanted the app on his iPhone while riding — the phone is already on the
handlebars. macOS was mentioned initially because Xcode runs on Mac, but the
clarification: Xcode is the *build tool*, iOS is the *target*. You build one iOS
app using Mac tooling and run it on the phone. Not two apps, one pipeline.

**Why SwiftUI, not UIKit or Storyboard**
SwiftUI is Apple’s modern declarative UI framework. The template explicitly chose
it over Storyboard (the legacy approach). Key concept established: declarative UI
*describes* what the screen looks like rather than issuing step-by-step commands
to build it. This mirrors React/JSX for anyone with web dev background. SwiftUI
is the present and future of Apple development; UIKit is legacy maintenance.

**Why iPhone SE 3rd Gen as the simulator target**
Carlos’s actual phone. Whatever fits on an SE screen works everywhere; designing
for a small screen from the start avoids painful layout rewrites later.
Constraint treated as a creative discipline — three decades of tight column
grid page layout in print production makes this a familiar challenge, not a
limitation. Also: what you see in the SE simulator is what you get on the real
device, so feedback is accurate from day one.

**Why the canvas view was turned off immediately**
Xcode’s live preview canvas attempts to re-render the UI in real time while you
type. On 8GB RAM it is a memory hog that stalls, errors, and competes with the
Simulator. The canvas is a convenience, not the real thing. Decision: disable
canvas permanently (Editor → Canvas off), use Simulator exclusively as the
source of truth. This also forces a more disciplined workflow — build and run
rather than relying on a preview that may not reflect actual behavior.

---

### Development Philosophy

**Why start with heading, not wind**
Two major subsystems were identified from the start: GPS heading (on-device,
no network) and wind data (weather API, network, authentication, credentials).
Decision: build one system at a time, never both simultaneously. Heading first
because it requires no external dependencies — no API account, no network calls,
no credentials. Prove one system works before introducing the next. This is the
same principle as preflight instrument checks in aviation: you confirm each
system independently before trusting the combination.

**Why hardcode 307° before wiring live GPS — the critical decision**
This is the single most important early decision and deserves full reasoning.

The goal of the first milestone was to answer one question: *can I get output
displaying on an iOS screen at all?* Not “can I get GPS working?” Not “can I
build the full app?” Just: does the display pipeline work — does a value appear
on the phone screen?

By hardcoding 307° (a static number in the source code), the display was proven
as a *known-good baseline* independently of any other system. When live GPS is
wired up next and something breaks, there is exactly one new variable: the GPS
code. The display is already confirmed working. One suspect, not three.

If display and GPS had been built simultaneously and the screen showed nothing,
the bug could be anywhere — GPS permissions, the location manager setup, the
data flow between systems, or the display itself. Debugging a tangle of unknowns
is slow and demoralizing. Debugging one new system against a proven baseline is
fast and precise.

This is standard engineering discipline: establish a baseline, change one thing,
observe the result. Aviation analogy: you don’t test a new autopilot system on
the same flight you’re trying a new engine configuration. One variable at a time.

307° specifically was not deliberate — it was the heading Carlos happened to type.
It is, incidentally, a northwest heading; a rider on that course into a north
wind would be taking a stiff cross-headwind from the left. The pilot brain
picked it unconsciously.

**Why the Simulator before the real device**
The Simulator cannot move, so it cannot produce a real GPS course value. Early
testing of the display (the hardcoded phase) works fine in the Simulator.
Live GPS testing requires walking or riding with the actual phone. The workflow:
prove display in Simulator → prove GPS on real device. Do not chase real GPS in
the Simulator; it will not work and the confusion is not worth it.

**Why the paid Apple Developer account can wait**
Free Apple ID provisioning lets you build and run on your own physical device
from Xcode. Limitation: apps expire after 7 days and must be rebuilt to
revive. During active development this is irrelevant — you rebuild daily anyway.
It becomes an annoyance only when the app genuinely works and you want to ride
with it for weeks without touching the Mac. That is the signal to pay the $99.
You do not pay Apple to learn; you pay Apple to ship.
App Store distribution, TestFlight, and iCloud require the paid account.
Those are future concerns.

---

### Repository Decisions

**Why public repo from day one**
The instinct — “it only says 307°, won’t it look unfinished?” — assumes
recruiters judge a repo by a single snapshot. They do not. For a developer
in transition, what matters is evidence of working like a developer: consistent
commits, clean history, professional tooling (gitignore, README, CHANGELOG).
A repo that starts at 307° and grows into a working wind-aware bike computer
is a stronger portfolio artifact than one that appears fully formed overnight.
The progression is the credibility. Commit history cannot be faked.

**Why no license**
Publishing code without a license file means default copyright applies: all
rights reserved. Anyone can view the source (portfolio value intact) but has
no legal right to copy, redistribute, or commercialize it. This protects the
wind orientation feature — the unique value of the app — while keeping the
portfolio visible. Choosing a permissive license (MIT, Apache) would allow
others to lift the feature and ship a competing app. GPL creates friction with
the App Store (historical VLC conflict). No license: visible and protected.
A license can always be added later; a permissive license cannot be un-rung.

**Why SSH over HTTPS for git authentication**
HTTPS authentication requires a token — not the GitHub password, which was
deprecated for git operations in 2021. Tokens expire, get forgotten, and require
re-creation. SSH key pairs: one-time setup, silent authentication forever.
Existing key pair was already registered with GitHub from prior projects.
The fix was not generating new keys but switching the remote URL format:
`https://github.com/...` → `git@github.com:...`
The colon after github.com (not a slash) is the SSH URL tell.
Habit going forward: always click SSH (not HTTPS) on GitHub’s Code button.

**Why .gitignore before first code commit**
Xcode generates large volumes of regenerable build artifacts: DerivedData,
xcuserdata, .xcuserstate, .DS_Store, build/. None of this belongs in version
control — it is machine-specific, regenerated on every build, and bloats the
repo with noise. Committing the .gitignore before any other files ensures clean
tracking from the start. Fixing a dirty history later is painful. The right
order: tooling first, then code.

**Why Terminal for some git operations, Xcode for others**
Xcode’s Source Control commit sheet is genuinely useful — visual diff, checkbox
staging, commit message all in one place. But Xcode’s git integration is not
always reliable: pull operations on iCloud-hosted projects hang on file locks.
Terminal git bypasses iCloud’s file-locking behavior and is always reliable.
Real professional workflow: use Xcode for commits (convenient, visual), use
Terminal for pulls and any operation that Xcode fumbles. One git, two doors.
Pick the right door for the job.

---

### Security Philosophy

**Why security was designed in, not bolted on**
Security was considered before a single line of production code was written.
The reasoning: it is architecturally cheap to build secure from the start and
expensive to retrofit security into a shipped app. The specific decisions:

- Location permission: “when in use” only. The app does not need background
  location to compute relative wind on a ride. Requesting more than needed
  is a privacy violation even if the user grants it. Least privilege.

- GPS data: computed on-device and used in the moment. Not stored, not logged,
  not transmitted. The app has no backend. There is nothing to breach.

- API keys: will live in the iOS Keychain, not in source code. A key committed
  to a public repo is a compromised key. The .gitignore already excludes config
  files; the Keychain is the correct store for secrets on Apple platforms.

- Network traffic: HTTPS with App Transport Security (ATS) enforced by default
  in iOS. API responses will be validated before use — external data is treated
  as untrusted until parsed and checked.

These are not advanced security features. They are the baseline of responsible
iOS development. They are also exactly the principles covered in CompTIA
Security+ and CySA+, which Carlos is pursuing. The app is a live lab.

---

## Open Questions

- **Weather API:** OpenWeather vs Apple WeatherKit — WeatherKit is native
  (no third-party dependency, free for low call volume) but requires paid
  Apple Developer account ($99/yr). OpenWeather has a generous free tier
  and no account dependency. Decide before implementing wind data. WeatherKit
  is the cleaner long-term choice if the paid account is coming anyway.

- **Wind dial design:** Does the arrow rotate to show wind direction while the
  dial stays fixed? Or does the whole dial rotate with the rider’s heading
  while the arrow stays pointing up? The first approach shows absolute wind;
  the second shows relative wind more intuitively. UX decision — build both
  prototypes and test on a real ride.

- **Units:** mph vs km/h — user preference toggle or system locale detection?
  System locale is less friction for the user. A toggle adds complexity but
  serves international App Store reach. Decide at UI implementation time.

- **Screen layout for iPhone SE:** SE has a small display. Grid of data
  fields around the wind dial — how many fit readably at cycling speed and
  glance distance? Priority order for fields: relative wind indicator,
  heading, speed, wind from (cardinal), distance. Real-world glance test
  needed before finalizing layout.

- **Background location:** “When in use” only satisfies the current use case.
  If ride logging is ever added (track recording, distance, elapsed time),
  background location permission is required. Revisit only with explicit
  user consent flow and a genuine feature need. Do not request it speculatively.

- **Xcode project location:** Currently in iCloud Drive (com~apple~CloudDocs).
  Creates git pull friction. Consider moving to ~/Developer/ post-cert season
  for a cleaner development environment.

---

## Cert Season Note

Active Swift development paused June 2026 during CompTIA cert prep:
- Network+ — June 22 – July 3, 2026 (Cyberkraft, live Zoom)
- Security+ — July 13 – July 24, 2026
- CySA+ — August 3 – August 14, 2026

Core Location and weather API implementation resume after certs.
Repo and README remain live and portfolio-active during the pause.
The cert knowledge maps directly onto the app’s implementation:
Network+ → the app’s network stack; Security+ → Keychain and ATS;
CySA+ → API input validation and threat modeling.
