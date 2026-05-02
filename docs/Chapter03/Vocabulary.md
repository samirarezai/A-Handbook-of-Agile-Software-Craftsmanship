# Chapter 3 — Vocabulary

Short definitions and common synonyms for words that appear in *Clean Code*, Chapter 3 (*Functions*), or that capture the chapter’s rules and metaphors.

---

## A

**Abstraction** (level of)  
*Meaning:* How “high-level” or “low-level” a statement is (`getHtml()` vs `.append("\n")`). Mixing levels in one function confuses readers.  
*Synonyms:* conceptual altitude, granularity of intent.

**Accrete**  
*Meaning:* To build up gradually in layers—once details mix with big ideas in a function, *more* details tend to *accrete* there (like broken windows).  
*Synonyms:* accumulate, pile on, grow by accretion.

**Arcane**  
*Meaning:* Mysterious or understood only by insiders (“odd strings,” obscure APIs).  
*Synonyms:* esoteric, cryptic, recondite, obscure.

---

## B

**Bury** (the `switch`)  
*Meaning:* Hide implementation detail deep in a factory or low-level type so callers use polymorphism instead of repeated type switches.  
*Synonyms:* encapsulate, hide, isolate, confine.

---

## C

**Casual reader**  
*Meaning:* Someone who is not studying every line—your functions should still communicate intent to them.  
*Synonyms:* skimming reader, future maintainer, quick reviewer.

**Cohesion**  
*Meaning:* How naturally parts belong together (`x` and `y` for a point share one geometric idea; `outputStream` and `name` may not).  
*Synonyms:* natural grouping, belonging, unity of purpose.

**Command Query Separation (CQS)**  
*Meaning:* A function should either *do* something (command: change state) or *answer* something (query: return data)—not both in one ambiguous API.  
*Synonyms:* Bertrand Meyer’s query/command split (related idea).

**Compelling**  
*Meaning:* Drawing the reader forward—small functions in a *compelling order* so the next step feels natural.  
*Synonyms:* irresistible flow, convincing sequence.

**Conceptual power**  
*Meaning:* Mental bandwidth; arguments cost conceptual power to interpret and test.  
*Synonyms:* cognitive load, mental overhead.

---

## D

**Decompose** / **decomposition**  
*Meaning:* Break a larger idea (the function’s name) into named steps at the *next* level down—why we write functions at all.  
*Synonyms:* factor, split, refine into steps.

**Deleterious**  
*Meaning:* Harmful—an unlimited family of `switch`-like functions all share the same *deleterious* shape.  
*Synonyms:* damaging, detrimental, injurious.

**Dependency magnet**  
*Meaning:* A type (e.g. a central `Error` enum) that many modules import; every change forces widespread rebuild/redeploy and discourages adding real new cases.  
*Synonyms:* hub type, global coupling point, widespread import root.

**Divining**  
*Meaning:* Inferring meaning from indirect clues—readers should not have to *divine* what code does from Listing 3–1 style mess.  
*Synonyms:* infer, deduce, puzzle out, intuit (with effort).

**Documentary value**  
*Meaning:* When an `if` block is a single call, the *callee’s name* documents what the branch does.  
*Synonyms:* self-narrating structure, name-as-comment.

**Double-take**  
*Meaning:* A second look because something felt wrong—output arguments and triadic `assertEquals` cause *double-takes*.  
*Synonyms:* cognitive stumble, re-read moment, confusion pause.

---

## E

**Evocative**  
*Meaning:* A name that calls up a clear mental picture (`writeField(name)` is more *evocative* than `write(name)`).  
*Synonyms:* suggestive, vivid, expressive, telling.

---

## H

**Happy path**  
*Meaning:* The normal successful flow of logic—exceptions let you separate error handling from the *happy path*.  
*Synonyms:* success path, nominal case, mainline flow.

---

## I

**Inobvious**  
*Meaning:* Not easy to see or understand at a glance (strange types and APIs).  
*Synonyms:* unobvious, opaque, unclear.

**Insidious**  
*Meaning:* Harmful in a subtle, creeping way (some triadic forms are “not quite so insidious”).  
*Synonyms:* treacherous, sneaky, subtly dangerous.

**Intuit**  
*Meaning:* To grasp quickly without a long proof—good functions let a reader *intuit* the kind of system they live in.  
*Synonyms:* sense, pick up on, infer effortlessly.

---

## M

**Mitigate**  
*Meaning:* To lessen a problem—keyword-style names *mitigate* wrong-argument order (`assertExpectedEqualsActual`).  
*Synonyms:* alleviate, reduce, soften, offset.

---

## N

**Narrative**  
*Meaning:* Code read top-to-bottom like a story; each function leads to the next level of detail.  
*Synonyms:* storyline, prose flow, top-down telling.

**Niladic** / **Monadic** / **Dyadic** / **Triadic** / **Polyadic**  
*Meaning:* Arity jargon: zero, one, two, three, or many formal arguments. The book prefers fewer arguments; polyadic needs strong justification.  
*Synonyms:* nullary/unary/binary/ternary/n-ary (alternate CS terms).

---

## O

**Obscured**  
*Meaning:* Hidden from view—good structure in Listing 3–2 vs intent *obscured* in Listing 3–1.  
*Synonyms:* masked, clouded, buried.

**Order dependency**  
*Meaning:* Callers must invoke operations in a particular sequence or things break (often tied to hidden side effects).  
*Synonyms:* sequencing constraint, call-order coupling.

**Output argument**  
*Meaning:* A parameter used to return data to the caller instead of using the return slot—surprising and easy to misread (`appendFooter(s)`).  
*Synonyms:* out parameter, in-out confusion source.

---

## P

**Polymorphism**  
*Meaning:* Dispatch behavior through types/interfaces so repeated `switch` on type is replaced by virtual methods on `Employee` (etc.).  
*Synonyms:* subtype dispatch, virtual call (language-dependent).

---

## S

**Scads**  
*Meaning:* Informal: a large number (“scads of functions” in the 100–300 line range).  
*Synonyms:* heaps, loads, tons, many.

**Side effect**  
*Meaning:* Hidden work beyond what the name promises (e.g. `checkPassword` that also initializes a session)—a “lie” that creates coupling.  
*Synonyms:* hidden mutation, covert state change, collateral behavior.

**Stepdown Rule**  
*Meaning:* Arrange functions so reading the program moves *down* one level of abstraction at a time, like nested “TO …” paragraphs.  
*Synonyms:* top-down reading rule, narrative descent.

---

## T

**Temporal coupling**  
*Meaning:* Two things are coupled in *time*: one function must be called only when it is safe (e.g. after/before another), often hidden in side effects.  
*Synonyms:* ordering coupling, time-based coupling, call-window constraint.

**Transparently obvious**  
*Meaning:* Utterly clear at a glance—Kent Beck’s tiny Sparkle functions were two–four lines each and *transparently obvious*.  
*Synonyms:* crystal clear, immediately readable, self-evident.

**Trial and error**  
*Meaning:* Learning by trying and fixing mistakes—the author’s experience that small functions win came through long *trial and error*.  
*Synonyms:* empirical learning, iteration, experience-led refinement.

---

## W

**Wordsmith**  
*Meaning:* To edit text until it reads well—you *wordsmith* a draft; similarly you massage functions until they read cleanly while tests stay green.  
*Synonyms:* edit for clarity, polish prose, refine wording.

---

## Phrases (chapter-specific)

**TO paragraph**  
*Meaning:* Describe a function as a short paragraph starting with *TO* (from LOGO’s style): what it does in plain steps at one abstraction level, each step delegating to the next.  
*Related:* Stepdown Rule, intention-revealing structure.

**One level of abstraction per function**  
*Meaning:* All statements in a function sit at the same “height” of detail—no mixing HTML calls, path rendering, and string punctuation in one lump.  
*Related:* Small blocks, extract method.

**Do one thing**  
*Meaning:* Steps inside the function are exactly those *one level below* the function’s name—not unrelated chores, and not a mix of heights. If you can extract a function whose name is *more* than a restatement of the snippet, the original was doing more than one thing.  
*Related:* Single Responsibility (SRP), cohesion.

**Flag argument**  
*Meaning:* A boolean (or similar) that selects between two behaviors in one function—signals “this function does more than one thing”; prefer two functions or clearer API.  
*Synonyms (idea):* mode switch parameter, boolean mode flag.

**Function arguments (ideal arity)**  
*Meaning:* Prefer zero, then one, then two arguments; three is suspect; more needs exceptional justification. Arguments complicate tests and reading.  
*Related:* argument object, parameter object, reducing arity.

**Argument object**  
*Meaning:* When several parameters travel together (`x`, `y`, `radius`), replace with a named concept (`Point`, etc.) to shrink the parameter list and reveal intent.  
*Related:* value object, DTO (loosely).

**Keyword form** (function naming)  
*Meaning:* Encode argument roles into the name (`assertExpectedEqualsActual`) so order mistakes hurt less.  
*Related:* self-documenting call sites.

**Extract try/catch**  
*Meaning:* Move `try`/`catch` bodies into their own small functions so one function is about errors and another about the happy path.  
*Related:* error handling is one thing.

**Error handling is one thing**  
*Meaning:* If `try` appears in a function, it should usually be the first word and the function should do little else—separation of concerns for errors.  
*Related:* extract try/catch.

**Prefer exceptions to error codes**  
*Meaning:* Returning `E_OK` style codes nests `if` predicates and creates dependency magnets; exceptions separate failure handling and extend more cleanly (OCP angle).  
*Related:* happy path, Command Query Separation.

**Structured programming** (Dijkstra)  
*Meaning:* Single entry/exit, caution with `break`/`continue`/`goto`—the book is sympathetic but says tiny functions relax the strictness; `goto` still discouraged.  
*Related:* one return rule (softened here).

**DRY (Don’t Repeat Yourself)**  
*Meaning:* Repeated setup/teardown algorithm four times is four times the bug surface; deduplication clarifies the module.  
*Synonyms:* deduplication, elimination of repetition.

**Domain-specific language (DSL)**  
*Meaning:* The hierarchy of well-named functions (verbs) and classes (nouns) you build *inside* the host language to tell the system’s story—not naive mapping from a requirements doc.  
*Related:* fluent API, internal language, storytelling code.
