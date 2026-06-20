# Structural Design Patterns

[← LLD Index](../README.md) | [Back to Hub](../../README.md)

Structural patterns deal with **composing classes and objects** into larger structures while keeping them flexible and efficient.

| Pattern | Intent |
|---------|--------|
| [Adapter](#1-adapter) | Make incompatible interfaces work together |
| [Decorator](#2-decorator) | Add behavior dynamically without subclassing |
| [Facade](#3-facade) | Simple interface over a complex subsystem |
| [Proxy](#4-proxy) | A stand-in controlling access to an object |
| [Composite](#5-composite) | Treat individual & groups of objects uniformly |
| [Bridge](#6-bridge) | Decouple abstraction from implementation |

---

## 1. Adapter

> Convert one interface into another that clients expect — a "translator." Lets incompatible classes collaborate.

**Use:** integrating a third-party/legacy library whose interface differs from yours.

```java
interface MediaPlayer { void play(String file); }         // what our app expects

class LegacyAudioLib {                                     // incompatible 3rd-party
    void playMp3(String filename) { /* ... */ }
}

class AudioAdapter implements MediaPlayer {                // the adapter
    private LegacyAudioLib legacy = new LegacyAudioLib();
    public void play(String file) { legacy.playMp3(file); }  // translate the call
}
```
- **Real-world:** power plug adapters; `Arrays.asList()`. Wraps the adaptee and maps methods.

---

## 2. Decorator ⭐

> **Attach responsibilities to an object dynamically** by wrapping it. A flexible alternative to subclassing (avoids class explosion).

**Use:** add features in combinations — coffee + milk + sugar; I/O streams; UI borders/scrollbars.

```java
interface Coffee { double cost(); String desc(); }
class SimpleCoffee implements Coffee {
    public double cost(){ return 2.0; } public String desc(){ return "Coffee"; }
}
abstract class CoffeeDecorator implements Coffee {        // wraps a Coffee
    protected Coffee inner;
    CoffeeDecorator(Coffee c){ this.inner = c; }
}
class MilkDecorator extends CoffeeDecorator {
    MilkDecorator(Coffee c){ super(c); }
    public double cost(){ return inner.cost() + 0.5; }
    public String desc(){ return inner.desc() + " + Milk"; }
}
class SugarDecorator extends CoffeeDecorator {
    SugarDecorator(Coffee c){ super(c); }
    public double cost(){ return inner.cost() + 0.25; }
    public String desc(){ return inner.desc() + " + Sugar"; }
}
// Stack decorators dynamically:
Coffee c = new SugarDecorator(new MilkDecorator(new SimpleCoffee()));
c.cost();  // 2.75 — "Coffee + Milk + Sugar"
```
- **Benefit:** mix-and-match behaviors at runtime without 2ⁿ subclasses (**OCP**). Java's `BufferedReader(new FileReader(...))` is exactly this.

---

## 3. Facade

> Provide a **simplified, unified interface** to a complex subsystem. Hides the messy internals.

**Use:** a one-call API over many subsystems (e.g., `computer.start()` hiding CPU/RAM/disk boot steps).

```java
class CPU { void start(){} }
class Memory { void load(){} }
class Disk { void read(){} }

class ComputerFacade {                  // the facade
    private CPU cpu = new CPU();
    private Memory mem = new Memory();
    private Disk disk = new Disk();
    public void start() {               // one simple method
        cpu.start(); mem.load(); disk.read();
    }
}
// client: new ComputerFacade().start();  — doesn't touch subsystems directly
```
- **Benefit:** reduces coupling between client and subsystem; easier to use. (An [API Gateway](../../hld/building-blocks/api-gateway.md) is a system-level facade.)

---

## 4. Proxy

> A **placeholder/surrogate** that controls access to another object — same interface, extra control.

**Types & uses:**
- **Virtual proxy** — lazy-load expensive objects (load a high-res image only when shown).
- **Protection proxy** — access control/authorization.
- **Remote proxy** — local stand-in for a remote object (RPC).
- **Caching proxy** — cache results.

```java
interface Image { void display(); }
class RealImage implements Image {
    RealImage(String f){ loadFromDisk(f); }   // expensive
    void loadFromDisk(String f){}
    public void display(){}
}
class ProxyImage implements Image {           // virtual proxy
    private RealImage real; private String file;
    ProxyImage(String f){ this.file = f; }
    public void display() {
        if (real == null) real = new RealImage(file);  // load on first use
        real.display();
    }
}
```
- **Proxy vs Decorator:** both wrap an object; **proxy controls access** (same behavior), **decorator adds behavior**.

---

## 5. Composite

> Compose objects into **tree structures** and treat **individual objects and compositions uniformly** (part-whole hierarchies).

**Use:** file systems (file vs folder), UI component trees, org charts, menus.

```java
interface FileSystemNode { int size(); }            // common interface

class File implements FileSystemNode {
    private int size;
    public int size(){ return size; }                // leaf
}
class Folder implements FileSystemNode {             // composite
    private List<FileSystemNode> children = new ArrayList<>();
    public void add(FileSystemNode n){ children.add(n); }
    public int size(){
        return children.stream().mapToInt(FileSystemNode::size).sum();  // recurse
    }
}
// A folder containing files and sub-folders — handled uniformly via size().
```
- **Benefit:** client code treats a single file and a whole folder tree the same way.

---

## 6. Bridge

> **Decouple an abstraction from its implementation** so both can vary independently. Avoids a combinatorial class explosion.

**Use:** shapes × rendering APIs, remotes × devices, messages × sending platforms.

```java
interface Renderer { void render(String shape); }    // implementation side
class VectorRenderer implements Renderer { public void render(String s){} }
class RasterRenderer implements Renderer { public void render(String s){} }

abstract class Shape {                                // abstraction side
    protected Renderer renderer;                      // bridge to implementation
    Shape(Renderer r){ this.renderer = r; }
    abstract void draw();
}
class Circle extends Shape {
    Circle(Renderer r){ super(r); }
    void draw(){ renderer.render("circle"); }
}
// Circle/Square × Vector/Raster — combine freely, no NxM subclasses.
```

---

## Quick Comparison

| Pattern | Solves |
|---------|--------|
| Adapter | Incompatible interface → expected interface |
| Decorator | Add behavior by wrapping (dynamic) |
| Facade | Simplify a complex subsystem |
| Proxy | Control access to an object |
| Composite | Tree of objects treated uniformly |
| Bridge | Separate abstraction from implementation |

---

## Key Takeaways
- **Adapter** translates interfaces; **Facade** simplifies subsystems; **Proxy** controls access — all wrap, with different intent.
- **Decorator** adds behavior dynamically (avoids subclass explosion, supports **OCP**) — *adds*, while Proxy *controls*.
- **Composite** treats leaves and trees uniformly (file systems, UI trees).
- **Bridge** decouples abstraction from implementation to avoid N×M class explosions.

---
[← Creational](./creational.md) | [Behavioral Patterns →](./behavioral.md)
