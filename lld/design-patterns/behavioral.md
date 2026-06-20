# Behavioral Design Patterns

[← LLD Index](../README.md) | [Back to Hub](../../README.md)

Behavioral patterns deal with **communication and responsibility** between objects — how they interact and distribute work.

| Pattern | Intent |
|---------|--------|
| [Strategy](#1-strategy) | Swap algorithms at runtime |
| [Observer](#2-observer) | Notify dependents of state changes |
| [State](#3-state) | Change behavior based on internal state |
| [Command](#4-command) | Encapsulate a request as an object |
| [Template Method](#5-template-method) | Fixed skeleton, overridable steps |
| [Chain of Responsibility](#6-chain-of-responsibility) | Pass a request along handlers |

---

## 1. Strategy ⭐ (the most-used pattern in LLD)

> Define a family of **interchangeable algorithms**, encapsulate each, and make them swappable at runtime. Favors **composition over inheritance**.

**Use:** payment methods, sorting strategies, pricing/discount rules, split logic ([Splitwise](../problems/splitwise.md)).

```java
interface PaymentStrategy { void pay(double amount); }
class CreditCardPayment implements PaymentStrategy { public void pay(double a){} }
class UpiPayment        implements PaymentStrategy { public void pay(double a){} }
class PayPalPayment     implements PaymentStrategy { public void pay(double a){} }

class Checkout {
    private PaymentStrategy strategy;                       // composed strategy
    void setStrategy(PaymentStrategy s){ this.strategy = s; }
    void pay(double amt){ strategy.pay(amt); }             // delegates
}
// Swap at runtime:
checkout.setStrategy(new UpiPayment());
checkout.pay(500);
```
- **Benefit:** add a new algorithm → new class, no edits to `Checkout` (**OCP/DIP**). Eliminates big `if/else` blocks.

---

## 2. Observer ⭐

> Define a **one-to-many** dependency: when one object (subject) changes state, all its **observers** are notified automatically.

**Use:** event systems, pub/sub, UI listeners, [notification systems](../../hld/case-studies/notification-system.md), stock tickers.

```java
interface Observer { void update(String news); }
class Subscriber implements Observer {
    private String name;
    public void update(String news){ System.out.println(name + " got: " + news); }
}
class NewsAgency {                                   // subject
    private List<Observer> observers = new ArrayList<>();
    public void subscribe(Observer o){ observers.add(o); }
    public void unsubscribe(Observer o){ observers.remove(o); }
    public void publish(String news){
        for (Observer o : observers) o.update(news);  // notify all
    }
}
```
- **Benefit:** loose coupling — subject doesn't know concrete observers, only the interface. Basis of event-driven systems and [Kafka pub/sub](../../hld/building-blocks/message-queues.md) at the conceptual level.

---

## 3. State ⭐

> Allow an object to **alter its behavior when its internal state changes** — it appears to change class. Replaces sprawling state `if/else`.

**Use:** [vending machine](../problems/vending-machine.md), [elevator](../problems/elevator-system.md), order lifecycle, TCP connection, traffic lights, media player.

```java
interface State { void next(VendingMachine m); void insertCoin(VendingMachine m); }

class IdleState implements State {
    public void insertCoin(VendingMachine m){ m.setState(new HasCoinState()); }
    public void next(VendingMachine m){ /* no-op */ }
}
class HasCoinState implements State {
    public void insertCoin(VendingMachine m){ /* already has coin */ }
    public void next(VendingMachine m){ m.setState(new DispensingState()); }
}
class DispensingState implements State {
    public void insertCoin(VendingMachine m){ /* wait */ }
    public void next(VendingMachine m){ m.setState(new IdleState()); }
}
class VendingMachine {
    private State state = new IdleState();
    public void setState(State s){ this.state = s; }
    public void insertCoin(){ state.insertCoin(this); }
    public void next(){ state.next(this); }
}
```
- **Benefit:** each state's logic is isolated in its own class; transitions are explicit. No giant `switch(currentState)`.
- **State vs Strategy:** structurally similar (both delegate to a composed object). **Strategy** = client picks an algorithm; **State** = the object transitions itself between states.

---

## 4. Command

> Encapsulate a **request as an object**, letting you parameterize, queue, log, and **undo** operations.

**Use:** undo/redo, task queues, remote controls, transactional operations, GUI buttons.

```java
interface Command { void execute(); void undo(); }
class Light { void on(){} void off(){} }

class LightOnCommand implements Command {
    private Light light;
    LightOnCommand(Light l){ this.light = l; }
    public void execute(){ light.on(); }
    public void undo(){ light.off(); }
}
class RemoteControl {                       // invoker
    private Command command;
    void setCommand(Command c){ this.command = c; }
    void pressButton(){ command.execute(); }
    void pressUndo(){ command.undo(); }
}
```
- **Benefit:** decouples invoker from receiver; supports undo/redo, macro/queued commands.

---

## 5. Template Method

> Define the **skeleton of an algorithm** in a base method, deferring some steps to subclasses (without changing the overall structure).

**Use:** data processing pipelines, frameworks (the "Hollywood principle": don't call us, we'll call you).

```java
abstract class DataProcessor {
    public final void process() {     // template — fixed sequence
        read(); transform(); save();
    }
    abstract void read();
    abstract void transform();
    void save(){ /* default save */ }    // hook with default
}
class CsvProcessor extends DataProcessor {
    void read(){} void transform(){}     // customize specific steps
}
```
- **Strategy vs Template Method:** Strategy uses **composition** (swap whole algorithm); Template Method uses **inheritance** (override steps of a fixed algorithm).

---

## 6. Chain of Responsibility

> Pass a request along a **chain of handlers**; each decides to handle it or pass it on.

**Use:** middleware pipelines, logging levels, approval workflows, event bubbling, [rate limiting / auth filters](../../hld/building-blocks/api-gateway.md).

```java
abstract class Handler {
    protected Handler next;
    public Handler setNext(Handler n){ this.next = n; return n; }
    public abstract void handle(Request r);
}
class AuthHandler extends Handler {
    public void handle(Request r){
        if (!r.isAuthenticated()) { /* reject */ return; }
        if (next != null) next.handle(r);     // pass along
    }
}
class RateLimitHandler extends Handler { public void handle(Request r){ /* ... */ } }
// chain: auth.setNext(rateLimit).setNext(logging);
```
- **Benefit:** decouples sender from receivers; flexible, ordered processing pipeline.

---

## Quick Comparison

| Pattern | Core idea |
|---------|-----------|
| Strategy | Swap algorithm (composition) |
| Observer | Auto-notify dependents (1-to-many) |
| State | Behavior changes with internal state |
| Command | Request as an object (undo/queue) |
| Template Method | Fixed skeleton, overridable steps |
| Chain of Responsibility | Pass request through handlers |

---

## Key Takeaways
- **Strategy** (swap algorithms) and **Observer** (event notifications) and **State** (state-driven behavior) are the **most common in LLD** — know them cold.
- **Strategy vs State:** same structure, different intent (client-chosen algorithm vs self-driven transitions).
- **Command** enables undo/redo & queuing; **Template Method** fixes a skeleton with overridable steps; **Chain of Responsibility** builds processing pipelines.
- These patterns embody **OCP + DIP** — they let you extend behavior without modifying existing code.

---
[← Structural](./structural.md) | [LLD Problems →](../problems/parking-lot.md)
