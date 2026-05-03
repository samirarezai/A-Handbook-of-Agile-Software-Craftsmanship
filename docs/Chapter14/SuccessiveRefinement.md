# Chapter 14: Successive Refinement

- [Home](../index.md)
- [Table of Contents](../SUMMARY.md)
- [Chapter 14 index](README.md)

<p align="center">
  <img src="../../assets/images/Chapter14/ch14.jpg" alt="Clean Code - Successive Refinement" />
</p>

This chapter is a **case study** in **successive refinement**: a module that started in decent shape, **did not scale** when new argument types arrived, then was **refactored** step by step into something clean.

The vehicle is **Args**, a small **command-line argument** parser. Most of us have walked a `String[] args` by hand or used a library that almost fits. The author built **Args** to match his needs: pass a **schema** string plus `main`'s args, then query typed values by flag letter.

## Using Args

Schema example `"l,p#,d*"` means:

- `-l` is a **boolean** flag  
- `-p` takes an **integer**  
- `-d` takes a **string**

The second constructor parameter is the raw `args` array. If construction finishes without `ArgsException`, parsing succeeded; use `getBoolean`, `getInt`, `getString`, and friends. On failure, `ArgsException.errorMessage()` explains what went wrong.

### Listing 14-1: Simple use of Args

```java
 public static void main(String[] args) {
 try {
 Args arg = new Args("l,p#,d*", args);
 boolean logging = arg.getBoolean('l');
 int port = arg.getInt('p');
 String directory = arg.getString('d');
 executeApplication(logging, port, directory);
 } catch (ArgsException e) {
 System.out.printf("Argument error: %s\n", e.errorMessage());
 }
 }
```

## The clean end state (what we are aiming for)

The finished design centers on a **`Map<Character, ArgumentMarshaler>`**: one **marshaler** per schema letter. Parsing walks the CLI with a **`ListIterator`**, and each marshaler knows how to consume iterator input for its type. **`ArgsException`** (often in its own module) carries **`ErrorCode`**, argument id, and optional parameter for consistent error text.

The book shows the full **`Args.java`**, **`ArgumentMarshaler`**, concrete marshalers, and **`ArgsException`** across Listings **14-2** through **14-7** (and again after later refactors). Read those listings in the book for every line; below is the **spine** of `Args` so you can see top-to-bottom flow without jumping.

### Listing 14-2 (excerpt): `Args.java` core flow

```java
package com.objectmentor.utilities.args;
import static com.objectmentor.utilities.args.ArgsException.ErrorCode.*;
import java.util.*;

public class Args {
 private Map<Character, ArgumentMarshaler> marshalers;
 private Set<Character> argsFound;
 private ListIterator<String> currentArgument;

 public Args(String schema, String[] args) throws ArgsException {
 marshalers = new HashMap<Character, ArgumentMarshaler>();
 argsFound = new HashSet<Character>();
 parseSchema(schema);
 parseArgumentStrings(Arrays.asList(args));
 }

 private void parseSchema(String schema) throws ArgsException {
 for (String element : schema.split(","))
 if (element.length() > 0)
 parseSchemaElement(element.trim());
 }

 private void parseSchemaElement(String element) throws ArgsException {
 char elementId = element.charAt(0);
 String elementTail = element.substring(1);
 validateSchemaElementId(elementId);
 if (elementTail.length() == 0)
 marshalers.put(elementId, new BooleanArgumentMarshaler());
 else if (elementTail.equals("*"))
 marshalers.put(elementId, new StringArgumentMarshaler());
 else if (elementTail.equals("#"))
 marshalers.put(elementId, new IntegerArgumentMarshaler());
 else if (elementTail.equals("##"))
 marshalers.put(elementId, new DoubleArgumentMarshaler());
 else if (elementTail.equals("[*]"))
 marshalers.put(elementId, new StringArrayArgumentMarshaler());
 else
 throw new ArgsException(INVALID_ARGUMENT_FORMAT, elementId, elementTail);
 }

 private void validateSchemaElementId(char elementId) throws ArgsException {
 if (!Character.isLetter(elementId))
 throw new ArgsException(INVALID_ARGUMENT_NAME, elementId, null);
 }

 private void parseArgumentStrings(List<String> argsList) throws ArgsException {
 for (currentArgument = argsList.listIterator(); currentArgument.hasNext();) {
 String argString = currentArgument.next();
 if (argString.startsWith("-")) {
 parseArgumentCharacters(argString.substring(1));
 } else {
 currentArgument.previous();
 break;
 }
 }
 }

 private void parseArgumentCharacters(String argChars) throws ArgsException {
 for (int i = 0; i < argChars.length(); i++)
 parseArgumentCharacter(argChars.charAt(i));
 }

 private void parseArgumentCharacter(char argChar) throws ArgsException {
 ArgumentMarshaler m = marshalers.get(argChar);
 if (m == null) {
 throw new ArgsException(UNEXPECTED_ARGUMENT, argChar, null);
 } else {
 argsFound.add(argChar);
 try {
 m.set(currentArgument);
 } catch (ArgsException e) {
 e.setErrorArgumentId(argChar);
 throw e;
 }
 }
 }

 public boolean has(char arg) {
 return argsFound.contains(arg);
 }

 public boolean getBoolean(char arg) {
 return BooleanArgumentMarshaler.getValue(marshalers.get(arg));
 }
 public String getString(char arg) {
 return StringArgumentMarshaler.getValue(marshalers.get(arg));
 }
 public int getInt(char arg) {
 return IntegerArgumentMarshaler.getValue(marshalers.get(arg));
 }
 public double getDouble(char arg) {
 return DoubleArgumentMarshaler.getValue(marshalers.get(arg));
 }
 public String[] getStringArray(char arg) {
 return StringArrayArgumentMarshaler.getValue(marshalers.get(arg));
 }
}
```

### Listing 14-3: `ArgumentMarshaler.java`

```java
public interface ArgumentMarshaler {
 void set(Iterator<String> currentArgument) throws ArgsException;
}
```

### Listing 14-4: `BooleanArgumentMarshaler.java`

```java
public class BooleanArgumentMarshaler implements ArgumentMarshaler {
 private boolean booleanValue = false;
 public void set(Iterator<String> currentArgument) throws ArgsException {
 booleanValue = true;
 }
 public static boolean getValue(ArgumentMarshaler am) {
 if (am != null && am instanceof BooleanArgumentMarshaler)
 return ((BooleanArgumentMarshaler) am).booleanValue;
 else
 return false;
 }
}
```

*(**Listing 14-5** `StringArgumentMarshaler` mirrors the string case: read the next iterator token, map `MISSING_STRING`, and expose a static `getValue`.)*

### Listing 14-6: `IntegerArgumentMarshaler.java`

```java
import static com.objectmentor.utilities.args.ArgsException.ErrorCode.*;

public class IntegerArgumentMarshaler implements ArgumentMarshaler {
 private int intValue = 0;
 public void set(Iterator<String> currentArgument) throws ArgsException {
 String parameter = null;
 try {
 parameter = currentArgument.next();
 intValue = Integer.parseInt(parameter);
 } catch (NoSuchElementException e) {
 throw new ArgsException(MISSING_INTEGER);
 } catch (NumberFormatException e) {
 throw new ArgsException(INVALID_INTEGER, parameter);
 }
 }
 public static int getValue(ArgumentMarshaler am) {
 if (am != null && am instanceof IntegerArgumentMarshaler)
 return ((IntegerArgumentMarshaler) am).intValue;
 else
 return 0;
 }
}
```

### Listing 14-7: `ArgsException.java` (shape)

`ArgsException` carries **`errorArgumentId`**, **`errorParameter`**, and **`errorCode`**, offers several constructors, and implements **`errorMessage()`** with a **`switch`** on **`ErrorCode`** (`OK`, `UNEXPECTED_ARGUMENT`, `MISSING_STRING`, `INVALID_INTEGER`, `MISSING_INTEGER`, `INVALID_DOUBLE`, `MISSING_DOUBLE`, `INVALID_ARGUMENT_NAME`, `INVALID_ARGUMENT_FORMAT`, and related cases in later versions). The printed chapter shows the full **`switch`** body and **`enum ErrorCode`**.

The point of the layout: you can read **`Args`** from top to bottom with minimal lookahead. Adding a new type is mostly a new **`ArgumentMarshaler`**, a new **`getXxx`**, a new branch in **`parseSchemaElement`**, and usually a new **`ErrorCode`** plus message.

> Static typing makes Java verbose here; the author notes a Ruby rewrite landed near **one seventh** the size with slightly cleaner structure.

## How we got here (not in one sitting)

The author **did not** type Listing 14-2 in a single inspired session. **Programming is a craft**: write **dirty** code that works, then **clean** it. Same lesson as school essays: **rough draft**, revise, repeat.

### Rough draft and incremental pain

**Listing 14-8** (first draft) "works" but piles on maps, flags, `valid`, `ParseException`, inner `ArgsException`, and ad hoc error handling. **Listing 14-9** shows **boolean-only** code that was still compact. Adding **String**, then **Integer**, duplicated patterns in **three** places (schema parse, CLI parse/set, getters). That triplication is what **`ArgumentMarshaler`** replaces.

### So I stopped

Rather than bulldoze more types into the mess, the author **paused for structure** while tests stayed green (**JUnit** unit tests plus **FitNesse** acceptance tests).

### On incrementalism

Big-bang "improvements" often break programs for a long time. **TDD** discipline: **never** leave the system broken; each micro-step should leave behavior as verified as before.

### Marshaler emergence

Early steps introduced a **`BooleanArgumentMarshaler`** skeleton and moved one map to **`ArgumentMarshaler`**, which briefly broke **`getBoolean`** when `null` marshalers appeared. Fix: **null-check the marshaler**, not only the boolean box (`am` vs `falseIfNull`). Small lesson: **run the whole test suite** (the book caught a **`ClassCastException`** expectation mismatch between JUnit and FitNesse and added a bridging unit test).

The refactor then:

- Pushed **set/get** behavior into marshalers and eventually **`set(Iterator<String>)`** so **`Args`** did not need `args` and `currentArgument` passed everywhere (**[F1]** smell avoided).  
- Collapsed multiple maps into **`marshalers`** and removed the **`instanceof`** chain in **`setArgument`** so it became a single **`m.set(currentArgument)`**.  
- Moved **`ArgsException`** and message formatting out of **`Args`** for clearer **partitioning** (tradeoff: canned messages vs. total SRP purity on messages; the book calls that a **pragmatic compromise**).

Smells cited along the way include **[G23]** (eliminate needless type switches) and **[N5]** (rename **`am`** when local clarity beats length).

## Tests that anchor the story

**Listing 14-13** (`ArgsTest`) and **14-14** (`ArgsExceptionTest`) show how **acceptance-level expectations** (doubles, missing values, invalid formats) stay pinned while internals move.

## Bibliography (chapter references)

| Tag | Pointer |
|-----|---------|
| **[G23]** | Smell: avoid gratuitous type switching (see book's smell catalog). |
| **[F1]** | Long parameter lists / avoid passing parallel context when one object will do. |
| **[N5]** | Naming: short names when scope is tiny and meaning stays clear (`am`). |

## Conclusion

**Working** is not enough. Code that merely works is often **badly broken** as a maintainable asset. Bad schedules can be redone; bad requirements can be redefined; team problems can be repaired. **Bad code rots**: it gains hidden dependencies, slows every later change, and dominates the team's future.

Cleaning late is **expensive**. Cleaning **soon** after a small mess is cheap. **Keep code as clean and simple as it can be all the time**, and never let the rot get a foothold.
