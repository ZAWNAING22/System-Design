# CAP Theorem

The **CAP Theorem** states that in any distributed database/system, you can guarantee only **TWO** out of these **THREE** properties at the same time.

```
                      Consistency (C)
                           ▲
                          / \
                         /   \
                        /  X  \   <-- CA is NOT possible
                       /       \      in distributed systems!
                      /         \
   Availability (A)  ◄───────────►  Partition Tolerance (P)
                          ▲
                          │
                   CP or AP Systems

```

> **Crucial Rule:** In real-world distributed networks, network failures are inevitable—making **Partition Tolerance (P)** non-negotiable. Therefore, the architectural choice always comes down to **CP (Consistency + Partition Tolerance)** vs. **AP (Availability + Partition Tolerance)**.

---

### The Three Pillars

* **1. Consistency (C):** Every server should show the **SAME** latest data at any given moment.
* **2. Availability (A):** Every request should receive **SOME** response (non-error), even during system failures.
* **3. Partition Tolerance (P):** The system continues working even if the **network breaks** between servers.

---

### CP vs. AP: Architectural Trade-offs

| Characteristic | CP (Consistency + Partition Tolerance) | AP (Availability + Partition Tolerance) |
| --- | --- | --- |
| **Network Split Behavior** | Blocks/rejects requests until data synchronizes. | Returns stale/cached data rather than failing. |
| **Primary Guarantee** | Absolute data correctness over uptime. | Maximum uptime and low latency over freshness. |
| **Failure Mode** | Error responses or timeouts during partitions. | Eventual consistency (data syncs later). |

---

### When to Use Which?

**Choose CP (Consistency + Partition Tolerance) when:**

* **Financial accuracy is critical:** Account balances, money transfers, and payments cannot afford dirty reads or double spending.
* **Inventory & Booking:** Airline seats, hotel rooms, or concert ticketing where selling the same resource twice causes business failure.
* **Authorization & Auth Tokens:** Password resets, security credentials, and identity verification.

**Choose AP (Availability + Partition Tolerance) when:**

* **Social feeds & Messaging:** Users seeing a post 2 seconds late is preferable to an outage or error screen.
* **Streaming & Media catalogs:** Video recommendations, search autocomplete, or views/likes counters.
* **IoT & Telemetry:** Sensor data aggregation where individual data-point drops don't break the application.

---

### Real Company & System Preferences

| Company / System | Likely Preference | Trade-off Rationale |
| --- | --- | --- |
| **Banks** | **CP** | Cannot risk displaying incorrect balances or double spending. |
| **WhatsApp** | **AP** | Messages deliver eventually; sending and reading must never fail completely. |
| **Instagram** | **AP** | A delay in showing new likes or comments is better than feed downtime. |
| **Netflix** | **AP** | Streaming playback and catalog browsing must always stay up, even with stale metadata. |
| **ATM Systems** | **CP** | Hardware must decline transactions if it cannot verify the central balance. |
