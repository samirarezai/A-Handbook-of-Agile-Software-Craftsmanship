# Chapter 2: Meaningful Names

- [Home](../index.md)
- [Table of Contents](../SUMMARY.md)
- [Chapter 2 index](README.md)

<p align="center">
  <img src="../../assets/images/Chapter02/ch2.jpg" alt="Chapter 1" />
</p>

Names are everywhere in software, so naming well is not a nice-to-have. Good names take time, but they pay you back by making code easier to read, discuss, and change.

## Use intention-revealing names

A good name answers the big questions: why it exists, what it does, and how it’s used. If a name needs a comment to explain it, the name is not doing its job.

The chapter shows how a vague name like `d` tells you nothing, while something like `elapsedTimeInDays` instantly tells you meaning and unit.

```java
int d; // elapsed time in days
```

Better:

```java
int elapsedTimeInDays;
int daysSinceCreation;
int daysSinceModification;
int fileAgeInDays;
```

It also shows a small function that is “simple” but unclear because the context is hidden. Once you rename things (and remove magic numbers), the same logic becomes obvious without changing complexity.

Unclear version:

```java
public List<int[]> getThem() {
  List<int[]> list1 = new ArrayList<int[]>();
  for (int[] x : theList)
    if (x[0] == 4)
      list1.add(x);
  return list1;
}
```

Clearer version (better names, fewer magic numbers):

```java
public List<int[]> getFlaggedCells() {
  List<int[]> flaggedCells = new ArrayList<int[]>();
  for (int[] cell : gameBoard)
    if (cell[STATUS_VALUE] == FLAGGED)
      flaggedCells.add(cell);
  return flaggedCells;
}
```

Even clearer version (use a real type and hide details behind a method):

```java
public List<Cell> getFlaggedCells() {
  List<Cell> flaggedCells = new ArrayList<Cell>();
  for (Cell cell : gameBoard)
    if (cell.isFlagged())
      flaggedCells.add(cell);
  return flaggedCells;
}
```

## Avoid disinformation

Bad names leave false clues. Using words with strong existing meanings can trick readers into wrong assumptions (for example, naming something `accountList` when it is not actually a `List`).

Avoid names that are almost the same but differ in tiny ways. They look alike, they sort near each other, and they cause mistakes, especially with autocomplete.

Also avoid confusing single-character names like `l` and `O`. They can look like `1` and `0` and create bugs that are painful to spot.

```java
int a = l;
if ( O == l )
  a = O1;
else
  l = 01;
```

## Make meaningful distinctions (not fake ones)

Don’t change names just to satisfy the compiler. Names like `a1` and `a2`, or adding noise words like `Info`, `Data`, `Object`, or `the`, do not create real meaning.

If names must be different, they should mean something different. Otherwise readers waste time guessing what the distinction is supposed to be.

Number-series example:

```java
public static void copyChars(char a1[], char a2[]) {
  for (int i = 0; i < a1.length; i++) {
    a2[i] = a1[i];
  }
}
```

## Use pronounceable names

Programming is social. If nobody can pronounce a name, nobody can comfortably talk about it. Prefer real words over cryptic abbreviations so discussions stay clear and professional.

Hard-to-pronounce example:

```java
class DtaRcrd102 {
  private Date genymdhms;
  private Date modymdhms;
  private final String pszqint = "102";
  /* ... */
};
```

Better:

```java
class Customer {
  private Date generationTimestamp;
  private Date modificationTimestamp;;
  private final String recordId = "102";
  /* ... */
};
```

## Use searchable names

Single-letter variables and raw numbers are hard to search for. If a value matters across the codebase, give it a name. Searchable names beat magic constants because you can find every use and understand intent.

Single-letter names are acceptable only in very small scopes (like short loops inside short functions).

Hard-to-search example:

```java
for (int j=0; j<34; j++) {
  s += (t[j]*4)/5;
}
```

Better (named constants and clearer intent):

```java
int realDaysPerIdealDay = 4;
const int WORK_DAYS_PER_WEEK = 5;
int sum = 0;
for (int j=0; j < NUMBER_OF_TASKS; j++) {
  int realTaskDays = taskEstimate[j] * realDaysPerIdealDay;
  int realTaskWeeks = (realdays / WORK_DAYS_PER_WEEK);
sum += realTaskWeeks;
}
```

## Avoid encodings

Don’t encode type or scope into names. It adds decoding work and gets in the way of refactoring.

The chapter calls out Hungarian notation and member prefixes like `m_` as outdated in modern languages and IDEs, where types are enforced and editors already show scope clearly.

For interfaces, prefer clean names for the interface itself, and if you must add a suffix, put it on the implementation rather than on the interface.

Type-encoding example:

```java
PhoneNumber phoneString;
// name not changed when type changed!
```

Member prefix example:

```java
public class Part {
  private String m_dsc; // The textual description
  void setName(String name) {
    m_dsc = name;
  }
}
```

Better:

```java
public class Part {
  String description;
  void setDescription(String description) {
    this.description = description;
  }
}
```

Interface naming idea:

```java
// Prefer: ShapeFactory (interface) and ShapeFactoryImp (implementation)
// Avoid: IShapeFactory when it adds noise for readers
```

## Avoid mental mapping

Readers should not have to translate your names into what you “really meant.” Single-letter names outside tiny scopes force mental mapping and slow everyone down.

Clarity is a professional choice. Being “clever” with names is not a skill to show off.

## Class names and method names

- **Class names** should be nouns (like `Customer` or `AddressParser`) and avoid vague words like `Data`, `Info`, or `Manager` when they don’t add meaning.
- **Method names** should be verbs (like `save`, `deletePage`, `postPayment`). Use clear `get`, `set`, and `is` naming for accessors and predicates.
- When constructors are overloaded, use named factory methods that explain the arguments.

Factory method example:

```java
Complex fulcrumPoint = Complex.FromRealNumber(23.0);
```

Compared to:

```java
Complex fulcrumPoint = new Complex(23.0);
```

## Don’t be cute

Choose clarity over jokes and slang. “Funny” names are only funny while the joke is remembered, and they exclude people who don’t share the same context.

## Pick one word per concept, and don’t pun

Use one word for one concept and stick to it. Mixing near-synonyms like `fetch`, `get`, and `retrieve` across the same codebase creates friction and forces people to memorize style differences.

Avoid puns, using the same word for different meanings. If one method “adds” numbers and another “adds” an item to a collection, that’s not the same idea. Choose a word that matches the real action, like `insert` or `append`.

## Use solution-domain and problem-domain names

Your readers are programmers, so it’s fine to use CS terms, algorithm names, and pattern names when they fit. When there is no good technical term, use the domain language so maintainers can ask domain experts.

## Add meaningful context, but don’t add gratuitous context

Most names need context. Put them inside well-named classes, functions, or namespaces so their meaning is clear.

If you have `firstName`, `lastName`, `city`, and `state`, the set implies an address, but `state` alone is ambiguous. A dedicated `Address` type provides context better than a prefix on every variable.

But don’t overdo context. Prefixing everything with the app name (like `GSD`) creates noise and fights your tools. Add only as much context as needed.

Context example from the chapter:

```java
private void printGuessStatistics(char candidate, int count) {
  String number;
  String verb;
  String pluralModifier;
  if (count == 0) {
    number = "no";
    verb = "are";
    pluralModifier = "s";
  } else if (count == 1) {
    number = "1";
    verb = "is";
    pluralModifier = "";
  } else {
    number = Integer.toString(count);
    verb = "are";
    pluralModifier = "s";
  }
  String guessMessage = String.format(
    "There %s %s %s%s", verb, number, candidate, pluralModifier
  );
  print(guessMessage);
}
```

Better (give those variables a real home):

```java
public class GuessStatisticsMessage {
  private String number;
  private String verb;
  private String pluralModifier;

  public String make(char candidate, int count) {
    createPluralDependentMessageParts(count);
    return String.format(
      "There %s %s %s%s",
      verb, number, candidate, pluralModifier
    );
  }

  private void createPluralDependentMessageParts(int count) {
    if (count == 0) {
      thereAreNoLetters();
    } else if (count == 1) {
      thereIsOneLetter();
    } else {
      thereAreManyLetters(count);
    }
  }

  private void thereAreManyLetters(int count) {
    number = Integer.toString(count);
    verb = "are";
    pluralModifier = "s";
  }

  private void thereIsOneLetter() {
    number = "1";
    verb = "is";
    pluralModifier = "";
  }

  private void thereAreNoLetters() {
    number = "no";
    verb = "are";
    pluralModifier = "s";
  }
}
```

## Final words

Naming is hard because it requires good descriptive skill and a shared understanding. Renaming for the better is a form of improvement, and good tools make it safe. Try these rules, refactor names as you learn, and you’ll make code easier to read in the short term and easier to maintain in the long run.

