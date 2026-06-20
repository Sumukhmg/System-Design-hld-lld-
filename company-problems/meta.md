# Meta (Facebook) — System Design Problems

[← Company Index](./README.md) | [Back to Hub](../README.md)

Meta focuses on **large-scale product systems** — news feeds, messaging, social graphs — with billions of users. Expect **HLD product-design** rounds emphasizing **feed generation, fan-out, caching, and the read-heavy social workload**. Meta is famous for memcache, TAO (social graph store), and aggressive caching.

---

## 🔷 HLD Questions

| # | Problem | Key concepts | Resource |
|---|---------|--------------|----------|
| 1 | Design the Facebook/Instagram News Feed | fan-out (push/pull), ranking, caching | [twitter](../hld/case-studies/twitter.md), [instagram](../hld/case-studies/instagram.md) |
| 2 | Design Facebook Messenger / WhatsApp | WebSockets, presence, delivery, E2E | [whatsapp](../hld/case-studies/whatsapp.md) |
| 3 | Design Instagram | media storage, feed, CDN | [instagram](../hld/case-studies/instagram.md) |
| 4 | Design a notification system | fan-out, queues, channels | [notification-system](../hld/case-studies/notification-system.md) |
| 5 | Design the "People You May Know" / friend graph | graph storage, traversal | [databases](../hld/building-blocks/databases.md) |
| 6 | Design a typeahead search | trie, caching, ranking | [indexing](../hld/building-blocks/indexing.md) |
| 7 | Design a live commenting / reactions system | real-time fan-out, counters | [twitter](../hld/case-studies/twitter.md) |
| 8 | Design Facebook Live / video streaming | transcoding, CDN, low latency | [youtube](../hld/case-studies/youtube.md) |
| 9 | Design a distributed cache (memcache at scale) | sharding, consistency, leases | [caching](../hld/building-blocks/caching.md) |
| 10 | Design a rate limiter | token bucket, distributed | [rate-limiter](../hld/case-studies/rate-limiter.md) |

### Signature: **Design the News Feed**
The canonical Meta question. Nail these:
- **Fan-out on write (push)** vs **fan-out on read (pull)** — and the **hybrid** for high-degree users (celebrities/pages). → [Twitter deep dive](../hld/case-studies/twitter.md)
- **Feed ranking** — not pure chronological; an ML ranking service scores candidate posts (engagement prediction).
- **Aggressive caching** (memcache) for timelines, posts, counts.
- **Social graph** access patterns (TAO-like: objects + associations).
- **Read-heavy** scale: hundreds of millions of reads/s → replicas + caching everywhere.

---

## 🔶 LLD / OOD Questions

| # | Problem | Patterns | Resource |
|---|---------|----------|----------|
| 1 | Design a parking lot | Strategy, Factory | [parking-lot](../lld/problems/parking-lot.md) |
| 2 | Design an LRU/LFU cache | DLL + HashMap | [caching](../hld/building-blocks/caching.md) |
| 3 | Design a news-feed data model | entity modeling | [twitter](../hld/case-studies/twitter.md) |
| 4 | Design a chat data model | entity modeling | [whatsapp](../hld/case-studies/whatsapp.md) |

> Meta leans more on **HLD product design** than pure OOD machine-coding, but LLD/cache problems still appear.

---

## 💡 Meta-Specific Tips
- **News feed fan-out** is *the* topic — be fluent in push vs pull vs hybrid and the celebrity problem.
- **Cache everything** — Meta runs one of the world's largest memcache deployments; discuss cache consistency, leases (thundering herd), and invalidation. → [Caching failure modes](../hld/building-blocks/caching.md)
- Embrace **eventual consistency** for social data (AP) — a like count being slightly off is fine.
- Think at **billions-of-users scale**; quantify with [estimation](../fundamentals/08-estimation.md).
- Discuss **ranking/ML** as a separate service feeding the feed.
- Meta values **product sense** — clarify the user experience you're optimizing.

---

## Key Takeaways
- Meta = **massive read-heavy social product systems**; the **News Feed fan-out** problem is central.
- Master **push vs pull vs hybrid** fan-out and the **celebrity/high-degree** problem.
- **Aggressive caching** (memcache) with careful **consistency/invalidation** is a recurring theme.
- Accept **eventual consistency** (AP) for social data; layer an **ML ranking** service over the feed.

---
[← Company Index](./README.md) | [Back to Hub](../README.md)
