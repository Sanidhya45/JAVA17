## Sealed Class in Java

**Sealed Classes** were introduced in Java 15 (preview) and became a standard feature in Java 17.

A sealed class allows you to **restrict which classes can extend or implement it**.

### Why do we need Sealed Classes?

Before sealed classes:

```java
public class Vehicle {
}
```

Any class could extend `Vehicle`:

```java
class Car extends Vehicle {}
class Bike extends Vehicle {}
class Truck extends Vehicle {}
class RandomVehicle extends Vehicle {}
```

Sometimes you want to control the inheritance hierarchy and allow only a fixed set of subclasses.

Sealed classes solve this problem.

---

## Declaration Syntax

```java
public sealed class Vehicle
    permits Car, Bike, Truck {
}
```

Only the classes listed in `permits` can extend `Vehicle`.

```java
public final class Car extends Vehicle {
}

public final class Bike extends Vehicle {
}

public final class Truck extends Vehicle {
}
```

If another class tries to extend it:

```java
class Bus extends Vehicle {
}
```

Compilation error:

```text
Class is not allowed to extend sealed class Vehicle
```

---

## Rules for Subclasses

Every subclass of a sealed class must declare one of the following:

### 1. final

Cannot be extended further.

```java
public final class Car extends Vehicle {
}
```

---

### 2. sealed

Allows only specific subclasses.

```java
public sealed class Car extends Vehicle
    permits ElectricCar, PetrolCar {
}
```

```java
public final class ElectricCar extends Car {
}

public final class PetrolCar extends Car {
}
```

---

### 3. non-sealed

Removes the restriction and allows normal inheritance.

```java
public non-sealed class Truck extends Vehicle {
}
```

Now anyone can extend `Truck`.

```java
class MiniTruck extends Truck {
}
```

---

## Real-World Use Case

### Payment System

You have only three payment types:

```java
public sealed interface Payment
    permits CreditCardPayment,
            UPIPayment,
            NetBankingPayment {
}
```

Implementations:

```java
public final class CreditCardPayment
        implements Payment {
}

public final class UPIPayment
        implements Payment {
}

public final class NetBankingPayment
        implements Payment {
}
```

This ensures no unexpected payment types are introduced.

---

## Use Case with Switch Expression (Java 17+)

Sealed classes work very well with Pattern Matching.

```java
public sealed interface Shape
        permits Circle, Rectangle {
}

public record Circle(double radius)
        implements Shape {
}

public record Rectangle(double length,
                        double width)
        implements Shape {
}
```

```java
public double calculateArea(Shape shape) {
    return switch(shape) {
        case Circle c ->
                Math.PI * c.radius() * c.radius();

        case Rectangle r ->
                r.length() * r.width();
    };
}
```

Since the compiler knows all possible subclasses, no `default` case is needed.

---

## Interview Answer (6–8 Years Experience)

> A sealed class is a special type of class introduced in Java 17 that restricts inheritance to a predefined set of subclasses. It helps model closed hierarchies where only known implementations are allowed. The parent class uses the `sealed` keyword and specifies permitted subclasses using the `permits` clause. Every permitted subclass must be declared as `final`, `sealed`, or `non-sealed`.
>
> Common use cases include domain modeling, state machines, payment types, notification channels, and pattern matching with switch expressions, where the compiler can verify that all possible cases are handled.

---

## Sealed Class vs Final Class

| Feature              | Final Class               | Sealed Class                        |
| -------------------- | ------------------------- | ----------------------------------- |
| Can be inherited?    | No                        | Yes, but only by permitted classes  |
| Restricts hierarchy? | Completely                | Partially                           |
| Extensibility        | None                      | Controlled                          |
| Use case             | Immutable utility classes | Domain modeling with known subtypes |

### Example

```java
final class Employee {
}
```

No class can extend it.

```java
sealed class Employee
    permits Manager, Developer {
}
```

Only `Manager` and `Developer` can extend it.

---

## Common Interview Questions

### Q1. Can a sealed class be abstract?

Yes.

```java
public abstract sealed class Vehicle
        permits Car, Bike {
}
```

---

### Q2. Can interfaces be sealed?

Yes.

```java
public sealed interface Payment
        permits UPI, Card {
}
```

---

### Q3. What is `non-sealed`?

It removes the restriction imposed by the sealed parent.

```java
public non-sealed class Truck
        extends Vehicle {
}
```

---

### Q4. Is `permits` mandatory?

No, if all permitted subclasses are declared in the same source file.

```java
sealed class Vehicle {}

final class Car extends Vehicle {}
final class Bike extends Vehicle {}
```

The compiler can infer them.

---

### Q5. What problem does a sealed class solve?

It provides **controlled inheritance**, improves **domain modeling**, and enables **exhaustive pattern matching**, making code safer and more maintainable.
