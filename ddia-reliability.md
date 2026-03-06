# Reliability in Distributed Systems

Reliability is one of the foundational principles of system architecture.

At scale, systems must continue operating correctly even when components fail. Hardware crashes, software bugs, and human mistakes are inevitable in complex distributed systems. Reliable architectures anticipate these faults and ensure that they do not escalate into full system failures.

In practical terms, reliability means **continuing to work correctly even when things go wrong**.

---

## Faults vs Failures

In system design it is important to distinguish between **faults** and **failures**.

**Fault**
A fault occurs when a component deviates from its expected behavior.

Examples:
- A disk becomes corrupted
- A service returns incorrect responses
- A network link drops packets

**Failure**
A failure occurs when the system as a whole stops providing the expected service to users.

Reliable systems are designed so that **faults do not escalate into failures**.

Since it is impossible to eliminate faults entirely, architecture focuses on **fault tolerance**.

---

## Designing Fault-Tolerant Systems

Fault tolerance allows systems to continue operating despite component failures.

Instead of relying on perfectly reliable hardware, modern distributed systems assume that failures will occur and design mechanisms to recover automatically.

Examples of fault tolerance strategies include:

- replication across multiple machines
- automated failover
- retry mechanisms
- process isolation
- rolling upgrades

Some organizations even deliberately inject failures to test resilience. Netflix’s Chaos Monkey is a well-known example of this practice.

---

## Hardware Faults

Hardware failures are a common source of system disruptions.

Typical examples include:

- disk crashes
- memory corruption
- network cable failures
- power outages

In large datacenters these failures occur regularly.

For example, in a cluster with thousands of disks, disk failures may occur daily.

Traditional approaches relied heavily on hardware redundancy such as:

- RAID storage
- redundant power supplies
- backup generators

Modern distributed systems increasingly rely on **software-based fault tolerance**, allowing entire machines to fail without affecting system availability.

Examples include:

- distributed storage systems
- replicated databases
- multi-node clusters

This approach also enables operational improvements such as **rolling upgrades**, where machines can be updated one at a time without downtime.

---

## Software Errors

Software faults are often more dangerous than hardware faults.

Unlike hardware failures, software bugs tend to be **systematic and correlated**. A single bug can affect every instance of a service simultaneously.

Examples include:

- application crashes triggered by unexpected input
- runaway processes consuming shared resources
- dependency services returning invalid responses
- cascading failures across services

These failures may remain dormant for long periods before being triggered by unusual conditions.

Mitigation strategies include:

- defensive programming
- process isolation
- automatic service restarts
- comprehensive monitoring
- extensive testing
- production observability

Reliable systems continuously verify assumptions and alert operators when anomalies occur.

---

## Human Errors

Human mistakes are one of the leading causes of outages in production systems.

Common examples include:

- misconfigured infrastructure
- accidental data deletion
- incorrect deployment procedures
- operational missteps

Research into large-scale internet services shows that configuration errors frequently cause more outages than hardware faults.

Designing systems that account for human error is therefore essential.

Key strategies include:

### Safer Interfaces

Administrative tools and APIs should encourage correct actions and reduce the likelihood of mistakes.

### Sandbox Environments

Non-production environments allow engineers to experiment safely without affecting real users.

### Automated Testing

Automated testing helps detect errors before deployment.

### Gradual Rollouts

Releasing changes incrementally ensures that failures affect only a small portion of users.

### Observability

Detailed monitoring and telemetry provide early warning signals when systems behave unexpectedly.

### Rapid Recovery

Systems should support quick rollback mechanisms and tools to recompute data if errors occur.

---

## Why Reliability Matters

Reliability is not limited to mission-critical systems such as aviation or nuclear control software.

Everyday applications also depend on reliable infrastructure.

Failures in modern applications can lead to:

- lost revenue
- reduced productivity
- damaged reputation
- regulatory risk

Users also trust systems with valuable data such as personal photos, financial information, and business records.

When reliability fails, that trust is broken.

---

## Architectural Perspective

Reliable systems are not built by eliminating failures.

They are built by **anticipating failures and designing systems that tolerate them**.

Key architectural principles include:

- isolate failures
- limit blast radius
- replicate critical components
- design for graceful degradation
- enable fast recovery

Infrastructure provides resources.

Architecture provides resilience.

---

## Key Takeaways

- Faults are inevitable in distributed systems.
- Reliable systems prevent faults from becoming failures.
- Hardware failures are common at scale and must be expected.
- Software errors can propagate across entire systems.
- Human mistakes are a major source of outages.
- Resilient architecture anticipates failure and enables recovery.
