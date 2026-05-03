# Chapter 12 - Vocabulary

Short definitions and common synonyms for words that appear in *Clean Code*, Chapter 12 (*Emergence*), or that capture emergent / simple-design ideas.

---

## E

**Emergent design**  
*Meaning:* Good structure surfaces from **tight feedback**: tests, small steps, and refactoring—rather than from a big upfront blueprint alone.  
*Related:* simple design, TDD, refactoring.

**Expressiveness (of code)**  
*Meaning:* Code that states **intent** clearly so maintainers spend less time decoding surprises—names, small units, standard pattern vocabulary, and tests as documentation.  
*Synonyms:* clarity, readable design.

---

## M

**Minimal classes and methods**  
*Meaning:* Kent Beck’s **fourth** simple-design rule—avoid pointless sprawl of types and members—but only **after** tests, no duplication, and expressiveness; push back on dogmatic “interface per class” or artificial data/behavior splits.  
*Related:* pragmatism vs. dogma.

---

## R

**Runs all the tests**  
*Meaning:* The **first** simple-design rule: behavior is **verified**; untestable systems are not deployable in good conscience. Driving testability encourages **SRP**, **DIP**, injection, and abstractions—lower coupling, higher cohesion.  
*Related:* continuous integration, test pyramid.

---

## S

**Simple Design (four rules)**  
*Meaning:* Kent Beck’s rules, **in order of importance**: (1) **runs all the tests**, (2) **contains no duplication**, (3) **expresses the intent of the programmer**, (4) **minimizes the number of classes and methods**. The chapter treats (2)–(4) as what you enforce while **refactoring** behind a test suite.  
*Source:* **[XPE]** (*Extreme Programming Explained*).

---

## D

**Duplication (as primary enemy)**  
*Meaning:* Repeated logic, structure, or **concept** increases work, risk, and complexity—squeeze it even when small; small extractions often expose **SRP** violations and enable reuse.  
*Related:* DRY, Template Method.

---

## T

**Template Method pattern**  
*Meaning:* Base class defines an algorithm **skeleton**; subclasses override **hooks** for the varying steps—removes higher-level duplication when only a fragment differs.  
*Synonyms:* fixed algorithm + variable steps.  
*See:* **[GOF]**.

---

## Phrases (chapter-specific)

**Reuse in the small**  
*Meaning:* Extracting tiny shared pieces elevates visibility and can unlock **larger** reuse—essential path toward reuse at scale.  
*Related:* refactoring, emerging abstractions.

**Refactoring with a safety net**  
*Meaning:* With tests, you can clean after each small change and **prove** nothing broke—fear drops, design can improve continuously.  
*Related:* Rule 1 enables Rules 2–4.
