# Chapter 10 - Vocabulary

Short definitions and common synonyms for words that appear in *Clean Code*, Chapter 10 (*Classes*), or that capture class-level design ideas.

---

## C

**Cohesion**  
*Meaning:* How strongly the pieces of a class belong together - methods that use the same instance fields are more cohesive; split when only subsets of fields cluster around subsets of methods.  
*Synonyms:* internal consistency, single logical whole.

---

## D

**Dependency Inversion Principle (DIP)**  
*Meaning:* Depend on **abstractions** (interfaces, abstract concepts), not on volatile **concrete** details - makes tests stable and limits blast radius when implementations change.  
*Related:* ports and adapters, test doubles.

---

## E

**Encapsulation (pragmatic)**  
*Meaning:* Prefer `private` fields and helpers, but **tests win** - same-package tests may justify `protected` or package visibility after trying to keep privacy first.  
*Synonyms:* information hiding (related), visibility boundary.

---

## G

**God class**  
*Meaning:* A class that knows and does far too much - dozens of public methods, many unrelated concerns, hard to navigate and risky to change.  
*Synonyms:* blob class, monolith class (informal).

---

## O

**Open-Closed Principle (OCP)**  
*Meaning:* Open for **extension** (new types, new subclasses), closed for **modification** of existing stable code - add `UpdateSql` without editing every other SQL class.  
*Related:* plugin-style design, composition.

---

## S

**Single Responsibility Principle (SRP)**  
*Meaning:* A class or module should have **one reason to change** - one axis of variation, one job; use it to judge class size by **responsibilities**, not line count.  
*Related:* separation of concerns, reason to change.

**SRP violation (organizational clue)**  
*Meaning:* Private helpers that only make sense for one feature (e.g. `selectWithCriteria`) hint that the class may be doing more than one job - split when real change pressure appears.  
*Related:* feature envy within a class.

---

## W

**Weasel words (in class names)**  
*Meaning:* Vague suffixes like **Processor**, **Manager**, **Super** - often signal a dumping ground for unrelated responsibilities instead of a crisp concept.  
*Synonyms:* fuzzy aggregate name, grab-bag naming.

---

## Phrases (chapter-specific)

**Class organization (Java convention)**  
*Meaning:* Order: constants (`public static`), then `private static`, then `private` instance fields; then public methods; keep private helpers **just below** the public callers they support (newspaper / step-down reading).  
*Related:* readability, scoping.

**Count responsibilities, not lines**  
*Meaning:* Small classes are measured by **how many reasons they change**, not raw LOC - five methods can still be too big if the name needs “and.”  
*Related:* 25-word description rule (without *if / and / or / but*).

**Organizing for change**  
*Meaning:* Structure classes so new features touch **few** places - prefer new types over reopening large classes that force full regression risk.  
*Related:* OCP, SRP.

**Isolate from change**  
*Meaning:* Interfaces and abstract types sit between clients and concrete APIs (exchanges, databases) so details can swap without rewriting core logic.  
*Related:* DIP, test seams.

**Maintaining cohesion results in many small classes**  
*Meaning:* Extracting functions often reveals hidden object boundaries - shared locals promoted to fields may really be a **new class** waiting to exist.  
*Related:* refactoring to patterns, feature extraction.
