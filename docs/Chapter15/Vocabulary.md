# Chapter 15 - Vocabulary

Short definitions and common synonyms for words that appear in *Clean Code*, Chapter 15 (*JUnit Internals*), or that capture small-module refactoring ideas.

---

## B

**Boy Scout Rule**  
*Meaning:* Leave the campground (the codebase) **cleaner than you found it** — small, safe improvements whenever you touch a module.  
*Related:* incremental hygiene, no “later” pile.

---

## C

**ComparisonCompactor**  
*Meaning:* JUnit helper that **shortens** differing **expected** vs **actual** strings for failure messages, marking the differing region with **`[`** **`]`** and **`...`** when context is clipped.  
*Related:* assertion diff, context length.

**Context length**  
*Meaning:* How many characters of **shared** prefix/suffix neighborhood to keep visible around the highlighted difference.  
*Related:* startingContext / endingContext.

---

## D

**Defactored**  
*Meaning:* The book’s **intentionally worse** rewrite (Listing 15-3) that shows how readability collapses when structure and names erode.  
*Related:* cautionary contrast, not production style.

---

## S

**Suffix overlaps prefix**  
*Meaning:* When growing a common **tail** match would **eat into** the already-counted **head** match (strings like **`ab`** vs **`abbc`**); the compactor must **stop** extending the suffix before the regions collide.  
*Related:* overlapping match edge cases in Listing 15-1 tests.

---

## T

**Temporal coupling**  
*Meaning:* One function **only works** if another ran first (here: suffix scan depends on **prefix length**). If call order is wrong, you get subtle bugs.  
*Related:* merge into `findCommonPrefixAndSuffix`, or pass explicit parameters, or document order ruthlessly.

---

## Phrases (chapter-specific)

**Analysis vs synthesis**  
*Meaning:* First cluster **figures out** indices and lengths (prefix, suffix, overlap); later cluster **builds** the display string from those facts.  
*Related:* top-down reading order in the final listing.

**100% coverage as confidence, not proof of good tests**  
*Meaning:* Full line execution shows the authors exercised branches; it does not by itself prove tests read well as **specification**.  
*Related:* critique `ComparisonCompactorTest` as documentation.

**Refactoring can undo earlier refactors**  
*Meaning:* Extract/rename/invert, then **re-merge** when a better whole appears — convergence beats pride in the first idea.  
*Related:* iterative craft, not single-pass perfection.
