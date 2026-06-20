# 🏛️ High-Level Design (HLD)

[← Back to Hub](../README.md)

High-Level Design is about the **architecture**: how services, databases, caches, queues, and networks fit together to meet scale, availability, and latency requirements. You think in **boxes and arrows**, not classes.

---

## 📦 Part 1 — Building Blocks

Master these reusable components first. Every case study is assembled from them.

| Block | What it solves |
|-------|----------------|
| [Load Balancing](./building-blocks/load-balancing.md) | Distribute traffic, avoid overload, remove SPOF |
| [Caching](./building-blocks/caching.md) | Cut latency & DB load by serving hot data from memory |
| [Databases: SQL vs NoSQL](./building-blocks/databases.md) | Choose the right store for your data & access patterns |
| [Database Indexing](./building-blocks/indexing.md) | Make queries fast |
| [Sharding & Partitioning](./building-blocks/sharding.md) | Scale data beyond one machine |
| [Replication](./building-blocks/replication.md) | Availability, durability, read scaling |
| [Consistent Hashing](./building-blocks/consistent-hashing.md) | Distribute keys with minimal reshuffling |
| [Message Queues & Kafka](./building-blocks/message-queues.md) | Decouple services, absorb spikes, async processing |
| [CDN](./building-blocks/cdn.md) | Serve static content close to users |
| [API Gateway & Reverse Proxy](./building-blocks/api-gateway.md) | Single entry point, cross-cutting concerns |
| [Rate Limiting](./building-blocks/rate-limiting.md) | Protect services from abuse & overload |
| [Microservices vs Monolith](./building-blocks/microservices.md) | Structure the system |

---

## 🧩 Part 2 — Case Studies

Apply the blocks to real, frequently-asked problems. Each follows the same 7-step template.

| Problem | Key concepts |
|---------|--------------|
| [URL Shortener (TinyURL)](./case-studies/url-shortener.md) | ID generation, base-62, caching, redirects |
| [Twitter / X](./case-studies/twitter.md) | Feed fan-out (push vs pull), timeline |
| [Chat (WhatsApp)](./case-studies/whatsapp.md) | WebSockets, presence, delivery, message store |
| [Rate Limiter](./case-studies/rate-limiter.md) | Token bucket, sliding window, distributed counters |
| [Instagram](./case-studies/instagram.md) | Media storage, feed, object store + CDN |
| [YouTube / Netflix](./case-studies/youtube.md) | Video upload, transcoding, streaming, CDN |
| [Uber / Lyft](./case-studies/uber.md) | Geospatial indexing, matching, real-time location |
| [Notification System](./case-studies/notification-system.md) | Fan-out, queues, multi-channel, retries |
| [Web Crawler](./case-studies/web-crawler.md) | BFS frontier, politeness, dedup, scale |
| [Key-Value Store](./case-studies/key-value-store.md) | Consistent hashing, quorum, gossip, Merkle trees |

---

## How to Study This Section
1. Read all building blocks once for vocabulary.
2. Do **URL Shortener** and **Twitter** first — they teach the most patterns.
3. For each case study, try to design it yourself *before* reading the solution.
4. Re-derive estimations using the [Estimation cheat card](../fundamentals/08-estimation.md).

[← Back to Hub](../README.md)
