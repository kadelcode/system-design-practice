<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [URL Shortener - Capacity Estimation](#url-shortener---capacity-estimation)
  - [Goal](#goal)
  - [Assumptions](#assumptions)
  - [Traffic Estimation](#traffic-estimation)
  - [Storage Estimation](#storage-estimation)
  - [Peak Traffic Estimation (Important)](#peak-traffic-estimation-important)
    - [Peak QPS:](#peak-qps)
  - [Bandwidth Estimation (Optional)](#bandwidth-estimation-optional)
    - [Read Bandwidth](#read-bandwidth)
    - [Write Bandwidth](#write-bandwidth)
  - [Key Observations](#key-observations)
  - [Design Implications](#design-implications)
  - [Conclusion](#conclusion)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# URL Shortener - Capacity Estimation

## Goal
Estimate system scale to guide architectural decisions (storage, database choice, caching, etc.)

---

## Assumptions
Since we don't have real data, we define reasonable assumptions:

1. Daily Active Users (DAU): `1 million users`

2. URLs created per user per day: `1`

3. Total new URLs per day: `1M URLs/day`

4. Average reads per URL per day: `10`
    - Each shortened link is accessed ~ 10 times daily

5. Average URL size:
    - Original URL: 500 bytes
    - Short code: 10 bytes
    - Metadata (timestamps, etc.): 90 bytes
    - Total per record ~ 600 bytes (0.6 KB)

6. Data retention period:
    - Assume 5 years (URLs are rarely deleted)

7. Peak traffic multiplier:
    - Assume 5x average traffic

---

## Traffic Estimation

1. Write Traffic (URL Creation)

- Daily writes: `= 1M URLs/day`

- Writes per second (QPS):
```
1,000,000 / (24 x 60 x 60) = 11.6 ~ 12 writes/sec
```

---

2. Read Traffic (Redirection)
- Daily reads:
```
= 1M x 10 = 10M reads/day
```

- Reads per second (QPS)
```
10,000,000 / (24 x 60 x 60) = 116 reads/sec
```

---

3. Read/Write Ratio
```
Reads : Writes = 10M : 1M = 10 : 1
```
**NB:** This is a read-heavy system

---

## Storage Estimation

1. Daily Storage
```
1M URLs x 0.6 KB = 600 MB/day
```

---

2. Yearly Storage
```
600 MB x 365 = 219,000 MB ~ 219 GB/year
```

---

3. Total Storage (based on retention period: 5 years)
```
219 GB x 5 = ~1.1 TB
```

---

## Peak Traffic Estimation (Important)
Traffic is not evenly distributed.

Assume:
- Peak = 5 x average

---

### Peak QPS:
- Writes:
```
~ 12 x 5 = 60 writes/sec
```

- Reads:
```
~ 116 x 5 = 580 reads/sec
```

**Insight:**
System must be designed to handle peak traffic, not average load.

---

## Bandwidth Estimation (Optional)

### Read Bandwidth
```
580 QPS × 0.6 KB ≈ 348 KB/sec ≈ 0.35 MB/sec
```

### Write Bandwidth
```
60 QPS × 0.6 KB ≈ 36 KB/sec
```

---

## Key Observations
1. Read-heavy system
    - 10:1 read-to-write ratio
    - Strong need for caching (e.g., Redis)

2. Moderate write traffic
    - Can be handled by a single database initially

3. Storage grows steadily
    - ~219 GB/year -> ~1.1 TB over 5 years
    - Requires scalable storage strategy

4. Low latency is critical
    - URL redirection must be extremely fast

5. Hot URLs problem
    - Some URLs may receive massive traffic
    - Requires caching + CDN

6. Peak traffic matters
    - System must handle ~580 read QPS at peak

---

## Design Implications
Based on the estimates:

- ✅ Use cache (Redis) for frequent reads
- ✅ Use NoSQL or scalable DB for URL storage
- ✅ Consider CDN for global redirection performance
- ✅ Plan for horizontal scaling
- ✅ Add rate limiting to handle spikes

## Conclusion
- The system is read-heavy (10:1 ratio)
- Expected scale:
    - ~580 peak read QPS
    - ~60 peak write QPS
    - ~219 GB storage/year
- Architecture should prioritize:
    - Fast reads (caching)
    - High availability
    - Scalable storage