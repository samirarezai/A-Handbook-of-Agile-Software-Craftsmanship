# Chapter 16: Refactoring SerialDate

- [Home](../index.md)
- [Table of Contents](../SUMMARY.md)
- [Chapter 16 index](README.md)

<p align="center">
  <img src="../../assets/images/Chapter16/ch16.jpg" alt="Clean Code - Refactoring SerialDate" />
</p>

This chapter is a case study in **professional refactoring**: not “rewriting,” not “style wars,” but a disciplined pass through real code (JCommon’s `org.jfree.date.SerialDate`) guided by tests, coverage, and steady improvement.

## What SerialDate is (and why it exists)

`SerialDate` represents a **date without time**. The chapter argues that Java’s built-ins historically made it too easy to get tangled in **time-of-day / timezone** concerns when the real intent is “just a day.”

## First, make it work

The existing tests (`SerialDateTests`) pass, but do not cover the whole class. The author uses a usage search and then coverage (Clover) to show large regions are untested, and writes a new, independent test suite to drive confidence.

Key themes in this phase:

- **Tests reveal missing behavior**: some tests are added for “obvious” expectations; some are left commented out where the intended behavior is ambiguous.
- **Coverage is a guide, not a goal**: the author uses it to find unexecuted code and untested branches, then writes tests to understand behavior.

### Small, safe fixes driven by tests

The chapter walks through fixes that the tests make undeniable:

- **Case-insensitive weekday parsing**: make weekday-string parsing accept common case variations (`equalsIgnoreCase`).
- **Month parsing defects**: adjust `stringToMonthCode` logic so expected inputs work.
- **Boundary bug in “following day-of-week”**: a classic off-by-one where “following Saturday” of a Saturday incorrectly returns the same day.
- **Incorrect “nearest day-of-week” algorithm**: tests expose a failure pattern; the algorithm is replaced with one that correctly chooses between past/future candidates.
- **Prefer exceptions over error strings**: methods that returned error strings are changed to throw `IllegalArgumentException`, making failures explicit.

At the end of this stage, the code is believed to be correct enough to refactor safely.

## Then, make it right

Now the chapter becomes a top-to-bottom critique and cleanup pass. The refactoring principles emphasized include:

- **Remove obsolete change history**: source control already tracks it.
- **Reduce clutter**: remove redundant comments, unnecessary `final` noise, and needless Javadocs that restate signatures.
- **Improve names and abstraction**: “SerialDate” implies an implementation detail; an abstract “day date” should not.

## Replace integer codes with enums

A major structural improvement is converting integer “code” constants into enums:

- **Month** enum replaces `MonthConstants`.
- **Day** enum replaces weekday codes and also owns parsing/formatting behavior.
- Enums for “week in month,” date interval boundaries (open/closed), and range selection (last/next/nearest) clarify intent and prevent invalid values.

This eliminates swaths of validation code (e.g., “is this month code valid?”) and makes method signatures self-documenting.

## Push implementation details down; pull generic behavior up

The chapter challenges what belongs in an abstract class vs. an implementation:

- Values that only the spreadsheet-style implementation uses should live in `SpreadsheetDate`, not the base abstraction.
- Some methods in `SpreadsheetDate` don’t depend on implementation details and should move up to the abstract base.

## Introduce a factory to avoid base-class knowledge of subclasses

The original code has the abstract base creating a concrete `SpreadsheetDate` indirectly. The chapter replaces this with a **factory** (`DayDateFactory`) that:

- creates instances (`makeDate(...)`)
- exposes implementation bounds (minimum/maximum year) without polluting the abstract class

This prevents “base class depends on derived class” coupling.

## Make date arithmetic read correctly

Static utilities are turned into instance methods to read naturally, then renamed to avoid the “mutates or returns new?” ambiguity:

- `addDays` → `plusDays`
- `addMonths` → `plusMonths`
- similar for years

The chapter highlights how a fluent name can prevent a reader from wrongly assuming the original instance is mutated.

## Simplify day-of-week computations

The “previous/following/nearest day-of-week” methods are simplified with clearer intermediate variables, consistent patterns, and corrected edge handling.

## Final pass: cohesion and flow

In the last sweep, remaining enums are moved into their own files, date utilities are consolidated, and “magic numbers” are replaced by enum accessors to improve readability and maintainability.

The resulting class is **smaller**, **clearer**, and **better tested**, even if the percentage coverage number changes due to the class shrinking.

