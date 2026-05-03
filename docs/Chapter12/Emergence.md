# Chapter 12: Emergence

- [Home](../index.md)
- [Table of Contents](../SUMMARY.md)
- [Chapter 12 index](README.md)

<p align="center">
  <img src="../../assets/images/Chapter12/ch12.jpg" alt="Clean Code - Emergence" />
</p>

*Getting Clean via Emergent Design* — **Jeff Langr**

What if **four simple rules** guided you toward good design as you worked—surfacing structure so principles like **SRP** and **DIP** were easier to apply? **Kent Beck’s four rules of Simple Design** are widely used for exactly that: they help **good design emerge**.

> Reference: **[XPE]** (*Extreme Programming Explained*).

According to Kent, a design is **“simple”** if it follows these rules (in **order of importance**):

1. **Runs all the tests**
2. **Contains no duplication**
3. **Expresses the intent of the programmer**
4. **Minimizes the number of classes and methods**

## Simple Design Rule 1: Runs all the tests

A design must produce a system that **acts as intended**. Perfect-on-paper design is weak if you cannot **verify** behavior. A system that is **comprehensively tested** and **always green** is **testable**—and a system that is not testable is not truly **verifiable**; arguably it should **never** be deployed.

Making systems **testable** pushes you toward **small**, **single-purpose** classes (**SRP**). The more tests you write, the more you favor shapes that are **easy to test**.

**Tight coupling** makes tests hard—so more tests push you toward **DIP**, **dependency injection**, **interfaces**, and **abstractions** to **reduce coupling**. Tests improve **cohesion** and **coupling** almost as a side effect.

**Writing tests leads to better designs.**

## Simple Design Rules 2–4: Refactoring

Once tests exist, you can keep code and classes **clean** by **refactoring in small steps**: after a few new lines, ask whether the design **degraded**; if so, clean it and **run the tests** to show nothing broke. Tests remove the **fear** that cleanup will break behavior.

In that loop you can apply the whole craft of good design: cohesion, coupling, separation of concerns, modularization, smaller functions and classes, better names—and the **last three** simple-design rules: **eliminate duplication**, **ensure expressiveness**, and **minimize** classes and methods.

## No duplication

Duplication is the **primary enemy** of good design: extra work, extra risk, extra complexity. Duplication appears as **identical** lines, **similar** lines you can massage to match before refactoring, or **duplicated implementation** (same policy expressed twice).

### Example: `size` and `isEmpty`

Two methods might duplicate policy:

```java
int size() {}
boolean isEmpty() {}
```

You could track separate state for each—or **eliminate** duplication by defining one in terms of the other:

```java
boolean isEmpty() {
 return 0 == size();
}
```

### Example: image scaling and rotation

To keep the system clean, remove even **small** duplication between `scaleToOneDimension` and `rotate`:

```java
 public void scaleToOneDimension(
 float desiredDimension, float imageDimension) {
 if (Math.abs(desiredDimension - imageDimension) < errorThreshold)
 return;
 float scalingFactor = desiredDimension / imageDimension;
 scalingFactor = (float)(Math.floor(scalingFactor * 100) * 0.01f);
 RenderedOp newImage = ImageUtilities.getScaledImage(
 image, scalingFactor, scalingFactor);
 image.dispose();
 System.gc();
 image = newImage;
 }
 public synchronized void rotate(int degrees) {
 RenderedOp newImage = ImageUtilities.getRotatedImage(
 image, degrees);
 image.dispose();
 System.gc();
 image = newImage;
 }
```

Refactor the shared “replace image” behavior:

```java
 public void scaleToOneDimension(
 float desiredDimension, float imageDimension) {
 if (Math.abs(desiredDimension - imageDimension) < errorThreshold)
 return;
 float scalingFactor = desiredDimension / imageDimension;
 scalingFactor = (float)(Math.floor(scalingFactor * 100) * 0.01f);
 replaceImage(ImageUtilities.getScaledImage(
 image, scalingFactor, scalingFactor));
 }
 public synchronized void rotate(int degrees) {
 replaceImage(ImageUtilities.getRotatedImage(image, degrees));
 }
 private void replaceImage(RenderedOp newImage) {
 image.dispose();
 System.gc();
 image = newImage;
 }
```

Tiny extractions often reveal **SRP** issues—you might **move** the new helper to another class, raising its visibility so teammates can **abstract** and **reuse** it elsewhere. **Reuse in the small** can shrink overall complexity dramatically.

### Template Method for higher-level duplication

**Template Method** removes duplication when only a **slice** of an algorithm varies—for example, US vs. EU vacation accrual:

```java
public class VacationPolicy {
 public void accrueUSDivisionVacation() {
 // code to calculate vacation based on hours worked to date
 // ...
 // code to ensure vacation meets US minimums
 // ...
 // code to apply vaction to payroll record
 // ...
 }
 public void accrueEUDivisionVacation() {
 // code to calculate vacation based on hours worked to date
 // ...
 // code to ensure vacation meets EU minimums
 // ...
 // code to apply vaction to payroll record
 // ...
 }
}
```

The US and EU methods are largely the same except for **legal minimums**. Refactor with **Template Method**:

```java
abstract public class VacationPolicy {
 public void accrueVacation() {
 calculateBaseVacationHours();
 alterForLegalMinimums();
 applyToPayroll();
 }
 private void calculateBaseVacationHours() { /* ... */ }
 abstract protected void alterForLegalMinimums();
 private void applyToPayroll() { /* ... */ }
}
public class USVacationPolicy extends VacationPolicy {
 @Override protected void alterForLegalMinimums() {
 // US specific logic
 }
}
public class EUVacationPolicy extends VacationPolicy {
 @Override protected void alterForLegalMinimums() {
 // EU specific logic
 }
}
```

Subclasses fill the **hole** in `accrueVacation`; the duplicated skeleton lives once in the base class.

> **Template Method:** **[GOF]** (*Design Patterns*).

## Expressive

Convoluted code is costly: **maintenance** dominates lifetime cost, and complexity increases **time to understand** and the odds of **misunderstanding**. Code should **express the author’s intent**; clearer code means fewer defects and lower maintenance cost.

**Ways to be expressive:**

- **Good names** — hear a class or function name and not be surprised by its responsibilities.
- **Small** functions and classes — easier to name, write, and understand.
- **Standard nomenclature** — pattern names like **COMMAND** or **VISITOR** in type names communicate design quickly.
- **Well-written unit tests** — documentation by example; readers grasp what a class is for.

The most important habit: **try**. Too often we stop once code “works” without making it easy for the **next** reader—often **you**. Take pride: rename, split large functions, care for what you created. **Care** is a scarce resource—spend it here.

## Minimal classes and methods

Even **DRY**, **expressiveness**, and **SRP** can be taken too far: dogmatic splitting yields **too many** tiny types and methods. Rule 4 says to **keep counts low** as well—but it is the **lowest priority** of the four.

**Pointless dogmatism** examples: an **interface for every class**, or always splitting **data** and **behavior** into separate types. Resist that; stay pragmatic.

**Goal:** a **small overall system** with **small** functions and classes—but **tests**, **no duplication**, and **clarity** still rank higher.

## Bibliography (chapter references)

| Tag | Pointer |
|-----|---------|
| **[XPE]** | Beck, *Extreme Programming Explained* |
| **[GOF]** | Gamma et al., *Design Patterns* |

## Conclusion

No set of practices **replaces** experience—but the practices in this chapter (and book) **crystallize** decades of craft. **Simple design**, especially with **tests** and **refactoring**, helps developers **stick to** good principles and patterns that otherwise take years to internalize.
