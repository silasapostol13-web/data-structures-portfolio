# Debugging Journal

Interesting bugs I've hit, what caused them, and how I solved them.

---

## The hash that never matched

**Project:** Strut · **Symptom:** silent, no crash, no warning

### What was happening

Strut registers monitors with iOS to wake its extension on a schedule. Re-registering them is expensive and destructive — tearing down and rebuilding resets the day's accumulated usage counters, so a user who opened the app a few times would lose their screen-time history.

The fix was supposed to be simple: hash everything that affects what gets registered, and skip the whole pass if nothing changed. Compare the new hash to the stored one, bail if equal.

It never bailed. Every single launch, the hashes differed, and the app re-registered anyway. No error, no crash — just the exact behavior I'd written the check to prevent.

### What I assumed first

My first guess was that something in the hashed inputs genuinely was changing — active hours drifting, or a timestamp sneaking into the payload. I logged each input separately and compared across launches. All identical. The inputs matched and the hashes didn't, which made no sense.

### The actual cause

The app selections are stored as `Set`s of opaque tokens, and I was hashing the encoded selection directly.

Swift seeds `Set` hashing randomly per process. The iteration order is deliberately different every launch — it's a defense against hash-flooding attacks. So encoding the same set produced the same *elements* in a different *order*, and therefore different bytes, and therefore a different hash.

The inputs were identical. The serialization wasn't.

### The fix

Canonical serialization. Encode each token individually, sort the resulting byte blobs, then hash the sorted sequence — with a separator byte between entries so `[ab, c]` and `[a, bc]` can't produce the same input.

The pipeline is: encode each token, sort the byte blobs, join them with a `0x1F` separator, then SHA-256 the result.

The separator matters more than it looks. Without it, concatenation is ambiguous and two genuinely different selections could hash the same, which would cause the opposite bug — skipping a registration that actually needed to happen.

### What I took from it

Two things.

Anything hashed for comparison across processes or launches needs a canonical form. "Encode it and hash the bytes" quietly assumes the encoding is deterministic, and for unordered collections it isn't.

And this bug produced no error of any kind. It just did the expensive thing forever while the code that was supposed to stop it sat there looking correct. Those are much harder to find than crashes, because nothing points at them — I only caught it because I noticed users losing history that should have been preserved. Now I check that guard clauses actually fire rather than assuming they do.
