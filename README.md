# System Design Interview Study Guide (Archived — Consolidated into `coding_practice`)

> **Moved & Archived:** All content from this repository has been consolidated into **[`jinloes/coding_practice`](https://github.com/jinloes/coding_practice)**:
> - **[`system-design/`](https://github.com/jinloes/coding_practice/tree/master/system-design)** — the migrated pattern catalog (`docs/patterns/`), worked post-attempt case studies (`docs/case-studies/`), runnable JDK-only reference implementations (`ratelimit`, `partitioning`, `resilience`, `outbox`, `coordination`, `saga`), and the PostgreSQL/Debezium/Kafka/Elasticsearch Change Data Capture lab (`labs/change-data-capture/`).
> - **[`system-design-plugin/`](https://github.com/jinloes/coding_practice/tree/master/system-design-plugin)** — the standalone JetBrains IDE plugin for practicing system design interviews with embedded Excalidraw diagrams, `notes.md`, concept guides, and level-calibrated Copilot CLI feedback.

---

A pattern-first guide for designing scalable, reliable systems in technical
interviews. The goal is not to memorize one architecture per prompt. It is to
recognize a system pressure, choose an appropriate pattern, and explain the
trade-off you are accepting.

## Start here

| Step | Resource | Outcome |
| --- | --- | --- |
| 1 | [Foundations](study-guide/foundations.md) | Learn the vocabulary and calculations used in every design. |
| 2 | [Interview playbook](study-guide/interview-playbook.md) | Practice a repeatable 45-minute design process. |
| 3 | [Pattern catalog](study-guide/patterns/README.md) | Learn when to use each common pattern and when not to. |
| 4 | [Case study track](study-guide/case-studies/README.md) | Work through eight realistic interview prompts of increasing difficulty. |
| 5 | [Practice drills](study-guide/practice.md) and [prompt deck](study-guide/case-studies/practice-prompts.md) | Apply the material under interview constraints. |
| 6 | [Design template](study-guide/template.md) | Run a mock interview without missing a major section. |

The [change data capture lab](change-data-capture/README.md) is the repository's
hands-on example. It propagates PostgreSQL changes through Kafka into an
Elasticsearch read model.

## Core pattern map

| System pressure | Patterns to consider | Trade-off to say out loud |
| --- | --- | --- |
| More traffic than one host can serve | Load balancing, horizontal scaling, CDN | More capacity adds coordination and operational cost. |
| Repeated expensive reads | Caching, materialized views | Faster reads introduce staleness and invalidation work. |
| Overload or abusive clients | Rate limiting, load shedding | Protection can reject legitimate work. |
| Read scale or regional availability | Replication | Replicas may lag and failover can lose recent writes. |
| Data no longer fits one node | Partitioning, consistent hashing | Cross-partition operations become harder. |
| Slow work should not block requests | Queues, streams, pub/sub | Asynchrony introduces lag, duplicates, and reordering. |
| Retried requests or events | Idempotency, deduplication | Deduplication requires durable keys and retention. |
| Unreliable dependencies | Timeouts, retries, circuit breakers, bulkheads | Aggressive retries can amplify an outage. |
| Database update plus event publication | Transactional outbox, CDC | Delivery is normally at-least-once, not magically atomic end to end. |
| Transaction spans services | Saga | Compensation is business logic, not a database rollback. |
| Read and write needs diverge | CQRS, projections | More models mean more synchronization and debugging work. |
| Feed or notification fan-out | Fan-out on write, read, or hybrid | Cheap reads cost writes; cheap writes cost reads. |
| Exactly one active coordinator | Leader election, leases, fencing | A lease alone does not prevent a stale leader from writing. |

## How to study

For each pattern, be able to answer four questions without notes:

1. What pressure makes this pattern useful?
2. How does data move through it?
3. What new failure mode does it create?
4. What simpler option would you try first?

A productive session is one pattern card, one hand-drawn diagram, and one
10-minute design drill. Revisit the same prompt with different constraints,
such as stronger consistency, a 10x traffic increase, or a regional outage.

## Repository layout

```text
.
|-- README.md
|-- study-guide/
|   |-- foundations.md
|   |-- interview-playbook.md
|   |-- patterns/
|   |-- case-studies/
|   |-- practice.md
|   `-- template.md
`-- change-data-capture/      # Docker-based CDC example
```
