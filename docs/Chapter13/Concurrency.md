# Chapter 13: Concurrency

- [Home](../index.md)
- [Table of Contents](../SUMMARY.md)
- [Chapter 13 index](README.md)

<p align="center">
  <img src="../../assets/images/Chapter13/ch13.jpg" alt="Clean Code - Concurrency" />
</p>

> “Objects are abstractions of processing. Threads are abstractions of schedule.”  
> **James O. Coplien**  
> *Private correspondence.*

Writing **clean concurrent** programs is **hard**. Single-threaded code is easier; multithreaded code can look fine yet be **broken** under stress. This chapter covers **why** concurrency matters, **what** makes it difficult, **principles** for cleaner concurrent code, and **testing** concerns.

**Clean concurrency** deserves a book of its own; this chapter is an **overview**, with a deeper tutorial in the book’s **“Concurrency II”** section (later pages in the printed edition). Read this chapter for orientation; follow the book’s advanced material when you need depth.

## Why concurrency?

Concurrency is a **decoupling** strategy: it separates **what** gets done from **when** it gets done. In a single-threaded app, **what** and **when** are tightly coupled - stack traces can reveal much of the system’s story. Decoupling can improve **throughput** and **structure** (many small collaborators instead of one big loop).

**Servlets** illustrate the idea: a **Web or EJB container** manages some concurrency; each request can live in its own world. In practice, servlet authors must still be **very careful** - container decoupling is imperfect, but the **structural** benefit is real.

Other motives: **response time** and **throughput** (e.g. an aggregator hitting many sites in parallel instead of sequentially past a 24-hour budget), **many users** at once, or **parallel** processing of large data sets across machines.

## Myths and misconceptions

- **“Concurrency always improves performance.”**: Only when there is **wait time** to overlap (I/O, multiple CPUs) - neither is automatic.
- **“Design does not change for concurrent programs.”**: Concurrent algorithms can differ **radically** from single-threaded designs; **what/when** decoupling reshapes structure.
- **“Containers mean I can ignore concurrency.”**: You must know what the **container** does and how to guard **concurrent update** and **deadlock**.

**More balanced** points:

- Concurrency adds **overhead** (runtime and extra code).
- **Correct** concurrency is hard even for “simple” problems.
- Concurrency bugs are often **non-repeatable** - they are still **defects**, not one-offs.
- Concurrency may require a **fundamental** design shift.

> Footnote flavor from the book: “cosmic rays,” glitches, and so on - tempting labels for rare failures.

## Challenges

Even trivial shared state can surprise you:

```java
public class X {
 private int lastIdUsed;
 public int getNextId() {
 return ++lastIdUsed;
 }
}
```

Share one `X` with `lastIdUsed == 42` between **two** threads that both call `getNextId()`. Possible outcomes include **43/44** (either order with field 44) - or **both return 43** with field **43** when interleavings corrupt the increment.

The book counts **execution paths** through the generated code (JIT and memory model matter); a rough bytecode-level estimate for two threads in `getNextId` is on the order of **tens of thousands** of paths for `int`, vastly more for `long` - most paths are fine; **some** are not.

> Dig deeper in the book’s supplementary concurrency material (“Possible Paths of Execution,” memory model, JIT).

## Concurrency defense principles

### Single Responsibility Principle

**SRP** means one reason to change per unit. **Concurrency** is complex enough to be its **own** reason to change - **separate** it from the rest of the code.

- Concurrency code has its own **lifecycle** (development, tuning).
- Its failure modes differ from - and often exceed - non-concurrent code in difficulty.
- Miswritten concurrency fails in **many** ways; do not pile on unrelated application concerns.

**Recommendation:** Keep **concurrency-related** code **separate** from other code.

> **SRP:** **[PPP]**. Client/server threading example appears in the book’s extended concurrency tutorial.

### Corollary: Limit the scope of data

Two threads updating the same field can **interfere**. `synchronized` (or equivalent) can guard **critical sections**, but **every** update site must participate. More shared-mutation sites mean:

- Higher odds you **forget** to guard one - breaking **all** guarded logic.
- **DRY** pain: duplicated locking discipline. **[PRAG]**
- Harder **diagnosis** when failures are already elusive.

**Recommendation:** **Encapsulate** aggressively; **severely limit** access to data that may be **shared**.

### Corollary: Use copies of data

When feasible, **avoid sharing**: read-only **copies**, or per-thread copies merged **single-threaded** afterward. Extra allocation can cost less than **synchronization** and lock contention - **measure** if unsure.

### Corollary: Threads should be as independent as possible

Ideal: each thread in its own **world**, data from an **unshared** source, **locals** only - **no** synchronization. `HttpServlet`’s `doGet` / `doPost` model encourages this until **shared** resources (e.g. database pools) appear.

**Recommendation:** **Partition** data into subsets that **independent** threads (or processors) can own.

## Know your library

**Java 5+** improved concurrency substantially:

- Prefer **thread-safe collections** from the library.
- Use the **executor** framework for unrelated tasks.
- Prefer **nonblocking** approaches when they fit.
- Remember: **many** classes are **not** thread-safe.

### Thread-safe collections

**Doug Lea**’s work (see **[Lea99]**) fed `java.util.concurrent`. **`ConcurrentHashMap`** is a default choice in Java 5+ deployments: generally strong performance, concurrent reads/writes, and composite operations that would be unsafe to hand-roll on `HashMap`.

**Recommendation:** Know **`java.util.concurrent`**, **`java.util.concurrent.atomic`**, and **`java.util.concurrent.locks`**.

## Know your execution models

Definitions the book uses when discussing patterns:

| Term | Meaning |
|------|---------|
| **ReentrantLock** | Lock acquired in one method, released in another. |
| **Semaphore** | Classic counting lock. |
| **CountDownLatch** | Waits for N events so threads can start together “fairly.” |
| **Bound resources** | Fixed pools (DB connections, bounded buffers). |
| **Mutual exclusion** | At most one thread uses shared data or a resource at a time. |
| **Starvation** | A thread is blocked **too long** or forever (e.g. always favoring fast work). |
| **Deadlock** | Threads circularly wait on each other’s resources. |
| **Livelock** | Threads keep yielding work to each other without durable progress. |

### Producer–consumer

Producers enqueue work; consumers dequeue. The queue is a **bound resource** - coordinate **full/empty** signals. Background: [Producer–consumer problem](https://en.wikipedia.org/wiki/Producer%E2%80%93consumer_problem).

### Readers–writers

Many **readers**, occasional **writers** - balance **throughput**, **freshness**, and **starvation** (writers waiting forever behind readers, or the reverse). Background: [Readers–writers problem](https://en.wikipedia.org/wiki/Readers%E2%80%93writers_problem).

### Dining philosophers

Philosophers need **two forks** (shared resources) to eat - models **resource competition**, **deadlock**, **livelock**, and throughput traps in enterprise-style systems. Background: [Dining philosophers problem](https://en.wikipedia.org/wiki/Dining_philosophers_problem).

**Recommendation:** Learn these **patterns** and implement them yourself so real problems feel familiar.

## Beware dependencies between synchronized methods

Java can `synchronize` **individual** methods - but **multiple** synchronized methods on one **shared** object can still compose **incorrectly** if callers rely on interleavings between calls.

**Recommendation:** Prefer **one** method per shared object when you can.

If you need **multiple** calls, options include:

- **Client-based locking**: client holds a lock covering the full multi-call protocol.
- **Server-based locking**: server exposes one **coarse** method that locks, does all steps, unlocks.
- **Adapted server**: intermediary performs locking when the server cannot change.

> See the book’s section on dependencies between methods breaking concurrent code.

## Keep synchronized sections small

Locks add **delay** and **overhead** - fewer critical sections is better - but **enlarging** synchronized regions increases **contention** and hurts performance.

**Recommendation:** Guard the **minimal** critical section.

## Writing correct shut-down code is hard

Long-lived servers differ from **graceful shutdown**. Shutdown often surfaces **deadlock** (parent waits on children that never finish; producer exits while consumer blocks forever, missing the shutdown signal).

**Recommendation:** Design and **prove** shutdown **early** - it will take **longer** than you think; reuse known algorithms where possible.

## Testing threaded code

Proof is impractical; tests do not guarantee correctness but **reduce risk**. Shared mutable state among threads explodes complexity.

**Recommendation:** Write tests **able** to expose races; run them **often** under varied **programmatic** and **system** configurations and **loads**. **Investigate** every failure - do not dismiss flukes.

### Further testing guidance

- Treat **spurious** failures as **threading** suspects.
- Stabilize **non-threaded** code first (**POJOs** off the thread).
- Make threaded code **pluggable** (1 vs. many threads, real vs. test doubles, fast/slow/variable doubles, iteration counts).
- Make it **tunable** (thread counts, maybe runtime or self-tuning).
- Run **more threads than processors** to encourage preemption and bad interleavings.
- Run on **every target platform** - schedulers differ; the book’s course example failed more often on OS X than in an XP VM for the **same** buggy code.

> The **JVM** does not **guarantee** preemptive threading in the abstract; in practice modern OSes provide preemption, but behavior still varies.

### Instrument your code to force failures

Rare failures mean only a few **bad paths** out of astronomically many - **perturb** ordering with `wait`, `sleep`, `yield`, and priority changes (on **`Thread`** in real Java APIs) to surface bugs **sooner**.

**Hand-coded** example from the book:

```java
public synchronized String nextUrlOrNull() {
 if(hasNext()) {
 String url = urlGenerator.next();
 Thread.yield(); // inserted for testing.
 updateHasNext();
 return url;
 }
 return null;
}
```

If this “breaks” the code, the bug was **already** there - `yield` only exposed it. Downsides: manual placement, must not ship test hooks, shotgun odds.

**Better:** separate **POJOs** from **thread control** so instrumentation has clear seams; vary **test jigs** that drive different call patterns.

**Automated** sketch - `ThreadJigglePoint` with production no-op vs. test randomness; aspects (**AOP**), **CGLIB**, **ASM** can inject calls. The book’s sample calls `ThreadJiglePoint.jiggle()` (typo in the printed name) - the class is spelled **`ThreadJigglePoint`**:

```java
public class ThreadJigglePoint {
 public static void jiggle() {
 }
}
```

```java
public synchronized String nextUrlOrNull() {
 if(hasNext()) {
 ThreadJigglePoint.jiggle();
 String url = urlGenerator.next();
 ThreadJigglePoint.jiggle();
 updateHasNext();
 ThreadJigglePoint.jiggle();
 return url;
 }
 return null;
}
```

Run many iterations with randomized **jiggling** for due diligence. **IBM ConTest** (book-era link: [IBM alphaWorks ConTest](http://www.alphaworks.ibm.com/tech/contest)) offered a more sophisticated approach.

**Recommendation:** Use **jiggling** to ferret out errors.

## Bibliography (chapter references)

| Tag | Pointer |
|-----|---------|
| **[PPP]** | Martin, *Agile Software Development: Principles, Patterns, and Practices* |
| **[PRAG]** | Hunt & Thomas, *The Pragmatic Programmer* |
| **[Lea99]** | Lea, *Concurrent Programming in Java* |

## Conclusion

Concurrent code is easy to get **wrong** - simple logic becomes hard with **threads** and **shared data**. Use **rigor** and **clean structure** or suffer subtle, rare failures.

**SRP:** isolate **thread-aware** code in **small**, testable areas; keep **domain** logic in **thread-ignorant POJOs**. Understand **shared data** and **resource pools**; **shutdown** and loop boundaries bite especially hard.

**Know** libraries and **classic problems** (producer–consumer, readers–writers, dining philosophers). Lock **only** what must be locked; keep locks **small**; avoid **nested** lock dependencies without deep reasoning. **Minimize** shared state and its **scope**; prefer designs that do not push locking complexity onto **clients**.

Treat intermittent failures as **real** until disproven. Run threaded code in **many** configurations and platforms **continuously**; **TDD**’s testability encourages **plug-ability**, which helps. **Instrument** (by hand or tool) **early** and run threaded code **long** before production.

A **clean** separation of concerns and disciplined testing **greatly** improve your odds.
