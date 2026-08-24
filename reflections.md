# Reflections

Problems I hit that had no documented answer, and what I learned working them out.
These are the parts worth writing down — usually because the first approach was wrong
in a way that took a while to see.

Project summaries live in [Projects](projects.md).

---

## Strut

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
