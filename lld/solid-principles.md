# SOLID Principles

[← LLD Index](./README.md) | [Back to Hub](../README.md)

**SOLID** is five principles (Robert C. Martin) for writing maintainable, extensible OO code. Interviewers expect you to name them *and* apply them.

| Letter | Principle |
|--------|-----------|
| **S** | Single Responsibility |
| **O** | Open/Closed |
| **L** | Liskov Substitution |
| **I** | Interface Segregation |
| **D** | Dependency Inversion |

---

## S — Single Responsibility Principle (SRP)

> **A class should have only one reason to change** — one job.

❌ **Bad:** one class does everything.
```java
class User {
    void save() { /* DB logic */ }
    void sendEmail() { /* email logic */ }
    void generateReport() { /* reporting */ }
}
```
A change to email logic, DB logic, or reporting all touch `User` → fragile.

✅ **Good:** split responsibilities.
```java
class User { /* user data only */ }
class UserRepository { void save(User u) {} }
class EmailService { void send(User u) {} }
class ReportGenerator { void generate(User u) {} }
```
- **Benefit:** each class changes for one reason; easier testing & reuse.

---

## O — Open/Closed Principle (OCP)

> **Open for extension, closed for modification.** Add new behavior without changing existing code.

❌ **Bad:** modifying a method every time a new type appears.
```java
double area(Shape s) {
    if (s.type == CIRCLE) return ...;
    else if (s.type == SQUARE) return ...;   // must edit this for every new shape
}
```

✅ **Good:** extend via polymorphism (new class, no edits).
```java
interface Shape { double area(); }
class Circle implements Shape { public double area() { return Math.PI*r*r; } }
class Square implements Shape { public double area() { return s*s; } }
// Add Triangle → new class only; existing code untouched.
```
- **Achieved with:** abstraction + polymorphism (Strategy, Factory).

---

## L — Liskov Substitution Principle (LSP)

> **Subtypes must be substitutable for their base type** without breaking behavior. A subclass shouldn't violate the contract of its parent.

❌ **Classic violation:** `Square extends Rectangle`, but setting width/height independently breaks Rectangle's contract. Or:
```java
class Bird { void fly() {} }
class Ostrich extends Bird {
    void fly() { throw new UnsupportedOperationException(); }  // ❌ breaks substitutability
}
```
A function expecting `Bird.fly()` breaks when given an Ostrich.

✅ **Fix:** model correctly.
```java
interface Bird {}
interface FlyingBird extends Bird { void fly(); }
class Sparrow implements FlyingBird { public void fly() {} }
class Ostrich implements Bird {}   // doesn't promise fly()
```
- **Rule of thumb:** if a subclass can't honor the parent's promises, the hierarchy is wrong.

---

## I — Interface Segregation Principle (ISP)

> **Don't force clients to depend on methods they don't use.** Prefer many small, specific interfaces over one fat interface.

❌ **Bad:** fat interface.
```java
interface Worker {
    void work();
    void eat();
}
class Robot implements Worker {
    public void work() {}
    public void eat() { throw new UnsupportedOperationException(); }  // ❌ robots don't eat
}
```

✅ **Good:** split.
```java
interface Workable { void work(); }
interface Eatable { void eat(); }
class Human implements Workable, Eatable { ... }
class Robot implements Workable { ... }
```
- **Benefit:** classes implement only what's relevant; no empty/throwing methods.

---

## D — Dependency Inversion Principle (DIP)

> **Depend on abstractions, not concretions.** High-level modules shouldn't depend on low-level modules; both depend on interfaces.

❌ **Bad:** high-level class hard-wired to a concrete dependency.
```java
class NotificationService {
    private EmailSender email = new EmailSender();   // ❌ tightly coupled
    void notify(String msg) { email.send(msg); }
}
```
Can't switch to SMS without editing `NotificationService`.

✅ **Good:** depend on an interface; inject the implementation.
```java
interface MessageSender { void send(String msg); }
class EmailSender implements MessageSender { public void send(String m) {} }
class SmsSender   implements MessageSender { public void send(String m) {} }

class NotificationService {
    private final MessageSender sender;
    NotificationService(MessageSender sender) { this.sender = sender; }  // injected
    void notify(String msg) { sender.send(msg); }
}
```
- **Enables:** Dependency Injection, easy testing (mock the interface), swappable implementations.

---

## SOLID at a Glance

| Principle | Smell it fixes | Tool |
|-----------|----------------|------|
| **SRP** | God class, mixed concerns | Split classes |
| **OCP** | `if/else`/`switch` on type, editing to extend | Polymorphism, Strategy |
| **LSP** | Subclass throwing/ignoring parent methods | Correct hierarchy |
| **ISP** | Fat interface, empty methods | Small interfaces |
| **DIP** | `new ConcreteClass()` in high-level code | Interfaces + injection |

---

## Why SOLID Wins Interviews
Applying SOLID naturally produces designs that are **extensible** (the #1 follow-up: "now add feature X"), **testable**, and **decoupled**. When you justify a class split or an interface with "this follows SRP/OCP/DIP," you signal senior-level design thinking.

---

## Key Takeaways
- **S**RP: one class, one reason to change. **O**CP: extend without modifying. **L**SP: subtypes substitutable. **I**SP: small focused interfaces. **D**IP: depend on abstractions + inject.
- SOLID = the rationale behind most **design patterns**.
- It produces **extensible, testable, loosely-coupled** code — exactly what LLD interviews reward.
- Don't quote SOLID dogmatically; **apply** it and explain the trade-off.

---
[← LLD Index](./README.md) | [Back to Hub](../README.md)
