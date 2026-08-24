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

*On the App Store*

An offline personality assessment app — five-trait model, with results you can compare
against friends and family. No account, no backend, no network calls: comparison works
by passing a short text code between devices.

**This one was an experiment, and the experiment was the point.** I wanted to know how
far a single, tightly-specified prompt could get on its own, so I gave Claude Code one
instruction and no follow-up conversation: build an app that feeds and converts itself,
with no backend of any kind so it needs no maintenance, and make it easy to share on
social media. I went to bed, and put the result on the App Store the next morning.

Every constraint in that prompt was deliberate. No backend means no running cost and
nothing to keep alive. Shareability was the growth mechanic. What I was testing was
whether the constraints could carry the design — whether specifying the *shape* of a
product precisely enough would produce something shippable without me steering it.

The implementation is the model's, not mine, and I'm not going to claim the engineering
decisions inside it as my own. What I own is the specification and the decision to ship.

**Stack:** Swift, SwiftUI, iOS 17+. No third-party dependencies.


## Course Projects

Implementations from ITSC 2214, added as the semester goes on.
