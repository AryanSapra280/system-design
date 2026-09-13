# Functional Requirements

## 1. What are Functional Requirements?

### Problem

When we receive an HLD question such as:

> **"Design a chat application."**

we don't immediately know what the system actually needs to do.

"Chat application" is only a **vague idea**, not a complete requirement.

Before thinking about:

* Database
* Kafka
* Redis
* Microservices
* Load balancers
* APIs

we first need to understand:

> **What exactly should the system do?**

---

## Solution

### Functional Requirements

Functional requirements define:

> **What the system should do.**

They describe the **features, capabilities, and behaviors** that users or other systems interact with.

For a chat application, functional requirements might be:

1. User should be able to send text messages.
2. User should be able to see message delivery status.
3. User should be able to create group chats.
4. User should be able to search messages.
5. User should be able to delete messages.
6. User should be able to block another user.

These requirements will eventually drive our architecture.

---

# 2. Why Are Functional Requirements Important in System Design?

A common mistake in HLD interviews is to jump directly into architecture:

```text
"Let's use Kafka."
"Let's use Redis."
"Let's use MongoDB."
"Let's use microservices."
```

without first understanding what the system needs to do.

The correct sequence is:

```text
Requirements
     ↓
Use cases
     ↓
Scale & constraints
     ↓
Architecture
     ↓
Components
     ↓
Technology choices
```

### Why?

Because every requirement can have architectural implications.

For example:

> "Users should be able to search messages."

Immediately raises questions:

```text
Where are messages stored?
        ↓
How are they indexed?
        ↓
How much data exists?
        ↓
Do we need full-text search?
        ↓
How quickly should search respond?
        ↓
Should old messages also be searchable?
```

A seemingly simple feature can significantly influence the architecture.

---

# 3. Example — Chat Application

Suppose the initial requirement is:

> **"User should be able to send text messages."**

This is a functional requirement, but it is still not detailed enough.

We need to clarify its scope.

### Questions to ask

#### Message type

* Only text?
* Images?
* Videos?
* Audio?
* Documents?

#### Message size

* Maximum text length?
* Maximum attachment size?

#### Message lifecycle

* Do messages expire?
* Can users delete messages?
* Can users edit messages?
* Should deleted messages be recoverable?

#### Delivery

* What does "sent" mean?
* What does "delivered" mean?
* What does "read" mean?
* Should the sender see these statuses?

#### Offline users

What happens when:

```text
User A → sends message → User B is offline
```

Should the message:

```text
be stored?
be delivered later?
expire?
```

These questions help us convert a vague requirement into a precise system behavior.

---

# 4. Technique #1 — Identify User Stories

## Problem

Different users may interact with the same system in completely different ways.

For example, an e-commerce platform has:

```text
Buyer
Seller
Administrator
```

Their requirements are different.

---

## Solution

Identify:

> **Who are the users, and what does each user want to accomplish?**

This is commonly captured through **user stories/use cases**.

---

# Example — E-commerce Platform

## Buyer

The buyer may need:

1. Search products
2. View product details
3. Add products to cart
4. Make payments
5. Track orders
6. Cancel orders
7. Request returns/refunds
8. Review products

---

## Seller

The seller may need:

1. List products
2. Update product information
3. Manage inventory
4. View sales
5. Process orders
6. Handle returns

---

## Administrator

The administrator may need:

1. Manage users
2. Monitor transactions
3. Generate reports
4. Handle disputes
5. Manage sellers
6. Monitor suspicious activity

---

## Why is this useful?

Instead of saying:

> "Build an e-commerce system."

we now have:

```text
                    E-Commerce
                        |
          +-------------+-------------+
          |             |             |
        Buyer         Seller        Admin
          |             |             |
       Search        Products       Users
       Cart          Inventory      Reports
       Payment       Orders         Disputes
       Orders        Returns        Monitoring
```

This gives us a much clearer understanding of the system.

---

# 5. Technique #2 — Understand Scope and Boundaries

## Problem

A large system can potentially have hundreds of features.

For example, a video streaming platform could support:

* Video upload
* Video playback
* Comments
* Likes
* Playlists
* Subscriptions
* Recommendations
* Live streaming
* Monetization
* Creator analytics
* Notifications
* Search

Trying to build everything simultaneously is usually a mistake.

---

## Solution

Define the **scope**.

Separate requirements into:

```text
Must-have
    vs
Nice-to-have
```

For example:

### V1 — MVP

```text
Video upload
Video playback
```

### V2

```text
Comments
Likes
Playlists
```

### V3

```text
Subscriptions
Monetization
Creator analytics
```

This first usable version is commonly called an:

> **MVP — Minimum Viable Product**

The goal of an MVP is not to build a poor-quality system.

It is to build the **smallest version that delivers the core value** and allows us to validate the product.

---

# 6. Scope Is Extremely Important in HLD Interviews

In an interview, don't try to design:

> "The entire YouTube."

Instead, explicitly define the scope.

For example:

> "For this design, I'll focus on video upload and playback. I'll exclude recommendations, comments, monetization, and live streaming unless we need them to explain another part of the architecture."

This is a strong architectural habit.

It prevents:

```text
Unbounded requirements
        ↓
Unnecessary components
        ↓
Over-engineering
        ↓
Confusing design
```

---

# 7. Technique #3 — Make Requirements Specific and Testable

## Problem

Some requirements are too vague.

For example:

> "Users should be able to upload videos quickly."

What does "quickly" mean?

```text
10 seconds?
1 minute?
5 minutes?
```

We cannot properly test the requirement.

---

## Solution

Make the requirement measurable.

For example:

> "The system should support uploading a 100 MB video within 2 minutes under defined network conditions."

Now we have something that can be tested.

---

### Another Example

Bad:

> "Search should be fast."

Good:

> "Search results should be returned within 200 ms for 95% of queries under the target workload."

This introduces measurable metrics such as:

```text
Latency
+
Percentile
+
Workload
```

---

# 8. Important: Functional vs Non-Functional Requirements

This distinction is extremely important for HLD interviews.

### Functional Requirement

Describes:

> **What the system does.**

Examples:

```text
User can send a message.

User can create a group.

User can search messages.

User can upload a video.

User can make a payment.
```

### Non-Functional Requirement

Describes:

> **How well the system performs or what qualities/constraints it must satisfy.**

Examples:

```text
Search latency < 200 ms for 95% of queries.

System supports 10 million active users.

System availability = 99.99%.

Messages must be encrypted.

System should recover from server failure.
```

Therefore, your original example:

> "Users should be able to upload a 100 MB video in 2 minutes."

contains both:

```text
Functional:
User can upload a video.

Non-functional:
100 MB within 2 minutes under defined conditions.
```

Similarly:

> "Search should return relevant results within 200 ms for 95% of queries."

contains:

```text
Functional:
System provides search.

Non-functional:
Latency requirement + percentile target.
```

This distinction will become very important when we study **Non-Functional Requirements** next.

---

# 9. Technique #4 — Identify Hidden Functional Requirements

## Problem

Requirements often contain functionality that isn't explicitly mentioned.

For example:

> "Users should be able to post on a social media platform."

We might initially identify:

```text
Post
Like
Comment
```

But real-world behavior requires many additional decisions.

---

## Solution

Ask:

> **"What other actions or edge cases naturally come with this feature?"**

---

# Example — Social Media

Basic requirements:

```text
Create post
Like post
Comment on post
```

But we should also ask:

### Delete

```text
Can a user delete their post?

What happens to comments?

What happens to likes?

Should the post disappear immediately?

Should deleted posts be recoverable?
```

### Edit

```text
Can the user edit the post?

Should previous versions be retained?

Should followers be notified?
```

### Privacy

```text
Can users make their profile private?

Who can see a post?

Who can comment?

Who can message the user?
```

### Blocking

Suppose:

```text
User A blocks User B
```

What should happen?

```text
Can B see A's profile?

Can B see A's posts?

Can B comment?

Can B send messages?

What happens to existing conversations?
```

These are **hidden requirements** that can significantly affect the design.

---

# 10. Edge Cases Are Part of Requirements

An architect should not only ask:

> "What happens when everything works?"

Also ask:

> **"What happens at the boundaries?"**

For example, messaging:

```text
Normal:
A sends message → B receives message
```

Edge cases:

```text
B is offline
A loses network
Message is sent twice
Message arrives out of order
Message is very large
Message contains unsupported content
User blocks B while message is being sent
```

These scenarios eventually influence:

* Data model
* APIs
* Queues
* Idempotency
* Retry mechanisms
* Storage
* Consistency model

---

# 11. Technique #5 — Identify Constraints

## Problem

A functional requirement doesn't exist in isolation.

The same functionality can require completely different architectures depending on its constraints.

For example:

```text
"Users can upload photos."
```

is very different from:

```text
"100 million users can upload photos."
```

---

## Solution

Attach important constraints to the requirements.

### Scale constraint

```text
10 million active users
```

Now we need to think about:

```text
Horizontal scaling
Load balancing
Caching
Database scaling
Partitioning
```

---

### Security constraint

For a messaging application:

```text
End-to-end encryption
```

This has significant architectural implications.

We now need to think about:

```text
Key management
Encryption/decryption
Message storage
Search limitations
Device management
```

---

### Compliance constraint

Suppose the system handles financial data.

We may need:

```text
Audit logs
Data retention
Access control
Data residency
Encryption
```

Again, the architecture changes.

---

# 12. Functional Requirements + Constraints

A useful way to think about requirements is:

```text
Functional Requirement
        +
Scale
        +
Security
        +
Performance
        +
Availability
        +
Compliance
        ↓
Actual System Design
```

For example:

```text
Requirement:
"User can search messages."

        +

10 million users

        +

Messages retained for 5 years

        +

Search response < 200 ms

        +

Messages encrypted

        ↓

Completely different architecture
```

than a simple:

```text
Small application
100 users
1-month retention
No strict latency requirement
```

---

# 13. Technique #6 — Requirements Evolve Over Time

## Problem

Today's requirements may not be tomorrow's requirements.

For example:

```text
V1:
User can post text.
```

Then:

```text
V2:
User can post text + emojis.
```

Then:

```text
V3:
User can post images.
```

Then:

```text
V4:
User can post videos.
```

Then:

```text
V5:
Stories disappear after 24 hours.
```

The system needs to evolve.

---

## Solution

Design for **reasonable extensibility**.

But there is an important balance.

Don't try to predict every possible future requirement.

That leads to:

> **Over-engineering**

Instead:

```text
Current requirements
        +
Reasonable expected evolution
        ↓
Simple extensible design
```

The goal is:

> **Don't build everything today, but don't make tomorrow's obvious changes unnecessarily painful.**

---

# 14. Example — Message Model

Suppose V1 only supports text.

A naive model might be:

```text
message
-------
id
sender
receiver
text
```

Later we want:

```text
Images
Videos
Files
```

We could design the model more flexibly:

```text
message
-------
id
sender
receiver
message_type
content
created_at
```

where:

```text
message_type:
TEXT
IMAGE
VIDEO
FILE
```

This gives us some room for evolution without prematurely building an extremely complex system.

---

# 15. Functional Requirements and Failure Scenarios

Failure scenarios aren't always separate functional requirements, but they often expose **missing behavior that must be specified**.

For example:

> "User should be able to make a payment."

What happens if:

```text
Payment succeeds
        ↓
Response is lost
        ↓
Client retries
```

Now we need to decide:

> Should the second request create another payment?

Obviously, we don't want:

```text
₹1,000
+
₹1,000
=
₹2,000 charged
```

for a single intended payment.

This leads to requirements around:

```text
Idempotency
Payment state
Retry behavior
Reconciliation
```

Therefore, asking about failure scenarios can reveal important functional behavior.

---

# 16. A Practical Requirement-Gathering Framework

When you receive an HLD problem, use this sequence.

## Step 1 — Identify Actors

Ask:

```text
Who uses the system?
```

For example:

```text
Buyer
Seller
Admin
```

---

## Step 2 — Identify Core Actions

Ask:

```text
What does each actor need to do?
```

Example:

```text
Buyer → Search → Cart → Payment → Order
```

---

## Step 3 — Define Scope

Ask:

```text
What are we building?

What are we NOT building?
```

Separate:

```text
MVP / Must-have
        vs
Nice-to-have
```

---

## Step 4 — Clarify Ambiguous Requirements

For every requirement, ask:

```text
What exactly does this mean?
```

For:

> "Send a message."

ask:

```text
Text only?
Attachments?
Maximum size?
Editing?
Deletion?
Expiration?
Offline delivery?
Read receipts?
```

---

## Step 5 — Identify Hidden Requirements

Ask:

```text
What operations naturally accompany this feature?
```

For example:

```text
Create
Read
Update
Delete
Search
Share
Block
Report
```

---

## Step 6 — Identify Constraints

Ask:

```text
How many users?

How much traffic?

How much data?

What latency?

What availability?

What security requirements?

What retention?
```

---

## Step 7 — Think About Evolution

Ask:

```text
What is likely to change?

Can our design accommodate reasonable changes?
```

But avoid designing for imaginary requirements.

---

# 17. Example — Requirement Gathering for a Chat Application

Instead of:

> "Design WhatsApp."

Start with:

### Actors

```text
Users
```

### Core functional requirements

```text
1. Users can send one-to-one text messages.
2. Users can receive messages.
3. Users can see delivery status.
4. Users can see read status.
5. Users can create group chats.
6. Users can search their messages.
```

### Clarifications

```text
Do we support images?
Do we support videos?
Do we support files?
Can messages be edited?
Can messages be deleted?
Do messages expire?
What happens when a user is offline?
```

### Scope

For V1:

```text
One-to-one text messaging
Message delivery
Read receipts
```

Out of scope:

```text
Video calls
Stories
Payments
Channels
Large file sharing
```

### Scale

```text
100 million registered users
10 million daily active users
```

### Performance

```text
Message delivery should normally happen within a few seconds.
```

### Security

```text
Messages should be encrypted.
```

Now we have something concrete to design.

---

# 18. Why Missing Requirements Can Force a Redesign

Consider that we initially design:

```text
Application
    ↓
Database
```

Then halfway through development someone says:

> "Oh, by the way, messages must support end-to-end encryption."

That can affect:

```text
Client architecture
Key management
Message storage
Search
Notifications
Message processing
Server-side access
```

Or suppose someone says:

> "Oh, the system needs to support 100 million users."

Now we may need to reconsider:

```text
Database architecture
Caching
Partitioning
Load balancing
Messaging
Storage
Capacity planning
```

Therefore:

> **Getting requirements right early can prevent expensive architectural redesign later.**

---

# 19. Requirements → Architecture

The most important mental model is:

```text
                 User Need
                    ↓
            Functional Requirement
                    ↓
             Clarifications
                    ↓
            Scope / Boundaries
                    ↓
              Scale / Constraints
                    ↓
            Failure Scenarios
                    ↓
               Architecture
                    ↓
          Components + Interfaces
                    ↓
             Technology Choices
```

Notice that:

```text
Kafka
Redis
PostgreSQL
MongoDB
Microservices
```

come **after** understanding the requirements.

We don't start with technology.

We start with the problem.

---

# Final Summary

## What are Functional Requirements?

> **Functional requirements define what the system should do.**

Examples:

```text
User can send a message.

User can create a group.

User can search messages.

User can upload a video.

User can make a payment.
```

---

## Six Things to Remember

### 1. Identify User Stories

```text
Who uses the system?
What does each user want to do?
```

---

### 2. Define Scope

```text
Must-have
    vs
Nice-to-have
```

Build the MVP first.

---

### 3. Make Requirements Specific

Avoid:

```text
"Search should be fast."
```

Prefer measurable requirements:

```text
"Search should return results within
200 ms for 95% of queries."
```

Remember: the measurable latency target is a **non-functional requirement** attached to the search functionality.

---

### 4. Look for Hidden Requirements

Don't stop at:

```text
Create
```

Think about:

```text
Read
Update
Delete
Search
Privacy
Blocking
Permissions
Failure cases
```

---

### 5. Identify Constraints

Think about:

```text
Scale
Performance
Availability
Security
Compliance
Data retention
```

These constraints can dramatically change the architecture.

---

### 6. Think About Evolution

Ask:

> **"How might this requirement evolve?"**

But don't over-engineer for requirements that don't exist yet.

---

# Interview Mental Checklist

When the interviewer gives you:

> **"Design X."**

Before drawing the architecture, ask:

```text
1. Who are the users?
2. What are the core use cases?
3. What exactly should each feature do?
4. What is in scope?
5. What is out of scope?
6. What are the hidden/edge-case requirements?
7. What happens when something fails?
8. How many users are we expecting?
9. What are the performance requirements?
10. What are the availability requirements?
11. What are the security/compliance requirements?
12. How might the system evolve?
```

### Golden rule

> **Don't design the system until you understand the problem you're designing it for.**

```text
Bad HLD approach:

Requirement
    ↓
Kafka + Redis + PostgreSQL + Microservices
    ↓
Try to fit the requirement into the architecture


Good HLD approach:

Requirement
    ↓
Clarify
    ↓
Scope
    ↓
Scale + Constraints
    ↓
Failure scenarios
    ↓
Design
    ↓
Technology choices
```
