# Netflix — System Design Problems

[← Company Index](./README.md) | [Back to Hub](../README.md)

Netflix is the reference for **video streaming at global scale**, **microservices**, **resilience engineering** (chaos engineering, circuit breakers), and **personalization/recommendations**. Expect HLD on streaming, CDN, and fault-tolerant microservices.

---

## 🔷 HLD Questions

| # | Problem | Key concepts | Resource |
|---|---------|--------------|----------|
| 1 | Design Netflix / a video streaming service | transcoding, CDN, adaptive bitrate | [youtube](../hld/case-studies/youtube.md) |
| 2 | Design a content delivery network (CDN) | edge caching, Open Connect | [cdn](../hld/building-blocks/cdn.md) |
| 3 | Design a video transcoding pipeline | parallel chunked encoding, queues | [youtube](../hld/case-studies/youtube.md), [message-queues](../hld/building-blocks/message-queues.md) |
| 4 | Design a recommendation system | offline ML pipeline, feature store, ranking | [microservices](../hld/building-blocks/microservices.md) |
| 5 | Design a resilient microservices architecture | circuit breaker, bulkhead, fallback | [microservices](../hld/building-blocks/microservices.md) |
| 6 | Design an A/B testing / experimentation platform | config, bucketing, metrics | [databases](../hld/building-blocks/databases.md) |
| 7 | Design a viewing-history / continue-watching service | write-heavy, eventual consistency | [consistency](../fundamentals/05-consistency.md) |
| 8 | Design a search service | inverted index, autocomplete | [indexing](../hld/building-blocks/indexing.md) |
| 9 | Design a real-time analytics / metrics pipeline | Kafka, stream processing | [message-queues](../hld/building-blocks/message-queues.md) |

### Signature: **Design Netflix (streaming)**
- **Pre-encoding pipeline** — Netflix encodes a fixed catalog into many resolutions/codecs (**per-title optimized encoding**), segmented for **adaptive bitrate (HLS/DASH)**. → [YouTube case study](../hld/case-studies/youtube.md)
- **Open Connect** — Netflix's **custom CDN appliances placed inside ISPs** to serve video efficiently and cheaply.
- **Adaptive streaming** — player switches quality per segment based on bandwidth.
- **Control plane on AWS microservices** (account, recommendations, metadata); **data plane (video bytes) via Open Connect CDN**.
- **Resilience** — circuit breakers (Hystrix), fallbacks, chaos engineering (Chaos Monkey) to ensure availability.

---

## 🔶 LLD / OOD Questions

| # | Problem | Patterns | Resource |
|---|---------|----------|----------|
| 1 | Design a parking lot | Strategy, Factory | [parking-lot](../lld/problems/parking-lot.md) |
| 2 | Design an LRU cache | DLL + HashMap | [caching](../hld/building-blocks/caching.md) |
| 3 | Design a video-player state machine | State | [vending-machine](../lld/problems/vending-machine.md) |
| 4 | Design a rate limiter (class design) | Strategy | [rate-limiter](../hld/case-studies/rate-limiter.md) |

---

## 💡 Netflix-Specific Tips
- **Separate control plane from data plane:** metadata/recommendations on **AWS microservices**; heavy video bytes via the **Open Connect CDN**.
- **Resilience is graded:** discuss **circuit breakers, bulkheads, fallbacks, timeouts, retries**, and **chaos engineering**. → [Microservices resilience](../hld/building-blocks/microservices.md)
- **Pre-encoding vs on-the-fly transcoding:** Netflix pre-encodes (fixed catalog) → can optimize per title; contrast with YouTube's user uploads.
- **Adaptive bitrate (HLS/DASH)** and **CDN edge caching** drive smooth playback — be specific.
- **Recommendations** as a separate offline ML pipeline feeding a serving layer.
- Embrace **eventual consistency** for viewing history/continue-watching (AP).

---

## Key Takeaways
- Netflix = **global video streaming + microservices + resilience + personalization**.
- Know the **transcoding pipeline**, **adaptive bitrate (HLS/DASH)**, and **CDN/Open Connect** cold.
- **Split control plane (AWS microservices) from data plane (CDN video delivery).**
- Emphasize **resilience patterns** (circuit breaker, bulkhead, chaos engineering) and **recommendation** pipelines.

---
[← Company Index](./README.md) | [Back to Hub](../README.md)
