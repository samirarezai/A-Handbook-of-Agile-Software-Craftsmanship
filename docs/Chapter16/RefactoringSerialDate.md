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

---

## Chapter excerpt (full text provided)

If you go to `http://www.jfree.org/jcommon/index.php`, you will find the JCommon library.
Deep within that library there is a package named `org.jfree.date`. Within that package
there is a class named `SerialDate`. We are going to explore that class.

The author of `SerialDate` is David Gilbert. David is clearly an experienced and competent programmer. As we shall see, he shows a significant degree of professionalism and
discipline within his code. For all intents and purposes, this is “good code.” And I am
going to rip it to pieces.

This is not an activity of malice. Nor do I think that I am so much better than David
that I somehow have a right to pass judgment on his code. Indeed, if you were to find some
of my code, I’m sure you could find plenty of things to complain about.

No, this is not an activity of nastiness or arrogance. What I am about to do is nothing
more and nothing less than a professional review. It is something that we should all be
comfortable doing. And it is something we should welcome when it is done for us. It is
only through critiques like these that we will learn. Doctors do it. Pilots do it. Lawyers do
it. And we programmers need to learn how to do it too.

One more thing about David Gilbert: David is more than just a good programmer.
David had the courage and good will to offer his code to the community at large for free.
He placed it out in the open for all to see and invited public usage and public scrutiny. This
was well done!

`SerialDate` (Listing B-1, page 349) is a class that represents a date in Java. Why have
a class that represents a date, when Java already has `java.util.Date` and
`java.util.Calendar`, and others? The author wrote this class in response to a pain that I
have often felt myself. The comment in his opening Javadoc (line 67) explains it well. We
could quibble about his intention, but I have certainly had to deal with this issue, and I
welcome a class that is about dates instead of times.

### First, Make It Work

There are some unit tests in a class named `SerialDateTests` (Listing B-2, page 366). The
tests all pass. Unfortunately a quick inspection of the tests shows that they don’t test everything \[T1]. For example, doing a “Find Usages” search on the method `MonthCodeToQuarter`
(line 334) indicates that it is not used \[F4]. Therefore, the unit tests don’t test it.

So I fired up Clover to see what the unit tests covered and what they didn’t. Clover
reported that the unit tests executed only 91 of the 185 executable statements in `SerialDate`
(~50 percent) \[T2]. The coverage map looks like a patchwork quilt, with big gobs of unexecuted code littered all through the class.

It was my goal to completely understand and also refactor this class. I couldn’t do that
without much greater test coverage. So I wrote my own suite of completely independent
unit tests (Listing B-4, page 374).

As you look through these tests, you will note that many of them are commented out.
These tests didn’t pass. They represent behavior that I think `SerialDate` should have. So as
I refactor `SerialDate`, I’ll be working to make these tests pass too.

Even with some of the tests commented out, Clover reports that the new unit tests are
executing 170 (92 percent) out of the 185 executable statements. This is pretty good, and I
think we’ll be able to get this number higher.

The first few commented-out tests (lines 23-63) were a bit of conceit on my part. The
program was not designed to pass these tests, but the behavior seemed obvious \[G2] to me.
I’m not sure why the `testWeekdayCodeToString` method was written in the first place, but
because it is there, it seems obvious that it should not be case sensitive. Writing these tests
was trivial \[T3]. Making them pass was even easier; I just changed lines 259 and 263 to
use `equalsIgnoreCase`.

I left the tests at line 32 and line 45 commented out because it’s not clear to me that
the “tues” and “thurs” abbreviations ought to be supported.

The tests on line 153 and line 154 don’t pass. Clearly, they should \[G2]. We can easily
fix this, and the tests on line 163 through line 213, by making the following changes to the
`stringToMonthCode` function.

The commented test on line 318 exposes a bug in the `getFollowingDayOfWeek` method
(line 672). December 25th, 2004, was a Saturday. The following Saturday was January 1st,
2005. However, when we run the test, we see that `getFollowingDayOfWeek` returns December 25th as the Saturday that follows December 25th. Clearly, this is wrong \[G3],\[T1]. We
see the problem in line 685. It is a typical boundary condition error \[T5].

It is interesting to note that this function was the target of an earlier repair. The change
history (line 43) shows that “bugs” were fixed in `getPreviousDayOfWeek`, `getFollowingDayOfWeek`, and `getNearestDayOfWeek` \[T6].

The `testGetNearestDayOfWeek` unit test (line 329), which tests the `getNearestDayOfWeek`
method (line 705), did not start out as long and exhaustive as it currently is. I added a lot
of test cases to it because my initial test cases did not all pass \[T6]. You can see the pattern
of failure by looking at which test cases are commented out. That pattern is revealing \[T7].
It shows that the algorithm fails if the nearest day is in the future. Clearly there is some
kind of boundary condition error \[T5].

The pattern of test coverage reported by Clover is also interesting \[T8]. Line 719
never gets executed! This means that the if statement in line 718 is always false. Sure
enough, a look at the code shows that this must be true. The `adjust` variable is always negative and so cannot be greater or equal to 4. So this algorithm is just wrong.

The right algorithm is:

```java
int delta = targetDOW - base.getDayOfWeek();
int positiveDelta = delta + 7;
int adjust = positiveDelta % 7;
if (adjust > 3)
  adjust -= 7;
return SerialDate.addDays(adjust, base);
```

Finally, the tests at line 417 and line 429 can be made to pass simply by throwing an
`IllegalArgumentException` instead of returning an error string from `weekInMonthToString`
and `relativeToString`.

With these changes all the unit tests pass, and I believe `SerialDate` now works. So now
it’s time to make it “right.”

### Then Make It Right

We are going to walk from the top to the bottom of `SerialDate`, improving it as we go
along. Although you won’t see this in the discussion, I will be running all of the JCommon
unit tests, including my improved unit test for `SerialDate`, after every change I make. So
rest assured that every change you see here works for all of JCommon.

Starting at line 1, we see a ream of comments with license information, copyrights,
authors, and change history. I acknowledge that there are certain legalities that need to be
addressed, and so the copyrights and licenses must stay. On the other hand, the change history is a leftover from the 1960s. We have source code control tools that do this for us now.
This history should be deleted \[C1].

The import list starting at line 61 could be shortened by using `java.text.*` and
`java.util.*`. \[J1]

I wince at the HTML formatting in the Javadoc (line 67). Having a source file with
more than one language in it troubles me. This comment has four languages in it: Java,
English, Javadoc, and html \[G1]. With that many languages in use, it’s hard to keep things
straight.

Line 86 is the class declaration. Why is this class named `SerialDate`? What is the significance of the word “serial”?

I won’t keep you guessing. I know why (or at least I think I know why) the word
“serial” was used. The clue is in the constants `SERIAL_LOWER_BOUND` and
`SERIAL_UPPER_BOUND`. An even better clue is in the comment that begins on line 830. This class is named `SerialDate` because it is implemented using a “serial number,” which happens to be the number of days since December 30th, 1899.

I have two problems with this. First, the term “serial number” is not really correct. The representation is more of a relative offset than a serial number. A more descriptive term might be “ordinal.”

The second problem is more significant. The name `SerialDate` implies an implementation. This class is an abstract class. There is no need to imply anything at all about the implementation. The name is at the wrong level of abstraction \[N2].

In my opinion, the name of this class should simply be `Date`. Unfortunately, there are already too many classes in the Java library named `Date`, so in the end the text uses `DayDate` as the compromise.

(Excerpt continues in the source material.)

