# System Design Fundamentals — Back-of-the-Envelope Calculations

In system design interviews, we often don't need exact numbers.

We need to answer questions like:

> "Approximately how much storage do we need?"

> "How much bandwidth will this system require?"

> "Can one server handle this traffic?"

The goal is to make **quick, reasonable estimates**.

---

# 1. Storage Calculation

## Problem

Suppose an application generates:

```text
500 million messages/day
```

We need to estimate:

> **How much storage do we need per day?**

To solve this, we first need to understand basic storage units.

---

# 2. Storage Units

For system-design calculations, we usually use **decimal units** for simplicity:

```text
1 Byte (B) = 8 bits

1 KB = 1,000 Bytes        ≈ 10³ Bytes
1 MB = 1,000 KB           ≈ 10⁶ Bytes
1 GB = 1,000 MB           ≈ 10⁹ Bytes
1 TB = 1,000 GB           ≈ 10¹² Bytes
1 PB = 1,000 TB           ≈ 10¹⁵ Bytes
```

### Easy way to remember

```text
KB → 10³
MB → 10⁶
GB → 10⁹
TB → 10¹²
PB → 10¹⁵
```

Every step is approximately:

```text
× 1,000
```

---

# 3. Simple Data Size Assumptions

In system-design interviews, we often use rough assumptions.

These are **estimates**, not universal facts.

For example:

```text
1 character        ≈ 1 Byte        (rough estimate)

Short text message ≈ 100–500 Bytes

Tweet/post text + metadata
                   ≈ 300 Bytes     (assumption)

High-quality photo
                   ≈ 4 MB          (assumption)

HD video           ≈ 50 MB/min     (assumption)

4K video           ≈ 300 MB/min    (assumption)
```

The exact size depends heavily on:

* Encoding
* Compression
* Resolution
* Metadata
* File format

The important thing is to **state your assumption before calculating**.

---

# 4. Example — Twitter/X-like System

Suppose:

```text
500 million tweets/day
```

Assume:

```text
1 tweet + metadata ≈ 300 Bytes
```

We want to calculate:

> **Text storage per day**

---

## Step 1 — Daily storage

```text
500 million × 300 Bytes
```

Approximately:

```text
500 × 10⁶ × 300
= 150 × 10⁹ Bytes
```

Since:

```text
1 GB = 10⁹ Bytes
```

we get:

```text
≈ 150 GB/day
```

So:

> **Text storage ≈ 150 GB/day**

---

# 5. Now Add Images

Suppose:

```text
10% of tweets contain an image
```

Total tweets:

```text
500 million/day
```

Therefore:

```text
10% × 500 million
= 50 million images/day
```

Assume:

```text
1 image ≈ 4 MB
```

Therefore:

```text
50 million × 4 MB
= 200 million MB
```

Since:

```text
1 million MB = 1 TB
```

we get:

```text
200 million MB
≈ 200 TB
```

Therefore:

```text
Text:
≈ 150 GB/day

Images:
≈ 200 TB/day
```

---

# 6. Key Insight

Look at the difference:

```text
Text       → 150 GB/day

Images     → 200 TB/day
```

Convert:

```text
200 TB = 200,000 GB
```

So images are roughly:

```text
200,000 / 150
≈ 1,333×
```

larger than the text storage in this example.

### Important system-design insight

> **Media files often dominate storage requirements.**

This is why systems dealing with:

* Images
* Videos
* Audio
* Documents

usually pay much more attention to:

```text
Object Storage
CDN
Compression
Data lifecycle
Archival
Storage cost
```

than systems dealing only with text.

---

# 7. Important Storage Formula

The basic formula to remember is:

```text
Daily Storage

= Number of objects/day
  × Size of each object
```

For example:

```text
500M tweets/day
×
300 Bytes/tweet

≈ 150 GB/day
```

For images:

```text
50M images/day
×
4 MB/image

≈ 200 TB/day
```

---

# 8. Long-Term Storage

Once we know daily storage, we can estimate yearly storage.

For example:

```text
200 TB/day
```

Approximately:

```text
200 × 365
≈ 73,000 TB/year
```

Since:

```text
1 PB = 1,000 TB
```

we get:

```text
≈ 73 PB/year
```

And over 5 years:

```text
≈ 365 PB
```

This is before considering:

* Replication
* Backups
* Multiple copies
* Compression
* Metadata
* Storage overhead

Therefore, real storage requirements can be significantly larger.

---

# 9. Replication Multiplier

Suppose we need:

```text
100 TB
```

of actual data.

If we maintain 3 copies:

```text
100 TB × 3
= 300 TB
```

So a useful interview formula is:

```text
Required Storage

≈ Raw Data
× Replication Factor
+
Backups
+
Overhead
```

Don't forget replication when doing HLD capacity estimation.

---

# 10. Bandwidth Calculation

## Problem

Suppose YouTube-like traffic involves:

```text
1 billion hours of video watched/day
```

We want to estimate:

> **How much network bandwidth is required to deliver this video?**

---

# 11. Bits vs Bytes

This is extremely important.

### Storage

Usually represented in:

```text
Bytes
```

For example:

```text
100 GB
```

### Network bandwidth

Usually represented in:

```text
bits/second
```

For example:

```text
100 Mbps
```

Remember:

```text
1 Byte = 8 bits
```

Therefore:

```text
1 MB/s = 8 Mbps
```

and:

```text
1 GB/s = 8 Gbps
```

---

# 12. Example — Video Streaming

Assume:

```text
1 billion hours watched/day
```

and:

```text
HD video = 50 MB/minute
```

---

## Step 1 — Convert hours to minutes

```text
1 billion hours
× 60

= 60 billion minutes
```

---

## Step 2 — Calculate total data

```text
60 billion minutes
×
50 MB/minute
```

Approximately:

```text
3,000 billion MB
```

or:

```text
3 × 10¹² MB
```

Since:

```text
1 PB = 10⁹ MB
```

we get:

```text
≈ 3,000 PB/day
```

So:

> **Video delivery ≈ 3,000 PB/day**

under our simplified assumption.

---

# 13. Convert Daily Data to Bandwidth

One day has:

```text
24 × 60 × 60
= 86,400 seconds
```

For rough mental calculations, we might use:

```text
≈ 100,000 seconds/day
```

Then:

```text
3,000 PB
÷
100,000 seconds

≈ 0.03 PB/s
```

Since:

```text
1 PB = 1,000 TB
```

we get:

```text
0.03 PB/s
= 30 TB/s
```

So approximately:

```text
30 TB/s
```

of average data throughput.

---

# 14. Convert TB/s to Tbps

Remember:

```text
1 Byte = 8 bits
```

Therefore:

```text
30 TB/s
× 8

= 240 Tbps
```

So our rough estimate is:

```text
≈ 240 Tbps
```

of average bandwidth.

### Important

This is an intentionally simplified estimate based on the assumption:

```text
50 MB/minute
```

Real video platforms use:

* Multiple resolutions
* Compression
* Adaptive bitrate streaming
* CDN caching
* Different codecs
* Regional distribution

So actual infrastructure calculations would be much more detailed.

The point of the interview calculation is to understand the **order of magnitude**.

---

# 15. Important Bandwidth Formula

The basic formula is:

```text
Bandwidth

= Total Data Transferred
  ÷ Time
```

For example:

```text
Total data/day
÷
86,400 seconds
```

Then convert:

```text
Bytes → bits

× 8
```

---

# 16. Average vs Peak Traffic

## Problem

Average traffic does not mean traffic is evenly distributed throughout the day.

For example:

```text
Average traffic = 100 Gbps
```

But during peak hours:

```text
Traffic might reach 200–300 Gbps
```

---

## Solution

For rough system-design estimation, you can assume:

```text
Peak traffic ≈ 2–3 × average traffic
```

This is a **rule of thumb**, not a universal law.

Example:

```text
Average = 100 Gbps

Peak ≈ 2–3 × 100

Peak ≈ 200–300 Gbps
```

A real system should use actual traffic patterns when available.

---

# 17. 80/20 Rule

Another useful intuition is the **Pareto principle**:

> A relatively small portion of content may generate a disproportionately large amount of traffic.

A common rough assumption is:

```text
20% of content
        ↓
80% of traffic
```

Again, this is a heuristic, not a law.

---

# 18. Why Is the 80/20 Rule Useful?

Suppose we have:

```text
1 billion videos
```

but most users repeatedly watch a small subset of popular videos.

If:

```text
20% of videos
```

generate:

```text
80% of requests
```

we don't necessarily need every request to hit our origin storage.

We can cache popular content.

```text
User
 ↓
CDN Cache
 ↓
Cache Hit
 ↓
Video delivered
```

Only when the content is not available in the cache:

```text
User
 ↓
CDN
 ↓
Cache Miss
 ↓
Origin Storage
```

This dramatically reduces:

```text
Origin bandwidth
+
Origin load
+
Latency
```

---

# 19. CDN + Caching Insight

This leads to an important system-design principle:

> **Cache frequently accessed data as close to the user as possible.**

For video:

```text
                    Origin Storage
                         |
                         ↓
                       CDN
                    /   |   \
                   ↓    ↓    ↓
                 User User User
```

Popular videos can be served from CDN edge locations instead of repeatedly fetching them from origin storage.

---

# 20. The Most Important Formulas

You don't need to memorize dozens of formulas.

Start with these.

### Storage

```text
Storage

= Number of objects
×
Size per object
```

---

### Bandwidth

```text
Bandwidth

= Total data
÷
Time
```

---

### Bits and Bytes

```text
1 Byte = 8 bits

1 MB/s = 8 Mbps

1 GB/s = 8 Gbps

1 TB/s = 8 Tbps
```

---

### Daily → Yearly

```text
Yearly storage

≈ Daily storage × 365
```

---

### Replication

```text
Actual storage

≈ Raw data × replication factor
```

plus backups and overhead.

---

### Peak traffic

```text
Peak ≈ 2–3 × average
```

Use only as a rough interview assumption.

---

# 21. Quick Unit Conversion Table

| Unit   | Approximate value |
| ------ | ----------------: |
| 1 KB   |         10³ Bytes |
| 1 MB   |         10⁶ Bytes |
| 1 GB   |         10⁹ Bytes |
| 1 TB   |        10¹² Bytes |
| 1 PB   |        10¹⁵ Bytes |
| 1 Byte |            8 bits |
| 1 MB/s |            8 Mbps |
| 1 GB/s |            8 Gbps |
| 1 TB/s |            8 Tbps |

---

# 22. Mental Math Tricks

In system-design interviews, don't waste time doing complicated arithmetic.

Use powers of 10.

```text
1 million = 10⁶
1 billion = 10⁹
1 trillion = 10¹²
```

And:

```text
1 day ≈ 100,000 seconds
```

for quick estimates.

For example:

```text
1 billion requests/day

≈ 10⁹ / 10⁵

≈ 10⁴

≈ 10,000 requests/sec
```

This is one of the most useful system-design calculations.

---

# 23. Example — Calculate QPS

Suppose:

```text
1 billion requests/day
```

Then:

```text
QPS

= 1 billion / 86,400
```

For rough estimation:

```text
≈ 10⁹ / 10⁵
≈ 10⁴
```

Therefore:

```text
≈ 10,000 QPS
```

If peak traffic is approximately 3×:

```text
Peak QPS
≈ 30,000 QPS
```

Now we can start asking:

```text
Can one server handle 30,000 QPS?

Do we need multiple servers?

Do we need caching?

Do we need database partitioning?
```

This is exactly how back-of-the-envelope calculations help us make architectural decisions.

---

# 24. One Important Interview Rule

Never present an assumption as a fact.

Instead of:

> "A high-quality photo is 4 MB."

Say:

> **"I'll assume an average photo size of 4 MB for this estimation."**

Instead of:

> "An HD video is 50 MB/min."

Say:

> **"For this rough calculation, I'll assume 50 MB/min for HD video."**

This shows the interviewer that you understand these are **estimation assumptions**.

---

# Final Mental Model

For system-design estimation, remember:

```text
                 USER ACTIVITY
                      ↓
              How many users?
                      ↓
              How many actions?
                      ↓
              How much data?
                      ↓
        +-------------+-------------+
        |                           |
     STORAGE                     TRAFFIC
        |                           |
Objects × Size              Data ÷ Time
        |                           |
        ↓                           ↓
     GB/TB/PB                  MB/s / Gbps
        |                           |
        +-------------+-------------+
                      ↓
                 Peak Traffic
                      ↓
                 Architecture
```

### The 5 numbers worth memorizing first

```text
1. 1 Byte = 8 bits

2. 1 KB ≈ 10³ Bytes
   1 MB ≈ 10⁶ Bytes
   1 GB ≈ 10⁹ Bytes
   1 TB ≈ 10¹² Bytes
   1 PB ≈ 10¹⁵ Bytes

3. 1 day ≈ 100,000 seconds
   (for rough mental math)

4. Storage = Objects × Size

5. Bandwidth = Data ÷ Time
```

Once these become automatic, you can quickly estimate **QPS, storage, bandwidth, cache size, database capacity, and server requirements** during an HLD interview.

## Example: Bandwidth of Instagram

Assume:

* 500 million daily active users
* Each user uploads 2 photos/day
* Average photo size = 4 MB

### 1. Photos uploaded per day

```text
500 Million users × 2 photos/day
= 1 Billion photos/day
```

### 2. Storage generated per day

```text
1 Billion × 4 MB
= 4 Billion MB/day
≈ 4 PB/day
```

### 3. Storage generated per year

```text
4 PB/day × 365
≈ 1,460 PB/year
≈ 1.46 EB/year
```

After 5 years:

```text
1.46 EB × 5
≈ 7.3 EB
≈ 7.5 EB
```

So Instagram would need roughly:

> **7.5 EB of original photo storage after 5 years.**

This does **not** include:

* Videos
* Stories
* Reels
* Thumbnails
* Metadata
* Backups
* Replication

---

### 4. Replication and backups

If we roughly assume storage overhead makes the requirement **3×**:

```text
7.5 EB × 3
= 22.5 EB
```

> **~22.5 EB total storage**

This is only a rough interview estimate. The actual replication/backup strategy depends on the architecture.

---

## Now let's calculate Bandwidth

We already calculated:

```text
4 PB/day
```

This is **data generated per day**, not bandwidth.

Bandwidth asks:

> **How much data needs to be transferred every second?**

Use:

```text
Bandwidth = Total data / Time
```

Assume:

```text
1 day ≈ 100,000 seconds
```

Therefore:

```text
4 PB/day
= 4 × 10^15 bytes/day

Bandwidth:

4 × 10^15 / 10^5
= 4 × 10^10 bytes/sec
```

Convert Bytes → bits:

```text
4 × 10^10 × 8
= 3.2 × 10^11 bits/sec
```

Therefore:

```text
≈ 320 Gbps
```

So the estimated **average upload bandwidth** is:

> **~320 Gbps**

### Important

Do not confuse:

```text
4 PB/day     → amount of data generated
320 Gbps     → rate at which data is transferred
```

---

## CDN and Caching

For a platform like Instagram, users also **read/download** photos and videos.

The read bandwidth can be much larger than the upload bandwidth because the same popular content may be viewed many times.

For example:

```text
100 Tbps total user traffic
```

Assume:

```text
80% CDN cache hit
20% cache miss
```

Then approximately:

```text
100 Tbps × 20%
= 20 Tbps
```

Only around **20 Tbps** needs to reach the origin.

The remaining traffic is served directly by the CDN.

```text
                    Users
                  ↙   ↓   ↘
                 CDN CDN CDN
                  ↓
          80% served from cache
                  ↓
              Origin
                  ↑
          20% cache miss
```

### Key Insight

> **CDN does not eliminate user bandwidth. It reduces the load and bandwidth requirement on the origin servers.**

It also improves latency because users can receive content from a nearby CDN location.

---

# Another Example: YouTube

Let's take a simplified YouTube-like system.

### Assumptions

```text
1 Billion hours of video watched per day

Average video bitrate = 5 Mbps
```

### 1. Convert hours to seconds

```text
1 Billion hours × 3600
= 3.6 × 10^12 seconds of video watched/day
```

Average concurrent viewers:

```text
3.6 × 10^12 / 86,400
≈ 41.7 Million concurrent viewers
```

### 2. Calculate bandwidth

Each viewer consumes approximately:

```text
5 Mbps
```

Therefore:

```text
41.7 Million × 5 Mbps
≈ 208.5 Tbps
```

So:

> **Average video delivery bandwidth ≈ 200 Tbps**

Peak bandwidth could be significantly higher than the average.

---

## Storage Calculation for YouTube

Now let's calculate storage separately.

Assume:

```text
500,000 new videos/day

Average video size = 100 MB
```

Daily storage:

```text
500,000 × 100 MB
= 50,000,000 MB
≈ 50 TB/day
```

Yearly storage:

```text
50 TB × 365
≈ 18.25 PB/year
```

Therefore:

```text
Storage:
~18 PB/year

Bandwidth:
~200 Tbps
```

These are **completely different calculations**.

---

# Storage vs Bandwidth

| Question                      | Calculation                   |            Example |
| ----------------------------- | ----------------------------- | -----------------: |
| How much data is created?     | Objects × Size                |           4 PB/day |
| How much data is stored?      | Daily data × Days             |   7.5 EB / 5 years |
| How fast is data transferred? | Data / Time                   |           320 Gbps |
| Network bandwidth             | Bytes/sec × 8                 |           320 Gbps |
| CDN benefit                   | Origin traffic × cache-miss % | 100 Tbps → 20 Tbps |

### Mental Model

```text
Storage
↓
How much data do we KEEP?

Bandwidth
↓
How much data do we MOVE per second?
```

Remember:

```text
4 PB
     ↓
Amount of data

4 PB/day
     ↓
Data generated per day

4 PB/s
     ↓
Data transferred every second

4 PB/s × 8
     ↓
32 Pbps
     ↓
Network bandwidth
```

> **Important:** We don't convert `PB → Pbps` just by multiplying by 8. We first need a **time component**. `PB` is a quantity; `PB/s` is a rate.
