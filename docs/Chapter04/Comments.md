# Chapter 4: Comments

- [Home](../index.md)
- [Table of Contents](../SUMMARY.md)
- [Chapter 4 index](README.md)

<p align="center">
  <img src="../../assets/images/Chapter04/Screenshot%202026-05-02%20084211.jpg" alt="Clean Code – Comments" />
</p>

> “Don’t comment bad code—rewrite it.”  
> — Brian W. Kernighan and P. J. Plaugher

Comments can help—but they are not automatically good. The chapter argues that **the only fully accurate description of behavior is the code itself**. Comments compensate when we fail to express intent in code; that is a **failure worth fixing**, not celebrating.

## Why comments go wrong

Code moves, splits, merges, and is refactored. Comments often **do not follow**, drift from truth, or sit next to the wrong lines. **Inaccurate comments are worse than none**—they delude readers and preserve old rules that no longer apply.

### Orphaned comment (drift example)

The explanatory line no longer sits next to the constant it described:

```java
MockRequest request;
private final String HTTP_DATE_REGEXP =
  "[SMTWF][a-z]{2}\\,\\s[0-9]{2}\\s[JFMASOND][a-z]{2}\\s" +
  "[0-9]{4}\\s[0-9]{2}\\:[0-9]{2}\\:[0-9]{2}\\sGMT";
private Response response;
private FitNesseContext context;
private FileResponder responder;
private Locale saveLocale;
// Example: "Tue, 02 Apr 2003 22:18:49 GMT"
```

Other fields were inserted between the regex and the comment—classic drift.

## Comments do not make up for bad code

If a module is a mess, **clean it** instead of narrating the mess. Clear code with few comments beats tangled code with many.

## Explain yourself in code

Prefer a name or a small function over a comment that merely repeats logic.

```java
// Check to see if the employee is eligible for full benefits
if ((employee.flags & HOURLY_FLAG) && (employee.age > 65))
```

versus:

```java
if (employee.isEligibleForFullBenefits())
```

## Good comments (when they earn their keep)

The chapter still allows **good** comments—but the best comment is often the one you **deleted** by improving the code.

### Legal / standard headers

Copyright, license pointers, authorship—often required. Keep them short; link out to full legal text when possible.

### Informative comments (sparingly)

Sometimes a comment carries facts that are awkward to encode in a name yet—though renaming (`responderBeingTested`) is still preferred when it works.

```java
// format matched kk:mm:ss EEE, MMM dd, yyyy
Pattern timeMatcher = Pattern.compile(
  "\\d*:\\d*:\\d* \\w*, \\w* \\d*, \\d*");
```

Better long-term: a small date-format helper class so the regex is not “naked magic.”

### Explanation of intent

```java
public int compareTo(Object o) {
  if (o instanceof WikiPagePath) {
    WikiPagePath p = (WikiPagePath) o;
    String compressedName = StringUtil.join(names, "");
    String compressedArgumentName = StringUtil.join(p.names, "");
    return compressedName.compareTo(compressedArgumentName);
  }
  return 1; // we are greater because we are the right type.
}
```

The `return 1` documents a **domain choice**, not an obvious mechanical step.

```java
public void testConcurrentAddWidgets() throws Exception {
  WidgetBuilder widgetBuilder =
    new WidgetBuilder(new Class[] { BoldWidget.class });
  String text = "'''bold text'''";
  ParentWidget parent =
    new BoldWidget(new MockWidgetRoot(), "'''bold text'''");
  AtomicBoolean failFlag = new AtomicBoolean();
  failFlag.set(false);
  // This is our best attempt to get a race condition
  // by creating large number of threads.
  for (int i = 0; i < 25000; i++) {
    WidgetBuilderThread widgetBuilderThread =
      new WidgetBuilderThread(widgetBuilder, text, parent, failFlag);
    Thread thread = new Thread(widgetBuilderThread);
    thread.start();
  }
  assertEquals(false, failFlag.get());
}
```

### Clarification (third-party / unchangeable APIs)

```java
public void testCompareTo() throws Exception {
  WikiPagePath a = PathParser.parse("PageA");
  WikiPagePath ab = PathParser.parse("PageA.PageB");
  WikiPagePath b = PathParser.parse("PageB");
  WikiPagePath aa = PathParser.parse("PageA.PageA");
  WikiPagePath bb = PathParser.parse("PageB.PageB");
  WikiPagePath ba = PathParser.parse("PageB.PageA");
  assertTrue(a.compareTo(a) == 0); // a == a
  assertTrue(a.compareTo(b) != 0); // a != b
  assertTrue(ab.compareTo(ab) == 0); // ab == ab
  assertTrue(a.compareTo(b) == -1); // a < b
  assertTrue(aa.compareTo(ab) == -1); // aa < ab
  assertTrue(ba.compareTo(bb) == -1); // ba < bb
  assertTrue(b.compareTo(a) == 1); // b > a
  assertTrue(ab.compareTo(aa) == 1); // ab > aa
  assertTrue(bb.compareTo(ba) == 1); // bb > ba
}
```

Clarifying comments can still be **wrong**—verify them carefully.

### Warning of consequences

```java
// Don't run unless you
// have some time to kill.
public void _testWithReallyBigFile() {
  writeLinesToFile(10000000);
  response.setBody(testFile);
  response.readyToSend(this);
  String responseString = output.toString();
  assertSubString("Content-Length: 1000000000", responseString);
  assertTrue(bytesSent > 1000000000);
}
```

Modern style: `@Ignore("Takes too long to run")` instead of underscore naming.

```java
public static SimpleDateFormat makeStandardHttpDateFormat() {
  // SimpleDateFormat is not thread safe,
  // so we need to create each instance independently.
  SimpleDateFormat df = new SimpleDateFormat("EEE, dd MMM yyyy HH:mm:ss z");
  df.setTimeZone(TimeZone.getTimeZone("GMT"));
  return df;
}
```

### TODO comments (disciplined)

```java
// TODO-MdM these are not needed
// We expect this to go away when we do the checkout model
protected VersionInfo makeVersion() throws Exception {
  return null;
}
```

TODOs are not an excuse to leave bad code. Review and remove them regularly.

### Amplification

```java
String listItemContent = match.group(3).trim();
// the trim is real important. It removes the starting
// spaces that could cause the item to be recognized
// as another list.
new ListItemWidget(this, listItemContent, this.level + 1);
return buildList(text.substring(match.end()));
```

### Javadoc on public APIs

Well-written public API docs are valuable—and can still become misleading like any other comment if not maintained.

---

## Bad comments (most comments)

### Mumbling

```java
public void loadProperties() {
  try {
    String propertiesPath = propertiesLocation + "/" + PROPERTIES_FILE;
    FileInputStream propertiesStream = new FileInputStream(propertiesPath);
    loadedProperties.load(propertiesStream);
  } catch (IOException e) {
    // No properties files means all defaults are loaded
  }
}
```

If the reader must **hunt other modules** to know what this means, the comment failed.

### Redundant (and subtly misleading) header — Listing 4-1

```java
// Utility method that returns when this.closed is true. Throws an exception
// if the timeout is reached.
public synchronized void waitForClose(final long timeoutMillis)
  throws Exception {
  if (!closed) {
    wait(timeoutMillis);
    if (!closed)
      throw new Exception("MockResponseSender could not be closed");
  }
}
```

The comment is less precise than the code: the method waits up to `timeoutMillis`; it does not magically return “when `closed` becomes true” in the sense readers might assume without reading `wait`.

### Noise Javadoc on fields — Listing 4-2 (excerpt)

```java
public abstract class ContainerBase
  implements Container, Lifecycle, Pipeline,
    MBeanRegistration, Serializable {

  /**
   * The processor delay for this component.
   */
  protected int backgroundProcessorDelay = -1;

  /**
   * The lifecycle event support for this component.
   */
  protected LifecycleSupport lifecycle = new LifecycleSupport(this);

  /**
   * The container event listeners for this Container.
   */
  protected ArrayList listeners = new ArrayList();

  /**
   * The Loader implementation with which this Container is associated.
   */
  protected Loader loader = null;

  /**
   * The Logger implementation with which this Container is associated.
   */
  protected Log logger = null;

  /**
   * Associated logger name.
   */
  protected String logName = null;

  /**
   * The Manager implementation with which this Container is associated.
   */
  protected Manager manager = null;

  /**
   * The cluster with which this Container is associated.
   */
  protected Cluster cluster = null;

  /**
   * The human-readable name of this Container.
   */
  protected String name = null;

  /**
   * The parent Container to which this Container is a child.
   */
  protected Container parent = null;

  /**
   * The parent class loader to be configured when we install a Loader.
   */
  protected ClassLoader parentClassLoader = null;

  /**
   * The Pipeline object with which this Container is associated.
   */
  protected Pipeline pipeline = new StandardPipeline(this);

  /**
   * The Realm with which this Container is associated.
   */
  protected Realm realm = null;

  /**
   * The resources DirContext object with which this Container is associated.
   */
  protected DirContext resources = null;
}
```

### Mandated noise — Listing 4-3

```java
/**
 * @param title The title of the CD
 * @param author The author of the CD
 * @param tracks The number of tracks on the CD
 * @param durationInMinutes The duration of the CD in minutes
 */
public void addCD(String title, String author, int tracks, int durationInMinutes) {
  CD cd = new CD();
  cd.title = title;
  cd.author = author;
  cd.tracks = tracks;
  cd.duration = durationInMinutes;
  cdList.add(cd);
}
```

### Journal / change-log comments (obsolete style)

```java
/*
 * Changes (from 11-Oct-2001)
 * --------------------------
 * 11-Oct-2001 : Re-organised the class and moved it to new package
 *   com.jrefinery.date (DG);
 * 05-Nov-2001 : Added a getDescription() method, and eliminated NotableDate
 *   class (DG);
 * 12-Nov-2001 : IBD requires setDescription() method, now that NotableDate
 *   class is gone (DG); Changed getPreviousDayOfWeek(),
 *   getFollowingDayOfWeek() and getNearestDayOfWeek() to correct bugs (DG);
 * 05-Dec-2001 : Fixed bug in SpreadsheetDate class (DG);
 * 29-May-2002 : Moved the month constants into a separate interface
 *   (MonthConstants) (DG);
 * 27-Aug-2002 : Fixed bug in addMonths() method, thanks to N???levka Petr (DG);
 * 03-Oct-2002 : Fixed errors reported by Checkstyle (DG);
 * 13-Mar-2003 : Implemented Serializable (DG);
 * 29-May-2003 : Fixed bug in addMonths method (DG);
 * 04-Sep-2003 : Implemented Comparable. Updated the isInRange javadocs (DG);
 * 05-Jan-2005 : Fixed bug in addYears() method (1096282) (DG);
 */
```

Source control owns history; long journals at the top of files **obfuscate**.

### More noise

```java
/**
 * Default constructor.
 */
protected AnnualDateRule() {
}
```

```java
/** The day of the month. */
private int dayOfMonth;
```

```java
/**
 * Returns the day of the month.
 *
 * @return the day of the month.
 */
public int getDayOfMonth() {
  return dayOfMonth;
}
```

### Venting instead of refactoring — Listing 4-4

```java
private void startSending() {
  try {
    doSending();
  } catch (SocketException e) {
    // normal. someone stopped the request.
  } catch (Exception e) {
    try {
      response.add(ErrorResponder.makeExceptionString(e));
      response.closeAll();
    } catch (Exception e1) {
      // Give me a break!
    }
  }
}
```

### Listing 4-5 (refactored)

```java
private void startSending() {
  try {
    doSending();
  } catch (SocketException e) {
    // normal. someone stopped the request.
  } catch (Exception e) {
    addExceptionAndCloseResponse(e);
  }
}

private void addExceptionAndCloseResponse(Exception e) {
  try {
    response.add(ErrorResponder.makeExceptionString(e));
    response.closeAll();
  } catch (Exception e1) {
  }
}
```

### Scary noise (copy-paste errors)

```java
/** The name. */
private String name;
/** The version. */
private String version;
/** The licenceName. */
private String licenceName;
/** The version. */
private String info;
```

### Prefer names over comments

```java
// does the module from the global list <mod> depend on the
// subsystem we are part of?
if (smodule.getDependSubsystems().contains(subSysMod.getSubSystem()))
```

Refactor to:

```java
ArrayList moduleDependees = smodule.getDependSubsystems();
String ourSubSystem = subSysMod.getSubSystem();
if (moduleDependees.contains(ourSubSystem))
```

### Position markers

```java
// Actions //////////////////////////////////
```

Rarely worth the visual noise—especially slash “banners.”

### Closing-brace comments — Listing 4-6

```java
import java.io.*;

public class wc {
  public static void main(String[] args) {
    BufferedReader in = new BufferedReader(new InputStreamReader(System.in));
    String line;
    int lineCount = 0;
    int charCount = 0;
    int wordCount = 0;
    try {
      while ((line = in.readLine()) != null) {
        lineCount++;
        charCount += line.length();
        String words[] = line.split("\\W");
        wordCount += words.length;
      } // while
      System.out.println("wordCount = " + wordCount);
      System.out.println("lineCount = " + lineCount);
      System.out.println("charCount = " + charCount);
    } // try
    catch (IOException e) {
      System.err.println("Error:" + e.getMessage());
    } // catch
  } // main
}
```

Small, well-factored functions rarely need brace labels—**shorten the function** instead.

### Attribution / byline comments

```java
/* Added by Rick */
```

Use version control for authorship and history.

### Commented-out code

```java
InputStreamResponse response = new InputStreamResponse();
response.setBody(formatter.getResultStream(), formatter.getByteCount());
// InputStream resultsStream = formatter.getResultStream();
// StreamReader reader = new StreamReader(resultsStream);
// response.setContent(reader.read(formatter.getByteCount()));
```

```java
this.bytePos = writeBytes(pngIdBytes, 0);
// hdrPos = bytePos;
writeHeader();
writeResolution();
// dataPos = bytePos;
if (writeImageData()) {
  writeEnd();
  this.pngBytes = resizeByteArray(this.pngBytes, this.maxPos);
} else {
  this.pngBytes = null;
}
return this.pngBytes;
```

Delete dead code; **git remembers**.

### HTML noise in source comments

```java
/**
 * Task to run fit tests.
 * This task runs fitnesse tests and publishes the results.
 * <p/>
 * <pre>
 * Usage:
 * &lt;taskdef name=&quot;execute-fitnesse-tests&quot;
 * classname=&quot;fitnesse.ant.ExecuteFitnesseTestsTask&quot;
 * classpathref=&quot;classpath&quot; /&gt;
 * OR
 * &lt;taskdef classpathref=&quot;classpath&quot;
 * resource=&quot;tasks.properties&quot; /&gt;
 * <p/>
 * &lt;execute-fitnesse-tests
 * suitepage=&quot;FitNesse.SuiteAcceptanceTests&quot;
 * fitnesseport=&quot;8082&quot;
 * resultsdir=&quot;${results.dir}&quot;
 * resultshtmlpage=&quot;fit-results.html&quot;
 * classpathref=&quot;classpath&quot; /&gt;
 * </pre>
 */
```

Let tools generate HTML for the web; keep comments readable in the IDE.

### Nonlocal information

```java
/**
 * Port on which fitnesse would run. Defaults to <b>8082</b>.
 *
 * @param fitnessePort
 */
public void setFitnessePort(int fitnessePort) {
  this.fitnessePort = fitnessePort;
}
```

The setter does not own the default port constant—this comment **couples** to distant configuration.

### Too much information (RFC dump)

```java
/*
 RFC 2045 - Multipurpose Internet Mail Extensions (MIME)
 Part One: Format of Internet Message Bodies
 section 6.8. Base64 Content-Transfer-Encoding
 The encoding process represents 24-bit groups of input bits as output
 strings of 4 encoded characters. Proceeding from left to right, a
 24-bit input group is formed by concatenating 3 8-bit input groups.
 These 24 bits are then treated as 4 concatenated 6-bit groups, each
 of which is translated into a single digit in the base64 alphabet.
 When encoding a bit stream via the base64 encoding, the bit stream
 must be presumed to be ordered with the most-significant-bit first.
 That is, the first bit in the stream will be the high-order bit in
 the first 8-bit byte, and the eighth bit will be the low-order bit in
 the first 8-bit byte, and so on.
 */
```

Readers maintaining a base64 test helper rarely need the whole RFC inlined.

### Inobvious connection

```java
/*
 * start with an array that is big enough to hold all the pixels
 * (plus filter bytes), and an extra 200 bytes for header info
 */
this.pngBytes = new byte[((this.width + 1) * this.height * 3) + 200];
```

If the comment does not tie cleanly to `+1`, `*3`, and `200`, it fails its job.

### Javadoc on non-public code

Heavy formal Javadoc on internal types is usually **cruft**—public APIs deserve docs; implementation details deserve clarity.

---

## Example: “well documented” vs cleaned — Listings 4-7 and 4-8

### Listing 4-7 — `GeneratePrimes.java` (problem style)

```java
/**
 * This class Generates prime numbers up to a user specified
 * maximum. The algorithm used is the Sieve of Eratosthenes.
 * <p>
 * Eratosthenes of Cyrene, b. c. 276 BC, Cyrene, Libya --
 * d. c. 194, Alexandria. The first man to calculate the
 * circumference of the Earth. Also known for working on
 * calendars with leap years and ran the library at Alexandria.
 * <p>
 * The algorithm is quite simple. Given an array of integers
 * starting at 2. Cross out all multiples of 2. Find the next
 * uncrossed integer, and cross out all of its multiples.
 * Repeat until you have passed the square root of the maximum
 * value.
 *
 * @author Alphonse
 * @version 13 Feb 2002 atp
 */
import java.util.*;

public class GeneratePrimes {
  /**
   * @param maxValue is the generation limit.
   */
  public static int[] generatePrimes(int maxValue) {
    if (maxValue >= 2) { // the only valid case
      // declarations
      int s = maxValue + 1; // size of array
      boolean[] f = new boolean[s];
      int i;
      // initialize array to true.
      for (i = 0; i < s; i++)
        f[i] = true;
      // get rid of known non-primes
      f[0] = f[1] = false;
      // sieve
      int j;
      for (i = 2; i < Math.sqrt(s) + 1; i++) {
        if (f[i]) { // if i is uncrossed, cross its multiples.
          for (j = 2 * i; j < s; j += i)
            f[j] = false; // multiple is not prime
        }
      }
      // how many primes are there?
      int count = 0;
      for (i = 0; i < s; i++) {
        if (f[i])
          count++; // bump count.
      }
      int[] primes = new int[count];
      // move the primes into the result
      for (i = 0, j = 0; i < s; i++) {
        if (f[i]) // if prime
          primes[j++] = i;
      }
      return primes; // return the primes
    } else // maxValue < 2
      return new int[0]; // return null array if bad input.
  }
}
```

### Listing 4-8 — `PrimeGenerator.java` (restrained comments)

```java
/**
 * This class Generates prime numbers up to a user specified
 * maximum. The algorithm used is the Sieve of Eratosthenes.
 * Given an array of integers starting at 2:
 * Find the first uncrossed integer, and cross out all its
 * multiples. Repeat until there are no more multiples
 * in the array.
 */
public class PrimeGenerator {
  private static boolean[] crossedOut;
  private static int[] result;

  public static int[] generatePrimes(int maxValue) {
    if (maxValue < 2)
      return new int[0];
    else {
      uncrossIntegersUpTo(maxValue);
      crossOutMultiples();
      putUncrossedIntegersIntoResult();
      return result;
    }
  }

  private static void uncrossIntegersUpTo(int maxValue) {
    crossedOut = new boolean[maxValue + 1];
    for (int i = 2; i < crossedOut.length; i++)
      crossedOut[i] = false;
  }

  private static void crossOutMultiples() {
    int limit = determineIterationLimit();
    for (int i = 2; i <= limit; i++)
      if (notCrossed(i))
        crossOutMultiplesOf(i);
  }

  private static int determineIterationLimit() {
    // Every multiple in the array has a prime factor that
    // is less than or equal to the root of the array size,
    // so we don't have to cross out multiples of numbers
    // larger than that root.
    double iterationLimit = Math.sqrt(crossedOut.length);
    return (int) iterationLimit;
  }

  private static void crossOutMultiplesOf(int i) {
    for (int multiple = 2 * i; multiple < crossedOut.length; multiple += i)
      crossedOut[multiple] = true;
  }

  private static boolean notCrossed(int i) {
    return crossedOut[i] == false;
  }

  private static void putUncrossedIntegersIntoResult() {
    result = new int[numberOfUncrossedIntegers()];
    for (int j = 0, i = 2; i < crossedOut.length; i++)
      if (notCrossed(i))
        result[j++] = i;
  }

  private static int numberOfUncrossedIntegers() {
    int count = 0;
    for (int i = 2; i < crossedOut.length; i++)
      if (notCrossed(i))
        count++;
    return count;
  }
}
```

The class-level comment may still parallel the top function; the **square-root loop bound** comment is the kind that is hard to replace with a name alone—so it may earn its place.

## Conclusion

Use comments where they add **truth** that code cannot carry cheaply (legal, intent, warnings, public contracts). Otherwise, **rewrite the code** so readers trust the source of truth: the program itself.
