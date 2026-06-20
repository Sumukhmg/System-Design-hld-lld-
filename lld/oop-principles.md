# OOP Principles

[← LLD Index](./README.md) | [Back to Hub](../README.md)

The **four pillars** of Object-Oriented Programming. Every LLD answer rests on these.

---

## 1. Encapsulation — "Bundle & Hide"

Bundle data (fields) and the methods operating on it into one unit (class), and **hide internal state** behind a controlled interface (getters/setters/methods). Outside code can't tamper with internals directly.

```java
class BankAccount {
    private double balance;                 // hidden state

    public void deposit(double amount) {    // controlled access
        if (amount <= 0) throw new IllegalArgumentException();
        balance += amount;
    }
    public double getBalance() { return balance; }
}
// account.balance = -1000;  ❌ not allowed — protected by encapsulation
```

- **Why:** protects invariants (balance can't go invalid), reduces coupling, allows internal changes without breaking callers.
- **Keywords:** `private`, `protected`, `public`.

---

## 2. Abstraction — "Show essentials, hide complexity"

Expose **what** an object does, hide **how** it does it. Users interact with a simple interface without knowing internal mechanics.

```java
interface PaymentGateway {
    boolean pay(double amount);     // WHAT — caller only knows this
}

class StripeGateway implements PaymentGateway {
    public boolean pay(double amount) {
        // HOW — API calls, retries, tokens... all hidden
        return true;
    }
}
```

- **Analogy:** driving a car — you use the steering wheel & pedals (interface), not the engine internals.
- **Achieved via:** `interface` and `abstract` classes.
- **Encapsulation vs Abstraction:** encapsulation hides *data*; abstraction hides *complexity/implementation*. They work together.

---

## 3. Inheritance — "is-a" reuse

A subclass inherits fields/methods from a parent class, enabling reuse and specialization.

```java
class Vehicle {
    protected String brand;
    void start() { System.out.println("Starting..."); }
}
class Car extends Vehicle {        // Car IS-A Vehicle
    int numDoors;
    void openTrunk() { ... }
}
```

- **Pros:** code reuse, polymorphism, models real "is-a" relationships.
- **Cons / cautions:**
  - **Tight coupling** to the parent; changes ripple down.
  - **Fragile base class** problem; deep hierarchies are hard to maintain.
  - ⚠️ **Favor composition over inheritance** — model "has-a" with member objects instead of forcing "is-a". Use inheritance only for genuine is-a relationships.

```java
// Composition (often better):
class Car {
    private Engine engine;   // Car HAS-A Engine
}
```

---

## 4. Polymorphism — "many forms"

The same interface/method behaves differently depending on the actual object. Lets you write code against abstractions.

### Compile-time (Overloading)
Same method name, different parameters.
```java
int add(int a, int b);
double add(double a, double b);
int add(int a, int b, int c);
```

### Runtime (Overriding) ⭐
Subclass redefines a parent method; the **actual object's** method runs (dynamic dispatch).
```java
class Shape { double area() { return 0; } }
class Circle extends Shape { double area() { return Math.PI*r*r; } }
class Square extends Shape { double area() { return s*s; } }

Shape shape = new Circle();   // reference type Shape
shape.area();                 // runs Circle.area() — chosen at runtime
```

- **Why it matters:** you can process a `List<Shape>` calling `area()` on each without knowing concrete types — the engine of extensibility and the [Strategy](./design-patterns/behavioral.md) pattern.

---

## Quick Summary

| Pillar | One-liner | Mechanism |
|--------|-----------|-----------|
| **Encapsulation** | Bundle data + methods, hide state | `private` fields, getters/setters |
| **Abstraction** | Expose what, hide how | `interface`, `abstract` |
| **Inheritance** | Reuse via "is-a" | `extends` (prefer composition!) |
| **Polymorphism** | One interface, many behaviors | overriding / overloading |

---

## How These Power LLD
- **Abstraction + Polymorphism** → program to **interfaces**, swap implementations → **Open/Closed** ([SOLID](./solid-principles.md)) and **Strategy** pattern.
- **Encapsulation** → each class guards its own state → low coupling.
- **Composition over inheritance** → flexible designs (Decorator, Strategy).

---

## Key Takeaways
- Four pillars: **Encapsulation** (hide state), **Abstraction** (hide complexity), **Inheritance** (is-a reuse), **Polymorphism** (one interface, many forms).
- **Favor composition over inheritance** — avoid fragile deep hierarchies.
- **Program to interfaces** (abstraction + polymorphism) for extensible designs.
- These pillars underpin **SOLID** and every **design pattern**.

---
[← LLD Index](./README.md) | [Back to Hub](../README.md)
