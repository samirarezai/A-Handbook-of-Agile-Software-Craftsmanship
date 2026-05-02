# Chapter 5 - Vocabulary

Short definitions and common synonyms for words that appear in *Clean Code*, Chapter 5 (*Formatting*), or that capture its metaphors and advice.

---

## A

**Affinity** (conceptual)  
*Meaning:* Some parts of code “belong” together because they share responsibility, naming, or call patterns - they should sit close vertically.  
*Synonyms:* kinship, grouping pull, logical neighborhood.

**Agglomeration**  
*Meaning:* A confused mass of facts or details - a file that reads like one long undifferentiated blob.  
*Synonyms:* jumble, heap, unstructured pile.

**Attenuated**  
*Meaning:* Shortened or reduced for illustration - the author *attenuated* `TestSuite` to make a point about misplaced instance variables.  
*Synonyms:* abridged, trimmed, condensed.

---

## B

**Bevy**  
*Meaning:* A group, often informal - code that looks like it was written by a *bevy* of drunken sailors.  
*Synonyms:* crowd, bunch, gang (informal).

**Broad-brush**  
*Meaning:* High-level, without fine detail - the top of a file should give *broad-brush* concepts first, like a lede paragraph.  
*Synonyms:* big-picture, coarse summary, overview.

---

## C

**Conjoined**  
*Meaning:* Joined together - the function name and `(` should feel *conjoined*, not separated like unrelated tokens.  
*Synonyms:* attached, coupled visually, unified.

**Conceit** (personal cleverness)  
*Meaning:* A clever trick mainly for the author - tight `sqrt` loop bounds can be a *conceit* if others pay readability cost.  
*Synonyms:* affectation, clever-for-its-own-sake (informal).

---

## D

**Disabused**  
*Meaning:* Freed from a false belief - the book hopes you are *disabused* of “getting it working” as the only priority.  
*Synonyms:* corrected, undeceived, set straight.

**Disjoint**  
*Meaning:* Separated, not reading as one unit - extra spaces can make name and argument list look *disjoint*.  
*Synonyms:* detached, disconnected.

---

## E

**Extensibility**  
*Meaning:* How easy the system is to grow and change - readability and style affect *extensibility* long after feature code is gone.  
*Synonyms:* evolvability, growth-friendly design (informal).

---

## G

**Gestalt** / **gist**  
*Meaning:* Overall impression or main idea - vertical ordering lets you catch the *gist* from the first few functions.  
*Synonyms:* essence, big picture, scan-level understanding.

---

## H

**Hollerith**  
*Meaning:* Punch-card era - the “80 column” limit traces to *Hollerith* card width; the chapter treats 80 as conventional, not sacred.  
*Synonyms:* (historical reference only).

---

## I

**Impenetrable**  
*Meaning:* Impossible to understand without extreme effort - unindented code is virtually *impenetrable*.  
*Synonyms:* unreadable, opaque, inscrutable.

**Inappropriately** (low-level)  
*Meaning:* At the wrong layer of abstraction - hiding a well-known constant inside a function that should not own that knowledge.  
*Synonyms:* misplaced, wrongly scoped.

---

## M

**Minutia** / **minutiae**  
*Meaning:* Small precise details - newspaper articles move from headline to *minutiae*; source files should deepen detail downward.  
*Synonyms:* fine points, trivia (neutral), granular facts.

---

## O

**Obscuring**  
*Meaning:* Hiding structure - removing blank lines has an *obscuring* effect on readability.  
*Synonyms:* clouding, masking, muddying.

**Orderliness**  
*Meaning:* Neat, consistent arrangement - readers infer professionalism from *orderliness* under the hood.  
*Synonyms:* tidiness, discipline, coherence.

---

## P

**Pervades**  
*Meaning:* Spreads through the whole - sloppy formatting suggests inattention *pervades* the project.  
*Synonyms:* saturates, runs through, taints (informal).

**Polluting** (detail)  
*Meaning:* Smearing low-level noise into high-level narrative - put important ideas first with the least *polluting* detail.  
*Synonyms:* cluttering, contaminating, obscuring.

**Precedence** (operators)  
*Meaning:* Which operations bind tighter - horizontal space can echo operator *precedence* in formulas.  
*Synonyms:* binding priority, order of operations.

**Profound**  
*Meaning:* Deep, far-reaching - readability has a *profound* effect on future changes.  
*Synonyms:* deep, significant, lasting.

---

## R

**Rat's nest**  
*Meaning:* Tangled mess - hopping functions in a *rat's nest* layout wastes mental energy on navigation.  
*Synonyms:* maze, tangle, spaghetti (informal).

**Regularity**  
*Meaning:* Even, predictable pattern - line length distributions show striking *regularity* around ~45 characters in sampled Java projects.  
*Synonyms:* consistency, uniformity.

---

## S

**Scissors rule**  
*Meaning:* Old C++ convention placing instance variables at the *bottom* of the class (contrast with common Java top placement).  
*Synonyms:* (historical layout rule).

**Sigma** (box plot)  
*Meaning:* Spread measure - figure captions describe file length boxes using sigma/2 around the mean (informal visualization, not strict normality).  
*Synonyms:* standard deviation (loose usage in chapter).

---

## V

**Virtually**  
*Meaning:* Almost completely - without indentation, programs are *virtually* unreadable.  
*Synonyms:* practically, for all intents, nearly.

---

## Phrases (chapter-specific)

**Newspaper metaphor**  
*Meaning:* A source file should read top-down like an article: headline (name), lede (high level), then increasing detail, with small “articles” (small files) when possible.  
*Related:* vertical ordering, stepdown reading.

**Vertical openness**  
*Meaning:* Blank lines separate distinct thoughts or declarations so the eye catches group boundaries.  
*Related:* paragraph breaks in code.

**Vertical density**  
*Meaning:* Lines that belong together (tight concept) should not be separated by noise - remove useless comments that break variable groups.  
*Related:* affinity, locality.

**Vertical distance**  
*Meaning:* Related concepts in one file should be close; separation should reflect weaker relatedness - avoid hop-scotch reading.  
*Related:* G10 (proximity rule in book).

**Dependent functions**  
*Meaning:* Callee below caller when possible so readers read down into detail after seeing the call site.  
*Related:* top-down flow, newspaper ordering.

**Vertical ordering**  
*Meaning:* Call dependencies point downward; high-level first, details last - opposite of Pascal-style define-before-use constraint.  
*Related:* stepdown, newspaper metaphor.

**Horizontal openness and density**  
*Meaning:* Space around `=` separates major sides of assignment; no space between function name and `(` keeps function+args one unit; space can echo math precedence.  
*Related:* readability, formatter limitations.

**Horizontal alignment (columns of names)**  
*Meaning:* Lining up variable names or rvalues in columns - the chapter argues it often emphasizes the wrong axis and fights formatters; long declaration lists signal a class-splitting problem.  
*Related:* rvalue, declaration list smell.

**Dummy scope**  
*Meaning:* An empty loop body (often a lone `;`) - indent the semicolon on its own line so the “empty body” is visible.  
*Related:* while-loop semicolon trap.

**Team rules**  
*Meaning:* Pick one shared formatting style, encode it in the IDE formatter, and comply - consistency beats personal preference on a team.  
*Related:* collective ownership, style guide automation.
