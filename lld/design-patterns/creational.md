# Creational Design Patterns

[← LLD Index](../README.md) | [Back to Hub](../../README.md)

Creational patterns deal with **object creation** — making it flexible, controlled, and decoupled from the concrete classes used.

| Pattern | Intent |
|---------|--------|
| [Singleton](#1-singleton) | Exactly one instance, global access |
| [Factory Method](#2-factory-method) | Delegate object creation to subclasses |
| [Abstract Factory](#3-abstract-factory) | Create families of related objects |
| [Builder](#4-builder) | Construct complex objects step by step |
| [Prototype](#5-prototype) | Create objects by cloning |

---

## 1. Singleton

> Ensure a class has **only one instance** and provide a global point of access.

**Use:** config managers, logging, DB connection pools, caches.

```java
public class Logger {
    private static volatile Logger instance;
    private Logger() {}                          // private constructor

    public static Logger getInstance() {
        if (instance == null) {                  // double-checked locking
            synchronized (Logger.class) {
                if (instance == null) {
                    instance = new Logger();
                }
            }
        }
        return instance;
    }
    public void log(String msg) { System.out.println(msg); }
}
```

- **Thread safety:** use double-checked locking (`volatile`), or an **eager** static instance, or the **enum** singleton (best in Java).
```java
public enum Logger { INSTANCE; public void log(String m){...} }   // simplest thread-safe singleton
```
- ⚠️ **Downsides:** global state, hard to unit-test (hidden dependency), can hide bad design. Use sparingly.

---

## 2. Factory Method

> Define an interface for creating an object, but let **subclasses/factory decide which class** to instantiate. Removes `new ConcreteClass()` from client code.

**Use:** when the exact type isn't known until runtime, or to centralize creation logic.

```java
interface Notification { void notifyUser(); }
class EmailNotification implements Notification { public void notifyUser(){} }
class SmsNotification   implements Notification { public void notifyUser(){} }

class NotificationFactory {
    public Notification create(String type) {
        switch (type) {
            case "EMAIL": return new EmailNotification();
            case "SMS":   return new SmsNotification();
            default: throw new IllegalArgumentException();
        }
    }
}
// client: Notification n = factory.create("EMAIL");  — decoupled from concrete classes
```
- **Benefit:** client depends on the `Notification` interface, not concrete classes (**OCP/DIP**). Add a new type → new class + one factory line.

---

## 3. Abstract Factory

> Create **families of related objects** without specifying concrete classes — a "factory of factories."

**Use:** cross-platform UI kits (Windows vs Mac buttons + checkboxes), theme systems.

```java
interface Button { void render(); }
interface Checkbox { void render(); }

interface GUIFactory {                  // abstract factory
    Button createButton();
    Checkbox createCheckbox();
}
class WindowsFactory implements GUIFactory {
    public Button createButton() { return new WindowsButton(); }
    public Checkbox createCheckbox() { return new WindowsCheckbox(); }
}
class MacFactory implements GUIFactory {
    public Button createButton() { return new MacButton(); }
    public Checkbox createCheckbox() { return new MacCheckbox(); }
}
```
- **Factory Method vs Abstract Factory:** Factory Method creates **one** product; Abstract Factory creates a **family** of related products consistently.

---

## 4. Builder ⭐

> Construct a **complex object step by step**, separating construction from representation. Avoids "telescoping constructors" (many overloaded constructors).

**Use:** objects with many optional fields (e.g., building an HTTP request, a `Pizza`, a `User`).

```java
class Burger {
    private final String bun;
    private final boolean cheese, lettuce, tomato;

    private Burger(Builder b) {            // private; built via Builder
        this.bun = b.bun; this.cheese = b.cheese;
        this.lettuce = b.lettuce; this.tomato = b.tomato;
    }
    static class Builder {
        private String bun;
        private boolean cheese, lettuce, tomato;
        Builder bun(String b){ this.bun=b; return this; }
        Builder cheese(boolean c){ this.cheese=c; return this; }
        Builder lettuce(boolean l){ this.lettuce=l; return this; }
        Builder tomato(boolean t){ this.tomato=t; return this; }
        Burger build(){ return new Burger(this); }   // returns the product
    }
}
// Fluent usage:
Burger b = new Burger.Builder().bun("wheat").cheese(true).tomato(true).build();
```
- **Benefit:** readable, immutable objects, no constructor explosion, optional params handled cleanly.

---

## 5. Prototype

> Create new objects by **cloning** an existing instance (the prototype) instead of constructing from scratch.

**Use:** when object creation is expensive, or you need copies of a configured object.

```java
interface Prototype { Prototype clone(); }
class Document implements Prototype {
    String content;
    public Document clone() {
        Document d = new Document();
        d.content = this.content;        // copy state (deep copy if nested)
    return d;
    }
}
```
- **Watch out:** **shallow vs deep copy** — nested mutable objects must be deep-copied to avoid shared references.

---

## Quick Comparison

| Pattern | Creates | Key idea |
|---------|---------|----------|
| Singleton | One shared instance | Global single access |
| Factory Method | One product (subtype chosen at runtime) | Decouple creation |
| Abstract Factory | A family of products | Consistent product sets |
| Builder | One complex object | Step-by-step, optional fields |
| Prototype | Copy of an object | Clone instead of construct |

---

## Key Takeaways
- **Singleton:** one instance (use enum / double-checked locking; beware global state).
- **Factory Method / Abstract Factory:** decouple clients from concrete classes (supports **OCP/DIP**); abstract factory makes **families**.
- **Builder:** clean construction of objects with many optional fields (fluent, immutable).
- **Prototype:** clone existing objects; mind **deep vs shallow** copy.

---
[← LLD Index](../README.md) | [Structural Patterns →](./structural.md)
