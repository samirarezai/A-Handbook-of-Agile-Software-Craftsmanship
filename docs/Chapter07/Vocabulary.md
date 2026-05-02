# Chapter 7 - Vocabulary

Short definitions and common synonyms for words that appear in *Clean Code*, Chapter 7 (*Error Handling*), or that capture its techniques and warnings.

---

## A

**Abort** (execution)  
*Meaning:* Stop the normal forward path - a `try` says execution may **abort** and resume in `catch`.  
*Synonyms:* cut short, halt, unwind.

**Ad infinitum**  
*Meaning:* Without end - a checked exception can force signature changes up the call stack **ad infinitum**.  
*Synonyms:* endlessly, recursively up the tree, all the way up.

---

## C

**Cascade** (of signature changes)  
*Meaning:* A low-level `throws` change that ripples upward so many modules rebuild though their own logic did not change.  
*Synonyms:* ripple, domino effect, propagation.

**Clutter**  
*Meaning:* Noise that hides intent - error handling that **clutters** the caller hides the main algorithm.  
*Synonyms:* obscure, bury, swamp.

---

## D

**Dominated** (codebase)  
*Meaning:* Error paths so pervasive that you can barely see what the happy path does - not that errors are all you do, but that they **scatter** across the design.  
*Synonyms:* overwhelmed, overshadowed, saturated.

---

## E

**Encapsulation** (broken by checked exceptions)  
*Meaning:* Callers should not need low-level failure types - forcing `throws` through every layer **breaks encapsulation** because intermediate code must know details it cannot act on.  
*Synonyms:* leaky abstraction (related), wide coupling.

---

## F

**Foist**  
*Meaning:* Impose unwelcome work - returning null **foists** null checks on every caller.  
*Synonyms:* impose, saddle, offload (onto others).

---

## O

**Obscures**  
*Meaning:* Covers up - error handling that **obscures** logic is wrong even if errors matter.  
*Synonyms:* hides, clouds, masks.

---

## R

**Robust**  
*Meaning:* Stands up to real failure - the goal is code that is clean **and** **robust**, not one at the expense of the other.  
*Synonyms:* resilient, fault-tolerant, dependable.

---

## S

**Scatter**  
*Meaning:* Spread thinly everywhere - **scattered** error handling makes the system hard to read.  
*Synonyms:* diffuse, spread out, peppered.

---

## T

**Tangled**  
*Meaning:* Interwoven so you cannot separate concerns - return codes **tangle** shutdown logic with error checks.  
*Synonyms:* intertwined, mixed together, knotted.

---

## U

**Unadorned**  
*Meaning:* Plain, without ceremony - good separation leaves the main algorithm reading like an **unadorned** sequence of steps.  
*Synonyms:* bare, straightforward, clean line of flow.

---

## V

**Valiant**  
*Meaning:* Brave but perhaps futile - C++ had **valiant attempts** at checked exceptions; the chapter argues the cost story still matters.  
*Synonyms:* earnest, determined, heroic (informal).

**Vendor** (API)  
*Meaning:* Third-party supplier - wrapping a **vendor** API lets you reshape exceptions and types to your app’s needs.  
*Synonyms:* third-party, external library provider.

---

## Phrases (chapter-specific)

**Exceptions vs return codes**  
*Meaning:* Prefer throwing when something fails so callers are not forced to check flags or codes after every call - separates **algorithm** from **recovery**.  
*Related:* fail fast, non-local exit.

**Try as transaction**  
*Meaning:* Treat `try` like a transaction boundary - `catch` (and `finally`) must leave the program in a **consistent** state. Writing `try`/`catch`/`finally` first clarifies that contract.  
*Related:* write tests that force exceptions, then fill the try scope.

**Checked vs unchecked exceptions**  
*Meaning:* Checked exceptions compile into signatures and catch obligations; the chapter argues they often violate **Open/Closed** at scale, while C#, Python, Ruby, and much C++ code stays robust without them. Prefer **unchecked** for most application code.  
*Related:* encapsulation, signature cascade.

**Context in exceptions**  
*Meaning:* Messages (and causes) should say **what operation** failed and **how** - stack traces show where, not intent.  
*Related:* chained causes, logging fields.

**Exception types from the caller’s view**  
*Meaning:* If every failure is handled the same (`reportPortError` + log), many catch branches are duplication - wrap the vendor API and translate to **one** application exception type (`PortDeviceFailure`) that matches how you actually recover.  
*Related:* anti-corruption layer, facade.

**Special Case Pattern**  
*Meaning:* Return an object that represents the “missing” case (`MealExpenses` that yields per diem) so callers stay free of `catch` clutter.  
*Related:* Null Object, default object.

**Don’t return null**  
*Meaning:* Null forces defensive checks and one missed check fails at runtime - return empty collections, special-case objects, or throw.  
*Related:* Optional (language feature), empty list.

**Don’t pass null**  
*Meaning:* Accidental null arguments are hard to recover from meaningfully - forbid null parameters by convention; use assertions only as documentation, not as a full fix.  
*Related:* fail-fast API contract.

**Wrapper for third-party APIs**  
*Meaning:* Isolate foreign exceptions, types, and design choices - easier test doubles, future library swaps, and cleaner app-level types.  
*Related:* boundary layer, adapter.
