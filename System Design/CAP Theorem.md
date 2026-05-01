# CAP Theorem

The CAP theorem defines three desirable properties of a distributed system with replicated data (where data is stored across multiple nodes, such as servers in India and the USA). The core principle is that a distributed system can only provide **two of these three properties simultaneously**; it is impossible to achieve all three at once.

---

## The Three Properties of CAP

### 1. Consistency (C)

- **Definition:** After a successful write to any node, any subsequent read from any other node in the system should return that same data.
- **Example:** If you update a value from 4 to 5 on Node B, a user reading from Node C must immediately see the value as 5.

### 2. Availability (A)

- **Definition:** Every request received by a non-failing node in the system must result in a response.
- **Requirement:** All nodes should respond to queries, even if they cannot guarantee the data is the most recent (success or failure response).

### 3. Partition Tolerance (P)

- **Definition:** The system continues to operate and fulfill requests even if there is a communication break (network partition) between nodes.
- **Context:** In a distributed system, a partition means Node B and Node C can no longer talk to each other, but the overall system remains "up" for the user.

---

## Why You Can't Have All Three (The Trade-offs)

In a distributed system, network partitions (P) are often inevitable. When a partition occurs, you must choose between Consistency and Availability:

### CP (Consistency + Partition Tolerance)

- To maintain Consistency during a partition, the system must ensure all nodes have the same data.
- If a node cannot communicate with others to sync an update, the system must shut down that node or refuse requests to it to prevent "stale" data.
- **Result:** You lose **Availability** because some nodes are no longer responding to requests.

### AP (Availability + Partition Tolerance)

- To maintain Availability, every node remains active and accepts reads/writes even if they cannot communicate with each other.
- Because nodes cannot sync during the partition, Node B might have the new data while Node C still has the old data.
- **Result:** You lose **Consistency**.

### CA (Consistency + Availability)

- This combination assumes there are no network partitions.
- If a partition occurs, the system is not partition-tolerant and will likely crash or fail to fulfill requests.

> **Note:** In modern distributed architectures, "P" is usually a requirement, so the choice is almost always between **CP** and **AP**.

---

## Summary Table

| Combination | Priority | Trade-off |
|---|---|---|
| **CP** | Data Accuracy | System may become unavailable during network issues. |
| **AP** | System Uptime | Data may be temporarily inconsistent across nodes. |
| **CA** | Accuracy & Uptime | System cannot handle network communication failures. |
