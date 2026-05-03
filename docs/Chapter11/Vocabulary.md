# Chapter 11 - Vocabulary

Short definitions and common synonyms for words that appear in *Clean Code*, Chapter 11 (*Systems*), or that capture system-level design ideas.

---

## A

**Aspect-oriented programming (AOP)**  
*Meaning:* Modular **aspects** declare where behavior should attach so cross-cutting concerns (persistence, security, transactions) can be applied **consistently** without smearing the same policy through every class.  
*Synonyms:* aspects, cross-cutting modularity (informal).

---

## B

**BDUF (Big Design Up Front)**  
*Meaning:* Designing **everything** before implementing anything - harmful in software when it blocks learning and makes teams resist throwing away sunk-cost design. Not the same as healthy up-front design exploration.  
*Related:* iterative architecture, test-driven system design.

---

## C

**Cross-cutting concerns**  
*Meaning:* Policies like persistence or logging that **span** many types - tend to intersect domain objects at fine grain, which is why separate **framework** or **aspect** layers help.  
*Related:* AOP, decorators, non-invasive infrastructure.

---

## D

**Dependency Injection (DI)**  
*Meaning:* A class stays **passive** about constructing collaborators - dependencies arrive via **constructors** or **setters** while a **main** routine or **container** performs wiring.  
*Synonyms:* IoC applied to dependencies, wiring external to the domain object.

**Domain-Specific Language (DSL)**  
*Meaning:* A small language or fluent API so domain code reads like **prose** experts recognize - shrinks the gap between domain vocabulary and implementation.  
*Related:* testing DSLs (Chapter 9), declarative configuration.

---

## I

**Inversion of Control (IoC)**  
*Meaning:* Push **secondary** responsibilities (like resolving dependencies) out of an object into another mechanism so the object focuses on its core job (**SRP**).  
*Related:* DI, frameworks, lifecycle containers.

---

## L

**Lazy initialization / evaluation**  
*Meaning:* Construct or compute only when first needed - can speed startup but often **mixes** construction with use and hides **global** wiring decisions.  
*Related:* factories, DI container lazy beans.

---

## P

**POJO (Plain Old Java Object)**  
*Meaning:* Domain logic in simple types **without** entangling enterprise framework APIs - easier to test, evolve, and wrap with aspects or decorators.  
*Synonyms:* plain objects, framework-free core.

---

## S

**Separation of Main**  
*Meaning:* Push **construction and wiring** into `main` (or modules it calls) so the application assumes objects already exist - dependencies point **away** from `main` into the app.  
*Related:* composition root, factories on the “main side.”

---

## Phrases (chapter-specific)

**Russian doll of decorators**  
*Meaning:* Nested wrappers (DAO around domain, data source around JDBC) so the client calls one surface while policy layers stack outward - often driven by DI configuration.  
*Related:* Decorator pattern, Spring beans.

**Software physics**  
*Meaning:* Software has constraints, but radical architectural change stays **economically** feasible when concerns stay separated - unlike physical buildings mid-construction.  
*Related:* Kolence’s term in the book’s references.

**Test-drive the system architecture**  
*Meaning:* If domain logic lives in **POJOs** and infrastructure attaches **non-invasively**, architecture can grow from simple to sophisticated as stories demand - validated by tests like lower-level code.  
*Related:* evolutionary design, incremental scaling.
