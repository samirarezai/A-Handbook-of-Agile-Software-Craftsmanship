# Chapter 8 - Vocabulary

Short definitions and common synonyms for words that appear in *Clean Code*, Chapter 8 (*Boundaries*), or that capture integration ideas.

---

## B

**Bogged down**  
*Meaning:* Stuck in slow, painful work - mixing **learning** a library with **integrating** it in production often leaves teams **bogged down** in long debug sessions.  
*Synonyms:* mired, stuck, thrashing.

**Broad applicability**  
*Meaning:* Framework authors widen APIs to serve many users - that **broad** surface becomes a **liability** at your boundary if you leak it everywhere.  
*Synonyms:* general-purpose design, wide surface area.

---

## D

**Doubly hard**  
*Meaning:* Learning third-party behavior and wiring production at once is **harder than the sum** of the parts - split learning into tests first.  
*Synonyms:* twice the risk, compounded difficulty.

---

## I

**Inhibit**  
*Meaning:* Hold back - huge `Map` usage can **inhibit** adopting Java 5 generics because the ripple of edits is too large.  
*Synonyms:* block, deter, slow adoption.

---

## L

**Liability**  
*Meaning:* Risk or burden - `Map`'s power can be a **liability** if callers can call `clear()` when you meant "read-only sensor view."  
*Synonyms:* downside, risk surface, foot-gun (informal).

---

## M

**Mists** (metaphor)  
*Meaning:* Uncertainty beyond the boundary - “mists and clouds of ignorance” hide what the other team will ship.  
*Synonyms:* fog, unknowns, unclear future API.

---

## S

**Seam**  
*Meaning:* A place you can swap implementations - your `Transmitter` interface plus `FakeTransmitter` gives a **test seam** before the real API exists.  
*Synonyms:* joint, plug point, substitution boundary.

---

## T

**Tension**  
*Meaning:* Pull between provider goals (wide reuse) and consumer goals (narrow needs) - healthy to resolve with **wrappers** and **adapters**.  
*Synonyms:* conflict, tradeoff, opposing pressures.

---

## Phrases (chapter-specific)

**Boundary interface**  
*Meaning:* A type from outside your system (`Map`, log4j `Logger`, vendor SDK) - keep it **inside** a small area; do not thread it through public APIs.  
*Related:* anti-corruption layer (DDD term, same spirit).

**Learning tests** (Jim Newkirk)  
*Meaning:* Small experiments that call the third-party API the way you plan to use it - they document behavior, speed learning, and catch breaking vendor releases.  
*Related:* characterization tests, spike in test harness.

**Adapter pattern**  
*Meaning:* Convert your application’s ideal interface to the vendor’s real one (`TransmitterAdapter`) so changes stay localized.  
*Related:* wrapper, facade (overlaps).

**Using code that does not yet exist**  
*Meaning:* Define the interface **you wish you had**, code against it, then bridge with an adapter when the real subsystem arrives - avoids blocking and keeps client code expressive.  
*Related:* fake, stub, seam-based testing.

**Fake / test double at boundary**  
*Meaning:* `FakeTransmitter` stands in for hardware or remote APIs so `CommunicationsController` tests stay fast and deterministic.  
*Related:* mock, stub, seam.

**Encapsulate the Map**  
*Meaning:* Hide `Map` (or any wide boundary type) inside `Sensors` (or similar) so casting, generics, and allowed operations live in one place.  
*Related:* narrow public surface, hide third-party types.
