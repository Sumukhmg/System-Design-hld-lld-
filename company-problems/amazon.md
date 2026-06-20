# Amazon — System Design Problems

[← Company Index](./README.md) | [Back to Hub](../README.md)

Amazon interviews are heavy on **scalability**, **trade-offs**, and tying technical choices to **customer obsession** and **operational excellence** (Leadership Principles). Expect **both** HLD and LLD/OOD rounds; senior roles emphasize HLD depth, SDE-1/2 often get OOD machine-coding.

---

## 🔷 HLD Questions

| # | Problem | Key concepts | Resource |
|---|---------|--------------|----------|
| 1 | Design a URL shortener | ID generation, caching, redirects | [url-shortener](../hld/case-studies/url-shortener.md) |
| 2 | Design Amazon product catalog / e-commerce | catalog, search, cart, inventory | [databases](../hld/building-blocks/databases.md), [sharding](../hld/building-blocks/sharding.md) |
| 3 | Design a distributed key-value store (DynamoDB) | consistent hashing, quorum, vector clocks | [key-value-store](../hld/case-studies/key-value-store.md) |
| 4 | Design a notification system (SNS) | queues, fan-out, multi-channel | [notification-system](../hld/case-studies/notification-system.md) |
| 5 | Design a rate limiter | token bucket, distributed counters | [rate-limiter](../hld/case-studies/rate-limiter.md) |
| 6 | Design Amazon Prime Video streaming | transcoding, CDN, adaptive bitrate | [youtube](../hld/case-studies/youtube.md) |
| 7 | Design a shopping cart | high availability, eventual consistency | [consistency](../fundamentals/05-consistency.md), [caching](../hld/building-blocks/caching.md) |
| 8 | Design an order/inventory system | strong consistency, transactions | [databases](../hld/building-blocks/databases.md) |
| 9 | Design a web crawler | frontier, politeness, dedup | [web-crawler](../hld/case-studies/web-crawler.md) |
| 10 | Design a recommendation system | offline pipeline, feature store | [microservices](../hld/building-blocks/microservices.md) |

### Signature: "Design Amazon.com" (e-commerce)
Core pieces to cover:
- **Catalog & Search** — product service + Elasticsearch.
- **Cart** — highly available (Dynamo-style, AP), never reject adds.
- **Orders & Payments** — strong consistency (ACID/CP), idempotent.
- **Inventory** — avoid overselling (atomic decrements / reservations).
- **Recommendations** — async ML pipeline.
- Glue with [load balancing](../hld/building-blocks/load-balancing.md), [caching](../hld/building-blocks/caching.md), [CDN](../hld/building-blocks/cdn.md), [queues](../hld/building-blocks/message-queues.md).

---

## 🔶 LLD / OOD Questions

| # | Problem | Patterns | Resource |
|---|---------|----------|----------|
| 1 | Design a parking lot | Strategy, Factory | [parking-lot](../lld/problems/parking-lot.md) |
| 2 | Design a vending machine | State | [vending-machine](../lld/problems/vending-machine.md) |
| 3 | Design an elevator system | State, Strategy | [elevator-system](../lld/problems/elevator-system.md) |
| 4 | Design a library management system | Observer, Strategy | [library-management](../lld/problems/library-management.md) |
| 5 | Design Splitwise | Strategy, graph settle | [splitwise](../lld/problems/splitwise.md) |
| 6 | Design an in-memory key-value cache (LRU) | LinkedHashMap / DLL + HashMap | [caching](../hld/building-blocks/caching.md) |
| 7 | Design a logging framework | Chain of Responsibility | [behavioral patterns](../lld/design-patterns/behavioral.md) |
| 8 | Design an ATM / coffee machine | State, Strategy | [vending-machine](../lld/problems/vending-machine.md) |

### Classic LLD warm-up: **LRU Cache** (also a LeetCode favorite)
HashMap + doubly-linked list for **O(1)** get/put; evict the tail (least recently used). Tie it to the [caching eviction policies](../hld/building-blocks/caching.md).

---

## 💡 Amazon-Specific Tips
- **Tie design to Leadership Principles:** *Customer Obsession* (latency/availability for users), *Ownership* (operational concerns, monitoring), *Frugality* (cost-aware choices), *Dive Deep* (be ready to go several layers down).
- Expect **deep trade-off questions**: "Why eventual consistency for the cart but strong for orders?" → know [CAP](../fundamentals/04-cap-theorem.md).
- They love **DynamoDB-style** discussions (it's an Amazon paper) → master [key-value store](../hld/case-studies/key-value-store.md).
- Always discuss **scaling, failure handling, and monitoring** — operational excellence is graded.

---

## Key Takeaways
- Amazon = **scalability + trade-offs + operational excellence**, mapped to **Leadership Principles**.
- HLD favorites: **DynamoDB KV store, e-commerce, notifications, rate limiter, streaming**.
- LLD favorites: **parking lot, vending machine, elevator, LRU cache**.
- Be ready to **dive deep** on consistency choices and failure modes.

---
[← Company Index](./README.md) | [Back to Hub](../README.md)
