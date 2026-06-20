# 03 · Latency, Throughput & Performance

[← Scalability](./02-scalability.md) | [Back to Hub](../README.md) | [Next: CAP Theorem →](./04-cap-theorem.md)

---

## Definitions

| Term | Meaning | Unit | Analogy |
|------|---------|------|---------|
| **Latency** | Time for *one* request to complete | ms / µs | Time for one car to drive the highway |
| **Throughput** | Number of requests handled per unit time | req/s, QPS, MB/s | Cars passing a point per minute |
| **Bandwidth** | Max data transfer capacity | bits/s | Number of lanes on the highway |
| **Response time** | Latency + processing + queueing as seen by client | ms | Total door-to-door trip time |

> **Latency ≠ Throughput.** A system can have low latency but low throughput (one fast worker) or high latency but high throughput (a huge batch pipeline). You often optimize one at the expense of the other.

---

## The Latency–Throughput Relationship

```
Throughput
   ▲
   │           ___________  ← saturation (queueing, latency spikes)
   │         /
   │       /
   │     /
   │   /
   │ /
   └────────────────────────► Offered Load
```

As load increases, throughput rises **until the system saturates**. Past that point, queues build up, latency explodes, and throughput plateaus or collapses. Designing for scale means pushing the saturation point higher.

---

## Percentiles — Why Averages Lie

Never describe latency with just an average. Use **percentiles (p50, p95, p99, p99.9)**.

- **p50 (median):** half of requests are faster than this.
- **p99:** 99% of requests are faster; the slowest 1% are worse.
- **Tail latency (p99/p99.9):** what your *worst* users experience.

> At scale, the tail dominates UX. If a page makes 100 backend calls and each has a p99 of 100ms, there's a high chance *at least one* call hits the tail — so the page feels slow for many users. This is the **"tail at scale"** problem (Dean & Barroso, Google).

```
Requests:  ▁▁▁▁▁▂▂▂▃▃▅█   ← most fast, a few very slow
           p50    p95  p99
```

**Interview tip:** When asked for an SLA, say something like *"p99 < 200ms"* rather than *"average 50ms."*

---

## Latency Numbers Every Engineer Should Know

(Approximate, from Jeff Dean's famous list — orders of magnitude matter more than exact figures.)

| Operation | Latency | Relative |
|-----------|---------|----------|
| L1 cache reference | 0.5 ns | 1× |
| Branch mispredict | 5 ns | 10× |
| L2 cache reference | 7 ns | 14× |
| Mutex lock/unlock | 25 ns | 50× |
| Main memory (RAM) reference | 100 ns | 200× |
| Compress 1 KB (Zippy/Snappy) | 3 µs | |
| Send 1 KB over 1 Gbps network | 10 µs | |
| Read 4 KB from SSD | 150 µs | |
| Read 1 MB sequentially from RAM | 250 µs | |
| Round trip within same datacenter | 500 µs | |
| Read 1 MB sequentially from SSD | 1 ms | |
| Disk seek (HDD) | 10 ms | |
| Read 1 MB sequentially from HDD | 20 ms | |
| Round trip **CA ↔ Netherlands** | 150 ms | |

### Takeaways from the table
- **RAM is ~100× faster than SSD; SSD is ~10–100× faster than HDD.** → cache hot data in memory.
- **Network round trips are expensive** (especially cross-region). → batch requests, co-locate data, use CDNs.
- **Sequential reads >> random reads** on disk. → design data layout for sequential access (LSM-trees, append logs).

---

## How to Reduce Latency

| Technique | How it helps |
|-----------|--------------|
| **Caching** | Serve from RAM instead of DB/disk |
| **CDN** | Move content physically closer to users |
| **Database indexing** | Avoid full table scans |
| **Connection pooling** | Avoid TCP/TLS handshake per request |
| **Async / non-blocking I/O** | Don't block threads waiting on I/O |
| **Data locality / co-location** | Reduce cross-network/region hops |
| **Compression** | Send fewer bytes over the wire |
| **Batching** | Amortize fixed costs over many items |
| **Precomputation / materialized views** | Trade write-time work for fast reads |

## How to Increase Throughput

| Technique | How it helps |
|-----------|--------------|
| **Horizontal scaling** | More workers process in parallel |
| **Load balancing** | Spread work evenly |
| **Asynchronous processing (queues)** | Decouple producers from consumers, smooth spikes |
| **Connection/thread pooling** | Reuse expensive resources |
| **Batch processing** | Higher efficiency per unit overhead |
| **Backpressure** | Prevent overload-induced collapse |

---

## Throughput vs Latency Trade-off (Batching Example)

Batching **increases throughput** (fewer fixed-cost operations) but **increases latency** for individual items (they wait to fill a batch).

```
No batching:  req → process → respond   (low latency, low throughput)
Batching:     req ─┐
              req ─┼─ wait → process all → respond  (higher latency, higher throughput)
              req ─┘
```

Kafka, database group commits, and GPU inference all exploit this trade-off.

---

## Key Takeaways
- **Latency** = time per request; **throughput** = requests per unit time. Optimizing one can hurt the other.
- Describe performance with **percentiles (p99)**, never averages — the **tail** is what users feel.
- Memorize **orders of magnitude**: RAM ≫ SSD ≫ HDD ≫ network round trip.
- Reduce latency with **caching, CDNs, indexing, locality**; raise throughput with **horizontal scaling, queues, batching**.

---
[← Scalability](./02-scalability.md) | [Back to Hub](../README.md) | [Next: CAP Theorem →](./04-cap-theorem.md)
