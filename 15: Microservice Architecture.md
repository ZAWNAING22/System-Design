**Microservices Architecture (Running on Kubernetes)**

A modern quick-commerce system architecture (inspired by platforms like Blinkit) designed around modularity, autonomous scaling, and polyglot persistence.

---

**Architecture Flow Diagram**

```mermaid
flowchart TR
    subgraph Clients["Clients"]
        direction TB
        C1["📱 Blinkit Mobile App"]
        C2["🌐 Web App"]
        C3["⌚ Partner / Rider App"]
        C4["🏪 Dark Store App"]
    end

    subgraph Gateway["API Gateway (Kong)"]
        direction TB
        GW["Kong Gateway<br/>• Authentication<br/>• Rate Limiting<br/>• Routing<br/>• Security<br/>• Logging"]
    end

    subgraph K8s["Microservices (Running on Kubernetes)"]
        direction TB
        subgraph Row1["Core Commerce"]
            S_User["User Service"]
            S_Cat["Product Catalog"]
            S_Search["Search Service"]
            S_Inv["Inventory Service"]
            S_Cart["Cart Service"]
        end
        subgraph Row2["Operations & Orders"]
            S_Price["Pricing & Discount"]
            S_Order["Order Service"]
            S_Pay["Payment Service"]
            S_Store["Store (Dark Store)"]
            S_Deliv["Delivery Service"]
        end
        subgraph Row3["Support & Platform"]
            S_Loc["Location / Map"]
            S_Notif["Notification"]
            S_Rev["Review & Rating"]
            S_CS["Customer Support"]
            S_Analytics["Analytics Service"]
        end
    end

    subgraph DBs["Databases (Per Service / Polyglot Persistence)"]
        direction LR
        DB_User[("User DB<br/>(PostgreSQL)")]
        DB_Cat[("Product DB<br/>(MongoDB)")]
        DB_Inv[("Inventory DB<br/>(MongoDB)")]
        DB_Order[("Order DB<br/>(PostgreSQL)")]
        DB_Pay[("Payment DB<br/>(PostgreSQL)")]
        DB_Cache[("Cache<br/>(Redis)")]
        DB_OLAP[("Analytics DB<br/>(ClickHouse)")]
    end

    Clients --> Gateway
    Gateway --> K8s
    K8s --> DBs

```

---

**Core Components Breakdown**

* **Client Tier:** Multiple front-facing touchpoints (Customer Apps, Web, Delivery Partner Apps, Dark Store Manager Apps) consume backend capabilities through a unified entry layer.
* **API Gateway (Kong):** Sits between clients and microservices to handle cross-cutting concerns:
* *Authentication & Security:* Token validation, TLS termination, and request authorization.
* *Traffic Management:* Rate limiting, IP whitelisting, and dynamic routing to target microservices.
* *Observability:* Centralized audit logging, request metrics, and tracing headers.


* **Kubernetes (K8s) Cluster:** Orchestrates stateless microservices across self-healing containers, providing auto-scaling, load balancing, and rolling zero-downtime updates.
* **Polyglot Persistence ("Database per Service"):**
* *Relational (PostgreSQL):* Guarantees strict ACID transactions for Users, Orders, and Payments.
* *Document (MongoDB):* Handles polymorphic, schema-free data like Product Catalogs and fast-changing Inventories.
* *In-Memory (Redis):* Ultra-fast sub-millisecond caching for shopping carts, rate limits, and sessions.
* *Columnar OLAP (ClickHouse):* High-throughput, real-time telemetry and supply-chain event analysis.



---

**When to Use This Pattern**

* **Large, Cross-Functional Teams:** When multiple engineering squads need to deploy, test, and release features independently without blocking each other.
* **Mixed Scaling Profiles:** When specific features experience wildly different traffic patterns (e.g., search queries outnumber actual order checkouts 100:1, so Search needs to scale without wasting resources on the Payment service).
* **Diverse Data Requirements:** When transactional guarantees (ACID) are strictly required for money/orders, but flexible documents or analytical columnar stores fit catalog searches and event logs better.
* **Zero-Downtime Mission-Critical Operations:** When downtime directly burns revenue and isolated fault tolerance is non-negotiable (e.g., if the Review service crashes, users can still check out).

---

**Real-World Example**

* **10-Minute Delivery Surge:** During an evening rush, a quick-commerce user searches for items and adds groceries to their basket.
* **Search & Product Catalog Services** read heavily from **Redis** and **MongoDB** caches to handle tens of thousands of read requests per second.
* Once the checkout button is tapped, the **Kong Gateway** routes traffic to the **Order & Payment Services**, which lock inventory and persist payment state inside **PostgreSQL** with strict transactional consistency.
* The **Dark Store & Delivery Services** immediately receive notifications, allocating the order to in-store pickers and routing the delivery rider via the **Location/Map Service**.
* Throughout this, the **Analytics Service** streams ride and batch data into **ClickHouse** to calculate delivery SLAs in real time.
