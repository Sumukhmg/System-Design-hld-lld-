# Qualcomm — System Design & Design Problems

[← Company Index](./README.md) | [Back to Hub](../README.md)

Qualcomm is primarily a **semiconductor / embedded systems** company (Snapdragon SoCs, modems, IoT). Its design rounds differ from web-scale companies: expect **C/C++ object-oriented design**, **operating-system & concurrency** concepts, **memory/resource constraints**, and **embedded/firmware** scenarios — plus some classic LLD. Web-scale HLD is less common but can appear for cloud/services or platform-software roles.

---

## 🔶 LLD / OOD in C++ (most common)

| # | Problem | Patterns / concepts | Resource |
|---|---------|---------------------|----------|
| 1 | Design a parking lot | Strategy, Factory | [parking-lot](../lld/problems/parking-lot.md) |
| 2 | Design an elevator controller | State, scheduling | [elevator-system](../lld/problems/elevator-system.md) |
| 3 | Design a vending machine | State | [vending-machine](../lld/problems/vending-machine.md) |
| 4 | Design a thread-safe LRU cache | DLL + HashMap, mutex | [caching](../hld/building-blocks/caching.md) |
| 5 | Design a memory allocator (malloc/free) | free lists, fragmentation | OS concepts (below) |
| 6 | Design a logger (thread-safe, levels) | Singleton, Chain of Responsibility | [behavioral](../lld/design-patterns/behavioral.md) |
| 7 | Design a producer-consumer / bounded buffer | condition variables, mutex | concurrency (below) |
| 8 | Design a state machine for a device driver / protocol | State pattern, FSM | [state pattern](../lld/design-patterns/behavioral.md) |
| 9 | Design a circular/ring buffer | fixed memory, lock-free options | embedded |
| 10 | Design an event/callback system | Observer | [observer](../lld/design-patterns/behavioral.md) |

> Qualcomm values **clean C++**: RAII, smart pointers, `const`-correctness, no leaks, and understanding **what happens under the hood** (vtables, memory layout).

---

## ⚙️ Operating Systems & Concurrency (frequently probed)

These come up because Qualcomm writes low-level/firmware code:

- **Threads vs processes**, context switching, scheduling.
- **Synchronization:** mutex, semaphore, spinlock, condition variable; when to use which.
- **Deadlock** — the 4 Coffman conditions, prevention/avoidance (banker's algorithm), detection.
- **Producer–consumer**, **readers–writers**, **dining philosophers** — implement with primitives.
- **Memory management:** stack vs heap, paging, virtual memory, fragmentation, cache coherence.
- **Interrupts & ISRs**, DMA, real-time constraints (RTOS).
- **Lock-free / atomic** programming (`std::atomic`, memory ordering) for performance-critical paths.

> Example: *"Implement a thread-safe bounded queue"* — use a mutex + two condition variables (not-full, not-empty). Be precise about wakeups and spurious wakeups.

---

## 📟 Embedded / Systems Design Scenarios

| Scenario | What they probe |
|----------|-----------------|
| Design firmware for a sensor that batches & uploads data | power/memory constraints, buffering, retries |
| Design a communication protocol between two chips | framing, checksums, state machine, flow control |
| Design an interrupt-driven UART/I2C driver | ISRs, ring buffers, concurrency |
| Design a task scheduler for an RTOS | priorities, preemption, starvation |
| Design over-the-air (OTA) firmware update | integrity, rollback, A/B partitions |

Key themes: **bounded resources**, **determinism/real-time**, **reliability**, **low power**.

---

## ☁️ HLD (for cloud / platform / services roles)
If interviewing for a services/cloud team, standard HLD applies:
- [Rate limiter](../hld/case-studies/rate-limiter.md), [notification system](../hld/case-studies/notification-system.md), [key-value store](../hld/case-studies/key-value-store.md), telemetry/metrics ingestion pipelines (devices → [Kafka](../hld/building-blocks/message-queues.md) → storage → dashboards).
- IoT device fleet management: millions of devices streaming telemetry → ingestion, time-series storage, command/control.

---

## 💡 Qualcomm-Specific Tips
- **Brush up C++ deeply:** pointers, references, virtual functions/vtables, memory model, smart pointers, move semantics, RAII.
- **OS & concurrency are first-class** — more than at typical web companies. Know synchronization primitives cold.
- Think about **constraints**: limited RAM, real-time deadlines, power. Solutions that "just add a server" don't apply to a chip.
- For LLD, emphasize **thread safety** and **resource management** (no leaks, deterministic cleanup).
- Be ready to **reason about hardware/software boundaries** (interrupts, DMA, registers) for firmware roles.

---

## Key Takeaways
- Qualcomm leans **embedded/systems + C++ OOD + OS/concurrency**, not web-scale HLD (except cloud teams).
- Master **LLD in clean C++** (RAII, smart pointers) with **thread safety** (mutexes, condition variables, atomics).
- Deeply review **OS concepts**: scheduling, deadlock, synchronization, memory management, interrupts, RTOS.
- Practice **embedded scenarios** with **bounded resources, determinism, and reliability** in mind.
- For services roles, standard HLD (telemetry ingestion, rate limiting, KV stores) still applies.

---
[← Company Index](./README.md) | [Back to Hub](../README.md)
