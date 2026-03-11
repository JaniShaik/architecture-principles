# Scalability

A system that works reliably today may not continue to work reliably in the future. As systems grow, they face increasing demands from users, data volume, and request traffic.

**Scalability** describes a system’s ability to handle increased load without degrading performance.

However, scalability is not a simple binary label like “this system scales” or “this system doesn’t scale.”  
Instead, scalability is about answering questions such as:

- What happens if our user base grows 10×?
- How will our system behave if traffic doubles?
- How can we add resources to handle additional demand?

Understanding scalability requires analyzing **how load affects system behavior** and how architecture evolves as that load increases.

---

# Understanding System Load

Before designing for scalability, we must first understand **how load is applied to the system**.

Load is typically described using **load parameters**, such as:

- Requests per second (RPS)
- Read-to-write ratios
- Number of concurrent users
- Data volume
- Cache hit rate

Different systems use different parameters depending on their architecture.

For example:

| System Type | Possible Load Parameter |
|-------------|-------------------------|
| Web API | Requests per second |
| Database | Read vs write ratio |
| Chat system | Concurrent users |
| Caching layer | Cache hit rate |

Once these parameters are known, engineers can reason about how the system behaves when those numbers grow.

---

# Case Study: Twitter Timeline Architecture

A well-known example of scalability challenges is **Twitter’s timeline system**.

Two primary operations dominate Twitter’s workload:

### 1. Posting a Tweet

When a user posts a tweet, it must be delivered to their followers.

Typical workload:

- ~4,600 tweets per second on average
- ~12,000 tweets per second at peak

### 2. Reading the Home Timeline

Users frequently open their home timeline to read tweets from people they follow.

Typical workload:

- ~300,000 timeline reads per second

This means Twitter is a **read-heavy system**, where read traffic is significantly higher than write traffic.

However, the major challenge is not just traffic volume—it is **fan-out**.

---

# The Fan-Out Problem

Each tweet must appear in the timelines of all followers.

If a user has 75 followers, one tweet must be delivered to 75 timelines.

But Twitter users have highly uneven follower counts:

- Typical users → tens of followers
- Influencers → thousands
- Celebrities → millions

A single tweet from a celebrity could require **millions of timeline updates**.

This creates a significant scalability challenge.

---

# Approach 1: Fan-Out on Read

In the first design, tweets were stored in a global collection.

When a user requested their timeline, the system would:

1. Retrieve the list of people the user follows
2. Fetch tweets from those users
3. Merge and sort them by timestamp

Example query:

```sql
SELECT tweets.*
FROM tweets
JOIN follows ON tweets.user_id = follows.followee_id
WHERE follows.follower_id = current_user
ORDER BY created_at DESC;
```

### Advantages

- Writing a tweet is simple
- Minimal work during write operations

### Disadvantages

- Timeline reads become expensive
- Queries become slower as follow relationships grow

At large scale, this approach struggles to handle heavy read traffic.

---

# Approach 2: Fan-Out on Write

To improve read performance, Twitter switched to **fan-out on write**.

When a tweet is posted:

1. The system identifies all followers
2. The tweet is inserted into each follower’s timeline cache

This means timelines are **precomputed**.

### Result

Timeline reads become extremely fast because the work was already done when the tweet was posted.

### Trade-off

The cost shifts to the write path.

For example:

- ~4,600 tweets per second
- average of ~75 followers

This results in roughly **345,000 timeline updates per second**.

---

# The Celebrity Problem

Fan-out on write works well for most users.

However, some accounts have **millions of followers**.

If a celebrity posts a tweet:

- pushing that tweet to millions of timelines instantly becomes extremely expensive.

This leads to scalability problems on the write path.

---

# Hybrid Fan-Out Architecture

To address this issue, Twitter adopted a **hybrid architecture**.

### Normal Users

Use **fan-out on write**.

Tweets are pushed to follower timelines when they are posted.

### Celebrity Accounts

Use **fan-out on read**.

Instead of pushing tweets to millions of timelines:

- The tweet is stored in the author’s timeline
- Followers retrieve those tweets during timeline reads

This hybrid approach balances system load and improves performance.

---

# Measuring System Performance

Once load is defined, engineers must measure how system performance changes as load increases.

Two key metrics are used.

---

## Throughput

Throughput measures how many operations the system can process per second.

Examples:

- requests per second
- messages processed per second

---

## Response Time

Response time measures how long a request takes from client request to response.

Response time includes:

- service processing time
- network delays
- queueing delays

Response times vary across requests, so they should be measured as **distributions rather than averages**.

---

# Percentiles and Tail Latency

Instead of averages, engineers measure response times using **percentiles**.

Common percentiles include:

| Percentile | Meaning |
|------|------|
| p50 | median response time |
| p95 | 95% of requests are faster |
| p99 | 99% of requests are faster |

Higher percentiles represent **tail latency**.

Tail latency is important because slow outliers directly affect user experience.

If an application requires multiple backend calls, the slowest call determines the overall response time.

This effect is known as **tail latency amplification**.

---

# Scaling Strategies

Systems typically scale using two main approaches.

---

## Vertical Scaling (Scale Up)

Increase the capacity of a single machine.

Examples:

- Adding more CPU
- Increasing memory
- Upgrading storage

Advantages:

- Simpler architecture

Limitations:

- Hardware limits
- Expensive scaling

---

## Horizontal Scaling (Scale Out)

Distribute workload across multiple machines.

This is often called **shared-nothing architecture**.

Advantages:

- Higher capacity
- Better fault tolerance
- Elastic scaling

Challenges:

- Distributed system complexity
- Data partitioning
- Coordination between nodes

Most large-scale systems combine both approaches.

---

# Architectural Lessons

Several lessons emerge from scalable system design.

### Architecture must follow workload

Understanding system load patterns is essential before designing scaling strategies.

### Systems evolve as they scale

Architectures that work for small systems often fail at larger scales.

### No universal scalable architecture exists

Different applications require different scaling strategies depending on their workloads.

### Distributed systems rely on common building blocks

Large-scale systems are typically built from components such as:

- Caches
- Load balancers
- Distributed databases
- Message queues

Combining these components effectively enables scalable architectures.

---

# Conclusion

Scalability is not simply about handling more traffic.

It is about understanding how systems behave under increased load and designing architectures that adapt to that growth.

The Twitter timeline example demonstrates how system architecture evolves based on real-world usage patterns.

Scalable systems are built by:

- Understanding load characteristics
- Measuring performance correctly
- Designing architectures that evolve with growth
