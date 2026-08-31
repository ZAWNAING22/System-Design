## Web Application Architecture

```mermaid
flowchart LR
    User([👤 Users])

    subgraph Client ["Frontend"]
        UI["🖥️ What User Sees & Interacts With<br>• HTML<br>• CSS<br>• JavaScript"]
    end

    subgraph Server ["Backend"]
        WS["⚙️ Web Server<br><b>App Logic:</b> PHP, JS, Python, Java"]
        FS[("📁 File System<br>HTML, CSS, Images")]
        DB[("🗄️ Database<br>MySQL, PostgreSQL, MariaDB")]
        
        WS --> FS
        WS --> DB
    end

    User -- "Collect Data" --> UI
    UI -- "Display Results" --> User
    UI -- "Request (API / REST)" --> WS
    WS -- "Response" --> UI

```

---

## API Architecture Styles Overview

| Protocol / Style | Primary Use Case | Characteristics |
| --- | --- | --- |
| **REST** | Standard web & mobile applications | Resource-based, HTTP methods, widely adopted |
| **GraphQL** | Complex data fetching / single-endpoint queries | Eliminates over/under-fetching, client specifies schema |
| **WebSocket** | Real-time, continuous two-way communication | Persistent full-duplex connection |
| **gRPC** | Microservice-to-microservice communication | High performance, lightweight binary serialization |
| **SOAP** | Legacy & enterprise banking systems | Strict XML-based protocol, built-in security standards |
| **Webhooks** | Asynchronous, event-driven notifications | Event-triggered HTTP callbacks |

---

## 1. REST (Representational State Transfer)

REST models data as resources and manipulates them using standard HTTP methods via specific URL paths.

* **GET** — Read/retrieve data: `GET /restaurants/101/menu`
* **POST** — Create a new resource: `POST /orders`
* **PUT** — Replace an existing resource completely
* **PATCH** — Partially update an existing resource: `PATCH /orders/501`
* **DELETE** — Remove a resource: `DELETE /cart/items/10`

---

## 2. GraphQL

Ideal for loading complex, nested views without making multiple round trips to the server.

```mermaid
flowchart TD
    subgraph REST_Approach ["REST: Multiple Round-trips"]
        R1["GET /users/10"]
        R2["GET /users/10/orders"]
        R3["GET /users/10/rewards"]
        R4["GET /users/10/addresses"]
        R5["GET /users/10/recommendations"]
    end

    subgraph GraphQL_Approach ["GraphQL: Single Request"]
        G1["POST /graphql<br><i>(Custom Query Payload)</i>"]
    end

```

**Single GraphQL Query:**

```graphql
query {
  user(id: 10) {
    name
    rewardPoints
    recentOrders(limit: 3)
    savedAddresses {
      city
    }
    recommendations {
      name
      rating
    }
  }
}

```

---

## 3. gRPC (Remote Procedure Calls)

Optimized for high-throughput, low-latency communication between internal microservices.

```mermaid
flowchart LR
    Order["Order Service"]
    Payment["Payment Service"]
    Restaurant["Restaurant Service"]
    Delivery["Delivery Service"]
    Notification["Notification Service"]

    Order -->|gRPC| Payment
    Order -->|gRPC| Restaurant
    Restaurant -->|gRPC| Delivery
    Delivery -->|gRPC| Notification

```

---

## 4. WebSocket

Replaces repetitive HTTP polling with an open, bidirectional connection for live data feeds (e.g., driver tracking).

```mermaid
sequenceDiagram
    autonumber
    actor Client as Client App
    participant Server as Tracking Server

    Note over Client,Server: Initial HTTP Handshake
    Client->>Server: GET /chat HTTP/1.1<br/>Upgrade: websocket<br/>Connection: Upgrade<br/>Sec-WebSocket-Key: dGhlIHNhbXBsZ==
    Server-->>Client: HTTP/1.1 101 Switching Protocols<br/>Upgrade: websocket<br/>Connection: Upgrade

    Note over Client,Server: Persistent Bi-Directional Stream
    Server-->>Client: Live location update #1
    Server-->>Client: Live location update #2
    Server-->>Client: Live location update #3

```

---

## 5. Webhooks

An event-driven HTTP callback where the server notifies an external receiver as soon as an event occurs.

```mermaid
sequenceDiagram
    autonumber
    participant AppA as Application A (Sender)
    participant AppB as Application B (Receiver URL)

    Note over AppA: Event Triggers (e.g., Payment Succeeded)
    AppA->>AppB: POST /webhook/payment
    Note over AppB: Processes event payload
    AppB-->>AppA: 200 OK Acknowledgement

```

**Payload Example:**

```http
POST /webhook/payment HTTP/1.1
Host: api.example.com
Content-Type: application/json
X-Webhook-Signature: a1b2c3d4
X-Webhook-Timestamp: 1720000000

{
  "event": "payment.success",
  "orderId": 1001,
  "amount": 50000
}

```

---

## 6. SOAP (Simple Object Access Protocol)

A strict, contract-driven XML protocol standard in legacy enterprise and banking infrastructures.

```xml
<?xml version="1.0"?>
<soap:Envelope xmlns:soap="http://www.w3.org/2003/05/soap-envelope/">
  <soap:Body>
    <GetBalance>
      <AccountNumber>123456789</AccountNumber>
    </GetBalance>
  </soap:Body>
</soap:Envelope>

```
