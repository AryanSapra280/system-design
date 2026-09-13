# Non-Functional Requirements

## 1. What are Non-Functional Requirements?

### Problem

Functional requirements tell us:

> **What should the system do?**

For example:

```text
User should be able to upload a photo.
```

But this alone doesn't tell us whether the system is good enough for real-world usage.

Consider two systems:

```text
System A
→ Uploads the photo
→ Takes 30 seconds
→ Works for 100 users
→ Goes down frequently
→ Sometimes loses uploaded photos

System B
→ Uploads the photo in 2 seconds
→ Handles millions of users
→ Highly available
→ Doesn't lose data
```

Both satisfy the basic functional requirement:

> "User can upload a photo."

But System B is much more suitable for production.

This is where **Non-Functional Requirements (NFRs)** come in.

---

# 2. Solution

Non-functional requirements describe:

> **How well the system performs its functional requirements and what quality attributes/constraints the system must satisfy.**

They commonly describe qualities such as:

```text
Performance
Scalability
Availability
Reliability
Security
Maintainability
Usability
```

Therefore:

```text
Functional Requirements
        ↓
What does the system do?

Non-Functional Requirements
        ↓
How well does it do it?
        +
Under what constraints?
```

---

# 3. Example — Photo Upload

### Functional Requirement

> User should be able to upload a photo.

This describes **what the system does**.

Now attach NFRs:

### Performance

> Photo upload should complete within 3 seconds under the defined conditions.

### Scalability

> System should support 500 million photo uploads per day.

### Reliability

> Once an upload is successfully acknowledged, the photo should not be lost.

### Availability

> Upload service should be available 99.9% of the time.

### Security

> Only authorized users should be able to access private photos.

Now we have a much more complete picture.

```text
                  Photo Upload
                       |
       +---------------+---------------+
       |               |               |
   Functional      Performance     Scalability
       |               |               |
   Upload photo     < 3 sec       500M/day
                       |
              +--------+--------+
              |        |        |
         Reliability Availability Security
```

These quality attributes often separate a **prototype** from a **production-ready system**.

---

# 4. Important NFRs

---

# NFR 1 — Performance

## Problem

A system can technically work but still provide a poor user experience if it is too slow.

For example:

```text
User clicks "Search"
        ↓
Waits 20 seconds
        ↓
Results appear
```

The functionality works, but the system is not performing well.

---

## Solution

Performance describes:

> **How quickly the system responds or completes an operation under a given workload.**

Performance is commonly measured using:

* Latency
* Response time
* Throughput

---

## Latency

Latency is the time taken to complete/respond to a request.

Examples:

```text
Web page
→ Should load within 2 seconds

API
→ Response within 200 ms

Database query
→ Complete within 100 ms
```

These numbers are examples; the actual target depends on the system.

---

## Throughput

Throughput describes:

> **How much work the system can process per unit of time.**

Examples:

```text
10,000 requests/second

100,000 messages/second

50,000 transactions/minute
```

Therefore:

```text
Performance
   |
   +--- Latency
   |
   +--- Throughput
```

A system can have low latency for individual requests but still have poor throughput under heavy load, so both can matter.

---

# NFR 2 — Scalability

## Problem

Suppose our application works perfectly for:

```text
100 users
```

But tomorrow:

```text
1 million users
```

start using it.

If the system cannot handle the increased load, we have a scalability problem.

---

## Solution

Scalability is the ability of a system to **accommodate increasing workload while maintaining acceptable performance and other required properties**.

There are two major approaches.

---

## Vertical Scaling

Increase the resources of an existing machine.

```text
Before:

4 CPU
16 GB RAM

        ↓

After:

16 CPU
64 GB RAM
```

Conceptually:

```text
More powerful machine
        ↓
Handle more workload
```

### Advantages

* Simple
* Often requires fewer architectural changes

### Disadvantages

* Hardware has limits
* Can become expensive
* May still leave a single-machine failure risk

---

# Horizontal Scaling

Add more machines/instances.

```text
             Load Balancer
             /     |     \
            ↓      ↓      ↓
         Server A Server B Server C
```

Instead of:

```text
1 very powerful server
```

we use:

```text
many servers
```

### Advantages

* Better ability to scale out
* Can improve availability
* Works well for stateless services

### Disadvantages

* More infrastructure
* More operational complexity
* Distributed-system problems become relevant

Examples:

* Load balancing
* Service discovery
* Distributed caching
* Database partitioning
* Data replication

---

## Vertical vs Horizontal

```text
Vertical Scaling
     ↓
Make one machine bigger


Horizontal Scaling
     ↓
Add more machines
```

---

# NFR 3 — Availability

## Problem

A system is useless to users if it is frequently unavailable.

For example:

```text
User
 ↓
Service
 ↓
"Service unavailable"
```

even though the underlying system may be perfectly designed when running.

---

## Solution

Availability measures:

> **How often a system is operational and accessible when users need it.**

Availability is commonly expressed using **"nines."**

Examples:

```text
99%
99.9%
99.99%
99.999%
```

---

# Availability and Downtime

Approximate maximum downtime per year:

| Availability | Downtime/year |
| ------------ | ------------: |
| 99%          |    ~3.65 days |
| 99.9%        |   ~8.76 hours |
| 99.99%       | ~52.6 minutes |
| 99.999%      | ~5.26 minutes |

So:

```text
99%
  ↓
~3.65 days downtime

99.9%
  ↓
~8.76 hours

99.99%
  ↓
~53 minutes

99.999%
  ↓
~5 minutes
```

Each additional nine generally becomes increasingly difficult and expensive to achieve.

---

## How do we improve availability?

We can introduce redundancy:

```text
                 Load Balancer
                 /           \
                ↓             ↓
            Server A       Server B
```

If Server A fails:

```text
Server A ❌
    ↓
Traffic → Server B
```

Other techniques include:

* Multiple application instances
* Multiple availability zones
* Database replication
* Failover
* Health checks
* Disaster recovery

---

## Important Point

Don't simply say:

> "Always design for 99.999% availability."

Instead ask:

> **"What level of availability does the business actually require?"**

For some internal applications:

```text
99%
```

may be acceptable.

For critical financial or infrastructure systems:

```text
99.99%+
```

may be justified.

Higher availability usually means higher:

```text
Infrastructure cost
+
Operational complexity
+
Engineering effort
```

---

# NFR 4 — Reliability

## Problem

A system may be available but still unreliable.

For example:

```text
Service is UP
     ↓
User adds item to cart
     ↓
Cart update fails
```

The service is technically available, but it did not perform the operation correctly.

---

## Solution

Reliability describes:

> **The ability of a system to consistently perform its intended function correctly and preserve data/integrity despite failures.**

Examples of failures:

```text
Server crashes
Network failures
Database failures
Message delivery failures
Hardware failures
Software bugs
```

A reliable system should have mechanisms to:

```text
Detect failure
     ↓
Recover
     ↓
Avoid data loss/corruption
     ↓
Continue operating where possible
```

---

## Example — Shopping Cart

Suppose:

```text
User adds:

Laptop
Phone
Headphones
```

Now one application server crashes.

A reliable architecture should ensure that the cart state is not simply lost because one server failed.

Possible mechanisms include:

```text
Replication
+
Durable storage
+
Failover
+
Recovery
```

The exact implementation depends on the system.

---

# Availability vs Reliability

This distinction is extremely important.

### Availability

> **Is the system accessible and operational?**

### Reliability

> **Does the system consistently perform correctly and preserve the intended state?**

Example:

```text
System is responding → Availability
System correctly processes the request → Reliability
```

A system can be:

```text
Highly available
but unreliable
```

For example, it responds to every request but occasionally loses data.

---

# NFR 5 — Security

## Problem

A system isn't production-ready merely because it works.

It must also protect:

```text
Users
Data
Infrastructure
Services
```

from unauthorized access and attacks.

---

## Solution

Security includes several areas.

### Authentication

> **Who are you?**

Example:

```text
Username + Password
OAuth
JWT
MFA
```

---

### Authorization

> **What are you allowed to do?**

Example:

```text
User A
→ Can access their own profile

Admin
→ Can access administrative functions
```

Authentication:

```text
"Who are you?"
```

Authorization:

```text
"What are you allowed to access?"
```

---

### Encryption

Protect data:

```text
In Transit
     ↓
TLS/HTTPS

At Rest
     ↓
Encrypted storage/database
```

---

### Audit Logging

Track:

```text
Who
  ↓
Did what
  ↓
When
```

For example:

```text
Admin Aryan
deleted user account
at 10:32 AM
```

This is important for security, debugging, and compliance.

---

# NFR 6 — Maintainability

## Problem

A system may work perfectly today but become extremely difficult to modify.

For example:

```text
Add one feature
      ↓
Modify 15 unrelated classes
      ↓
Break another feature
      ↓
Fix another bug
      ↓
Deploy
      ↓
Another feature breaks
```

This is a maintainability problem.

---

## Solution

Maintainability describes:

> **How easily a system can be understood, modified, tested, deployed, and operated over time.**

Good maintainability generally involves:

### Clean code

```text
Clear naming
Small responsibilities
Low unnecessary coupling
Good abstractions
```

### Documentation

Developers should understand:

```text
Why does this component exist?
How does it work?
What are its dependencies?
```

### Modularity

Different parts of the system should have clear responsibilities.

```text
Order Module
Payment Module
Inventory Module
Notification Module
```

Changes in one area should have limited impact on unrelated areas.

### Testing

Good automated tests help us change the system without fear of breaking existing behavior.

---

# NFR 7 — Usability

## Problem

A system can be technically excellent but difficult for users to operate.

For example:

```text
User enters wrong password

System:
"Error 0x8273A9"
```

This is technically an error message, but it isn't useful to the user.

---

## Solution

Usability focuses on:

> **How easy and intuitive the system is for users to accomplish their tasks.**

Examples:

```text
Clear UI
Helpful error messages
Simple workflows
Accessible interfaces
Responsive design
Mobile-friendly experience
```

Instead of:

```text
Error 0x8273A9
```

show:

```text
Incorrect password.
Please try again.
```

---

# NFR 8 — Operational Requirements

Some requirements are primarily concerned with how the system is **operated in production**.

Important areas include:

## Monitoring

We need to know:

```text
Is the system healthy?
Are requests failing?
Is latency increasing?
Is CPU/memory usage high?
```

---

## Logging

Logs help us:

```text
Investigate failures
Debug production issues
Understand system behavior
```

---

## Backup and Recovery

Ask:

```text
What happens if the database is lost?

How quickly can we restore it?

How much data can we afford to lose?
```

This leads to concepts such as:

```text
RPO → Recovery Point Objective
RTO → Recovery Time Objective
```

### RPO

> How much data loss can we tolerate?

Example:

```text
RPO = 5 minutes
```

means we should aim to recover with no more than approximately five minutes of data loss.

### RTO

> How long can the system remain unavailable before recovery?

Example:

```text
RTO = 30 minutes
```

means the system should be restored within approximately 30 minutes.

---

## Deployment

Deployment should ideally minimize:

```text
Downtime
+
Risk
+
User impact
```

Possible strategies:

```text
Rolling deployment
Blue-green deployment
Canary deployment
```

---

# 9. NFRs Can Conflict With Each Other

## Problem

System design is not about maximizing every quality attribute independently.

Improving one attribute can negatively affect another.

Therefore:

> **NFRs create trade-offs.**

---

# Example 1 — Availability vs Cost

Suppose we want extremely high availability.

We may need:

```text
Multiple servers
+
Multiple zones
+
Database replicas
+
Failover
+
Backup infrastructure
```

This improves availability.

But:

```text
More infrastructure
        ↓
Higher cost
```

Therefore:

```text
Higher Availability
        ↕
Higher Cost / Complexity
```

---

# Example 2 — Performance vs Consistency

Suppose every read must always retrieve the latest value from the primary database.

This may improve consistency but can increase:

```text
Latency
+
Database load
```

We could introduce caching:

```text
User
 ↓
Cache
 ↓
Fast response
```

but now we may temporarily serve stale data.

Therefore:

```text
Lower latency
        ↕
Potentially less freshness
```

Whether that trade-off is acceptable depends on the requirement.

---

# Example 3 — Consistency vs Availability

This is where distributed-system concepts such as **CAP** become important.

In a distributed system, if a **network partition** occurs, a system cannot simultaneously guarantee both:

```text
Strong consistency
+
Availability
```

under the CAP model.

For example:

```text
Node A  X  Node B
       network partition
```

The system has to make a trade-off in what it guarantees during that partition.

### Important

Don't simplify CAP to:

> "You can only choose two of consistency, availability, and partition tolerance."

A better understanding is:

> **When a network partition occurs, a distributed data system must choose between maintaining strong consistency and continuing to provide availability.**

Partition tolerance is effectively required for a distributed system operating across an unreliable network.

We'll study CAP in detail later.

---

# 10. NFRs Are Usually Quantifiable

A strong NFR should ideally be measurable.

Instead of:

```text
System should be fast.
```

say:

```text
95th percentile API latency < 200 ms
```

Instead of:

```text
System should be highly available.
```

say:

```text
Availability target = 99.99%
```

Instead of:

```text
System should handle many users.
```

say:

```text
System should support 10 million daily active users.
```

Instead of:

```text
System should be reliable.
```

define measurable goals such as:

```text
No loss of acknowledged transactions
+
RPO ≤ 5 minutes
+
RTO ≤ 30 minutes
```

This makes the requirement testable.

---

# 11. Functional + Non-Functional Requirements

The best way to think about system requirements is:

```text
                SYSTEM REQUIREMENTS
                        |
             +----------+----------+
             |                     |
        Functional             Non-Functional
             |                     |
        What to do            How well to do it
             |                     |
      Send message             < 200 ms
      Upload photo             99.99% available
      Make payment             10M users
      Search messages          Encrypted
                               Reliable
```

---

# 12. Complete Example — Chat Application

Suppose the requirement is:

> "Design a chat application."

## Functional Requirements

```text
1. User can send messages.
2. User can receive messages.
3. User can create group chats.
4. User can see delivery status.
5. User can see read status.
6. User can search messages.
```

These define:

> **What the system does.**

---

## Non-Functional Requirements

Now define:

### Performance

```text
Message delivery should normally occur within a few seconds.
```

### Scalability

```text
10 million daily active users.
```

### Availability

```text
99.99% availability for messaging.
```

### Reliability

```text
Successfully acknowledged messages should not be lost.
```

### Security

```text
Messages should be encrypted.
```

### Maintainability

```text
Services should have clear responsibilities
and well-defined APIs/events.
```

Now we have enough information to start making architectural decisions.

---

# 13. How NFRs Influence Architecture

This is the most important connection for HLD.

NFRs are not just documentation.

They **drive architectural decisions**.

For example:

### Requirement

```text
10 million users
```

may lead to:

```text
Horizontal scaling
Load balancing
Caching
Database partitioning
```

---

### Requirement

```text
99.99% availability
```

may lead to:

```text
Multiple instances
Multi-zone deployment
Replication
Failover
Health checks
```

---

### Requirement

```text
< 200 ms latency
```

may lead to:

```text
Caching
CDN
Database indexing
Read replicas
Async processing
Data locality
```

---

### Requirement

```text
No loss of acknowledged data
```

may lead to:

```text
Durable storage
Replication
Transactions
Acknowledgements
Retries
Reconciliation
```

Therefore:

```text
NFR
 ↓
Architectural constraint
 ↓
Design decision
```

---

# 14. A Useful HLD Mental Model

When designing a system, think:

```text
Functional Requirement
        ↓
What does the system need to do?
        ↓
Non-Functional Requirement
        ↓
How well does it need to do it?
        ↓
Scale
Performance
Availability
Reliability
Security
Maintainability
        ↓
Architecture
        ↓
Components
        ↓
Technology choices
```

---

# 15. NFR Checklist for HLD Interviews

When the interviewer gives you a system-design problem, ask:

## Performance

```text
What latency do we need?

What throughput do we need?

What percentile matters?
```

---

## Scalability

```text
How many users?

Requests per second?

Data volume?

How fast will traffic grow?
```

---

## Availability

```text
What availability target?

99%?
99.9%?
99.99%?
```

---

## Reliability

```text
Can we lose data?

What happens if a server fails?

What happens if the database fails?

Do we need retries?

Do we need reconciliation?
```

---

## Security

```text
Who can access the data?

How do we authenticate?

How do we authorize?

Does data need encryption?

Do we need audit logs?
```

---

## Maintainability

```text
How will developers modify the system?

How tightly coupled are components?

How will we test it?

How will we deploy it?
```

---

## Operations

```text
How do we monitor it?

How do we debug it?

How do we back it up?

How do we recover?

How do we deploy safely?
```

---

# Final Summary

## Functional Requirements

> **What should the system do?**

Examples:

```text
Send message
Upload photo
Search product
Make payment
```

---

## Non-Functional Requirements

> **How well should the system perform those functions and under what constraints?**

Examples:

```text
Performance
Scalability
Availability
Reliability
Security
Maintainability
Usability
Operability
```

---

# The Most Important Distinctions

### Performance

> **How fast?**

```text
Latency
Throughput
```

### Scalability

> **How well can the system accommodate growth?**

```text
Vertical
Horizontal
```

### Availability

> **Is the system accessible when needed?**

```text
99%
99.9%
99.99%
99.999%
```

### Reliability

> **Does the system consistently perform correctly and preserve intended state despite failures?**

### Security

> **Can unauthorized users access or manipulate the system/data?**

```text
Authentication
Authorization
Encryption
Audit
```

### Maintainability

> **How easily can we change and operate the system over time?**

### Usability

> **How easy is it for users to accomplish their goals?**

### Operability

> **How easily can engineers monitor, debug, deploy, and recover the system?**

---

# Golden Rule

Don't ask:

> **"What technology should I use?"**

first.

Ask:

```text
What does the system need to do?
             ↓
How well does it need to do it?
             ↓
At what scale?
             ↓
What happens when things fail?
             ↓
What constraints exist?
             ↓
What trade-offs are acceptable?
             ↓
THEN choose the architecture.
```

That is the core of **HLD thinking**.
