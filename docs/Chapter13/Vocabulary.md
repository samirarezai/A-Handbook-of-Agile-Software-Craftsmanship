# Chapter 13 - Vocabulary

Short definitions and common synonyms for words that appear in *Clean Code*, Chapter 13 (*Concurrency*), or that capture concurrent-programming ideas.

---

## B

**Bound resource**  
*Meaning:* A resource of **fixed** size or count in a concurrent system (connection pools, bounded queues) - producers and consumers must **wait** for space or work.  
*Related:* producer–consumer, backpressure.

---

## C

**Critical section**  
*Meaning:* Code that must **not** be executed by two threads at once for correctness - typically guarded by a **lock** or synchronized region.  
*Related:* mutual exclusion, contention.

---

## D

**Deadlock**  
*Meaning:* Two or more threads each hold a resource the others need and **wait forever** - no thread can proceed.  
*Related:* dining philosophers, lock ordering.

**Decoupling what from when**  
*Meaning:* Concurrency separates **what work** happens from **when** it runs - improves throughput and can yield clearer structure (many small collaborators vs. one big loop).  
*Related:* servlet model, async I/O.

---

## L

**Livelock**  
*Meaning:* Threads keep “trying” and **yielding** to each other but make no durable progress - similar to deadlock in effect, different in mechanism (active thrash).  
*Related:* lockstep contention.

---

## M

**Mutual exclusion**  
*Meaning:* At most **one** thread may use shared data or a shared resource at a time - enforced by locks, monitors, or atomic APIs.  
*Synonyms:* mutex, critical-section protection.

---

## S

**Starvation**  
*Meaning:* A thread (or class of threads) is **delayed indefinitely** because scheduling or locking always favors others (e.g. readers never releasing writers).  
*Related:* readers–writers balance, fairness.

---

## Phrases (chapter-specific)

**Concurrency defense**  
*Meaning:* Practices that reduce failure modes: **SRP** for concurrent code, **narrow** shared data, **copies** instead of sharing, **independent** threads, **small** synchronized regions, **know** libraries and execution models, **test** aggressively and **jiggle** schedules.

**Jiggling**  
*Meaning:* Inserting `yield`, `sleep`, `wait`, or priority tweaks - or automated hooks like **ThreadJigglePoint** - to **perturb** interleavings so latent races surface **earlier** and **more often** in tests.  
*Related:* stress testing, ConTest-style tools.

**One-off failures**  
*Meaning:* Sporadic test or production glares dismissed as cosmic rays - treat as **likely concurrency defects** until proven otherwise.  
*Related:* Heisenbugs, load-dependent bugs.

**Thread-aware vs. thread-ignorant**  
*Meaning:* Push **domain** logic into **POJOs** that are easy to test single-threaded; keep **small**, focused modules that **know** about threads, locks, and lifecycle.  
*Related:* SRP, pluggable concurrency.
