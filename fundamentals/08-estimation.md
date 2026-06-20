# 08 · Back-of-the-Envelope Estimation

[← Networking](./07-networking.md) | [Back to Hub](../README.md) | [Next: Interview Framework →](./09-interview-framework.md)

---

## Why Estimate?

Capacity estimation turns a vague problem into concrete numbers that justify architecture decisions: *how many servers? how much storage? do we need a cache/CDN/sharding?* Interviewers want to see you reason quantitatively — **exact numbers don't matter, orders of magnitude do.**

---

## Numbers to Memorize

### Powers of 2 (data sizes)
| Power | Exact | Approx | Name |
|-------|-------|--------|------|
| 2^10 | 1,024 | 1 thousand | 1 KB |
| 2^20 | ~1 million | 1 million | 1 MB |
| 2^30 | ~1 billion | 1 billion | 1 GB |
| 2^40 | ~1 trillion | 1 trillion | 1 TB |
| 2^50 | ~10^15 | 1 quadrillion | 1 PB |

### Time (seconds — very useful!)
```
1 day      ≈ 86,400 s        ≈ 10^5 s
1 month    ≈ 2.5 million s   ≈ 2.5 × 10^6 s
1 year     ≈ 31.5 million s  ≈ 3 × 10^7 s
```
> **Handy shortcut:** **1 day ≈ 100,000 seconds (10^5).** So *X per day ÷ 10^5 ≈ X per second*.

### Latency reference (from [Latency chapter](./03-latency-throughput.md))
- Memory read: ~100 ns · SSD read: ~150 µs · Disk seek: ~10 ms · Same-DC round trip: ~0.5 ms · Cross-continent: ~150 ms.

### Common object sizes
| Object | Approx size |
|--------|-------------|
| Char (ASCII) | 1 byte |
| UUID | 16 bytes |
| Timestamp | 8 bytes |
| Tweet (text) | ~300 bytes |
| Short URL row | ~100 bytes |
| Web page (HTML) | ~100 KB |
| Photo | ~200 KB – 2 MB |
| Minute of 1080p video | ~50 MB |

---

## The Estimation Workflow

1. **Users:** DAU (Daily Active Users), MAU.
2. **Traffic (QPS):** requests per second = actions/day ÷ 86,400. Compute **average** and **peak** (peak ≈ 2–3× average).
3. **Read:Write ratio:** e.g., 100:1 for social media → design read-heavy (caching/replicas).
4. **Storage:** data per item × items per day × retention period.
5. **Bandwidth:** QPS × payload size.
6. **Memory (cache):** apply the **80/20 rule** — cache the hot 20%.

---

## Worked Example 1: Twitter-like Feed

**Assumptions**
- 300M MAU, 50% daily active → **150M DAU**.
- Each user posts 2 tweets/day → **300M tweets/day**.
- Read:write ratio ≈ 100:1.

**Write QPS**
```
300M tweets/day ÷ 86,400 s ≈ 3,500 writes/s
Peak ≈ 3× ≈ ~10,000 writes/s
```

**Read QPS**
```
100 × 3,500 ≈ 350,000 reads/s
Peak ≈ ~1,000,000 reads/s
```
→ Massively read-heavy ⇒ aggressive **caching**, **read replicas**, **fan-out** strategy.

**Storage (text only, 5 years)**
```
Tweet ≈ 300 bytes (text + metadata)
Per day: 300M × 300 B = 90 GB/day
Per year: 90 GB × 365 ≈ 33 TB/year
5 years: ≈ 165 TB  (text alone)
```
With media (images/video), multiply by 10–100× ⇒ **petabytes** ⇒ object storage (S3) + CDN.

---

## Worked Example 2: URL Shortener

**Assumptions**
- 100M new URLs/day. Read:write = 100:1. Retain 5 years.

**QPS**
```
Write: 100M ÷ 86,400 ≈ 1,160 writes/s
Read:  100 × 1,160 ≈ 116,000 reads/s
```

**Storage**
```
Rows over 5 years: 100M × 365 × 5 ≈ 182 billion URLs
Per row ≈ 500 bytes (short code + long URL + metadata)
Total: 182B × 500 B ≈ 91 TB
```

**Short code length** (base-62: a–z, A–Z, 0–9)
```
62^6 ≈ 56.8 billion   → too few for 182B
62^7 ≈ 3.5 trillion   → enough ✅ → use 7-character codes
```

---

## Worked Example 3: Cache Sizing (80/20 Rule)

If reads = 116,000/s and we cache the hot **20%** of daily requests:
```
Daily reads ≈ 116,000 × 86,400 ≈ 10 billion reads/day
Unique items requested (assume) ≈ 100M
Hot 20% ≈ 20M items × 500 B ≈ 10 GB
```
→ A **10 GB Redis cache** can hold the hot set ⇒ huge DB-load reduction. Fits comfortably on one cache node (with replicas for HA).

---

## Bandwidth Example

For an image service serving 10,000 images/s at 200 KB each:
```
10,000 × 200 KB = 2,000,000 KB/s = 2 GB/s = 16 Gbps
```
→ Way beyond one server's NIC ⇒ need a **CDN** + many edge nodes.

---

## Estimation Cheat Card

```
QPS         = events_per_day / 86,400      (≈ /10^5)
Peak QPS    = 2–3 × average QPS
Storage     = item_size × items_per_day × retention_days
Bandwidth   = QPS × payload_size
Cache size  = hot_fraction (≈20%) × daily_unique × item_size
# servers   = peak_QPS / QPS_per_server
```

> **Always state assumptions out loud** and round aggressively (300M tweets ÷ 86,400 → "~3,500/s"). Interviewers care about method, not arithmetic precision.

---

## Key Takeaways
- Memorize **powers of 2**, **1 day ≈ 10^5 s**, latency numbers, and common object sizes.
- Workflow: **users → QPS (avg & peak) → read:write → storage → bandwidth → cache**.
- Use the **80/20 rule** to size caches; **peak ≈ 2–3× average**.
- Numbers drive architecture: high read QPS → caching/replicas; petabyte storage → object store + CDN; many writes → sharding/queues.
- **State assumptions, round aggressively, focus on orders of magnitude.**

---
[← Networking](./07-networking.md) | [Back to Hub](../README.md) | [Next: Interview Framework →](./09-interview-framework.md)
