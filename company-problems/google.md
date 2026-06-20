# Google — System Design Problems

[← Company Index](./README.md) | [Back to Hub](../README.md)

Google emphasizes **distributed-systems depth**, **massive scale**, **algorithmic rigor**, and crisp reasoning about **consistency, latency, and fault tolerance**. Expect to go deep on one area rather than broad. Many problems trace back to Google's own papers (GFS, Bigtable, MapReduce, Spanner, Chubby).

---

## 🔷 HLD Questions

| # | Problem | Key concepts | Resource |
|---|---------|--------------|----------|
| 1 | Design a web crawler (Googlebot) | frontier, politeness, dedup, scale | [web-crawler](../hld/case-studies/web-crawler.md) |
| 2 | Design a search engine / autocomplete (typeahead) | inverted index, trie, ranking | [indexing](../hld/building-blocks/indexing.md) |
| 3 | Design YouTube | transcoding, CDN, adaptive streaming | [youtube](../hld/case-studies/youtube.md) |
| 4 | Design Google Maps / nearby search | geospatial indexing, routing | [uber](../hld/case-studies/uber.md) |
| 5 | Design a distributed cache | consistent hashing, eviction | [caching](../hld/building-blocks/caching.md), [consistent hashing](../hld/building-blocks/consistent-hashing.md) |
| 6 | Design a globally distributed database (Spanner) | TrueTime, consensus, strong consistency | [consistency](../fundamentals/05-consistency.md), [replication](../hld/building-blocks/replication.md) |
| 7 | Design Google Docs (collaborative editing) | OT/CRDT, real-time sync | [consistency](../fundamentals/05-consistency.md) |
| 8 | Design a rate limiter | token bucket, sliding window | [rate-limiter](../hld/case-studies/rate-limiter.md) |
| 9 | Design a URL shortener | ID generation, caching | [url-shortener](../hld/case-studies/url-shortener.md) |
| 10 | Design a distributed message queue (Pub/Sub) | partitions, offsets, delivery | [message-queues](../hld/building-blocks/message-queues.md) |
| 11 | Design a distributed lock / coordination (Chubby/Zookeeper) | consensus (Paxos/Raft), leader election | [availability](../fundamentals/06-availability.md) |

### Signature: **Typeahead / Autocomplete**
- **Trie** of prefixes with top-K completions cached at each node.
- Built offline from query logs; ranked by frequency/recency.
- Served from memory; sharded by prefix; updated periodically.
- Discuss **latency (<100ms)**, **caching**, and **personalization**.

---

## 🔶 LLD / OOD Questions

| # | Problem | Patterns | Resource |
|---|---------|----------|----------|
| 1 | Design a parking lot | Strategy, Factory | [parking-lot](../lld/problems/parking-lot.md) |
| 2 | Design an elevator system | State, Strategy | [elevator-system](../lld/problems/elevator-system.md) |
| 3 | Design Tic-Tac-Toe / a game engine | clean modeling, Strategy | [tic-tac-toe](../lld/problems/tic-tac-toe.md) |
| 4 | Design an LRU/LFU cache | DLL + HashMap | [caching](../hld/building-blocks/caching.md) |
| 5 | Design a file system | Composite (tree), inodes | [structural patterns](../lld/design-patterns/structural.md) |

---

## 💡 Google-Specific Tips
- **Go deep, not wide.** Pick one component and reason rigorously about edge cases, failure modes, and consistency.
- **Know the papers' ideas:** GFS/Colossus (chunked replicated storage), Bigtable (wide-column), MapReduce (batch), Spanner (TrueTime + Paxos for global strong consistency), Chubby (locking via Paxos).
- **Quantify everything** — Google loves [back-of-the-envelope estimation](../fundamentals/08-estimation.md).
- Be precise about **consensus** (Paxos/Raft), **leader election**, and **clock/ordering** issues.
- Strong **DSA** underpins LLD here (tries, heaps, graphs) — they blend algorithmic and design thinking.

---

## Key Takeaways
- Google = **distributed-systems depth + massive scale + rigor**; go deep on one area.
- HLD favorites: **web crawler, search/autocomplete, Maps, Spanner-style global DB, Pub/Sub, distributed cache**.
- Know the **Google papers' concepts** (GFS, Bigtable, MapReduce, Spanner/TrueTime, Chubby/Paxos).
- Emphasize **estimation, consistency models, consensus, and failure handling**.

---
[← Company Index](./README.md) | [Back to Hub](../README.md)
