# Chapter 6 - Vocabulary

Short definitions and common synonyms for words that appear in *Clean Code*, Chapter 6 (*Objects and Data Structures*), or that capture its contrasts and rules.

---

## A

**Abstraction** (data)  
*Meaning:* Expressing *what* coordinates or fuel mean (operations, percentages) without forcing callers to know *whether* storage is rectangular, polar, gallons, or something else.  
*Synonyms:* abstract model, hidden representation, essence without form.

**Admixture**  
*Meaning:* A careless blend of things that belong at different levels (dots, slashes, extensions, `File` objects) in one place.  
*Synonyms:* mishmash, mixture, jumble.

**Atomic** (operation)  
*Meaning:* Indivisible as a unit - set Cartesian coordinates **together** as one operation instead of tweaking `x` and `y` independently when that would break invariants.  
*Synonyms:* all-or-nothing update, transactional shape (informal).

---

## B

**Blithely**  
*Meaning:* Without enough thought - **blithely** adding getters and setters is one of the worst defaults.  
*Synonyms:* casually, unthinkingly, on autopilot.

---

## C

**Complementary** (definitions)  
*Meaning:* Two ideas that **complete** each other as opposites - objects vs data structures trade off which extension dimension is easy. (The book text sometimes says "complimentary"; in modern usage **complementary** is the usual spelling for this sense.)  
*Synonyms:* paired opposites, dual, yin-yang (informal).

---

## D

**Diametrically**  
*Meaning:* In complete opposition - procedural ease for new functions vs OO ease for new types are **diametrically** opposed tradeoffs.  
*Synonyms:* utterly opposed, at opposite poles.

**Dichotomy**  
*Meaning:* A split into two camps - the fundamental **dichotomy** between object-heavy and procedure-plus-data-style areas of a system.  
*Synonyms:* split, fork, two-way tradeoff.

**Dual-dispatch**  
*Meaning:* Technique (including **Visitor**) to add operations across a type hierarchy without a single giant `switch` - carries its own costs and can resemble procedural structure again.  
*Synonyms:* multiple dispatch (related), visitor pattern.

---

## H

**Hybrid** (object / data structure)  
*Meaning:* A type that mixes significant behavior with accessors that effectively **publish** fields - hard to extend in *either* direction (new functions or new shapes).  
*Synonyms:* Frankenstein type, half-and-half design, muddy boundary.

---

## I

**Innards**  
*Meaning:* Internal structure - Demeter says a module should not depend on the **innards** of objects it touches.  
*Synonyms:* internals, guts, implementation details.

---

## M

**Muddled**  
*Meaning:* Unclear - hybrids suggest authors are **muddled** about whether they need protection from functions or from types.  
*Synonyms:* confused, incoherent, fuzzy.

---

## P

**Polymorphic**  
*Meaning:* Behavior chosen by runtime type - `area()` lives on each shape instead of one `Geometry.area` inspecting types.  
*Synonyms:* virtual dispatch, subtype polymorphism.

**Prejudice** (without)  
*Meaning:* Bias for one style everywhere - mature developers choose per subproblem **without prejudice** toward "everything is an object."  
*Synonyms:* dogma-free, even-handed.

**Procedural** (code)  
*Meaning:* Functions operating on exposed data structures - easy to add **new functions**, harder to add **new types** without editing each function.  
*Synonyms:* function-centric, process-oriented code.

---

## Q

**Quintessential**  
*Meaning:* The purest example - a DTO with public fields and no methods is a **quintessential** data structure in the chapter’s sense.  
*Synonyms:* archetypal, classic form.

**Quasi-encapsulation**  
*Meaning:* Private fields plus bean accessors that still **leak** the data model - feels like OO hygiene but often adds no real boundary.  
*Synonyms:* nominal privacy, bean theater (informal).

---

## T

**Train wreck**  
*Meaning:* A long chain of calls like `a.getB().getC().getD()` - hard to read and often a Demeter smell when intermediates are **objects** that should hide structure.  
*Synonyms:* lawnmower chain, dot chain, long hop navigation.

---

## U

**Unmistakably**  
*Meaning:* Clearly, without ambiguity - an abstract `Point` API can **unmistakably** still be a data structure while hiding coordinates.  
*Synonyms:* clearly, unambiguously, plainly.

---

## Phrases (chapter-specific)

**Data abstraction**  
*Meaning:* Hide representation behind meaningful operations (`setCartesian`, `setPolar`, `getPercentFuelRemaining`) instead of leaking coordinate storage or tank shape.  
*Related:* serious interfaces, not getters-by-default.

**Objects vs data structures (anti-symmetry)**  
*Meaning:* **Objects** hide data and expose behavior - easy new types, harder new operations across all types. **Data structures** expose data and use external functions - easy new operations, harder new types without touching each function. Pick per area of the system.  
*Related:* expression problem, procedural vs OO tradeoff.

**Law of Demeter**  
*Meaning:* A method should only talk to: itself, arguments, objects it creates, and its own fields - not dig through returned objects’ interiors ("friends, not strangers"). Interpretation depends on whether nodes are true **objects** or passive **DTOs**.  
*Related:* train wreck, tell, don’t ask (related slogan).

**Hiding structure**  
*Meaning:* If you need a scratch file stream, ask a collaborator to **do** that (`createScratchFileStream`) instead of navigating five getters to build a path yourself.  
*Related:* feature at right level, avoid leaky navigation.

**Data Transfer Object (DTO)**  
*Meaning:* Public fields (or near-equivalent), no behavior - useful at boundaries (DB rows, wire messages), often first stage of translation into real domain objects.  
*Related:* record-like payload, message shape.

**Active Record**  
*Meaning:* Row-shaped structure with persistence helpers (`save`, `find`) - keep it a **data structure**; put business rules in separate objects that hide their data instead of bloating the record.  
*Related:* anemic domain model risk, hybrid smell.

**Feature Envy** (footnote)  
*Meaning:* Fowler smell - code that obsesses over another type’s data - related to misplacing behavior on DTOs/Active Records.  
*Related:* misplaced responsibility.
