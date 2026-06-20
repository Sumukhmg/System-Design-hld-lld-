# Uber — System Design Problems

[← Company Index](./README.md) | [Back to Hub](../README.md)

Uber interviews center on **real-time, location-based, geospatial systems** at scale — matching riders and drivers, live tracking, surge pricing, and high-throughput event ingestion. Expect deep dives into **geospatial indexing** and **real-time data**.

---

## 🔷 HLD Questions

| # | Problem | Key concepts | Resource |
|---|---------|--------------|----------|
| 1 | Design Uber / Lyft (ride hailing) | geospatial index, matching, real-time | [uber](../hld/case-studies/uber.md) |
| 2 | Design "find nearby drivers/restaurants" | geohash, quadtree, H3 | [uber](../hld/case-studies/uber.md) |
| 3 | Design Uber Eats / food delivery | matching, ETA, multi-party tracking | [uber](../hld/case-studies/uber.md) |
| 4 | Design real-time location tracking | high-write ingestion, in-memory geo | [uber](../hld/case-studies/uber.md), [message-queues](../hld/building-blocks/message-queues.md) |
| 5 | Design surge pricing | stream processing, supply/demand per cell | [message-queues](../hld/building-blocks/message-queues.md) |
| 6 | Design an ETA service | road graph, traffic, caching | [uber](../hld/case-studies/uber.md) |
| 7 | Design a payment system | idempotency, strong consistency | [databases](../hld/building-blocks/databases.md), [consistency](../fundamentals/05-consistency.md) |
| 8 | Design a notification system | push, queues, fan-out | [notification-system](../hld/case-studies/notification-system.md) |
| 9 | Design a distributed unique ID generator | Snowflake | [sharding](../hld/building-blocks/sharding.md) |
| 10 | Design a rate limiter | token bucket, distributed | [rate-limiter](../hld/case-studies/rate-limiter.md) |

### Signature: **Design Uber (driver–rider matching)**
The must-know flow:
- **Geospatial indexing** — geohash / quadtree / **H3** (Uber's own hexagonal grid) to answer "drivers near me" efficiently. → [Uber case study](../hld/case-studies/uber.md)
- **High-write location ingestion** — millions of location updates/s kept **in memory** (Redis GEO), streamed to **Kafka** for history/analytics. Don't hammer SQL.
- **Matching** — query nearby cells, rank candidates, atomic **claim** to avoid double-booking.
- **Trip & payment state** — **strong consistency (SQL)**; location can be **eventually consistent**.
- **Region/city sharding** for locality and bounded queries.
- **Real-time updates** to the rider via **WebSocket**.

---

## 🔶 LLD / OOD Questions

| # | Problem | Patterns | Resource |
|---|---------|----------|----------|
| 1 | Design Splitwise (fare splitting) | Strategy, graph settle | [splitwise](../lld/problems/splitwise.md) |
| 2 | Design a parking lot | Strategy, Factory | [parking-lot](../lld/problems/parking-lot.md) |
| 3 | Design an elevator system | State, Strategy | [elevator-system](../lld/problems/elevator-system.md) |
| 4 | Design a ride-matching service (class design) | Strategy, Observer | [uber](../hld/case-studies/uber.md) |
| 5 | Design an LRU cache | DLL + HashMap | [caching](../hld/building-blocks/caching.md) |

---

## 💡 Uber-Specific Tips
- **Geospatial is the differentiator** — know **geohash, quadtree, and H3** and when each shines. Redis `GEORADIUS` is a clean go-to.
- Separate **volatile, high-write location data** (in-memory) from **durable trip/payment state** (SQL, strong consistency). This split is the key insight.
- Discuss **region/city sharding** for data locality.
- Handle **race conditions** in matching (two riders, one driver) with atomic claims.
- Know **Snowflake IDs**, **Kafka** ingestion, and **stream processing** (for surge/ETA).
- Mention **idempotency** for payments and retries.

---

## Key Takeaways
- Uber = **real-time geospatial matching at scale**; the **driver–rider** design is central.
- Master **geospatial indexing (geohash / quadtree / H3)** and Redis GEO.
- Keep **location data in memory** (high write, volatile), stream to **Kafka**; keep **trip/payment in SQL** (strong consistency).
- **Shard by city/region**; use **atomic claims** to avoid double-matching; **Snowflake** IDs + **WebSocket** updates.

---
[← Company Index](./README.md) | [Back to Hub](../README.md)
