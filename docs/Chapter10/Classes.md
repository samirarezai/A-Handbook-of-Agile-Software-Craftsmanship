# Chapter 10: Classes

- [Home](../index.md)
- [Table of Contents](../SUMMARY.md)
- [Chapter 10 index](README.md)

<p align="center">
  <img src="../../assets/images/Chapter10/ch10.jpg" alt="Clean Code - Classes" />
</p>

So far in this book the focus has been on how to write lines and blocks of code well, on composing functions and how they interrelate. For all the attention to the expressiveness of statements and the functions they comprise, we still do not have clean code until we have paid attention to **higher levels of code organization**. This chapter is about **clean classes**.

## Class organization

Following the standard Java convention, a class should begin with a list of variables. **Public static** constants, if any, should come first. Then **private static** variables, followed by **private instance** variables. There is seldom a good reason to have a public variable.

**Public functions** should follow the list of variables. Private utilities called by a public function should sit **right after** that public function. This follows the **step-down rule** and helps the program read like a **newspaper article**.

## Encapsulation

Keep variables and utility functions **private**, but not fanatically. Sometimes a variable or helper must be **protected** (or package scope) so a test in the same package can call it. **Tests rule** - first try to preserve privacy; loosening encapsulation is a **last resort**.

## Classes should be small

The first rule of classes is that they should be **small**. The second rule is that they should be **smaller than that**. As with **functions**, smaller is the primary rule - but **size is not counted in lines**. We count **responsibilities**.

> Footnote: responsibility counting ties to **RDD** (Responsibility-Driven Design) in the book’s references.

### Listing 10-1 - Too many responsibilities

```java
public class SuperDashboard extends JFrame implements MetaDataUser
 public String getCustomizerLanguagePath()
 public void setSystemConfigPath(String systemConfigPath)
 public String getSystemConfigDocument()
 public void setSystemConfigDocument(String systemConfigDocument)
 public boolean getGuruState()
 public boolean getNoviceState()
 public boolean getOpenSourceState()
 public void showObject(MetaObject object)
 public void showProgress(String s)
 public boolean isMetadataDirty()
 public void setIsMetadataDirty(boolean isMetadataDirty)
 public Component getLastFocusedComponent()
 public void setLastFocused(Component lastFocused)
 public void setMouseSelectState(boolean isMouseSelected)
 public boolean isMouseSelected()
 public LanguageManager getLanguageManager()
 public Project getProject()
 public Project getFirstProject()
 public Project getLastProject()
 public String getNewProjectName()
 public void setComponentSizes(Dimension dim)
 public String getCurrentDir()
 public void setCurrentDir(String newDir)
 public void updateStatus(int dotPos, int markPos)
 public Class[] getDataBaseClasses()
 public MetadataFeeder getMetadataFeeder()
 public void addProject(Project project)
 public boolean setCurrentProject(Project project)
 public boolean removeProject(Project project)
 public MetaProjectHeader getProgramMetadata()
 public void resetDashboard()
 public Project loadProject(String fileName, String projectName)
 public void setCanSaveMetadata(boolean canSave)
 public MetaObject getSelectedObject()
 public void deselectObjects()
 public void setProject(Project project)
 public void editorAction(String actionName, ActionEvent event)
 public void setMode(int mode)
 public FileManager getFileManager()
 public void setFileManager(FileManager fileManager)
 public ConfigManager getConfigManager()
 public void setConfigManager(ConfigManager configManager)
 public ClassLoader getClassLoader()
 public void setClassLoader(ClassLoader classLoader)
 public Properties getProps()
 public String getUserHome()
 public String getBaseDir()
 public int getMajorVersionNumber()
 public int getMinorVersionNumber()
 public int getBuildNumber()
 public MetaObject pasting(
MetaObject target, MetaObject pasted, MetaProject project)
 public void processMenuItems(MetaObject metaObject)
 public void processMenuSeparators(MetaObject metaObject)
 public void processTabPages(MetaObject metaObject)
 public void processPlacement(MetaObject object)
 public void processCreateLayout(MetaObject object)
 public void updateDisplayLayer(MetaObject object, int layerIndex)
 public void propertyEditedRepaint(MetaObject object)
 public void processDeleteObject(MetaObject object)
 public boolean getAttachedToDesigner()
 public void processProjectChangedState(boolean hasProjectChanged)
 public void processObjectNameChanged(MetaObject object)
 public void runProject()
```

Most developers would agree that `SuperDashboard` is too large; some would call it a **God class**.

But what if `SuperDashboard` contained only the methods in Listing 10-2? **Five methods** is not too many by count - yet it **can** still be too much, because those methods can represent **too many responsibilities**.

### Naming and the “25 words” rule

The **name** of a class should describe what responsibilities it fulfills; naming is often the first way to judge size. If you cannot derive a **concise** name, the class is likely too large. Ambiguous names - and weasel words like **Processor**, **Manager**, or **Super** - often hint at bad aggregation of responsibilities.

You should be able to write a **brief class description** in about **25 words** without using **if**, **and**, **or**, or **but**. The book’s example: *“The SuperDashboard provides access to the component that last held the focus, **and** it also allows us to track the version and build numbers.”* That **and** signals **too many responsibilities**.

### Listing 10-2 - Small enough?

```java
public class SuperDashboard extends JFrame implements MetaDataUser
 public Component getLastFocusedComponent()
 public void setLastFocused(Component lastFocused)
 public int getMajorVersionNumber()
 public int getMinorVersionNumber()
 public int getBuildNumber()
}
```

### Listing 10-1 (continued) - Too many responsibilities

```java
 public void setAçowDragging(boolean allowDragging)
 public boolean allowDragging()
 public boolean isCustomizing()
 public void setTitle(String title)
 public IdeMenuBar getIdeMenuBar()
 public void showHelper(MetaObject metaObject, String propertyName)
 // ... many non-public methods follow ...
}
```

## The Single Responsibility Principle (SRP)

The **Single Responsibility Principle** states that a class or module should have **one, and only one, reason to change**. That gives both a definition of **responsibility** and a guideline for class size: **one responsibility - one reason to change**.

> Read more in **[PPP]** (*Agile Software Development: Principles, Patterns, and Practices*).

The small `SuperDashboard` in Listing 10-2 still has **two** reasons to change: (1) **version information** that changes when the software ships, and (2) **Java Swing** concerns (`JFrame`). You might change version data without touching Swing, or the reverse is not always true - so the concerns are not the same axis of change.

Extracting version behavior into a dedicated type clarifies the model and improves reuse.

### Listing 10-3 - A single-responsibility class

```java
public class Version {
 public int getMajorVersionNumber()
 public int getMinorVersionNumber()
 public int getBuildNumber()
}
```

SRP is one of the more important ideas in OO design and one of the simpler to state - yet it is **often violated**. Getting software to **work** and making software **clean** are different activities; many people stop when it works and never return to split overstuffed classes. Others fear many small classes will obscure the big picture - but a system has a fixed amount of logic; **small, labeled drawers** beat a few drawers full of mixed junk. Prefer **many small classes**, each with one responsibility, collaborating to produce behavior.

## Cohesion

Classes should have a **small number** of instance variables. Each method should manipulate **one or more** of those variables; in general, the **more** fields a method touches, the **more cohesive** it is to its class. If **every** variable is used by **every** method, cohesion is maximal. That extreme is neither advisable nor usually possible - but you still want **high** cohesion: methods and variables **co-depend** and read as one logical whole.

### Listing 10-4 - `Stack.java` (a cohesive class)

```java
public class Stack {
 private int topOfStack = 0;
 List<Integer> elements = new LinkedList<Integer>();

 public int size() {
 return topOfStack;
 }

 public void push(int element) {
 topOfStack++;
 elements.add(element);
 }

 public int pop() throws PoppedWhenEmpty {
 if (topOfStack == 0)
 throw new PoppedWhenEmpty();
 int element = elements.get(--topOfStack);
 elements.remove(topOfStack);
 return element;
 }
}
```

Of the three methods, only `size()` fails to use **both** variables - still a cohesive class.

Keeping functions small and parameter lists short can produce **many instance variables** touched by only **some** methods. That pattern usually means **another class** is trying to escape: split variables and methods so new classes are **more cohesive**.

### Maintaining cohesion results in many small classes

Breaking a large function into smaller ones often **creates** more classes. If an extracted fragment needs four locals from the parent function, one shortcut is to promote those locals to **instance fields** so extraction needs no parameters - but that can **lower cohesion** as unrelated fields accumulate.

If a **few functions** share a **subset** of variables, that subset is often a **class in its own right**. When cohesion drops, **split** the class. Refactoring a big function into small pieces is therefore a common path to **better structure** and **transparency**.

The book uses Knuth’s **PrintPrimes** (from *Literate Programming*) as a demonstration: a single sprawling routine versus a factored design.

> Reference: **[Knuth92]**: Knuth, *Literate Programming*, CSLI, 1992.

### Listing 10-5 - `PrintPrimes.java`

```java
package literatePrimes;

public class PrintPrimes {
 public static void main(String[] args) {
 final int M = 1000;
 final int RR = 50;
 final int CC = 4;
 final int WW = 10;
 final int ORDMAX = 30;
 int P[] = new int[M + 1];
 int PAGENUMBER;
 int PAGEOFFSET;
 int ROWOFFSET;
 int C;
 int J;
 int K;
 boolean JPRIME;
 int ORD;
 int SQUARE;
 int N;
 int MULT[] = new int[ORDMAX + 1];

 J = 1;
 K = 1;
 P[1] = 2;
 ORD = 2;
 SQUARE = 9;

 while (K < M) {
 do {
 J = J + 2;
 if (J == SQUARE) {
 ORD = ORD + 1;
 SQUARE = P[ORD] * P[ORD];
 MULT[ORD - 1] = J;
 }
 N = 2;
 JPRIME = true;
 while (N < ORD && JPRIME) {
 while (MULT[N] < J)
 MULT[N] = MULT[N] + P[N] + P[N];
 if (MULT[N] == J)
 JPRIME = false;
 N = N + 1;
 }
 } while (!JPRIME);
 K = K + 1;
 P[K] = J;
 }

 {
 PAGENUMBER = 1;
 PAGEOFFSET = 1;

 while (PAGEOFFSET <= M) {
 System.out.println("The First " + M +
 " Prime Numbers --- Page " + PAGENUMBER);
 System.out.println("");
 for (ROWOFFSET = PAGEOFFSET; ROWOFFSET < PAGEOFFSET + RR; ROWOFFSET++){
 for (C = 0; C < CC;C++)
 if (ROWOFFSET + C * RR <= M)
 System.out.format("%10d", P[ROWOFFSET + C * RR]);
 System.out.println("");
 }
 System.out.println("\f");
 PAGENUMBER = PAGENUMBER + 1;
 PAGEOFFSET = PAGEOFFSET + RR * CC;
 }
 }
 }
}
```

Listings 10-6 through 10-8 show the same behavior refactored into **smaller classes and functions** with clearer names. The refactored program is **longer** in lines: longer identifiers, declarations as **commentary**, and whitespace for readability.

Responsibilities split roughly as: **`PrimePrinter`** - execution / entry (would change if invocation became a SOAP service, etc.); **`RowColumnPagePrinter`** - row/column/page layout; **`PrimeGenerator`** - prime generation (static scope hiding algorithm state). The refactor was **not a rewrite from scratch**: the book describes driving the change with a **test suite** and **tiny steps**, preserving behavior.

### Listing 10-6 - `PrimePrinter.java` (refactored)

```java
package literatePrimes;

public class PrimePrinter {
 public static void main(String[] args) {
 final int NUMBER_OF_PRIMES = 1000;
 int[] primes = PrimeGenerator.generate(NUMBER_OF_PRIMES);
 final int ROWS_PER_PAGE = 50;
 final int COLUMNS_PER_PAGE = 4;
 RowColumnPagePrinter tablePrinter =
 new RowColumnPagePrinter(ROWS_PER_PAGE,
 COLUMNS_PER_PAGE,
"The First " + NUMBER_OF_PRIMES +
 " Prime Numbers");
 tablePrinter.print(primes);
 }
}
```

### Listing 10-7 - `RowColumnPagePrinter.java`

```java
package literatePrimes;
import java.io.PrintStream;

public class RowColumnPagePrinter {
 private int rowsPerPage;
 private int columnsPerPage;
 private int numbersPerPage;
 private String pageHeader;
 private PrintStream printStream;

 public RowColumnPagePrinter(int rowsPerPage,
 int columnsPerPage,
 String pageHeader) {
 this.rowsPerPage = rowsPerPage;
 this.columnsPerPage = columnsPerPage;
 this.pageHeader = pageHeader;
 numbersPerPage = rowsPerPage * columnsPerPage;
 printStream = System.out;
 }

 public void print(int data[]) {
 int pageNumber = 1;
 for (int firstIndexOnPage = 0;
 firstIndexOnPage < data.length;
 firstIndexOnPage += numbersPerPage) {
 int lastIndexOnPage =
 Math.min(firstIndexOnPage + numbersPerPage - 1,
 data.length - 1);
 printPageHeader(pageHeader, pageNumber);
 printPage(firstIndexOnPage, lastIndexOnPage, data);
 printStream.println("\f");
 pageNumber++;
 }
 }

 private void printPage(int firstIndexOnPage,
 int lastIndexOnPage,
 int[] data) {
 int firstIndexOfLastRowOnPage =
 firstIndexOnPage + rowsPerPage - 1;
 for (int firstIndexInRow = firstIndexOnPage;
 firstIndexInRow <= firstIndexOfLastRowOnPage;
 firstIndexInRow++) {
 printRow(firstIndexInRow, lastIndexOnPage, data);
 printStream.println("");
 }
 }

 private void printRow(int firstIndexInRow,
 int lastIndexOnPage,
 int[] data) {
 for (int column = 0; column < columnsPerPage; column++) {
 int index = firstIndexInRow + column * rowsPerPage;
 if (index <= lastIndexOnPage)
 printStream.format("%10d", data[index]);
 }
 }

 private void printPageHeader(String pageHeader,
 int pageNumber) {
 printStream.println(pageHeader + " --- Page " + pageNumber);
 printStream.println("");
 }

 public void setOutput(PrintStream printStream) {
 this.printStream = printStream;
 }
}
```

### Listing 10-8 - `PrimeGenerator.java` (complete listing)

The book prints this class across two pages; below is the **full** class with the loop closed and helper methods included (nothing omitted).

```java
package literatePrimes;

import java.util.ArrayList;

public class PrimeGenerator {
 private static int[] primes;
 private static ArrayList<Integer> multiplesOfPrimeFactors;

 protected static int[] generate(int n) {
 primes = new int[n];
 multiplesOfPrimeFactors = new ArrayList<Integer>();
 set2AsFirstPrime();
 checkOddNumbersForSubsequentPrimes();
 return primes;
 }

 private static void set2AsFirstPrime() {
 primes[0] = 2;
 multiplesOfPrimeFactors.add(2);
 }

 private static void checkOddNumbersForSubsequentPrimes() {
 int primeIndex = 1;
 for (int candidate = 3;
 primeIndex < primes.length;
 candidate += 2) {
 if (isPrime(candidate))
 primes[primeIndex++] = candidate;
 }
 }

 private static boolean isPrime(int candidate) {
 if (isLeastRelevantMultipleOfNextLargerPrimeFactor(candidate)) {
 multiplesOfPrimeFactors.add(candidate);
 return false;
 }
 return isNotMultipleOfAnyPreviousPrimeFactor(candidate);
 }

 private static boolean
 isLeastRelevantMultipleOfNextLargerPrimeFactor(int candidate) {
 int nextLargerPrimeFactor = primes[multiplesOfPrimeFactors.size()];
 int leastRelevantMultiple = nextLargerPrimeFactor * nextLargerPrimeFactor;
 return candidate == leastRelevantMultiple;
 }

 private static boolean
 isNotMultipleOfAnyPreviousPrimeFactor(int candidate) {
 for (int n = 1; n < multiplesOfPrimeFactors.size(); n++) {
 if (isMultipleOfNthPrimeFactor(candidate, n))
 return false;
 }
 return true;
 }

 private static boolean
 isMultipleOfNthPrimeFactor(int candidate, int n) {
 return
 candidate == smallestOddNthMultipleNotLessThanCandidate(candidate, n);
 }

 private static int
 smallestOddNthMultipleNotLessThanCandidate(int candidate, int n) {
 int multiple = multiplesOfPrimeFactors.get(n);
 while (multiple < candidate)
 multiple += 2 * primes[n];
 multiplesOfPrimeFactors.set(n, multiple);
 return multiple;
 }
}
```

## Organizing for change

In most systems **change is continual**; every change risks breaking the rest. Clean systems **organize classes** to reduce that risk.

### Listing 10-9 - A class that must be opened for change

```java
public class Sql {
 public Sql(String table, Column[] columns)
 public String create()
 public String insert(Object[] fields)
 public String selectAll()
 public String findByKey(String keyColumn, String keyValue)
 public String select(Column column, String pattern)
 public String select(Criteria criteria)
 public String preparedInsert()
 private String columnList(Column[] columns)
 private String valuesList(Object[] fields, final Column[] columns)
 private String selectWithCriteria(String criteria)
 private String placeholderList(Column[] columns)
}
```

`Sql` generates SQL from metadata. Adding **update** support means **opening** this class - any edit can break unrelated behavior, so the whole class must be **re-tested**. The class must change when you add a **new statement type** and when you **alter one type’s details** (e.g. subselects in `select`). **Two reasons to change** implies an **SRP violation**.

Private helpers that only relate to **one** feature (e.g. `selectWithCriteria`) are a **heuristic** for splits - but the real driver is **change pressure**. If `Sql` is “done” and update is not on the horizon, leaving it alone can be fine. Once you keep **opening** it, **fix the design**.

### Listing 10-10 - A set of closed classes

Each public operation from the monolithic `Sql` moves to its **own** type; private helpers move **with** the feature that needs them; shared bits go to small utilities **`Where`** and **`ColumnList`**.

```java
abstract public class Sql {
 public Sql(String table, Column[] columns)
 abstract public String generate();
}

public class CreateSql extends Sql {
 public CreateSql(String table, Column[] columns)
 @Override public String generate()
}

public class SelectSql extends Sql {
 public SelectSql(String table, Column[] columns)
 @Override public String generate()
}

public class InsertSql extends Sql {
 public InsertSql(String table, Column[] columns, Object[] fields)
 @Override public String generate()
 private String valuesList(Object[] fields, final Column[] columns)
}

public class SelectWithCriteriaSql extends Sql {
 public SelectWithCriteriaSql(
 String table, Column[] columns, Criteria criteria)
 @Override public String generate()
}

public class SelectWithMatchSql extends Sql {
 public SelectWithMatchSql(
 String table, Column[] columns, Column column, String pattern)
 @Override public String generate()
}

public class FindByKeySql extends Sql
 public FindByKeySql(
 String table, Column[] columns, String keyColumn, String keyValue)
 @Override public String generate()
}

public class PreparedInsertSql extends Sql {
 public PreparedInsertSql(String table, Column[] columns)
 @Override public String generate() {
 private String placeholderList(Column[] columns)
}

public class Where {
 public Where(String criteria)
 public String generate()
}

public class ColumnList {
 public ColumnList(Column[] columns)
 public String generate()
}
```

Per-class logic becomes **simple**; comprehension time and cross-function break risk drop; tests can target **isolated** pieces. Adding **update** becomes a new subclass (e.g. **`UpdateSql`**) without modifying existing classes - supporting **SRP** and the **Open-Closed Principle (OCP)**.

> **OCP:** Classes should be **open for extension** but **closed for modification** - again discussed at length in **[PPP]**.

The goal is to **touch little** when evolving: ideally add behavior by **extension**, not by scattering edits across existing code.

## Isolating from change

**Concrete** details change; **abstractions** express stable concepts. Clients that depend directly on concrete APIs are fragile and **hard to test** (e.g. `Portfolio` calling `TokyoStockExchange` and getting **volatile** prices).

Introduce an interface for the concept you need:

```java
public interface StockExchange {
 Money currentPrice(String symbol);
}
```

`TokyoStockExchange` implements it; **`Portfolio`** takes a **`StockExchange`** in its constructor:

```java
public class Portfolio {
 private StockExchange exchange;

 public Portfolio(StockExchange exchange) {
 this.exchange = exchange;
 }

 // ...
}
```

Tests use a **stub** (fixed prices) instead of the live exchange:

```java
public class PortfolioTest {
 private FixedStockExchangeStub exchange;
 private Portfolio portfolio;

 @Before
 protected void setUp() throws Exception {
 exchange = new FixedStockExchangeStub();
 exchange.fix("MSFT", 100);
 portfolio = new Portfolio(exchange);
 }

 @Test
 public void GivenFiveMSFTTotalShouldBe500() throws Exception {
 portfolio.add(5, "MSFT");
 Assert.assertEquals(500, portfolio.value());
 }
}
```

Decoupling for tests usually improves **flexibility** and **reuse**; dependencies on **abstractions** rather than details reflect the **Dependency Inversion Principle (DIP)**.

> **DIP:** Classes should depend upon **abstractions**, not **concrete details** - see **[PPP]**.

`StockExchange` isolates **how** a price is obtained; `Portfolio` depends on the **concept**, not on `TokyoStockExchange` specifics.

## Bibliography (chapter references)

| Tag | Pointer |
|-----|---------|
| **[RDD]** | Wirfs-Brock & McKean, *Object Design: Roles, Responsibilities, and Collaborations* |
| **[PPP]** | Martin, *Agile Software Development: Principles, Patterns, and Practices* |
| **[Knuth92]** | Knuth, *Literate Programming*, CSLI, 1992 |

## Conclusion

Clean classes honor **SRP**, **cohesion**, and **OCP**; they use **abstractions** (**DIP**) to isolate volatile details. Measure classes by **responsibilities**, keep them **small**, order members for **readability**, and split or close modules so **change** stays safe and local.
