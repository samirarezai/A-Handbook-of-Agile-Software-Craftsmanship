# Chapter 7: Error Handling

- [Home](../index.md)
- [Table of Contents](../SUMMARY.md)
- [Chapter 7 index](README.md)

<p align="center">
  <img src="../../assets/images/Chapter07/ch7.jpg" alt="Clean Code - Error Handling" />
</p>

Error handling is part of **clean code**, not a side topic. Many systems are **dominated** by it in the sense that **scattered** checks make the **happy path hard to see**. Handling must be **important**, but it must not **obscure** logic.

The chapter pushes techniques that keep code **robust** and **readable** - **separate** normal flow from error flow, choose exceptions thoughtfully, shape types around **how callers catch**, and avoid **null** as an error channel.

## Use exceptions rather than return codes

Return codes and flags **clutter** callers and are easy to forget. Exceptions let the main algorithm read straight while failures jump to a handler.

### Listing 7-1 - `DeviceController.java` (flags and nesting)

```java
public class DeviceController {
  // ...

  public void sendShutDown() {
    DeviceHandle handle = getHandle(DEV1);
    // Check the state of the device
    if (handle != DeviceHandle.INVALID) {
      // Save the device status to the record field
      retrieveDeviceRecord(handle);
      // If not suspended, shut down
      if (record.getStatus() != DEVICE_SUSPENDED) {
        pauseDevice(handle);
        clearDeviceWorkQueue(handle);
        closeDevice(handle);
      } else {
        logger.log("Device suspended. Unable to shut down");
      }
    } else {
      logger.log("Invalid handle for: " + DEV1.toString());
    }
  }

  // ...
}
```

### Listing 7-2 - `DeviceController.java` (with exceptions)

```java
public class DeviceController {
  // ...

  public void sendShutDown() {
    try {
      tryToShutDown();
    } catch (DeviceShutDownError e) {
      logger.log(e);
    }
  }

  private void tryToShutDown() throws DeviceShutDownError {
    DeviceHandle handle = getHandle(DEV1);
    DeviceRecord record = retrieveDeviceRecord(handle);
    pauseDevice(handle);
    clearDeviceWorkQueue(handle);
    closeDevice(handle);
  }

  private DeviceHandle getHandle(DeviceID id) {
    // ...
    throw new DeviceShutDownError("Invalid handle for: " + id.toString());
    // ...
  }

  // ...
}
```

Shutdown steps and error reporting are **untangled** - each concern can be read on its own.

## Write your `try` / `catch` / `finally` first

A `try` defines a **scope** where execution may **abort** and continue in `catch`. Treat it like a **transaction**: `catch` / `finally` must leave the system **consistent**.

Starting from a **test** that demands an exception helps you define that boundary before filling in logic.

```java
@Test(expected = StorageException.class)
public void retrieveSectionShouldThrowOnInvalidFileName() {
  sectionStore.retrieveSection("invalid - file");
}
```

Stub that fails the test until it throws:

```java
public List<RecordedGrip> retrieveSection(String sectionName) {
  // dummy return until we have a real implementation
  return new ArrayList<RecordedGrip>();
}
```

First implementation that opens a bad path:

```java
public List<RecordedGrip> retrieveSection(String sectionName) {
  try {
    FileInputStream stream = new FileInputStream(sectionName);
  } catch (Exception e) {
    throw new StorageException("retrieval error", e);
  }
  return new ArrayList<RecordedGrip>();
}
```

Narrow the catch to what is actually thrown:

```java
public List<RecordedGrip> retrieveSection(String sectionName) {
  try {
    FileInputStream stream = new FileInputStream(sectionName);
    stream.close();
  } catch (FileNotFoundException e) {
    throw new StorageException("retrieval error", e);
  }
  return new ArrayList<RecordedGrip>();
}
```

With the **transaction shape** fixed, you grow real behavior inside the `try` using TDD, as if failures did not exist, then keep the boundary honest.

## Use unchecked exceptions

The chapter argues **checked exceptions** often cost more than they return for **application** code: a new low-level checked type can force **signature changes** and rebuilds **up the entire stack**, which **breaks encapsulation** - the opposite of “handle errors at a distance.” Critical libraries may still justify checked types; most apps do better with **unchecked** exceptions (as in C#, Python, Ruby, and typical C++).

## Provide context with exceptions

Stack traces show **where**, not **what you were trying to do**. Attach **clear messages**, include the **operation** and **failure mode**, and preserve **cause** chains for logs.

## Define exception classes in terms of a caller’s needs

If every failure is handled the same way, many parallel `catch` types are **duplication**. Prefer one type (or a small family) that matches **recovery policy**, and put vendor-specific translation in one place.

### Third-party `ACMEPort` (many exception types)

```java
ACMEPort port = new ACMEPort(12);
try {
  port.open();
} catch (DeviceResponseException e) {
  reportPortError(e);
  logger.log("Device response exception", e);
} catch (ATM1212UnlockedException e) {
  reportPortError(e);
  logger.log("Unlock exception", e);
} catch (GMXError e) {
  reportPortError(e);
  logger.log("Device response exception");
} finally {
  // ...
}
```

### Wrapped `LocalPort` (one application exception)

```java
LocalPort port = new LocalPort(12);
try {
  port.open();
} catch (PortDeviceFailure e) {
  reportError(e);
  logger.log(e.getMessage(), e);
} finally {
  // ...
}
```

```java
public class LocalPort {
  private ACMEPort innerPort;

  public LocalPort(int portNumber) {
    innerPort = new ACMEPort(portNumber);
  }

  public void open() {
    try {
      innerPort.open();
    } catch (DeviceResponseException e) {
      throw new PortDeviceFailure(e);
    } catch (ATM1212UnlockedException e) {
      throw new PortDeviceFailure(e);
    } catch (GMXError e) {
      throw new PortDeviceFailure(e);
    }
  }

  // ...
}
```

**Wrappers** reduce vendor lock-in, ease mocking, and let you define APIs (and exception shapes) you are willing to live with.

## Define the normal flow (Special Case Pattern)

Sometimes you should not **abort** the main story for a “missing” case. If meal expenses are missing, returning a **special-case** `MealExpenses` whose `getTotal()` is the per diem removes `catch` noise from the caller.

```java
try {
  MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
  m_total += expenses.getTotal();
} catch (MealExpensesNotFound e) {
  m_total += getMealPerDiem();
}
```

Preferred when `getMeals` always returns a usable object:

```java
MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
m_total += expenses.getTotal();
```

```java
public class PerDiemMealExpenses implements MealExpenses {
  public int getTotal() {
    // return the per diem default
    return 0;
  }
}
```

This is Fowler’s **Special Case** pattern - the odd case lives in one object, not in every caller.

## Don’t return null

Returning `null` creates endless **defensive** checks and one miss yields `NullPointerException` deep in the system.

```java
public void registerItem(Item item) {
  if (item != null) {
    ItemRegistry registry = peristentStore.getItemRegistry();
    if (registry != null) {
      Item existing = registry.getItem(item.getID());
      if (existing.getBillingPeriod().hasRetailOwner()) {
        existing.register(item);
      }
    }
  }
}
```

(The book uses this style to show missing guards and **too many** null checks. The store name is spelled `peristentStore` in the original listing.)

Prefer **exceptions**, **empty collections**, or **special-case objects** instead of `null` as a signal.

```java
List<Employee> employees = getEmployees();
if (employees != null) {
  for (Employee e : employees) {
    totalPay += e.getPay();
  }
}
```

```java
List<Employee> employees = getEmployees();
for (Employee e : employees) {
  totalPay += e.getPay();
}
```

```java
public List<Employee> getEmployees() {
  // if there are no employees ..
  return Collections.emptyList();
}
```

## Don’t pass null

Passing `null` is worse than returning it - there is often **no good recovery** beyond crashing.

```java
public class MetricsCalculator {
  public double xProjection(Point p1, Point p2) {
    return (p2.x - p1.x) * 1.5;
  }

  // ...
}
```

Call with `null` still blows up at runtime. Explicit checks that throw may be only slightly clearer:

```java
public class MetricsCalculator {
  public double xProjection(Point p1, Point p2) {
    if (p1 == null || p2 == null) {
      throw new IllegalArgumentException(
          "Invalid argument for MetricsCalculator.xProjection");
    }
    return (p2.x - p1.x) * 1.5;
  }
}
```

Assertions document expectations but **do not remove** the runtime hazard if callers ignore them:

```java
public class MetricsCalculator {
  public double xProjection(Point p1, Point p2) {
    assert p1 != null : "p1 should not be null";
    assert p2 != null : "p2 should not be null";
    return (p2.x - p1.x) * 1.5;
  }
}
```

**Default rule:** do not accept `null` arguments unless an external API forces you to.

## Conclusion

Clean and **robust** are compatible when error handling is a **separate concern** you can read and change on its own. Exceptions, boundaries, special cases, and a **no-null** discipline keep the main narrative visible.
