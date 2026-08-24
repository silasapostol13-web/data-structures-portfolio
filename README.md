# Data Structures Portfolio

Coursework and engineering write-ups from my Data Structures semester at UNC Charlotte,
plus the problems I hit building apps outside of class.

**Read it here → https://silasapostol13-web.github.io/data-structures-portfolio/**

## What's in it

**[Projects](projects.md)** — the design problems, what I tried, and what I'd change.

*From [Strut](https://apps.apple.com/us/app/strut-walk-more-scroll-less/id6761438303) (live on the App Store):*
- **You can't keep a step count fresh** — iOS guarantees you nothing about background
  execution, so the check was made lazy instead: the block screen itself is the trigger.
  The user's attempt to break the rule is what causes the evaluation.
- **Reading data out of a one-way sandbox** — getting screen-time data out of an API
  surface Apple designed so it can't leave.
- **Friend search in a database with no substring queries** — Firestore has no `LIKE`.

*From Dossier (also on the App Store):*
- **A result that changed every time you retook it**
- **Comparing two people with no server**
- **Testing a distribution instead of a value** — how do you write an assertion for
  something that isn't supposed to return the same answer twice?

**[Debugging journal](debugging.md)** — bugs worth remembering. Starts with a hash
comparison that never matched, which was silently destroying users' screen-time history
on every launch.

## Why it's public

Most of what I've learned came from problems with no documented answer, where the
interesting part is the reasoning rather than the final code. Writing them down is how
I check whether I actually understood the thing or just made the symptom go away.
