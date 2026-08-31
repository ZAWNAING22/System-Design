# 🏛️ System Design Foundation & Architecture Handbook

A comprehensive, production-oriented repository covering core fundamentals, distributed systems patterns, database internals, and real-world high-level system designs.

---

## 🗺️ High-Level Request Flow Architecture

```mermaid
flowchart TD
    Client(["📱 / 💻 Client"])

    subgraph Edge ["Edge Layer"]
        DNS["🌐 Route 53 / DNS"]
        CDN["⚡ CDN (Static Assets / Edge Cache)"]
    end

    subgraph Ingress ["Ingress & Gateway"]
        LB["⚖️ Load Balancer (ALB / NGINX)"]
        GW["🚪 API Gateway (Auth, Rate Limiting, Routing)"]
    end

    subgraph Services ["Application Layer"]
        App1["⚙️ Microservice A"]
        App2["⚙️ Microservice B"]
    end

    subgraph CacheLayer ["Caching Layer"]
        Cache[("⚡ Redis / Memcached (TTL, Invalidation)")]
    end

    subgraph AsyncLayer ["Asynchronous Messaging"]
        MQ["📬 Message Queue (Apache Kafka / RabbitMQ)"]
        Worker["⚙️ Background Workers"]
    end

    subgraph Persistence ["Persistence Layer"]
        PrimaryDB[("🗄️ Primary DB (Write Master)")]
        ReplicaDB[("🗄️ Read Replicas (Sharded / Replicated)")]
    end

    Client -->|1. Resolve IP| DNS
    Client -->|2. Request Static Content| CDN
    Client -->|3. API Request HTTPS| LB
    LB --> GW
    GW --> App1
    GW --> App2

    App1 <-->|Read-Through / Cache-Aside| Cache
    App1 -->|Write / Transact| PrimaryDB
    App1 -->|Read Query| ReplicaDB
    PrimaryDB -.->|Replication| ReplicaDB

    App2 -->|Produce Events| MQ
    MQ --> Worker
    Worker --> PrimaryDB
---
## 📚 Core Pillars
---
