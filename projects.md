# Projects

## Strut

*[Live on the App Store](https://apps.apple.com/us/app/strut-walk-more-scroll-less/id6761438303)*

An iOS app that ties phone access to walking. You pick apps to block and a daily step goal; during your active hours those apps are shielded, and you earn them back by hitting your step pace. Streaks, screen-time history, and friend leaderboards on top.

**Stack:** Swift, ~61k lines. SwiftUI, HealthKit, FamilyControls / DeviceActivity / ManagedSettings, WidgetKit, ActivityKit, WatchConnectivity, StoreKit 2. Firebase backend. Eight Xcode targets running across five separate processes.

### You can't keep a step count fresh

The premise depends on knowing the user's step count the moment they try to open a blocked app. iOS gives you no way to guarantee that. HealthKit background delivery is throttled. Background tasks are discretionary — the system runs them when convenient, never when you need. DeviceActivity wakes your extension on schedule but caps you at 20 monitors total, so even the best case is coarse polling.

I stopped trying to keep it fresh and made the check lazy. The shield in front of each blocked app *is* the trigger — tapping Instagram runs the shield extension in its own process at that instant, which reads HealthKit directly and decides right there whether to lift. The count doesn't need to be current in the background, because the only moment its currency matters is the moment someone tries to get past the gate.

The user's attempt to break the rule is what causes the evaluation.

It also fixed something I wasn't aiming at: that read writes through to shared storage, so the main app, widgets, and Live Activity all pick up the fresh number. Enforcement and refresh became the same path.

### Reading data out of a one-way sandbox

Apple's report extension is the only place you can read raw Screen Time data, and it deliberately can't export anything — it renders numbers it won't hand you.

Usage thresholds, though, *do* fire a callback in a writable extension. So I register a 19-rung ladder on the tracked apps and record the highest rung crossed each day. It's a monotone step function: never the exact minute count, but a reliable lower bound that coarsens as usage climbs.

My first version had the report extension write into shared storage. It appeared to work and didn't — the report only runs while it's on screen, so history was whatever happened to be visible when you opened Stats. The ladder fires with the app closed. Exactness traded for reliability, which is the right trade when the alternative is nothing.

### Friend search in a database with no substring queries

Firestore has no `LIKE '%smi%'`. Partial-name search meant either pulling every user to the client or accepting exact match only.

The backend generates every 2-to-15-character prefix of every word in a user's name into a token set on their document, capped at 250. Firestore's `array-contains` then makes substring search a single indexed lookup, merged with range-based prefix queries and re-ranked.

A deliberate space-for-query-time trade — storage per user grows to make an otherwise impossible query fast at the index. The cap exists because the token set is unbounded in principle.

### What I'd change

Five processes share about 44 string-keyed values through App Group storage, with day-scoped keys reconstructed by hand in multiple places and eight `KEEP IN SYNC` comments marking the seams. Every one is a typo away from a bug that doesn't crash or warn — it silently reads zero.

It should be a typed shared framework with day-keying handled once. I avoided it because cross-target membership in this project layout is fragile, which is real but a one-time cost against a permanent tax.

---

## Dossier

*iOS, unreleased*

A personality assessment app for iPhone. Fully offline — no account, no backend, no network calls at any point.

**Stack:** Swift, SwiftUI, iOS 17+. Observation for state, Swift Concurrency (actors, async/await), StoreKit 2. Zero third-party dependencies.

### A result that changed every time you retook it

The app sorts users into one of 32 categories using a high/low split across five traits — a 5-bit key. The problem: all five cuts land at the population median, which is exactly where most people sit. A trait wobbling from 51 to 49 changes the answer.

I simulated 3,000 retakes at realistic measurement reliability and got 36% agreement. Two times out of three, a user retaking would be told something different about themselves.

Lengthening the test doesn't help. Agreement is (per-trait match)^5, so it's geometric in the number of cuts — doubling the question count only reaches ~53%. The instability is structural in the discretization, not in the measurement. I tried four alternative assignment designs; none beat 54%.

The fix was hysteresis on each bit: a trait holds its previous side unless the new score crosses the midpoint by more than a 20-point deadband. I picked the width from a measured table (0 → 36%, 8 → 55%, 20 → 76%, 25 → 86%) weighed against how often it still responds to genuine change. 20 because the failure modes aren't symmetric — a retake returning a different answer destroys trust, while a lagging label is rare and self-evident, since the underlying scores always update immediately.

That made the result *state* rather than a pure function of the scores, which had knock-on effects: the resolved ID has to persist, has to survive a retake deliberately, and has to travel in the share payload so two devices agree. Three tests cover it, including one that re-runs the simulation and fails if agreement drops below 70%.

### Comparing two people with no server

Comparing profiles normally means a backend and user records. I wanted zero running cost, so the profile packs into bytes — version, five scores, category, length-prefixed name, checksum — bit-packed into Crockford Base32 with a hand-written 5-bit buffer encoder.

Crockford specifically because its alphabet omits I, L, O and U, so a code can't be misread when retyped or accidentally spell a word. The decoder folds ambiguous characters back before parsing, so a typo still resolves. The parser scans arbitrary pasted text for the prefix, so you can paste a whole message.

A URL would've been easier but needs a domain you keep paying for, and it rots. A code is inert text that survives every messaging app.

The checksum is a weak additive rolling sum, not a CRC. It catches typos, which is all it's for — it isn't tamper-proof.

### Testing a distribution instead of a value

Scores convert to percentiles via z-score and the normal CDF, which needs a population mean and SD per trait. My first version derived the SDs and got them far too tight — a third of simulated users landed in the bottom decile. Everyone was being told they were unusually low on everything.

Past fixing it, I wrote a test that generates 4,000 synthetic people, runs them through the real scoring pipeline, and asserts every decile holds between 4% and 17% of the population.

The obvious test is a fixture with known expected output. That would have passed happily with the broken norms, because the fixture would have been generated from them. Testing the distribution checks the thing that actually matters.

### What I'd change

About 900 lines of copy live in Swift source as static literals. Fixing a typo means a recompile and, once shipped, a review cycle. Completeness is enforced by a test and a force-unwrap rather than the type system, so a missing entry is a crash the compiler won't catch. It should be a bundled JSON resource decoded into the same types.

---

## Course Projects

Implementations from ITSC 2214, added as the semester goes on.
