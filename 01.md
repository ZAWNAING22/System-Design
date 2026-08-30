# DNS & Request Flow — What Happens When You Open an App

## 🌐 Big Picture

When you open an app like **Hotstar**, the app needs to communicate with a backend server. Before it can connect, it needs to know **where that server is**.

That is where **DNS (Domain Name System)** comes in.

> **DNS = converts a domain name → IP address**

For example:

```text
api.hotstar.com
      ↓
142.xxx.xxx.xxx
```

---

# 📱 Step 1 — User Opens the App

You tap the Hotstar app.

```text
👤 User
   │
   │ Tap app
   ▼
📱 App
   │
   ▼
⚙️ Operating System
   │
   ▼
🧠 RAM
```

The OS loads the application into memory so it can run.

---

# 📡 Step 2 — App Sends an API Request

The app needs data from the backend.

For example:

```http
GET /homefeed
```

Conceptually:

```text
📱 Client
   │
   │  GET /homefeed
   ▼
🖥️ Server
```

The problem is:

> **How does the phone know where `api.hotstar.com` is?**

It needs DNS.

---

# 🌍 Step 3 — DNS Lookup

The phone asks a **DNS resolver**:

> "What is the IP address of `api.hotstar.com`?"

```text
📱 Phone
   │
   │ "IP address of api.hotstar.com?"
   ▼
🌐 DNS Resolver
```

The resolver could be provided by:

* Your ISP
* Google DNS
* Cloudflare DNS
* Another DNS provider

---

# 🔎 Step 4 — DNS Finds the IP Address

If the resolver doesn't already know the answer, it follows the DNS hierarchy:

```text
             🌐 DNS Resolver
                    │
                    ▼
             🌳 Root DNS Server
                    │
                    ▼
               .com TLD Server
                    │
                    ▼
          Authoritative DNS Server
                    │
                    ▼
           api.hotstar.com
                    │
                    ▼
             142.xxx.xxx.xxx
```

### The hierarchy

```text
Root
 │
 ├── .com
 │
 │    └── hotstar.com
 │
 │          └── api.hotstar.com
 │
 └── other TLDs
```

---

# 🧩 What Each DNS Server Does

### ① Root Server 🌳

The root doesn't normally give you the final IP.

It tells the resolver:

> "For `.com`, ask the `.com` TLD servers."

```text
Resolver
   │
   │ api.hotstar.com?
   ▼
Root
   │
   └──→ Ask .com TLD
```

There are **13 logical root server identities**, operated using many physical servers around the world.

---

### ② TLD Server

TLD = **Top-Level Domain**

For:

```text
api.hotstar.com
```

The TLD is:

```text
.com
```

The `.com` server tells the resolver:

> "Ask Hotstar's authoritative DNS server."

```text
.com TLD
    │
    └──→ Authoritative DNS
```

---

### ③ Authoritative DNS Server

This is the server that has the actual DNS records for the domain.

It can return something like:

```text
api.hotstar.com
        ↓
142.xxx.xxx.xxx
```

---

# 🔄 Complete DNS Resolution

Put everything together:

```text
📱 Phone
   │
   │ "What is the IP of api.hotstar.com?"
   ▼
🌐 Recursive Resolver
   │
   │
   ▼
🌳 Root DNS
   │
   │ "Ask .com"
   ▼
🔵 .com TLD
   │
   │ "Ask Hotstar's authoritative DNS"
   ▼
🏛️ Authoritative DNS
   │
   │ "142.xxx.xxx.xxx"
   ▼
🌐 Recursive Resolver
   │
   │ Returns IP
   ▼
📱 Phone
```

---

# 🚀 Step 5 — Phone Connects to the Server

Now the phone knows the IP.

```text
api.hotstar.com
       ↓
142.xxx.xxx.xxx
```

The app can now connect.

But there is an important detail:

**The IP may belong to a Load Balancer rather than directly to one application server.**

So the simplified architecture becomes:

```text
📱 User
   │
   ▼
📱 App
   │
   │ API Request
   ▼
🌐 DNS
   │
   │ IP address
   ▼
⚖️ Load Balancer
   │
   ├──────────────┐
   ▼              ▼
🖥️ Server 1    🖥️ Server 2
   │              │
   └──────┬───────┘
          ▼
       🗄️ Database
```

---

# 🎯 Complete Flow to Remember

```text
👤 USER
   │
   │ Opens app
   ▼
📱 APP
   │
   │ API Request
   ▼
🌐 DNS RESOLUTION
   │
   ▼
🔎 Recursive Resolver
   │
   ▼
🌳 Root DNS
   │
   ▼
🔵 TLD (.com)
   │
   ▼
🏛️ Authoritative DNS
   │
   │ Returns IP
   ▼
📱 APP
   │
   │ Connects to IP
   ▼
⚖️ LOAD BALANCER
   │
   ├─────────────┐
   ▼             ▼
🖥️ Server 1   🖥️ Server 2
   │             │
   └──────┬──────┘
          ▼
       🗄️ DATABASE
```

## 🧠 The One-Sentence Version

> **DNS translates a human-readable domain name into an IP address, allowing the client to find the server it needs to communicate with.**

### Remember this hierarchy:

```text
Recursive Resolver
       ↓
     Root
       ↓
      TLD
       ↓
Authoritative
       ↓
   IP Address
```

**Important:** In real life, the resolver often already has the answer in its **cache**, so it may not need to contact Root → TLD → Authoritative every time.
