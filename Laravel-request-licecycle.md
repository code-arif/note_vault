# Laravel Request Lifecycle — Super Summary

### Think of Laravel like a pipeline.

```
User Request
     │
     ▼
public/index.php
     │
     ▼
Bootstrap App (bootstrap/app.php)
     │
     ▼
HTTP Kernel
     │
     ▼
Global Middleware
     │
     ▼
Service Providers Boot
     │
     ▼
Router
     │
     ▼
Route Middleware
     │
     ▼
Controller / Route Logic
     │
     ▼
Response Created
     │
     ▼
Middleware (Outgoing)
     │
     ▼
Response Sent to Browser
```
---

# Graphical Concept (Easy Brain Map)

```
Browser
   │
   ▼
Request
   │
   ▼
┌─────────────────────┐
│ public/index.php    │
│ Entry Point         │
└─────────────────────┘
           │
           ▼
┌─────────────────────┐
│ Bootstrap App       │
│ bootstrap/app.php   │
│ Create App Container│
└─────────────────────┘
           │
           ▼
┌─────────────────────┐
│ HTTP Kernel         │
│ (App Engine)        │
└─────────────────────┘
           │
           ▼
┌─────────────────────┐
│ Global Middleware   │
│ Auth, CSRF, etc     │
└─────────────────────┘
           │
           ▼
┌─────────────────────┐
│ Service Providers   │
│ Load DB, Queue,     │
│ Routes, Cache       │
└─────────────────────┘
           │
           ▼
┌─────────────────────┐
│ Router              │
│ Find matching route │
└─────────────────────┘
           │
           ▼
┌─────────────────────┐
│ Route Middleware    │
│ Auth, Role check    │
└─────────────────────┘
           │
           ▼
┌─────────────────────┐
│ Controller / Route  │
│ Business Logic      │
└─────────────────────┘
           │
           ▼
        Response
           │
           ▼
        Browser
```

---
## Ultra-Short Developer Memory Trick
```
Request
→ index.php
→ Kernel
→ Middleware
→ Service Providers
→ Router
→ Controller
→ Response