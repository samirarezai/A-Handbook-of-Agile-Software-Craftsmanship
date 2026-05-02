# Chapter 2 - Vocabulary

Short definitions and common synonyms for words that appear in *Clean Code*, Chapter 2 (*Meaningful Names*), or that capture naming ideas the chapter stresses.

---

## A

**Abundant**  
*Meaning:* Present in large quantity (e.g. comments that still don’t fix unclear names).  
*Synonyms:* plentiful, copious, numerous, pervasive.

**Accessor**  
*Meaning:* In OOP/JavaBeans style, a method that reads a property (often `get…`).  
*Synonyms:* getter, read accessor, query method (loosely).

**Adorned** / **Unadorned**  
*Meaning:* “Decorated” with extra bits in the name (e.g. an `I` prefix on every interface). The book prefers interfaces *unadorned* so callers think in terms of the role, not “this is an interface type.”  
*Synonyms (adorned):* prefixed, decorated, suffixed with type noise.  
*Synonyms (unadorned):* plain, bare, simple name.

**Arbitrary**  
*Meaning:* Chosen without a principled reason - often to appease the compiler (`a1`, `klass`).  
*Synonyms:* random, capricious, unmotivated, haphazard.

---

## C

**Coin** (*to coin a phrase*)  
*Meaning:* To invent a new expression deliberately (the book coins “implicity” for how much context is missing from the code).  
*Synonyms:* originate, neologize, introduce (a term).

**Colloquialism**  
*Meaning:* Informal or slangy usage (`whack` for `kill`) that may not travel across teams or cultures.  
*Synonyms:* slang, informalism, vernacular expression.

**Contrivance**  
*Meaning:* Something artificially constructed; the book admits the `l` / `O` / `0` example may feel forced - but such code exists.  
*Synonyms:* fabrication, artificial example, strained setup.

**Copious**  
*Meaning:* Very many of something (e.g. hoping readers will read *copious* comments instead of fixing names).  
*Synonyms:* abundant, extensive, plentiful.

**Crutch**  
*Meaning:* A aid you lean on because something else is weak (Hungarian notation when compilers didn’t check types).  
*Synonyms:* prop, workaround, band-aid, mnemonic aid.

---

## D

**Disinformation** / **Disinformative**  
*Meaning:* Names that suggest the wrong thing (`accountList` when it isn’t a `List`; `hp` when readers think “Hewlett-Packard”).  
*Synonyms:* misdirection, false clue, misleading signal, red herring (loose).

---

## E

**Encoding** (in names)  
*Meaning:* Embedding type, scope, or role into the identifier (`m_`, `str`, `I` prefix) - extra “language” readers must decode.  
*Synonyms:* type prefixing, Hungarian-style tagging, decorated identifier.

**Entrenched**  
*Meaning:* Firmly fixed by habit or convention (words with *entrenched* meanings in computing).  
*Synonyms:* established, fixed, deep-rooted, conventional.

**Explicit**  
*Meaning:* Stated plainly in the code so readers need less outside context. Contrast **implicit**.  
*Synonyms:* clear, stated, manifest, self-documenting (informal).

---

## F

**Finality** (*with finality*)  
*Meaning:* In a decisive, finished way - renaming solves the `l`/`O` problem *with finality* instead of relying on fonts or docs.  
*Synonyms:* decisively, conclusively, once and for all, definitively.

**Frightfully**  
*Meaning:* To a very high degree (often British tone) - names *frightfully* similar in shape.  
*Synonyms:* extremely, terribly, very, exceedingly.

---

## G

**Gratuitous**  
*Meaning:* Unnecessary; added without real benefit (e.g. stuffing every class name with a product prefix).  
*Synonyms:* needless, superfluous, uncalled-for, redundant.

---

## H

**Hypotenuse**  
*Meaning:* Long side of a right triangle opposite the right angle - context for why `hp` is a tempting but *disinformative* abbreviation in some codebases.  
*Synonyms:* (geometry term; no everyday synonym.)

---

## I

**Implicit**  
*Meaning:* Unsaid; relying on knowledge not shown in the code (“what is `theList`? why index `0`? why `4`?”).  
*Synonyms:* unstated, assumed, tacit, implied.

**Impediment**  
*Meaning:* Something that slows understanding or change (type encodings when the language already carries types).  
*Synonyms:* obstacle, hindrance, drag, friction.

**Indistinct** / **Indistinguishable**  
*Meaning:* Hard to tell apart - noise suffixes (`Info`, `Data`) or pairs like `money` vs `moneyAmount` with no clear semantic gap.  
*Synonyms:* blurry, fuzzy, ambiguous, interchangeable-looking.

**Intention-revealing** (names)  
*Meaning:* Identifiers that answer *why* something exists, *what* it does, and *how* it’s used - without needing a comment.  
*Synonyms:* self-explanatory name, purpose-driven identifier, clear intent in the name.

---

## L

**Lexicon**  
*Meaning:* The set of words and meanings your codebase uses; a *consistent lexicon* means one word per abstract idea (`get`, not `get`/`fetch`/`retrieve` at random).  
*Synonyms:* vocabulary, naming scheme, controlled terminology.

---

## M

**Mutator**  
*Meaning:* A method that changes state (often `set…` in JavaBean style).  
*Synonyms:* setter, write accessor, modifier.

---

## O

**Opaque**  
*Meaning:* Hard to see into - meaning not visible at first glance (`number`, `verb` until you read the whole function).  
*Synonyms:* unclear, murky, obscure, unintelligible at a glance.

---

## P

**Predicate**  
*Meaning:* In naming/JavaBeans, a boolean query (`isPosted`, `hasChildren`) - often prefixed with `is`, `has`, `can`, etc.  
*Synonyms:* boolean accessor, query method, truth test (informal).

**Pronounceable**  
*Meaning:* Easy to say out loud so humans can discuss the concept in conversation and code review.  
*Synonyms:* speakable, articulable, mouth-friendly (informal).

**Pun** (in naming)  
*Meaning:* Reusing the same word for a *different* idea (`add` for arithmetic vs `add` for “insert into collection”) - humorous in language, confusing in APIs.  
*Synonyms:* double duty, overloaded word (natural language sense), equivocation (loose).

---

## S

**Scholar** (vs paperback model)  
*Meaning:* The book contrasts the “scholar’s job” to dig meaning out of a paper with the “paperback” model where the *author* must be clear - prefer the latter for code.  
*Synonyms:* researcher, academic reader (as stereotype).

**Semantics** / **Semantically**  
*Meaning:* What an operation *means*, not its spelling - two `add` methods must be *semantically* equivalent if they share the name.  
*Synonyms:* meaning, sense, behavior contract (informal).

**Skim**  
*Meaning:* To read quickly for gist; clean naming aims for code you can *skim*, not “intense study.”  
*Synonyms:* scan, browse, speed-read, glance through.

---

## U

**Unadorned**  
*Meaning:* Without extra letters marking “interface” vs “class”; see **Adorned**.  
*Synonyms:* plain, undecorated, suffix-free / prefix-free (for type role).

---

## Phrases (chapter-specific)

**Intention-revealing names**  
*Meaning:* Names that carry purpose, domain, and usage so comments are not required to explain them.  
*Related:* self-documenting code, “names are documentation.”

**Implicity** (*coined in the chapter*)  
*Meaning:* How much the code leaves implicit - readers must already know context the code does not state. Opposite of making things **explicit** through naming and structure.  
*Related:* missing context, “tribal knowledge” names.

**Disinformation (in names)**  
*Meaning:* Anything that points the reader toward the wrong mental model (type in name, misleading suffix, confusable characters).  
*Related:* false affordance, misleading identifier.

**Noise words**  
*Meaning:* Filler parts that differentiate spellings but not meaning (`Data`, `Info`, `Object` next to `Customer`).  
*Related:* meaningless distinction, low-signal tokens.

**Mental mapping**  
*Meaning:* The translation readers do in their heads (“`c` means the URL without scheme…”) - professional code minimizes this.  
*Synonyms (idea):* cognitive decode step, name-to-concept translation.

**Hungarian notation (HN)**  
*Meaning:* Prefixing variables with type or kind hints; useful when compilers didn’t enforce types; usually unnecessary in modern strongly typed languages.  
*Related:* Systems Hungarian vs Apps Hungarian (not split in depth in this chapter).

**Problem domain vs solution domain**  
*Meaning:* **Problem domain:** business/real-world words (what the customer calls things). **Solution domain:** CS terms, patterns, algorithms - use when every programmer already shares that vocabulary.  
*Related:* ubiquitous language (DDD, not named in chapter).

**Pick one word per concept**  
*Meaning:* One term per abstract operation across the codebase (`get`, not `get`/`fetch`/`retrieve` arbitrarily).  
*Related:* consistent lexicon, API vocabulary discipline.

**Don’t pun**  
*Meaning:* Don’t reuse the same word for a different semantic operation just to “match” other APIs (`add` vs `insert`/`append`).  
*Related:* homonym hazard, semantic drift in names.

**Gratuitous context**  
*Meaning:* Over-prefixing every symbol with product or module initials so names are long, redundant, and IDE-completion-hostile.  
*Related:* shortest clear name, YAGNI for naming.

**Searchable names**  
*Meaning:* Prefer names (and named constants) you can find with search tools; avoid magic numbers and ultra-short globals like `e` where grep is useless.  
*Related:* `WORK_DAYS_PER_WEEK` vs bare `5`.

**Accessor / mutator / predicate** (JavaBean naming)  
*Meaning:* `getName`, `setName`, `isPosted` - verbs and prefixes aligned with common Java conventions so readers recognize roles instantly.  
*Related:* JavaBeans spec, boolean naming.
