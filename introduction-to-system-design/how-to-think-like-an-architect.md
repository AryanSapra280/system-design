# Coder vs Architect — Developing a System Design Mindset

## 1. Coder vs Architect

When working primarily as a **developer/coder**, we tend to focus on:

> **"How do I make this feature work correctly?"**

When thinking like an **architect**, we additionally ask:

> **"How will this feature behave when the system grows, fails, changes, and needs to be maintained?"**

The difference is not that one person writes code and the other doesn't.

Rather:

```text
Developer thinking
        ↓
How do I implement this correctly?

Architect thinking
        ↓
How does the entire system behave?
        ↓
At scale?
        ↓
Under failure?
        ↓
Over time?
        ↓
At what cost?
```

A good architect still needs strong coding knowledge.

---

# 2. Example — Introducing a New Feature

Suppose we want to introduce:

> **"Users can upload profile photos."**

## Developer thinking

The developer might ask:

1. How do I implement the upload API?
2. How do I validate the file?
3. Where do I store the image?
4. How do I write clean and testable code?

For example:

```text
POST /users/{id}/profile-picture

        ↓

Validate image

        ↓

Store image

        ↓

Update user record
```

This is necessary, but it is only the beginning.

---

## Architect thinking

The architect asks additional questions:

1. What happens when **1 million users** upload photos simultaneously?
2. Should images be stored in the database or object storage?
3. How large can an image be?
4. Do we need image resizing/compression?
5. Should image processing happen synchronously or asynchronously?
6. What happens if image processing fails?
7. How do we prevent one user from consuming excessive storage?
8. How much will storing these images cost over five years?
9. How do we scale upload, storage, and processing independently?
10. How will a team of 50 developers maintain this system?

The architect therefore thinks beyond the immediate implementation.

---

# Shift 1 — Think in Trade-offs

## Problem

There is almost never a perfect architecture.

Every design decision has advantages and disadvantages.

For example:

```text
Solution A
→ Cheaper
→ Simpler
→ Less scalable

Solution B
→ More scalable
→ More reliable
→ More expensive
→ More complex
```

Trying to make every component "the best" can actually produce an unnecessarily complicated system.

---

## Solution

An architect thinks in terms of:

> **Trade-offs**

The goal is not:

> "Build the perfect system."

The goal is:

> **"Choose the most appropriate design for the requirements."**

---

## Example — Payment System

Suppose we are designing a payment system.

We may need to think about:

```text
Consistency
Availability
Latency
Cost
Reliability
Complexity
```

For financial transactions, correctness of the transaction is extremely important.

For example:

```text
User pays ₹1,000
```

We cannot accidentally end up with:

```text
Customer account → ₹1,000 deducted

Merchant account → ₹0 received
```

without having a reliable reconciliation/recovery mechanism.

Therefore, payment systems generally place **very high importance on correctness and consistency of financial state**.

However, this does **not** mean:

> "Payment systems always choose consistency over availability."

Real payment systems still need availability and graceful degradation. They often use techniques such as:

* Idempotency
* Transactional state
* Retries
* Reconciliation
* Durable event logs
* Timeouts
* Fallbacks
* Circuit breakers

The actual architecture depends on the business requirements.

---

## Example — Social Media

Suppose a social media application displays:

```text
Follower count: 1,250
```

The actual count might temporarily be:

```text
1,251
```

but the user sees:

```text
1,250
```

For many social features, temporary staleness is acceptable.

Therefore, we may choose:

```text
Higher availability
+
Eventual consistency
```

instead of requiring every read to reflect the latest state immediately.

---

## Architect's Question

Instead of asking:

> "Which architecture is best?"

Ask:

> **"Which trade-off is appropriate for this requirement?"**

---

# Shift 2 — Think About Failure Scenarios

## Problem

Developers often start with the happy path:

```text
Request
  ↓
Service
  ↓
Database
  ↓
Success
```

But distributed systems fail in many ways.

---

## Solution

An architect asks:

> **"What happens when something fails?"**

For every important component, ask:

```text
What can fail?
        ↓
How will we detect it?
        ↓
What will the user experience?
        ↓
Can we recover?
        ↓
How do we prevent data loss?
```

---

## Common Failure Scenarios

Ask:

* What if the database crashes?
* What if the network becomes slow?
* What if a service times out?
* What if a downstream service is unavailable?
* What if a data center goes offline?
* What if a message is delivered twice?
* What if a message is lost?
* What if a deployment fails?
* What if storage becomes unavailable?
* What if traffic suddenly increases 100x?
* What if an attacker sends malicious traffic?

---

# Example — E-commerce Payment Failure

Suppose:

```text
User
 ↓
Order Service
 ↓
Payment Service
 ↓
Bank
```

The user clicks:

> **Pay ₹2,000**

The payment service crashes after the request reaches it.

What should happen?

## Option 1 — Fail immediately

```text
Payment Service unavailable
        ↓
Show error
        ↓
User tries again
```

### Advantage

Simple.

### Problem

The bank/payment provider might actually have processed the payment even though our service didn't receive the response.

Now we have a dangerous situation:

```text
Payment succeeded
        +
Our system thinks payment failed
```

---

## Option 2 — Save the order and retry payment

```text
Create Order
     ↓
Payment Pending
     ↓
Retry Payment
     ↓
Payment Success
```

### Advantage

Better resilience.

### Problem

Requires:

* Retry mechanism
* Idempotency
* Payment state management
* Reconciliation

---

## Option 3 — Use another payment provider

```text
Payment Provider A
        ↓
      Failed
        ↓
Payment Provider B
```

### Advantage

Higher availability.

### Problem

More complexity and potentially more cost.

---

## Architect's Responsibility

The architect evaluates:

```text
Reliability
+
User experience
+
Revenue impact
+
Complexity
+
Cost
+
Data correctness
```

and chooses an appropriate strategy.

---

# Shift 3 — Think in Components and Interfaces

## Problem

Suppose the requirement is:

> "Users should be able to upload photos."

A developer might think:

```text
uploadPhoto()
validatePhoto()
savePhoto()
```

These are implementation details.

An architect first thinks:

> **"What components should exist in the system?"**

---

## Solution

Break the system into components with clear responsibilities.

For example:

```text
                   User
                     |
                     ↓
               Upload Service
                     |
          +----------+----------+
          |                     |
          ↓                     ↓
    Object Storage       Image Processing
          |                     |
          |                     ↓
          |              Thumbnail Service
          |                     |
          +----------+----------+
                     |
                     ↓
                 Metadata DB
```

Now ask:

### How do components communicate?

Possibilities:

```text
REST API
gRPC
Message Queue
Kafka
NATS
Event
```

### What happens if one component is slow?

For example:

```text
Upload → Image Processing
```

If image processing takes 10 seconds, should the user wait 10 seconds?

Maybe not.

Instead:

```text
Upload
  ↓
Store image
  ↓
Publish ImageUploaded event
  ↓
Return success
```

Then:

```text
Image Processing Worker
        ↓
Process asynchronously
```

This improves the user-facing latency.

---

# Independent Scalability

Different components may have different workloads.

For example:

```text
Upload Service
→ 10,000 requests/sec

Image Processing
→ CPU intensive

Storage
→ High capacity requirement

Metadata DB
→ High read/write requirement
```

Therefore, separating components can allow independent scaling.

For example:

```text
Upload Service
3 instances → 10 instances

Image Processing
5 workers → 50 workers
```

without necessarily scaling everything equally.

---

# Shift 4 — Think About Cost

## Problem

A technically excellent architecture can still be a bad architecture if it is unnecessarily expensive.

For example:

```text
"We should use microservices."
```

Sounds good.

But microservices can introduce:

* More servers/containers
* More deployments
* More monitoring
* More networking
* More infrastructure
* More operational complexity
* More debugging difficulty

So the question becomes:

> **"Is the additional complexity worth the benefit?"**

---

# Example — Microservices vs Monolith

Suppose we have a small application:

```text
10,000 users
5 developers
```

We could build:

```text
20 microservices
```

But that might create unnecessary complexity.

A modular monolith could be much easier to build and operate.

On the other hand, if we have:

```text
100 million users
500 developers
Multiple independent business domains
Different scaling requirements
```

microservices may provide significant benefits.

The architecture depends on the requirements.

---

# Example — Log Storage

Suppose our application generates:

```text
1 TB logs/month
```

If we keep everything in expensive, frequently queried storage indefinitely:

```text
1 TB
2 TB
3 TB
...
60 TB
```

The cost keeps increasing.

Instead, we might define a lifecycle:

```text
Recent logs
   ↓
Fast / searchable storage

Older logs
   ↓
Cheaper object storage

Very old logs
   ↓
Archive / delete according to retention policy
```

This gives us:

```text
Performance where needed
+
Lower long-term storage cost
```

### Architect's question

> "Does every piece of data need to be stored in expensive, highly available storage forever?"

Usually, no.

---

# Shift 5 — Think About the System Lifecycle

## Problem

A system is not built once and forgotten.

A system may run for:

```text
5 years
10 years
15 years
```

During that time:

* Requirements change.
* Traffic increases.
* Teams change.
* Technologies become outdated.
* Database schemas evolve.
* New features are added.
* Old features are removed.

Therefore, an architect must think beyond today's requirements.

---

## Solution

Ask:

### How will the system evolve?

```text
Version 1
   ↓
New features
   ↓
More traffic
   ↓
New requirements
   ↓
Version 2
```

### How will we change the database schema?

For example:

```text
Old:

user
----
name
```

New requirement:

```text
first_name
last_name
```

How do we migrate millions of existing records without taking the application offline?

Possible approach:

```text
Add new columns
      ↓
Deploy application supporting both schemas
      ↓
Backfill existing data
      ↓
Start writing new fields
      ↓
Verify
      ↓
Remove old column later
```

This is architectural thinking.

---

# Technical Debt

Architects also consider:

> **"What shortcuts are we taking today, and what will they cost us later?"**

For example:

```text
Quick solution
     ↓
Works today
     ↓
More complexity later
     ↓
Technical debt
```

Technical debt isn't always bad.

Sometimes taking a shortcut is the correct business decision.

The important thing is to understand:

```text
Benefit today
      vs
Cost tomorrow
```

---

# Shift 6 — Think About Patterns and Anti-Patterns

## Problem

Many system-design problems appear repeatedly.

Instead of solving every problem from scratch, architects recognize common:

* Patterns
* Anti-patterns
* Failure modes
* Architectural solutions

---

# Anti-Pattern 1 — Single Point of Failure

Suppose our system looks like:

```text
Users
  |
  ↓
One Server
  |
  ↓
One Database
```

If the server fails:

```text
Entire application unavailable
```

The server is a:

> **Single Point of Failure (SPOF)**

---

## Solution — Redundancy

Introduce multiple instances:

```text
             Load Balancer
              /        \
             ↓          ↓
         Server A    Server B
             \          /
              \        /
                Database
```

We can also use:

* Database replicas
* Multiple application instances
* Multiple availability zones
* Backup systems
* Failover mechanisms

### Trade-off

Redundancy improves availability but increases:

```text
Cost
+
Operational complexity
```

Again:

> **Trade-offs.**

---

# Anti-Pattern 2 — Tight Coupling

Suppose:

```text
Order Service
      |
      ↓
Payment Service
      |
      ↓
Notification Service
```

and every service directly depends on the internal implementation of another service.

A small change in one service can break several other services.

This creates:

> **Tight coupling**

---

## Solution — Loose Coupling

Use clear boundaries and contracts.

For example:

```text
Order Service
      |
      | OrderCreated Event
      ↓
Message Broker
      |
      +----------→ Payment Service
      |
      +----------→ Notification Service
```

Now the services communicate through a defined contract.

Possible mechanisms include:

* Message queues
* Events
* API contracts
* Well-defined service boundaries
* Versioned APIs

This can make the system easier to evolve.

---

# Another Important Anti-Pattern — Shared Database

Suppose:

```text
Service A ──┐
            |
Service B ──┼──> Same Database Tables
            |
Service C ──┘
```

Every service knows the database schema of every other service.

Now changing a table becomes dangerous because multiple services depend on it.

A more loosely coupled architecture might establish clearer ownership:

```text
Service A → Database A

Service B → Database B

Service C → Database C
```

However, this is **not a universal rule**. A shared database can sometimes be the right choice, especially in a modular monolith or when operational simplicity is more valuable than service independence.

Again:

> Architecture is about trade-offs, not blindly following patterns.

---

# The Architect's Mental Model

Whenever you design a system, move through these questions:

```text
                 Requirement
                      |
                      ↓
             How do I implement it?
                      |
                      ↓
             What components exist?
                      |
                      ↓
          How do components communicate?
                      |
                      ↓
              How will it scale?
                      |
                      ↓
             What can go wrong?
                      |
                      ↓
            How will we recover?
                      |
                      ↓
             What will it cost?
                      |
                      ↓
          How will it evolve over time?
                      |
                      ↓
      What patterns/anti-patterns apply?
```

---

# Practical Example — Design a Photo Upload System

Let's apply all six shifts.

## Requirement

> Users can upload profile pictures.

### Step 1 — Basic implementation

```text
Client
  ↓
Upload API
  ↓
Storage
```

Works for the happy path.

---

### Step 2 — Scale

What if:

```text
1 million users
```

upload simultaneously?

We may need:

```text
Load Balancer
       ↓
Multiple Upload Service instances
       ↓
Object Storage
```

---

### Step 3 — Failure

What if image processing fails?

Instead of losing the upload:

```text
Upload
  ↓
Object Storage
  ↓
ImageUploaded Event
  ↓
Processing Queue
  ↓
Image Worker
```

Failed processing can be retried.

---

### Step 4 — Decouple

Don't make the upload request wait for expensive image processing.

```text
Upload
  ↓
Store
  ↓
Publish Event
  ↓
Return Success
```

Processing happens asynchronously.

---

### Step 5 — Cost

Don't store every generated thumbnail forever if it isn't needed.

Define:

```text
Original image → Long-term storage

Generated thumbnails → Regeneratable/cacheable
```

Choose storage tiers based on access patterns.

---

### Step 6 — Lifecycle

Tomorrow we may add:

```text
Face detection
Image moderation
Multiple resolutions
WebP/AVIF conversion
CDN
Watermarking
```

A component-based architecture makes these changes easier.

---

# Final Summary — The Six Shifts

## 1. Think in Trade-offs

Don't ask:

> "What is the perfect solution?"

Ask:

> **"What trade-off is appropriate for this requirement?"**

---

## 2. Think About Failure

Don't ask only:

> "What happens when everything works?"

Also ask:

> **"What happens when each component fails?"**

And:

> **"How do we recover?"**

---

## 3. Think in Components and Interfaces

Don't think only:

```text
functions
classes
methods
```

Also think:

```text
components
responsibilities
interfaces
APIs
events
contracts
```

---

## 4. Think About Cost

Every architectural decision has a cost.

Consider:

```text
Infrastructure
Storage
Network
Compute
Operations
Development
Maintenance
```

Ask:

> **"Is this complexity worth the benefit?"**

---

## 5. Think About Lifecycle

Don't design only for today.

Think about:

```text
Growth
New features
Schema changes
Migrations
Technology changes
Team changes
Technical debt
```

---

## 6. Recognize Patterns and Anti-Patterns

Learn to recognize recurring problems.

```text
Single Point of Failure
        ↓
Redundancy / Failover


Tight Coupling
        ↓
Clear interfaces / Events / APIs


Single server
        ↓
Horizontal scaling


Synchronous dependency chain
        ↓
Asynchronous processing where appropriate


Expensive storage forever
        ↓
Data lifecycle / tiered storage
```

---

# The Core Mindset

The biggest shift from implementation thinking to system-design thinking is:

```text
                    CODE
                     ↓
              Does it work?
                     ↓
                  SYSTEM
                     ↓
          Does it work at scale?
                     ↓
                 FAILURE
                     ↓
        Does it survive failures?
                     ↓
                  COST
                     ↓
             Is it economical?
                     ↓
                LIFECYCLE
                     ↓
          Can it evolve over time?
```

### Final Interview Mental Checklist

Whenever you get an HLD problem, train yourself to ask:

```text
1. What are the requirements?
2. What scale are we designing for?
3. What components do we need?
4. What are the responsibilities of each component?
5. How do they communicate?
6. Where is the data stored?
7. How will the system scale?
8. What are the bottlenecks?
9. What can fail?
10. How do we recover?
11. What consistency/availability trade-offs are acceptable?
12. What will the system cost?
13. How will we monitor it?
14. How will it evolve?
15. Which patterns and anti-patterns apply?
```

This is the mindset we want to build before jumping into individual HLD problems.
