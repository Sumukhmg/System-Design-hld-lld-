# Microsoft — System Design Problems

[← Company Index](./README.md) | [Back to Hub](../README.md)

Microsoft interviews are **balanced** — solid **HLD** plus strong **OOD/LLD** (they value clean object-oriented design and extensibility). Azure/cloud teams probe distributed-systems depth; product teams (Office, Teams, Xbox) probe product-system design.

---

## 🔷 HLD Questions

| # | Problem | Key concepts | Resource |
|---|---------|--------------|----------|
| 1 | Design a URL shortener | ID generation, caching | [url-shortener](../hld/case-studies/url-shortener.md) |
| 2 | Design Microsoft Teams / a chat system | WebSockets, presence, delivery | [whatsapp](../hld/case-studies/whatsapp.md) |
| 3 | Design OneDrive / Dropbox (file sync & storage) | chunking, metadata, sync, dedup | [databases](../hld/building-blocks/databases.md), [cdn](../hld/building-blocks/cdn.md) |
| 4 | Design a notification system | queues, fan-out, channels | [notification-system](../hld/case-studies/notification-system.md) |
| 5 | Design a rate limiter | token bucket, distributed | [rate-limiter](../hld/case-studies/rate-limiter.md) |
| 6 | Design a distributed cache (Azure Redis) | consistent hashing, eviction | [caching](../hld/building-blocks/caching.md) |
| 7 | Design a key-value / blob store (Azure Storage) | partitioning, replication | [key-value-store](../hld/case-studies/key-value-store.md) |
| 8 | Design a video conferencing system | WebRTC, SFU, media servers | [whatsapp](../hld/case-studies/whatsapp.md), [networking](../fundamentals/07-networking.md) |
| 9 | Design a typeahead / search suggestion | trie, caching, ranking | [indexing](../hld/building-blocks/indexing.md) |

### Signature: **Design Dropbox / OneDrive (file sync)**
Cover:
- **Chunking** — split files into blocks; only sync changed blocks (delta sync).
- **Metadata service** — file tree, versions, sharing (strongly consistent DB).
- **Block storage** — chunks in object storage (S3/Azure Blob), deduped by hash.
- **Sync** — clients watch for changes; notification service pushes updates; conflict resolution.
- **CDN** for downloads. Discuss [consistency](../fundamentals/05-consistency.md) for metadata vs blocks.

---

## 🔶 LLD / OOD Questions (Microsoft loves these)

| # | Problem | Patterns | Resource |
|---|---------|----------|----------|
| 1 | Design a parking lot | Strategy, Factory | [parking-lot](../lld/problems/parking-lot.md) |
| 2 | Design an elevator system | State, Strategy | [elevator-system](../lld/problems/elevator-system.md) |
| 3 | Design a deck of cards / card game | clean modeling, Factory | [creational patterns](../lld/design-patterns/creational.md) |
| 4 | Design Tic-Tac-Toe / Chess | State, Strategy | [tic-tac-toe](../lld/problems/tic-tac-toe.md) |
| 5 | Design an LRU cache | DLL + HashMap | [caching](../hld/building-blocks/caching.md) |
| 6 | Design a vending machine | State | [vending-machine](../lld/problems/vending-machine.md) |
| 7 | Design a file system | Composite | [structural patterns](../lld/design-patterns/structural.md) |
| 8 | Design a snake-and-ladder / board game | clean OOD | [lld index](../lld/README.md) |

> Microsoft OOD rounds reward **extensible class design** — expect "now add feature X" follow-ups. Lead with [SOLID](../lld/solid-principles.md) and the right [design pattern](../lld/design-patterns/creational.md).

---

## 💡 Microsoft-Specific Tips
- **Balance breadth and depth** — clear requirements, a clean diagram, then a deep dive.
- **OOD is heavily weighted** — practice translating requirements into clean classes; know your [design patterns](../lld/design-patterns/creational.md).
- For Azure roles, expect **cloud/distributed** depth (storage, consistency, replication, partitioning).
- Communicate clearly and **collaborate** — they value working *with* the interviewer.
- Always handle **extensibility** and **edge cases**.

---

## Key Takeaways
- Microsoft = **balanced HLD + strong OOD/LLD**; extensibility and clean classes matter.
- HLD favorites: **OneDrive/Dropbox, Teams/chat, notifications, caching, blob/KV storage**.
- LLD favorites: **parking lot, elevator, card games, LRU cache, file system**.
- Lead with **SOLID + design patterns** and prepare for **"add feature X"** follow-ups.

---
[← Company Index](./README.md) | [Back to Hub](../README.md)
