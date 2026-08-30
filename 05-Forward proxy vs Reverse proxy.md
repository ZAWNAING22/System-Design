# 🔀 Proxy vs Reverse Proxy

> The easiest way to understand the difference:
>
> **Forward Proxy → represents the CLIENT**
>
> **Reverse Proxy → represents the SERVER**

---

# 🌐 What is a Proxy?

A **proxy server** is an intermediary between two parties.

Instead of communicating directly:

```text
Client ─────────────→ Server
```

the communication goes through a proxy:

```text
Client ─────→ Proxy ─────→ Server
```

The key question is:

> **Whose side is the proxy acting on behalf of?**

---

# 1️⃣ Forward Proxy

## 👤 Works on Behalf of the Client

A **forward proxy** sits between clients and the internet.

It represents the **client**.

```text
              INTERNAL / PRIVATE NETWORK
              
     👤 Client 1
          │
     👤 Client 2
          │
     👤 Client N
          │
          ▼
     🔀 FORWARD PROXY
       "I represent
        the clients"
          │
          ▼
       🌐 INTERNET
          │
     ┌────┼─────┐
     ▼    ▼     ▼
   Google YouTube Websites
```

The internet sees the **proxy**, rather than directly seeing the individual clients.

### Simple flow

```text
👤 User
   │
   ▼
🔀 Forward Proxy
   │
   ▼
🌐 Internet
   │
   ▼
📺 YouTube
```

---

## 🎯 Forward Proxy Use Cases

### 1. 🌐 Web Filtering

An organization can control which websites employees can access.

```text
Employee
   ↓
Forward Proxy
   ↓
"Is this website allowed?"
   ↓
✅ Allow / ❌ Block
```

For example:

```text
Company Network
      │
      ▼
 Forward Proxy
      │
      ├──→ Google ✅
      ├──→ YouTube ✅
      └──→ Blocked Website ❌
```

---

### 2. 🚫 Content Blocking

The proxy can block specific:

* Websites
* Domains
* IP addresses
* Content categories

---

### 3. 🔐 Privacy

A proxy can make the destination see the **proxy's network identity** rather than the client's direct connection.

> Note: A normal proxy is **not automatically anonymous or secure**. Privacy depends on how it is configured and what information is forwarded.

---

### 4. 💾 Caching

A forward proxy can cache commonly requested resources.

```text
User 1 ──┐
         │
User 2 ──┼──→ Proxy Cache
         │        │
User 3 ──┘        ▼
               🌐 Internet
```

If the content is already cached:

```text
User → Proxy → Cache HIT → User
```

No request to the internet is necessary.

---

### 5. 🔑 Access Control

Organizations can control:

> **Who can access what?**

---

# 🧠 Forward Proxy Mental Model

Think:

> **"I am a user, but I don't want to go directly to the internet. Go there for me."**

```text
👤 CLIENT
    │
    │ "Go to Google for me"
    ▼
🔀 FORWARD PROXY
    │
    ▼
🌐 INTERNET
```

### Remember:

```text
CLIENT → PROXY → INTERNET
```

---

# 2️⃣ Reverse Proxy

## 🖥️ Works on Behalf of the Server

A **reverse proxy** sits between the internet and your backend servers.

It represents the **server**.

```text
                🌐 INTERNET
                     │
              👤 User 1
              👤 User 2
              👤 User N
                     │
                     ▼
             🔀 REVERSE PROXY
               "I represent
                the servers"
                     │
             ┌───────┼───────┐
             ▼       ▼       ▼
          🖥️ Web 1 🖥️ Web 2 🖥️ Web N
```

The users communicate with the reverse proxy rather than directly with the backend servers.

---

# 🎯 Reverse Proxy Use Cases

## 1. ⚖️ Load Balancing

The reverse proxy can distribute requests across multiple servers.

```text
                    🌐 Users
                       │
                       ▼
                🔀 Reverse Proxy
                  /      |      \
                 ▼       ▼       ▼
              🖥️ S1   🖥️ S2   🖥️ S3
```

For example, **NGINX** can act as both:

> **Reverse Proxy + Load Balancer**

---

# 2. 🔐 SSL/TLS Termination

The reverse proxy can handle HTTPS encryption.

```text
🌐 User
   │
   │ HTTPS
   ▼
🔀 Reverse Proxy
   │
   │ HTTP / HTTPS
   ▼
🖥️ Backend
```

Instead of configuring TLS certificates separately on every backend server, the reverse proxy can handle the TLS connection.

---

# 3. 💾 Caching

A reverse proxy can cache responses.

```text
User
 ↓
Reverse Proxy
 ↓
Cache
```

If the response is cached:

```text
User
 ↓
Reverse Proxy
 ↓
💾 Cache HIT
 ↓
Response
```

The backend doesn't need to process every request.

---

# 4. 🛡️ Security

The reverse proxy can act as a protective layer.

```text
🌐 Internet
     │
     ▼
🔀 Reverse Proxy
     │
     │ Filter / inspect
     ▼
🔒 Private Network
     │
     ▼
🖥️ Backend Servers
```

The backend servers don't need to be directly exposed to the public internet.

---

# 5. 🛡️ DDoS Protection

A reverse-proxy layer can help absorb, filter, rate-limit, or distribute malicious traffic.

Large systems often put additional infrastructure in front of the reverse proxy as well.

---

# 🏗️ Typical Reverse Proxy Architecture

```text
                         🌐 INTERNET
                              │
                    ┌─────────┴─────────┐
                    │                   │
                 👤 User 1           👤 User N
                    │                   │
                    └─────────┬─────────┘
                              ▼
                    🔀 REVERSE PROXY
                         (NGINX)
                              │
                  ┌───────────┼───────────┐
                  ▼           ▼           ▼
               🖥️ Web 1    🖥️ Web 2    🖥️ Web N
                  │           │           │
                  └───────────┼───────────┘
                              ▼
                         🗄️ Database
```

---

# 🆚 Forward Proxy vs Reverse Proxy

|                    | 🔀 Forward Proxy       | 🔄 Reverse Proxy          |
| ------------------ | ---------------------- | ------------------------- |
| Represents         | 👤 Client              | 🖥️ Server                |
| Sits in front of   | Clients                | Servers                   |
| Hides              | Clients                | Servers                   |
| Main direction     | Client → Internet      | Internet → Server         |
| Web filtering      | ✅                      | Sometimes                 |
| Content blocking   | ✅                      | Sometimes                 |
| Client privacy     | ✅                      | ❌ Main purpose isn't this |
| Load balancing     | ❌ Usually              | ✅                         |
| SSL termination    | ❌ Usually              | ✅                         |
| Backend protection | ❌                      | ✅                         |
| Server hiding      | ❌                      | ✅                         |
| Caching            | ✅                      | ✅                         |
| Common examples    | Squid, corporate proxy | NGINX, HAProxy            |

---

# 🧠 The Easiest Way to Remember

### Forward Proxy

**The CLIENT says:**

> "Proxy, go to the internet for me."

```text
👤 CLIENT
    ↓
🔀 FORWARD PROXY
    ↓
🌐 INTERNET
```

### Reverse Proxy

**The SERVER says:**

> "Proxy, receive requests for me."

```text
🌐 INTERNET
    ↓
🔀 REVERSE PROXY
    ↓
🖥️ SERVER
```

---

# 🔥 Reverse Proxy vs Load Balancer

This is important for your system-design learning.

They're **not exactly the same thing**.

A **reverse proxy** is an intermediary representing your backend.

A **load balancer** distributes traffic across backend servers.

But one piece of software can do **both**.

For example:

```text
🌐 Internet
      │
      ▼
🔀 NGINX
   │      │
   │      └── Reverse Proxy
   │
   └──────── Load Balancer
      │
 ┌────┼────┐
 ▼    ▼    ▼
S1   S2   S3
```

So:

> **Reverse Proxy = role**

> **Load Balancer = function**

NGINX can perform both roles.

---

# 🎯 Where This Fits in Your System Design Notes

You can connect the topics you've learned so far:

```text
                         🌍 USERS
                            │
                            ▼
                           DNS
                            │
                            ▼
                       🌐 CDN
                            │
                            ▼
                    🔀 Reverse Proxy
                            │
                            ▼
                     ⚖️ Load Balancer
                            │
              ┌─────────────┼─────────────┐
              ▼             ▼             ▼
           🖥️ API 1      🖥️ API 2      🖥️ API 3
              │             │             │
              └─────────────┼─────────────┘
                            ▼
                         💾 Cache
                            │
                            ▼
                        🗄️ Database
```

This is exactly why you're learning these topics together: **DNS, CDN, proxy, reverse proxy, load balancer, cache, and database are pieces of the larger system-design puzzle.**
