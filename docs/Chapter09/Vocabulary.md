# Chapter 9 - Vocabulary

Short definitions and common synonyms for words that appear in *Clean Code*, Chapter 9 (*Unit Tests*), or that capture testing craft ideas.

---

## B

**BUILD-OPERATE-CHECK**  
*Meaning:* A test structure: **build** data and context, **operate** on the system under test, **check** outcomes - makes intent obvious in readable tests.  
*Synonyms:* arrange-act-assert (AAA), given-when-then (naming variant).

---

## D

**Domain-specific testing language**  
*Meaning:* Small helpers (`makePages`, `submitRequest`, `assertResponseContains`) that wrap production APIs so tests read like specifications, not low-level calls.  
*Synonyms:* test API, testing DSL, fluent test helpers.

**Dual standard**  
*Meaning:* Test and production code both stay **clean**, but test-only code may trade **efficiency** for clarity (e.g. simple string concatenation in mocks) - never trade **cleanliness**.  
*Synonyms:* different performance bar, test-environment concessions.

---

## F

**F.I.R.S.T.**  
*Meaning:* **F**ast, **I**ndependent, **R**epeatable, **S**elf-validating, **T**imely - five properties of a healthy unit test suite.  
*Related:* test hygiene, CI feedback loop.

---

## G

**Given-when-then**  
*Meaning:* Naming style for test phases: **given** setup, **when** action, **then** assertion - improves scanability alongside BUILD-OPERATE-CHECK.  
*Synonyms:* BDD-style test names.

---

## T

**Three Laws of TDD**  
*Meaning:* (1) No production code without a **failing** unit test first. (2) Write only enough test to **fail** (not compiling counts). (3) Write only enough production code to **pass** that test.  
*Related:* red-green-refactor, tight feedback loop.

---

## Phrases (chapter-specific)

**Dirty tests**  
*Meaning:* Tests allowed to slip below production quality - they become hard to change, fail often for the wrong reasons, and teams eventually **delete** the suite; then production code rots from fear of change.  
*Related:* technical debt, test maintenance cost.

**Test code is first-class**  
*Meaning:* Tests deserve the same design, naming, and partitioning care as shipping code - bulk of tests can rival production size, so mess compounds quickly.  
*Related:* readability in tests.

**Single assert (guideline)**  
*Meaning:* Prefer one logical conclusion per test when a domain-specific language makes it natural; otherwise **minimize** asserts and keep **one concept** per test function.  
*Related:* single concept per test.

**Single concept per test**  
*Meaning:* Do not mix unrelated scenarios in one `testAddMonths`-style method - split so each test explains one behavior or edge case.  
*Related:* focused examples.

**Tests enable the -ilities**  
*Meaning:* Flexibility, maintainability, and reuse rise when tests remove **fear** of regression - you can refactor and improve design because behavior stays pinned.  
*Related:* safety net, regression guard.

**Mock / control time**  
*Meaning:* Replace real clocks and OS timing with test doubles so tests **step** time deterministically instead of waiting five seconds.  
*Related:* seams, test doubles.
