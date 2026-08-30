

# 🌍 CDN — Content Delivery Network

> **A CDN (Content Delivery Network) is a globally distributed network of servers that stores cached copies of content closer to users, allowing content to be delivered faster and reducing load on the origin server.**

### 🎯 Main Goal

```text
Bring content
     ↓
Closer to the user
     ↓
Lower latency ⚡
     ↓
Faster delivery 🚀
```

---

# 🧩 CDN Components

A CDN mainly consists of **three important components**:

### 1. 🏠 Origin Server

The **origin server** is the main source of the content.

It contains the actual data:

```text
🏠 Origin Server
      │
      ├── Images
      ├── Videos
      ├── CSS / JS
      ├── HTML
      └── Other files
```

Think:

> **Origin = Source of truth**

---

### 2. 🌐 Edge Server

An **edge server** is a CDN server located geographically closer to users.

It stores cached copies of content from the origin.

```text
                 🏠 Origin
                    │
        ┌───────────┼───────────┐
        ▼           ▼           ▼
     🌐 Edge      🌐 Edge      🌐 Edge
     Mumbai       Dubai       Singapore
        │           │             │
        ▼           ▼             ▼
     🇮🇳 Users     🇦🇪 Users     🇸🇬 Users
```

Edge servers are often located at **PoPs (Points of Presence)**.

---

### 3. 💾 Cache

The **cache** is where the edge server stores frequently requested content.

For example:

```text
🌐 Edge Server — Mumbai

💾 Cache
├── logo.png
├── movie-poster.jpg
├── app.js
└── video.mp4
```

Instead of repeatedly requesting these files from the origin, the CDN can serve them directly from the edge.

---

# 🌍 CDN Architecture

Imagine a company has one origin server and users around the world:

```text
                         🏠 ORIGIN SERVER
                         Main Data Center
                                │
                ┌───────────────┼────────────────┐
                │               │                │
                ▼               ▼                ▼
          🌐 EDGE SERVER   🌐 EDGE SERVER   🌐 EDGE SERVER
             Mumbai           Dubai          Singapore
                │               │                │
                ▼               ▼                ▼
              🇮🇳 Users       🇦🇪 Users        🇸🇬 Users
```

And globally:

```text
                         🏠 ORIGIN
                            │
          ┌─────────────────┼─────────────────┐
          │                 │                 │
          ▼                 ▼                 ▼
      🇮🇳 Mumbai         🇦🇪 Dubai        🇸🇬 Singapore
      🌐 Edge            🌐 Edge           🌐 Edge
          │                 │                 │
          ▼                 ▼                 ▼
      🇮🇳 Users          🇦🇪 Users         🇸🇬 Users

                            │
                            ▼
                       🇺🇸 New York
                       🌐 Edge
                            │
                            ▼
                       🇺🇸 Users
```

---

# 🚀 Why Do We Need a CDN?

Without a CDN:

```text
🇮🇳 User ───────────────────────→ 🏠 Origin
                                  │
                                  │ Long distance
                                  ▼
                              ⏱️ Higher latency
```

With a CDN:

```text
🇮🇳 User
   │
   │ Short distance
   ▼
🌐 Mumbai Edge
   │
   ▼
⚡ Fast response
```

### CDN provides:

* ⚡ Lower latency
* 🚀 Faster content delivery
* 📉 Reduced origin-server load
* 🌍 Global content distribution
* 📈 Better scalability
* 🛡️ Some protection against traffic attacks

---

# 🔄 HOW CDN WORKS

Let's say a user opens a website and requests:

```text
https://example.com/movie.mp4
```

The basic flow is:

```text
👤 User
   │
   │ 1. Request content
   ▼
🌐 DNS
   │
   │ 2. Find appropriate CDN
   ▼
🌐 CDN Edge Server
   │
   │ 3. Check cache
   ▼
💾 Cache
```

Now there are **two possible situations**.

---

# ✅ Case 1 — Cache HIT

The content is already stored on the edge server.

```text
👤 User
   │
   ▼
🌐 CDN Edge
   │
   ▼
💾 Cache
   │
   │ ✅ CACHE HIT
   ▼
📦 Content
   │
   ▼
👤 User
```

The CDN **doesn't need to contact the origin**.

### Result

```text
⚡ Fast response
📉 Low latency
📉 Less origin traffic
```

---

# ❌ Case 2 — Cache MISS

The content isn't currently stored on the edge server.

```text
👤 User
   │
   ▼
🌐 CDN Edge
   │
   ▼
💾 Cache
   │
   │ ❌ CACHE MISS
   ▼
🏠 Origin Server
   │
   │ Send content
   ▼
🌐 CDN Edge
   │
   ├── 💾 Store in cache
   │
   ▼
👤 User
```

The edge server:

1. Requests content from the origin
2. Receives the content
3. Stores it in its cache
4. Sends it to the user

The **next user** requesting the same cacheable content can get it directly from the edge.

---

# 🧠 Complete CDN Flow

```text
                 👤 USER
                    │
                    │ ① Request content
                    ▼
                  🌐 DNS
                    │
                    │ ② Route to CDN
                    ▼
             🌐 CDN EDGE / PoP
                    │
                    │ ③ Check cache
                    ▼
              💾 EDGE CACHE
               /           \
              /             \
             ▼               ▼
      ✅ CACHE HIT      ❌ CACHE MISS
             │               │
             │               ▼
             │          🏠 ORIGIN
             │               │
             │               │ ④ Fetch content
             │               ▼
             │          🌐 CDN EDGE
             │               │
             │               │ ⑤ Store cache
             │               ▼
             └──────────→ 👤 USER
                         ⑥ Deliver
```

---

# 🌎 CDN + Load Balancer

A CDN and a load balancer solve **different problems**.

### CDN

> **"Can I serve this content closer to the user?"**

### Load Balancer

> **"Which backend server should handle this request?"**

They can work together:

```text
                         🌍 USERS
                            │
                            ▼
                           DNS
                            │
                            ▼
                       🌐 CDN / Edge
                       /            \
                      /              \
                Cache HIT          Cache MISS
                   │                   │
                   ▼                   ▼
                 👤 User         ⚖️ Load Balancer
                                       │
                              ┌────────┼────────┐
                              ▼        ▼        ▼
                             🖥️       🖥️       🖥️
                           Server   Server   Server
                              │        │        │
                              └────────┼────────┘
                                       ▼
                                  🗄️ Database
```

### Important

A CDN is **not a replacement for a load balancer**.

They often work together.

---

# 🎬 Real-World Example — Video Streaming

Imagine you're watching a video from Türkiye.

Without CDN:

```text
🇹🇷 You
  │
  │
  │ Long-distance request
  ▼
🌎 Origin Server
  │
  ▼
🎬 Video
```

With CDN:

```text
🇹🇷 You
  │
  ▼
🌐 Nearby CDN Edge
  │
  ▼
💾 Cached Video
  │
  ▼
🎬 Play Video
```

If the video isn't cached:

```text
🇹🇷 You
   │
   ▼
🌐 CDN Edge
   │
   │ Cache MISS
   ▼
🏠 Origin
   │
   ▼
🌐 CDN Edge
   │
   │ Cache video
   ▼
🇹🇷 You
```

Now subsequent users near that edge location can potentially receive the cached content without every request traveling back to the origin.

---

# 🗺️ CDN — Mental Model

Remember it like this:

```text
                 🏠 ORIGIN
              Source of Truth
                    │
                    │
        ┌───────────┼───────────┐
        ▼           ▼           ▼
     🌐 Edge      🌐 Edge      🌐 Edge
     Mumbai       Dubai       New York
        │           │             │
        ▼           ▼             ▼
       🇮🇳         🇦🇪           🇺🇸
      Users       Users         Users
```

### The key idea:

> **Origin = Where the original content lives**

> **Edge = Where copies are placed closer to users**

> **Cache = The stored copy**

> **CDN = The global network connecting these edge locations**

---

# 🧠 CDN vs Load Balancer

|                                               | 🌐 CDN                 | ⚖️ Load Balancer            |
| --------------------------------------------- | ---------------------- | --------------------------- |
| Main purpose                                  | Deliver content faster | Distribute requests         |
| Main location                                 | Near users             | In front of backend servers |
| Caches content                                | ✅ Yes                  | ❌ Usually no                |
| Distributes traffic                           | Sometimes              | ✅ Yes                       |
| Reduces latency                               | ✅                      | Sometimes                   |
| Protects origin from repeated static requests | ✅                      | ❌                           |
| Routes to backend servers                     | Sometimes              | ✅                           |
| Common with web apps                          | ✅                      | ✅                           |

### Easy memory trick

```text
CDN
 ↓
"WHERE should content be served from?"
 ↓
🌍 CLOSE TO USER


Load Balancer
 ↓
"WHICH server should handle this request?"
 ↓
🖥️ BACKEND SERVER
```

---

# 🎯 What You Should Know for System Design

You don't need to memorize every CDN implementation.

Make sure you understand these **5 things**:

```text
1️⃣ Origin Server
       ↓
2️⃣ Edge Server / PoP
       ↓
3️⃣ Cache
       ↓
4️⃣ Cache HIT vs MISS
       ↓
5️⃣ CDN reduces latency + origin load
```

And remember the overall relationship:

```text
👤 User
   ↓
🌐 DNS
   ↓
🌍 CDN
   ↓
⚖️ Load Balancer
   ↓
🖥️ Backend Servers
   ↓
💾 Cache / 🗄️ Database
```

