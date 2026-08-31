# ⚡ Caching & Redis

> **Caching stores frequently accessed data in a faster storage layer so the application can retrieve it more quickly and reduce load on the main database.**

The basic idea:

```text
🐢 Slow / Expensive Storage
        ↓
   🗄️ Database
        ↓
   💾 Cache
        ↓
⚡ Fast / Frequently Used Data
```

---

# 🧠 Why Do We Need Cache?

Imagine 1 million users repeatedly asking:

> **"Show me my home feed."**

Without caching:

```text
👥 Users
   │
   ├────────→ 🗄️ Database
   ├────────→ 🗄️ Database
   ├────────→ 🗄️ Database
   ├────────→ 🗄️ Database
   └────────→ 🗄️ Database

              💥 Heavy DB Load
```

With caching:

```text
👥 Users
   │
   ▼
🖥️ Application Server
   │
   ▼
⚡ Redis
   │
   ▼
📦 Cached Data
```

The database doesn't have to handle every repeated request.

---

# 🏗️ Basic Architecture

```text
             👤 USER
                │
                │ Request
                ▼
       🖥️ APPLICATION SERVER
                │
                │ Check cache
                ▼
          ⚡ REDIS CACHE
             (RAM)
           /         \
          /           \
    ✅ HIT             ❌ MISS
      │                   │
      │                   ▼
      │              🗄️ DATABASE
      │                   │
      │                   │ Fetch data
      │                   ▼
      │              ⚡ REDIS
      │                   │
      │                   │ Store data
      │                   ▼
      └───────────────→ 🖥️ APP SERVER
                          │
                          │ Response
                          ▼
                       👤 USER
```

---

# ⚡ Redis

**Redis** is an in-memory data store commonly used as a cache.

> **In-memory = data is primarily stored in RAM, making access extremely fast.**

```text
⚡ REDIS
   │
   └── RAM
       │
       ├── User Feeds
       ├── Sessions
       ├── Trending Topics
       └── API Responses
```

### Why is Redis fast?

RAM is much faster to access than persistent disk storage.

Conceptually:

```text
⚡ RAM
   ↓
Very fast

        vs

💾 Disk
   ↓
Slower but persistent
```

---

# 🔄 Cache HIT vs Cache MISS

These two terms are **essential** in system design.

## ✅ Cache HIT

The requested data is already in the cache.

```text
User
 ↓
Application Server
 ↓
Redis
 ↓
✅ DATA FOUND
 ↓
Application Server
 ↓
User
```

### Result

```text
⚡ Fast
📉 Less database load
```

---

# ❌ Cache MISS

The requested data isn't in the cache.

```text
User
 ↓
Application Server
 ↓
Redis
 ↓
❌ DATA NOT FOUND
 ↓
Database
 ↓
Get Data
 ↓
Redis
 ↓
Store Data
 ↓
Application Server
 ↓
User
```

The next request can potentially become a **cache hit**.

---

# 📱 Example — Instagram Home Feed

Imagine:

> 👤 User → **"Show my Home Feed"**

### First request

```text
👤 User
   │
   ▼
🖥️ App Server
   │
   ▼
⚡ Redis
   │
   ❌ Cache MISS
   │
   ▼
🗄️ Database
   │
   │ Get feed
   ▼
⚡ Redis
   │
   │ Store feed
   ▼
🖥️ App Server
   │
   ▼
👤 User
```

### Next request

```text
👤 User
   │
   ▼
🖥️ App Server
   │
   ▼
⚡ Redis
   │
   ✅ Cache HIT
   │
   ▼
🖥️ App Server
   │
   ▼
👤 User
```

The second request can avoid going to the database.

---

# 🗄️ Database vs Cache

This distinction is **very important**.

### Database

```text
🗄️ DATABASE
     │
     ├── Persistent
     ├── Source of truth
     └── Stores important data
```

### Cache

```text
⚡ CACHE
    │
    ├── Fast
    ├── Usually temporary
    └── Stores copies of frequently used data
```

Think:

> **Database = truth**

> **Cache = fast copy**

If the cache disappears, the application should generally be able to retrieve the necessary data again from its underlying source.

---

# 🧩 What Can Be Cached?

Not everything should automatically be cached.

Good candidates are usually:

* Frequently requested data
* Expensive-to-compute data
* Data that doesn't change every second
* Data where slightly stale results are acceptable

Examples:

```text
👤 User Feed
🔥 Trending Topics
🖼️ Images
🎬 Video metadata
📊 API Responses
🔐 Sessions
🌐 DNS records
```

---

# 🌍 Different Cache Layers

Caching can happen at **many different levels**.

| Cache Layer        | What is Cached?                  | Example                    |
| ------------------ | -------------------------------- | -------------------------- |
| 💻 User Machine    | Images, CSS, JS, app data        | Browser cache              |
| 🔀 Forward Proxy   | Frequently requested web content | Company/school proxy       |
| 🌐 CDN             | Static/global content            | Videos, thumbnails, images |
| 🔄 Reverse Proxy   | HTTP responses                   | NGINX / Cloudflare         |
| ⚡ Redis            | Application data                 | Feeds, sessions, rankings  |
| 🗄️ Database Cache | Query/index-related data         | DB buffer pool             |
| 🌐 DNS Cache       | Domain → IP                      | `google.com → IP`          |

---

# 🌐 Cache Layers Together

A modern application can have **multiple caching layers**:

```text
👤 USER
  │
  ▼
💻 Browser/App Cache
  │
  ▼
🌐 CDN Cache
  │
  ▼
🔄 Reverse Proxy
  │
  ▼
⚖️ Load Balancer
  │
  ▼
🖥️ Application Server
  │
  ▼
⚡ Redis
  │
  ▼
🗄️ Database
```

This is an important system-design idea:

> **Caching isn't one technology. It's a strategy that can exist at different layers.**

---

# 🧠 Cache Hierarchy

Think of it as progressively moving toward the source of truth:

```text
        ⚡ FASTEST
           │
           ▼
💻 Browser/App Cache
           │
           ▼
🌐 CDN Cache
           │
           ▼
🔄 Reverse Proxy Cache
           │
           ▼
⚡ Redis
           │
           ▼
🗄️ Database
           │
           ▼
        🐢 SLOWER
```

**This isn't a universal speed ranking**—actual performance depends on implementation, network distance, workload, and the specific database/cache—but it is a useful conceptual model.

---

# ⏳ The Problem: Cached Data Can Become Old

Suppose Redis contains:

```text
User balance = $100
```

But the database now says:

```text
User balance = $50
```

The cache is **stale**.

Therefore, caching introduces an important system-design problem:

> **How do we keep cached data sufficiently fresh?**

This leads to concepts such as:

### TTL — Time To Live

Give cached data an expiration time.

```text
User Feed
   │
   ▼
Redis
   │
   │ TTL = 60 seconds
   ▼
Expires
```

After the TTL expires, the application can fetch fresh data again.

---

# 🔥 Common Cache Strategies

You will encounter these later in system design:

### Cache-Aside

Application checks cache first.

```text
App
 │
 ▼
Cache ── HIT ──→ Return
 │
 MISS
 ↓
Database
 ↓
Cache
```

This is one of the most common patterns for application caching.

### Write-Through

```text
App
 │
 ▼
Cache
 │
 ▼
Database
```

Data is written to the cache and underlying storage as part of the write process.

### Write-Behind / Write-Back

```text
App
 │
 ▼
Cache
 │
 │ later
 ▼
Database
```

Writes can be acknowledged from the cache first and persisted later, depending on the architecture.

---

# 🎯 The Most Important Things to Remember

For your system-design notes, focus on these:

```text
1️⃣ CACHE
   ↓
Stores frequently accessed data for faster retrieval.

2️⃣ REDIS
   ↓
Popular in-memory data store used for caching.

3️⃣ CACHE HIT
   ↓
Data found in cache → fast response.

4️⃣ CACHE MISS
   ↓
Data not found → fetch from underlying storage.

5️⃣ DATABASE
   ↓
Usually the persistent source of truth.

6️⃣ TTL
   ↓
Controls how long cached data remains valid.

7️⃣ MULTIPLE CACHE LAYERS
   ↓
Browser → CDN → Proxy → Redis → Database
```

---

# 🧠 One Mental Model

Whenever you see:

```text
👤 User
   ↓
🖥️ Application
```

ask:

> **"Does this request happen frequently enough that I should avoid going to the database every time?"**

If yes, you might introduce:

```text
🖥️ Application
       │
       ▼
   ⚡ Cache
    /     \
   HIT    MISS
   │        │
   │        ▼
   │     🗄️ DB
   │        │
   │        ▼
   └────→ ⚡ Cache
```

That is the **core caching concept** you need before moving into more advanced system design.
