# Chapter 14 - Vocabulary

Short definitions and common synonyms for words that appear in *Clean Code*, Chapter 14 (*Successive Refinement*), or that capture refactoring-in-the-small ideas.

---

## A

**ArgumentMarshaler**  
*Meaning:* Small type that knows how to **parse**, **store**, and **expose** one kind of flag (`boolean`, `#` int, `*` string, `##` double, `[*]` string array, etc.) behind a shared interface.  
*Related:* strategy per argument, open for new marshalers.

---

## F

**Festering pile**  
*Meaning:* Working code that kept **accumulating** concerns (extra maps, flags, error state) until it became hard to touch safely.  
*Related:* rough draft, incremental mess.

---

## I

**Incrementalism**  
*Meaning:* Improve structure in **tiny** steps that keep the system **running** (TDD discipline), instead of one big risky rewrite.  
*Related:* Rubik's cube metaphor, baby steps.

---

## P

**Partitioning**  
*Meaning:* Putting **error messages**, **marshalers**, and **tests** in the right modules so each file has a clear job (SRP for files as well as classes).  
*Related:* Args vs. ArgsException split.

---

## S

**Schema (Args format string)**  
*Meaning:* Comma-separated entries like `l,p#,d*`: bare letter = boolean, `#` = integer, `*` = string, `##` = double, `[*]` = string array.  
*Related:* declarative mini-language for CLI.

**Successive refinement**  
*Meaning:* Like essay **drafts**: write working (even dirty) code, then **clean** it in passes; professional craft expects several passes, not one-shot perfection.  
*Related:* rough draft first, then polish.

---

## Phrases (chapter-specific)

**Dirty code first**  
*Meaning:* You are not expected to ship elegant code in one pass; you **are** expected to circle back and improve structure once behavior is pinned by tests.  
*Related:* shameless green, then refactor.

**Three places per new flag type**  
*Meaning:* Adding a type touched **schema parsing**, **CLI parsing/set**, and **typed getters** - duplication across those spots signaled the need for marshalers.  
*Related:* triangulation of change points.

**Tests as safety net (Args)**  
*Meaning:* JUnit plus FitNesse-style acceptance tests let the author **shuffle** structure for tens of steps while staying confident behavior stayed put (including surprises like `getBoolean` on non-boolean flags).  
*Related:* whole-suite confidence.
