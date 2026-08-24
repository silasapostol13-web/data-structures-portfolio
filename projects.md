# Projects

Short descriptions of what I've built. The engineering problems behind them —
what broke, what I tried, and what I'd do differently — are in
[Reflections](reflections.md).

## Strut

*[Live on the App Store](https://apps.apple.com/us/app/strut-walk-more-scroll-less/id6761438303)*

An iOS app that ties phone access to walking. You pick the apps that eat your day and a
daily step goal; during your active hours those apps are shielded, and you earn them
back by hitting your step pace. Streaks, screen-time history, and friend leaderboards on
top.

**Stack:** Swift, SwiftUI, HealthKit, FamilyControls / DeviceActivity / ManagedSettings,
WidgetKit, ActivityKit, WatchConnectivity, StoreKit 2, Firebase. Eight Xcode targets
running across five separate processes.

Built and operated solo — design, engineering, backend, pricing, analytics and marketing.

→ Reflections: [keeping a step count fresh](reflections.md#you-cant-keep-a-step-count-fresh) ·
[reading out of a one-way sandbox](reflections.md#reading-data-out-of-a-one-way-sandbox) ·
[search without substring queries](reflections.md#friend-search-in-a-database-with-no-substring-queries)

## Dossier

An offline personality assessment app for iPhone. Five-trait model, results you can
compare with friends and family. No account, no backend, no network calls at any point —
comparison happens by passing a short text code between devices.

**Stack:** Swift, SwiftUI, iOS 17+. Observation for state, Swift Concurrency (actors,
async/await), StoreKit 2. Zero third-party dependencies.

→ Reflections: [a result that changed every retake](reflections.md#a-result-that-changed-every-time-you-retook-it) ·
[comparing two people with no server](reflections.md#comparing-two-people-with-no-server) ·
[testing a distribution instead of a value](reflections.md#testing-a-distribution-instead-of-a-value)

## Course Projects

Implementations from ITSC 2214, added as the semester goes on.
