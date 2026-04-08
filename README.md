# Project: Biashara Hub
# Author: Nyangabi Derrick Mburu
# Date: 2026
# Description: Biashara Hub is a premium e-commerce platform designed to provide a seamless and luxurious shopping experience. The interface is crafted to be elegant and user-friendly, with a focus on high-quality visuals and intuitive navigation. Key features include a dynamic hero section, advanced search functionality, comprehensive product filtering, and an engaging dashboard for sellers. Biashara Hub aims to connect discerning customers with exclusive products while empowering sellers with powerful tools to grow their businesses.

# Biashara_Hub-DSA-CH23
An algorithmic approach to e-commerce management. Biashara-Hub implements advanced data structures and optimized algorithms to handle real-world business logic and data flow efficiently.

# 🛒 Biashara Hub v2

> **Premium African E-Commerce Platform powered by Data Structures & Algorithms**
> *Where Commerce Meets Intelligence — Built with O(log n) efficiency in Nairobi, Kenya.*

![Version](https://img.shields.io/badge/version-2.0.0-gold)
![Stack](https://img.shields.io/badge/stack-HTML5%20%7C%20CSS3%20%7C%20JavaScript%20ES6%2B-blue)
![License](https://img.shields.io/badge/license-MIT-green)
![Zero Dependencies](https://img.shields.io/badge/dependencies-zero-brightgreen)
![Lines of Code](https://img.shields.io/badge/lines%20of%20code-2856-orange)

---

## Table of Contents

1. [Overview](#overview)
2. [What Changed in v2](#what-changed-in-v2)
3. [Step 1 — Use Cases Generation](#step-1--use-cases-generation)
4. [Step 2 — Constraints and Analysis](#step-2--constraints-and-analysis)
5. [Step 3 — Basic Design](#step-3--basic-design)
6. [Step 4 — Bottlenecks](#step-4--bottlenecks)
7. [Step 5 — Scalability](#step-5--scalability)
8. [Technology Stack](#technology-stack)
9. [Features](#features)
10. [How to Run](#how-to-run)
11. [Application Flow](#application-flow)
12. [DSA Reference Table](#dsa-reference-table)
13. [File Structure](#file-structure)
14. [Coupon Codes](#coupon-codes)
15. [Console DSA Map](#console-dsa-map)
16. [Scalability Roadmap](#scalability-roadmap)

---

## Overview

**Biashara Hub v2** is a fully self-contained, single-file e-commerce web application built to demonstrate how advanced Data Structures and Algorithms (DSA) solve real-world commerce problems. Every feature — from product search to delivery routing — is backed by a named, commented DSA implementation.

The application runs entirely in the browser with **zero external JavaScript dependencies**. There is no server, no build step, and no internet connection required beyond loading the Google Fonts stylesheet. All product data, user accounts, orders, and business logic live in-memory using hand-rolled DSA classes.

**v2** upgrades include a full authentication system with role-based access control, a Stack-backed cart undo feature, a Client Management page (admin-only), an interactive DSA Benchmark page with live timing, Quick Sort and Heap Sort additions, and a complete Inventory Audit Log.

---

## What Changed in v2

| Feature | v1 (initial) | v2 (updated) |
|---|---|---|
| Authentication | None — instant access | Login screen with Shopper / Admin / Vendor roles |
| Cart Undo | Not present | Stack-backed undo (O(1) push/pop) |
| Client Management | Not present | Admin-only page — KMP search, Merge Sort, toggle status |
| DSA Benchmark Page | Not present | Live timing of 10+ algorithms, sort comparison chart |
| Inventory Audit | Not present | Stack of add/edit actions with timestamp |
| Stack DSA | Not implemented | Full Stack class (1h) used in cart undo + inventory |
| Quick Sort | Not implemented | Added as benchmark comparison algorithm |
| Heap Sort | Not implemented | Added as benchmark comparison algorithm |
| badge-info | Not present | New CSS utility badge class |
| btn-success / btn-danger | Not present | New button variant CSS classes |
| Input / Select / Textarea | Not standardized | Unified `.input`, `.select`, `.textarea` design tokens |
| Navigation | No role display | Admin badge shown next to user name |
| Data Model | Products only | Adds `userDB` (HashMap of User objects) |

---

---

# Step 1 — Use Cases Generation

This section identifies all actors in the system and maps every application feature to a named use case with the DSA that powers it.

## 1.1 Actor Identification

| Actor | Role |
|---|---|
| **Shopper** | A buyer who browses, searches, wishlists, carts, and places orders |
| **Vendor** | A seller managing product listings, inventory, and reorder alerts |
| **Admin** | Platform manager with full dashboard, analytics, client, and inventory access |
| **System** | Background automated processes — recommendations, reorder alerts, delivery ETA |

## 1.2 Functional Use Cases

### UC-01 — Product Browsing & Search

**Actor:** Shopper
**Description:** The user types a partial product name into the search bar and receives real-time autocomplete suggestions. On confirmation, full-text search returns ranked results.

**DSA Used:**
- **Trie** — autocomplete in O(L + k) where L = prefix length, k = results returned
- **KMP (Knuth-Morris-Pratt)** — substring matching across name, description, and tags in O(n + m)
- **BM25-inspired relevance score** — results ranked by weighted relevance

**Flow:**
1. Shopper types `"head"` → Trie traverses to node `'d'` → BFS collects completions → `"Headphones"`, `"Headband"` etc. shown as dropdown suggestions.
2. On full search submission, KMP scans each product's concatenated text field for exact substring matches.
3. Matched results are ranked by a BM25-inspired popularity + rating score.

---

### UC-02 — Product Filtering & Sorting

**Actor:** Shopper
**Description:** User narrows results by category, price range, rating, and availability, then sorts by price, rating, or popularity.

**DSA Used:**
- **BST (Binary Search Tree)** — range-price queries in O(log n + k)
- **Merge Sort** — stable O(n log n) sort with a pluggable comparator
- **Binary Search** — O(log n) lower/upper bound on sorted price array

**Flow:**
1. Category filter → BST in-order traversal returns products in the selected category.
2. Price slider → Binary search on the sorted-by-price array locates the lower and upper bounds.
3. Sort dropdown → Merge Sort applied to the filtered result set using the chosen comparator.

---

### UC-03 — Cart Management

**Actor:** Shopper
**Description:** Add, remove, and update quantity of items; apply coupon codes; undo the last action; view running total.

**DSA Used:**
- **HashMap** — O(1) average for all cart operations (`productId → qty`)
- **Stack** — O(1) push/pop for cart undo history
- **Min-Heap** — priority queue for checkout ordering by vendor fulfilment time

**Flow:**
1. `addToCart(id)` → `cartMap.set(id, qty + 1)` — O(1).
2. Action pushed to `cartUndoStack` — O(1).
3. `undoLastCartAction()` → `cartUndoStack.pop()` → state restored — O(1).
4. `applyCoupon(code)` → `coupons.get(code)` — O(1) HashMap lookup.

---

### UC-04 — Order Placement & Tracking

**Actor:** Shopper
**Description:** Shopper checks out, order is created and tracked through a defined state machine.

**DSA Used:**
- **Doubly Linked List** — O(1) append for order history
- **State Machine** — governs order status transitions
- **Dijkstra's Algorithm** — delivery ETA routing from warehouse to customer zone

**Flow:**
1. `placeOrder()` → `orderHistory.append(order)` — O(1) DLL insertion.
2. Order status state machine: `pending → processing → shipped → out_for_delivery → delivered`.
3. Delivery route graph (Nairobi zones) → Dijkstra computes shortest path from warehouse — O((V+E) log V).

---

### UC-05 — Wishlist Management

**Actor:** Shopper
**Description:** Save and remove products from wishlist with instant feedback.

**DSA Used:** **Hash Set** — O(1) add, delete, contains.

**Flow:**
1. `toggleWishlist(id)` → `Set.has(id) ? Set.delete(id) : Set.add(id)` — all O(1).
2. Wishlist page renders all saved items from the set.

---

### UC-06 — Product Recommendations

**Actor:** System (automated)
**Description:** Suggest related products based on user's interaction history (wishlist + cart).

**DSA Used:**
- **Graph + BFS** — similarity traversal across product graph
- **Cosine Similarity** — collaborative filtering score computation

**Flow:**
1. An undirected product graph is built weighted by Jaccard similarity of tag sets.
2. For each interacted product → BFS depth-2 traversal collects recommendation candidates.
3. If no history → fallback to top-rated products sorted via Merge Sort by rating.

---

### UC-07 — Inventory Management (Vendor/Admin)

**Actor:** Vendor / Admin
**Description:** View, search, sort, add, and edit product inventory; receive reorder alerts; undo inventory edits.

**DSA Used:**
- **Min-Heap** — reorder alert priority queue (lowest stock = highest urgency)
- **Merge Sort** — stable table sorting
- **KMP** — inventory search across SKU and product names
- **Stack** — inventory audit log with O(1) push/pop

**Flow:**
1. Min-Heap built from low-stock products → `peek()` always returns most urgent reorder item — O(log n).
2. Bulk sort via Merge Sort — stable, O(n log n).
3. Add product → HashMap insertion O(1) amortised; Trie insertion O(L); Stack push for audit log.

---

### UC-08 — Analytics Dashboard

**Actor:** Admin
**Description:** View revenue trends, top products, recent orders, and KPIs.

**DSA Used:**
- **Segment Tree** — range revenue queries in O(log n)
- **Max-Heap / Top-K** — bestsellers extraction in O(n log k)
- **Sliding Window** — rolling average in O(n)

**Flow:**
1. Segment Tree built from 7-day revenue array → range queries for any date span.
2. Top-5 products extracted via Min-Heap of size k → O(n log k).
3. Sliding window max over revenue data for trend lines.

---

### UC-09 — Delivery Route Optimization

**Actor:** System
**Description:** Calculate shortest delivery path from warehouse to customer zone.

**DSA Used:** **Dijkstra's Algorithm** on a weighted directed graph of Nairobi delivery zones.

**Flow:**
1. City zones modelled as graph nodes; road distances as edge weights.
2. Dijkstra runs from the `"warehouse"` node → shortest distances to all zones computed.
3. ETA displayed to shopper at checkout based on zone selection.

---

### UC-10 — Product Detail with Caching

**Actor:** Shopper
**Description:** View full product details without redundant computation on repeated visits.

**DSA Used:** **LRU Cache** (HashMap + Doubly Linked List) — O(1) get/put.

**Flow:**
1. On product click → check LRU cache first (O(1)).
2. Cache hit → return immediately, move to front (most recently used).
3. Cache miss → fetch from `productDB`, store in cache, evict LRU tail if capacity (30 items) exceeded.

---

### UC-11 — Client Management (Admin)

**Actor:** Admin
**Description:** View all registered users, search by name or email, sort by spend or order count, toggle account status, and inspect individual order history.

**DSA Used:**
- **KMP** — search across user names and emails
- **Merge Sort** — sort by total spend, order count, or join date
- **HashMap** — `userDB` keyed by email for O(1) user lookup

**Flow:**
1. Admin types search query → KMP scans user names and emails.
2. Sort dropdown → Merge Sort by chosen field.
3. Toggle button → `userDB.get(email).active = !active; userDB.set(email, user)` — O(1).

---

### UC-12 — DSA Benchmark

**Actor:** Admin
**Description:** Run live timing benchmarks on 10+ DSA implementations for varying input sizes (n). Compare Merge Sort, Quick Sort, and Heap Sort side by side.

**DSA Benchmarked:**
HashMap insert/lookup, Merge Sort, Quick Sort, Heap Sort, Binary Search, KMP Search, Min-Heap build + extract, Top-K extraction, Trie insert + autocomplete, Segment Tree build + queries.

**Flow:**
1. Admin sets `n` (default 1,000).
2. Each algorithm runs on randomly generated data.
3. `performance.now()` measures execution in milliseconds.
4. Results rendered as proportional bar charts with complexity labels.

---

---

# Step 2 — Constraints and Analysis

## 2.1 Functional Constraints

| Constraint | Value | Justification |
|---|---|---|
| Product catalog size | ~10,000 SKUs | Typical mid-size African e-commerce |
| Concurrent users | ~500 (client-side) | Single-page app; all logic runs in browser |
| Search latency | < 50ms | Trie autocomplete target |
| Cart operations | O(1) | HashMap-backed cart |
| Order history retrieval | O(n) | DLL traversal; acceptable for small n |
| Recommendation BFS depth | ≤ 2 | Balances quality vs. runtime |
| LRU Cache capacity | 30 items | Evicted when exceeded |
| Revenue query window | 7-day | Segment tree over 7 nodes |
| Authentication users | Seeded in-memory | userDB HashMap; no persistent backend |

## 2.2 Non-Functional Constraints

| Constraint | Requirement |
|---|---|
| **Performance** | All user-facing operations < 100ms on modern hardware |
| **Correctness** | Sort algorithms must be stable — Merge Sort chosen |
| **Memory** | DSA structures must not exceed ~50MB RAM in browser |
| **Maintainability** | Each DSA class is self-contained with single responsibility |
| **Compatibility** | ES6+ JavaScript; no external runtime dependencies |
| **Security** | Role-based access — admin-only routes guarded by `currentUser.role` |

## 2.3 Complexity Analysis Summary

| Operation | Data Structure | Time Complexity | Space Complexity |
|---|---|---|---|
| Product lookup by ID | HashMap | O(1) avg | O(n) |
| User lookup by email | HashMap | O(1) avg | O(u) |
| Search autocomplete | Trie | O(L + k) | O(n·L) |
| Substring search | KMP | O(n + m) | O(m) |
| Price range filter | BST range query | O(log n + k) | O(n) |
| Sort products | Merge Sort | O(n log n) | O(n) |
| Quick Sort (benchmark) | Divide-and-conquer | O(n log n) avg | O(log n) |
| Heap Sort (benchmark) | In-place heapsort | O(n log n) | O(1) extra |
| Cart add/remove | HashMap | O(1) amortised | O(c) |
| Cart undo | Stack | O(1) | O(history) |
| Wishlist add/check | Hash Set | O(1) | O(w) |
| Order append | Doubly Linked List | O(1) | O(o) |
| Product cache get/put | LRU (DLL + HashMap) | O(1) | O(cap) |
| Revenue range query | Segment Tree | O(log n) | O(n) |
| Top-K products | Min-Heap | O(n log k) | O(k) |
| Reorder alerts | Min-Heap | O(n log n) build | O(n) |
| Similar products | Graph BFS | O(V + E) | O(V) |
| Delivery routing | Dijkstra | O((V+E) log V) | O(V) |
| Collaborative filtering | Cosine Similarity | O(n·m) | O(n) |

**Legend:** n = products, u = users, k = results, L = word length, m = pattern length, V = vertices, E = edges, c = cart size, w = wishlist size, o = orders, cap = cache capacity.

## 2.4 Trade-off Decisions

| Decision | Chosen | Alternative | Reason |
|---|---|---|---|
| Sorting algorithm | Merge Sort | Quick Sort | Stable; O(n log n) worst-case guaranteed; ties preserved |
| Hash collision | Chaining | Open Addressing | Simpler; better at high load factors |
| Product graph | Undirected | Directed | Similarity is symmetric: if A~B then B~A |
| Order storage | Doubly Linked List | Array | O(1) insert/delete from any position |
| BST type | Simple BST | AVL/Red-Black | Adequate at current scale; AVL adds rotation complexity |
| Cache eviction | LRU | LFU, FIFO | LRU best matches recency bias of browsing behaviour |
| Auth persistence | In-memory only | localStorage | Keeps the app stateless and self-contained |

---

---

# Step 3 — Basic Design

## 3.1 System Architecture

```
┌───────────────────────────────────────────────────────────────────────┐
│                        BIASHARA HUB v2 SPA                            │
│                                                                       │
│  ┌─────────────┐                                                      │
│  │ Login Screen│ ← Role-based auth (Shopper / Vendor / Admin)        │
│  └──────┬──────┘                                                      │
│         │                                                             │
│  ┌──────▼──────────────────────────────────────────────────────────┐ │
│  │   Navigation Bar (role-aware) + Toast System + Modal Overlay    │ │
│  └──────┬──────────────────────────────────────────────────────────┘ │
│         │                                                             │
│  ┌──────▼──────────────────────────────────────────────────────────┐ │
│  │                     PAGES (SPA routing)                         │ │
│  │  Home │ Shop │ Product │ Cart │ Orders │ Wishlist               │ │
│  │  Dashboard │ Inventory │ Clients (admin) │ Benchmark (admin)    │ │
│  └──────┬──────────────────────────────────────────────────────────┘ │
│         │                                                             │
│  ┌──────▼──────────────────────────────────────────────────────────┐ │
│  │                   STATE MANAGER  (state{})                      │ │
│  │  currentPage │ currentUser │ shopFilters │ shopSort             │ │
│  │  shopPage │ itemsPerPage │ currentProduct │ clientFilter        │ │
│  └──────┬──────────────────────────────────────────────────────────┘ │
│         │                                                             │
│  ┌──────▼──────────────────────────────────────────────────────────┐ │
│  │                        DSA LAYER                                │ │
│  │                                                                 │ │
│  │  Data Structures:                                               │ │
│  │  Trie │ MinHeap │ MaxHeap │ BST │ HashMap │ Stack │ Queue      │ │
│  │  DoublyLinkedList │ LRUCache │ Graph │ SegmentTree              │ │
│  │                                                                 │ │
│  │  Algorithms:                                                    │ │
│  │  MergeSort │ QuickSort │ HeapSort │ BinarySearch │ KMP         │ │
│  │  TopK │ Dijkstra │ BFS │ DFS │ CosineSimilarity │ SlidingWindow│ │
│  └──────┬──────────────────────────────────────────────────────────┘ │
│         │                                                             │
│  ┌──────▼──────────────────────────────────────────────────────────┐ │
│  │                   IN-MEMORY DATA STORE                          │ │
│  │                                                                 │ │
│  │  productDB    → HashMap<id, Product>                            │ │
│  │  userDB       → HashMap<email, User>          [NEW in v2]       │ │
│  │  cartMap      → HashMap<productId, qty>                         │ │
│  │  wishlistSet  → Set<productId>                                  │ │
│  │  coupons      → HashMap<code, discount>                         │ │
│  │  orderHistory → DoublyLinkedList<Order>                         │ │
│  │  detailCache  → LRUCache<productId, Product>                    │ │
│  │  cartUndoStack→ Stack<CartAction>             [NEW in v2]       │ │
│  │  inventoryStack→Stack<InventoryAction>        [NEW in v2]       │ │
│  │  searchTrie   → Trie                                            │ │
│  │  priceBST     → BST                                             │ │
│  │  reorderHeap  → MinHeap                                         │ │
│  │  similarityGraph → Graph                                        │ │
│  └─────────────────────────────────────────────────────────────────┘ │
└───────────────────────────────────────────────────────────────────────┘
```

## 3.2 Page / Module Map

| Page | Access | Purpose | Key DSA |
|---|---|---|---|
| **Login** | Public | Role-based authentication | HashMap (userDB) |
| **Home** | All roles | Hero, featured products, recommendations | Max-Heap Top-K, BFS Graph |
| **Shop** | All roles | Full catalog with search, filter, sort, pagination | Trie, KMP, Merge Sort, BST, Binary Search |
| **Product Detail** | All roles | Full product view, similar items | LRU Cache, BFS Graph |
| **Cart** | Shopper | Cart management, undo, coupon, checkout | HashMap, Stack, Min-Heap, Dijkstra |
| **Orders** | Shopper | Order history, status timeline | Doubly Linked List, State Machine |
| **Wishlist** | Shopper | Saved items | Hash Set |
| **Dashboard** | Admin/Vendor | Revenue, KPIs, top products | Segment Tree, Max-Heap, Sliding Window |
| **Inventory** | Admin/Vendor | CRUD, reorder alerts, audit log | Min-Heap, Merge Sort, KMP, Stack |
| **Clients** | Admin only | Client management, status toggle | HashMap, KMP, Merge Sort |
| **Benchmark** | Admin only | Live DSA timing + sort comparison | All algorithms |

## 3.3 Data Models

```javascript
// Product — primary entity
Product {
  id:            string,    // Primary key (HashMap key)
  name:          string,    // Indexed in Trie
  category:      string,    // BST key for category index
  price:         number,    // BST key for price index
  originalPrice: number,    // Optional — shown with strikethrough
  rating:        number,    // 0-5 float
  reviews:       number,    // Review count
  stock:         number,    // Monitored by Min-Heap reorder alerts
  reorderAt:     number,    // Threshold for reorder alert
  icon:          string,    // Emoji display
  vendor:        string,    // Vendor name (string)
  desc:          string,    // Full-text searched with KMP
  tags:          string[],  // Trie indexed; used for similarity graph
  popularity:    number     // 0-1000 score for Top-K extraction
}

// User — v2 addition
User {
  name:        string,
  email:       string,      // Primary key (HashMap key)
  password:    string,      // Plain text (demo only — no production use)
  role:        'shopper' | 'vendor' | 'admin',
  orderCount:  number,
  totalSpend:  number,      // KES
  joinDate:    string,      // ISO date string
  active:      boolean
}

// Order
Order {
  id:       string,         // Unique order ID
  items:    [{id, qty}],
  status:   'pending'|'processing'|'shipped'|'out_for_delivery'|'delivered',
  date:     string,
  total:    number,         // KES
  customer: string,         // User display name
  email:    string,         // Used to filter orders per client
  zone:     string          // Delivery zone (used for Dijkstra)
}

// CartAction — Stack frame for undo
CartAction {
  action:  'add' | 'remove' | 'update',
  id:      string,          // productId
  prevQty: number,
  newQty:  number,
  name:    string,
  ts:      number           // Unix timestamp ms
}

// InventoryAction — Stack frame for audit log
InventoryAction {
  action:    'add' | 'edit',
  productId: string,
  before:    Product | null,
  after:     Product,
  timestamp: number
}
```

## 3.4 Data Flow Diagrams

### Search Flow
```
User types "sanyo"
      │
      ▼
searchTrie.autocomplete("sanyo") → O(L + k)
      │ suggestions dropdown
      ▼
User confirms full search
      │
      ▼
for each product:
  kmpSearch(product.name + " " + product.desc + " " + tags.join(" "), query)
  → O(n · (m + |text|))
      │ matched products array
      ▼
mergeSort(results, comparator) → O(n log n)
      │ sorted array
      ▼
paginate(results, page=1, perPage=12) → render to DOM
```

### Cart → Checkout → Order Flow
```
addToCart(id)
      │
      ├─→ cartMap.set(id, qty + 1) — O(1)
      └─→ cartUndoStack.push(frame) — O(1)  [v2]
      │
      ▼
showCheckout()
      │
      └─→ dijkstra(deliveryGraph, 'warehouse') — O((V+E) log V)
             └─→ ETA displayed per zone
      │
      ▼
placeOrder()
      │
      ├─→ orderHistory.append(order) — O(1) DLL
      └─→ cartMap.clear() — O(n)
             └─→ toast("Order placed!") — O(1)
```

### Login & Role Routing Flow
```
User submits credentials
      │
      ▼
userDB.get(email) — O(1) HashMap
      │
      ├─ not found → toast error
      └─ found → check password
            │
            ├─ mismatch → toast error
            └─ match → set currentUser
                  │
                  ├─ role === 'admin'   → show all nav links
                  ├─ role === 'vendor'  → show dashboard + inventory
                  └─ role === 'shopper' → show shop + cart + orders
                        │
                        ▼
                  navigate('home') → renderHome()
```

### Recommendation Flow
```
interacted = [...wishlistSet, ...cartMap.keys()]
      │
      ├─ length > 0 →
      │     for each interactedId:
      │       similarityGraph.bfs(id, depth=2) — O(V+E)
      │       → collect non-visited neighbours
      │     → deduplicate → slice(0,4) → render
      │
      └─ length === 0 →
            mergeSort(all products, by rating DESC).slice(0,4) — O(n log n)
            → render top-rated fallback
```

---

---

# Step 4 — Bottlenecks

This section identifies the six primary performance bottlenecks in the current design, explains their complexity impact, and references the corresponding scalability mitigation.

## B1 — KMP Search on Large Catalogs

**Problem:** KMP runs O(n · (m + |text|)) over all products for every keystroke in the search box. For n = 10,000 products with avg text length 200 chars and pattern m = 10 chars:

```
Time ≈ 10,000 × (10 + 200) = 2,100,000 character comparisons per search
```

**Impact:** Potential lag on low-end devices at large catalog sizes.

**Mitigation:** See [Step 5.1 — Scalability for B1](#51-scalability-for-b1--kmp-search-at-scale)

---

## B2 — Similarity Graph Construction O(n²)

**Problem:** Building the product similarity graph requires comparing every pair of products. At scale:

```
Pairs = n(n-1)/2 = 10,000 × 9,999 / 2 ≈ 50,000,000 comparisons at startup
```

**Impact:** Initialization slowdown at startup for large catalogs.

**Mitigation:** See [Step 5.2 — Scalability for B2](#52-scalability-for-b2--similarity-graph-at-scale)

---

## B3 — Merge Sort on Every Filter Change

**Problem:** Every filter or sort interaction re-sorts the entire product array O(n log n). Re-sorting 10,000 items on every slider movement creates redundant work.

**Impact:** Sluggish UI on continuous slider input (price range).

**Mitigation:** See [Step 5.3 — Scalability for B3](#53-scalability-for-b3--sorting-at-scale)

---

## B4 — Segment Tree Limited to Fixed Array Size

**Problem:** The Segment Tree is built once at startup for a 7-day revenue array. Adding new revenue data requires rebuilding the tree in O(n).

**Impact:** Cannot efficiently support dynamic revenue windows (e.g., 30-day or 90-day view).

**Mitigation:** See [Step 5.4 — Scalability for B4](#54-scalability-for-b4--segment-tree-at-scale)

---

## B5 — BST Degeneracy (Unbalanced Tree)

**Problem:** A plain BST inserted in sorted order degenerates to a linked list with O(n) depth. If products are inserted in price order (low → high), the BST has O(n) search time.

**Impact:** Price range queries degrade from O(log n + k) to O(n + k).

**Mitigation:** See [Step 5.5 — Scalability for B5](#55-scalability-for-b5--bst-balance-at-scale)

---

## B6 — HashMap Resize Under Heavy Load

**Problem:** When the HashMap's load factor exceeds 0.75, a full rehash occurs in O(n), blocking all cart and product operations during the resize.

**Impact:** Jank during peak usage when catalog grows dynamically.

**Mitigation:** See [Step 5.6 — Scalability for B6](#56-scalability-for-b6--hashmap-resize-at-scale)

---

---

# Step 5 — Scalability

For each bottleneck identified in Step 4, this section iterates through progressively stronger mitigations until the solution is acceptable for production scale.

## 5.1 Scalability for B1 — KMP Search at Scale

### Iteration 1: Debounce the Search Handler
Wrap the search with a 200ms debounce. This reduces trigger frequency on continuous typing from ~10 events/second to ~5 events/second.
```javascript
let searchTimer;
function onSearchInput(val) {
  clearTimeout(searchTimer);
  searchTimer = setTimeout(() => applyFilters(), 200);
}
```
**Result:** 10× reduction in KMP invocations. Sufficient for n < 2,000.

### Iteration 2: Pre-index Searchable Text in HashMap
At startup, pre-compute a single lowercase string per product containing name + description + tags. Store it in a `searchIndex` HashMap. Each KMP call then reads from this index rather than concatenating strings on every search.
```javascript
// Build once — O(n)
products.forEach(p => {
  searchIndex.set(p.id, (p.name + ' ' + p.desc + ' ' + p.tags.join(' ')).toLowerCase());
});
// Search — O(n · (n + m)) with no runtime string allocation
```
**Result:** Zero string allocation during search; improves cache locality. Sufficient for n < 10,000.

### Iteration 3 (Production): Inverted Index or Typesense
Build an inverted index at startup: a HashMap where each unique word maps to a list of product IDs that contain it. Query decomposition + ID intersection replaces full-text KMP.

At full production scale, replace with a dedicated search engine such as **Typesense** or **Meilisearch**:
- Sub-millisecond full-text search at any catalog size.
- Typo tolerance, faceting, and ranking built in.
- Separates search load from the main application server.

**Result:** Sub-10ms search latency at n > 1,000,000 products.

---

## 5.2 Scalability for B2 — Similarity Graph at Scale

### Iteration 1: Build Graph Lazily (On-Demand)
Instead of computing all O(n²) edges at startup, compute edges only when a product detail page is visited:
```javascript
function getSimilarProducts(productId) {
  if (!graphCache.has(productId)) {
    const edges = computeEdgesForProduct(productId); // O(n · |tags|)
    graphCache.set(productId, edges);
  }
  return graphCache.get(productId);
}
```
**Result:** O(n · |tags|) per product view instead of O(n²) at startup. Suitable for n < 50,000.

### Iteration 2: Locality-Sensitive Hashing (LSH)
Group products into buckets by shared tag prefixes. Only compute similarity within each bucket:
```javascript
// Bucket products by their most common tag prefix
const buckets = {};
products.forEach(p => {
  const key = p.tags.slice(0, 2).sort().join('|');
  (buckets[key] = buckets[key] || []).push(p);
});
// Only compare within buckets — reduces O(n²) to O(n · avg_bucket_size²)
```
**Result:** Graph construction time reduced from O(n²) to O(n · k²) where k = average bucket size. Suitable for n < 500,000.

### Iteration 3 (Production): Approximate Nearest Neighbours (ANN)
Replace tag-based Jaccard with product embedding vectors. Use an ANN library (FAISS, Annoy, HNSW) to find the top-k most similar products in O(log n):
- Embed products using TF-IDF on their description and tags.
- Index embeddings at startup.
- Query at O(log n) per product page load.
- Rebuild nightly as catalog changes.

**Result:** Sub-millisecond similarity lookup at n > 1,000,000 products.

---

## 5.3 Scalability for B3 — Sorting at Scale

### Iteration 1: Debounce the Price Slider
Apply a 150ms debounce to price slider events so Merge Sort only triggers after the user stops moving the slider:
```javascript
let priceTimer;
function filterByPrice(val) {
  clearTimeout(priceTimer);
  priceTimer = setTimeout(() => { state.shopFilters.maxPrice = val; applyFilters(); }, 150);
}
```
**Result:** Merge Sort triggers ≤ 7 times per second instead of on every pixel of slider movement.

### Iteration 2: Sort Once, Filter with Binary Search
Maintain a permanently sorted-by-price array. Apply price filter using binary search rather than re-sorting on every price change:
```javascript
const sortedByPrice = mergeSort(productDB.values(), (a, b) => a.price - b.price); // Once O(n log n)

function filterByPrice(maxPrice) {
  const upper = upperBound(sortedByPrice, maxPrice, p => p.price); // O(log n)
  const inBudget = sortedByPrice.slice(0, upper); // O(k)
  // apply remaining filters on inBudget only
}
```
**Result:** Price filter is O(log n + k) after initial sort — zero re-sorting on slider interaction.

### Iteration 3 (Production): Server-Side Sorting with Pagination
Move sorting to the backend. PostgreSQL with a `(price, popularity, rating)` composite index handles sorting and pagination in O(log n + k) via index scan, with the browser receiving only the current page (12 items):
```sql
SELECT * FROM products
WHERE category = $1 AND price <= $2
ORDER BY price ASC
LIMIT 12 OFFSET $3;
```
**Result:** O(log n) backend query; browser receives only the current page. Linear horizontal scalability.

---

## 5.4 Scalability for B4 — Segment Tree at Scale

### Iteration 1: Expand the Array and Rebuild
Pre-allocate the Segment Tree for 90 days instead of 7. Rebuilding is O(n) where n = 90:
```javascript
const revenueData = new Array(90).fill(0);
// Seed last 7 days
const segTree = new SegmentTree(revenueData); // O(90) — negligible
```
**Result:** Supports date ranges up to 90 days with zero code change. Rebuild cost is trivial.

### Iteration 2: Dynamic Segment Tree with Lazy Propagation
Implement lazy propagation so range updates are O(log n) without rebuilding the whole tree:
```javascript
// Range update: add revenue to all days in [l, r] — O(log n)
segTree.updateRange(l, r, deltaRevenue);
// Query sum for any range — O(log n)
const total = segTree.query(startDay, endDay);
```
**Result:** Both updates and queries are O(log n); tree never needs full rebuild.

### Iteration 3 (Production): Time-Series Database
Replace the Segment Tree with a dedicated time-series store such as **InfluxDB** or **TimescaleDB**:
- Append-only writes at O(1) per event.
- Range aggregations (SUM, AVG, MAX) at O(log n) with built-in compression.
- Handles years of data with sub-second dashboard queries.
- Native grafana-style visualization support.

**Result:** O(log n) range queries on billions of revenue events with no code-level management.

---

## 5.5 Scalability for B5 — BST Balance at Scale

### Iteration 1: Insert in Random Order
Shuffle the product array before inserting into the BST. A randomly inserted BST has O(log n) expected height:
```javascript
const shuffled = [...products].sort(() => Math.random() - 0.5);
shuffled.forEach(p => priceBST.insert(p.price, p));
```
**Result:** Expected O(log n) height; worst case still O(n) but astronomically unlikely.

### Iteration 2: Convert to AVL Tree
Replace the simple BST with a self-balancing AVL Tree. After each insert, check balance factors and rotate to maintain height ≤ 1.44 log₂(n):
```javascript
// AVL rotation example
function rotateRight(node) {
  const newRoot = node.left;
  node.left = newRoot.right;
  newRoot.right = node;
  updateHeight(node); updateHeight(newRoot);
  return newRoot;
}
```
**Result:** O(log n) guaranteed for all operations. Standard in production databases.

### Iteration 3 (Production): B-Tree / PostgreSQL Index
At production scale, replace the in-memory BST with a B-Tree index in PostgreSQL:
```sql
CREATE INDEX products_price_idx ON products (price);
-- Range query: O(log n + k) via index scan
SELECT * FROM products WHERE price BETWEEN 1000 AND 5000;
```
**Result:** O(log n) with extremely small constants; battle-tested in production databases.

---

## 5.6 Scalability for B6 — HashMap Resize at Scale

### Iteration 1: Pre-size the HashMap
If catalog size is known at startup, initialise the HashMap with sufficient capacity to avoid any rehash:
```javascript
// With 20,000 products, set initial size > 20,000 / 0.75 ≈ 26,667
const productDB = new HashMap(32768); // Next power of 2 above 26,667
```
**Result:** Zero rehash events for the expected catalog size.

### Iteration 2: Incremental Rehashing
Instead of blocking O(n) rehash, spread the rehash work across subsequent `put()` calls. Maintain two tables (old, new) and gradually migrate one bucket per insert:
```javascript
put(key, value) {
  if (this.rehashing) {
    this._migrate_one_bucket(); // amortised O(1)
  }
  this.newTable.set(key, value);
}
```
**Result:** No blocking pauses; rehash cost amortised to O(1) per operation.

### Iteration 3 (Production): Redis / Distributed Hash Table
Replace the in-memory HashMap with **Redis**:
- O(1) GET/SET with persistence, TTL support, and pub/sub for cart session expiry.
- Horizontal sharding via consistent hashing across Redis Cluster nodes.
- Handles millions of concurrent cart sessions.
- Native support for session-based cart expiry (e.g., 30-minute TTL).

**Result:** Linear horizontal scalability; sub-millisecond operations at any scale.

---

---

# Technology Stack

## Languages & Standards

| Technology | Version | Role |
|---|---|---|
| **HTML5** | HTML Living Standard | Structure, semantic layout, accessibility attributes |
| **CSS3** | Modern baseline | Custom properties (design tokens), Grid, Flexbox, keyframe animations, backdrop-filter |
| **JavaScript** | ES6+ (ES2015+) | All application logic, DSA implementations, DOM manipulation |

## Fonts (External CDN)

| Font | Weight / Style | Use |
|---|---|---|
| **Cormorant Garamond** | 300, 400, 600; italic variants | Display headings, prices, product names — editorial serif |
| **DM Sans** | 300, 400, 500, 600 | Body text, labels, buttons — clean geometric sans-serif |
| **DM Mono** | 400, 500 | Code-like data (IDs, prices, badges, monospace values) |

## No Runtime Dependencies

The application has **zero** npm packages, zero bundler requirements, and zero framework dependencies. It uses:
- Native ES6 classes for all DSA implementations
- Native DOM APIs for rendering and event handling
- Native `performance.now()` for DSA benchmarking
- A single CDN call for Google Fonts (the app works offline if fonts are cached)

## Browser Compatibility

Requires any modern browser supporting ES6+: Chrome 60+, Firefox 55+, Safari 11+, Edge 79+.

---

# Features

## Authentication System (v2)

A full login screen with tabbed Shopper / Admin / Vendor roles. Credentials are validated against the seeded `userDB` HashMap. Role determines which navigation items and pages are accessible.

**Demo credentials:**
| Role | Email | Password |
|---|---|---|
| Shopper | `amina@example.com` | `shop123` |
| Admin | `admin@biashara.com` | `admin2024` |
| Vendor | `vendor@biashara.com` | `vendor123` |

## Home Page

An animated statistics counter runs on load using `setInterval`. The 6 category cards link directly to a filtered shop view. Featured products are extracted using a **Max-Heap Top-4** by popularity score. The AI Recommendations section uses **BFS graph traversal** (depth 2) on the product similarity graph seeded by wishlist and cart interactions, falling back to top-rated products when there is no interaction history.

## Shop Page

Real-time search autocomplete powered by the **Trie**. **KMP substring matching** across product names, descriptions, and tags. Category filter using radio buttons that trigger a BST range query. Price range slider with debounced **Merge Sort** applied to the filtered result set. Rating filter and In-Stock toggle. Six sort modes: Relevance, Price ascending, Price descending, Rating, Newest, Popularity. Pagination at 12 products per page with a page number bar.

## Product Detail Page

An **LRU Cache** (capacity 30) accelerates repeat visits — the second click on the same product is instant. Quantity selector capped to available stock. Similar products panel computed by **BFS depth-2 graph traversal** from the current product's node.

## Cart Page

Add, remove, and update quantity via **HashMap** operations (O(1)). A **Stack** records every cart action, enabling O(1) undo of the last operation. Coupon code field validated against a **HashMap** of discount codes. Free shipping on orders over KES 10,000. An undo bar appears after each action.

## Checkout Modal

Select a Nairobi delivery zone. **Dijkstra's Algorithm** computes the shortest path from the warehouse node to the selected zone and displays the ETA and distance. Supports M-Pesa, Visa, and Cash on Delivery.

## Orders Page

Orders rendered from the **Doubly Linked List** in reverse (newest first). Click any order card to expand the status timeline. A state machine governs transitions: `pending → processing → shipped → out_for_delivery → delivered`.

## Wishlist Page

Powered by a **Hash Set** — all toggle, check, and render operations are O(1).

## Dashboard Page (Admin/Vendor)

A 7-day revenue bar chart with **Segment Tree** range query support. Four animated KPI stat cards. Top-5 products extracted via **Min-Heap Top-K** (O(n log 5)). Recent orders table from the DLL tail.

## Inventory Page (Admin/Vendor)

Full product table with colour-coded stock progress bars. Min-Heap-driven reorder alerts with a red badge count. **KMP-powered** inventory search across SKUs and product names. Sort by name, stock, or price. Add Product modal that inserts into the **HashMap** and **Trie** simultaneously. Edit modal for price, stock, and reorder threshold updates. Reorder button auto-restocks a product. An **inventory audit Stack** records every add/edit action with timestamps.

## Client Management Page (Admin only)

Full user table from `userDB`. **KMP** search across name and email. **Merge Sort** by total spend, order count, or join date. Activate/deactivate toggle. Per-client order history modal, filtered from the DLL order list.

## DSA Benchmark Page (Admin only)

Run live performance benchmarks with a configurable `n`. Algorithms benchmarked include HashMap insert/lookup, Merge Sort, Quick Sort, Heap Sort, Binary Search, KMP, Min-Heap build + extract-all, Top-K, Trie insert + autocomplete, and Segment Tree queries. Results rendered as proportional bar charts with complexity labels. A separate sort comparison chart shows Merge Sort vs Quick Sort vs Heap Sort across five input sizes (n = 100, 500, 1000, 2000, 5000).

---

# How to Run

## Option 1: Direct Browser Open (Zero Setup)
```bash
# Clone the repository
git clone https://github.com/your-username/biashara-hub.git
cd biashara-hub

# Open the file directly in your browser
open biashara_hub.html        # macOS
start biashara_hub.html       # Windows
xdg-open biashara_hub.html    # Linux
```

## Option 2: Local HTTP Server (Recommended)
```bash
# Python 3
python3 -m http.server 8080

# Node.js (npx)
npx serve .

# Then open: http://localhost:8080/biashara_hub.html
```

No npm install, no build step, no environment variables required. The application is completely self-contained in a single HTML file.

---

# Application Flow

## First-Time User Journey

```
1. Open biashara_hub.html in a browser
        │
        ▼
2. Login screen appears
   Choose role tab (Shopper / Admin / Vendor)
   Enter credentials → click "Sign In"
        │
        ▼
3. Home page renders
   ├─ Stats counter animates
   ├─ Category cards appear
   ├─ Featured products loaded (Max-Heap Top-4)
   └─ Recommendation section loads (BFS graph or top-rated fallback)
        │
        ▼
4. Navigate to Shop (via nav or category card)
   ├─ Type in search bar → Trie autocomplete dropdown
   ├─ Select suggestion or press Enter → KMP search executes
   ├─ Adjust price slider (debounced) → filter + Merge Sort
   ├─ Click product card → Product Detail page
   │     ├─ LRU cache checked (O(1))
   │     ├─ Product info rendered
   │     └─ Similar products (BFS depth-2) shown
   └─ Click "+" or "Add to Cart" button
        │
        ▼
5. Go to Cart page
   ├─ View cart items (HashMap)
   ├─ Adjust quantities → HashMap update O(1)
   ├─ Apply coupon code → HashMap lookup O(1)
   ├─ Undo last action → Stack pop O(1)  [v2]
   └─ Click "Checkout"
        │
        ▼
6. Checkout modal
   ├─ Select delivery zone
   ├─ Dijkstra computes ETA
   ├─ Select payment method
   └─ "Place Order" → DLL append O(1) → cart cleared
        │
        ▼
7. Orders page (via nav)
   └─ All orders from DLL shown newest-first
      Click card → status timeline expands
```

## Admin Journey

```
After login as admin:
        │
        ▼
Dashboard → Revenue chart, KPI cards, Top-5 products
        │
        ▼
Inventory → View all SKUs, search (KMP), add/edit products
        │
        ▼
Clients → View all users, search, sort, toggle status
        │
        ▼
Benchmark → Set n, run all DSA timings, view sort comparison chart
```

---

# DSA Reference Table

| Tag in Code | DSA | Time Complexity | Space | Used In |
|---|---|---|---|---|
| `// ── 1a.` | **Trie** | insert O(L), autocomplete O(L+k) | O(n·L) | Product autocomplete |
| `// ── 1b.` | **Min/Max-Heap** | push/pop O(log n), peek O(1) | O(n) | Reorder alerts, Dijkstra PQ, Top-K |
| `// ── 1c.` | **BST** | search O(log n) avg, range O(log n+k) | O(n) | Category/price index |
| `// ── 1d.` | **HashMap** | get/set O(1) avg | O(n) | Product DB, User DB, cart, coupons |
| `// ── 1e.` | **Doubly Linked List** | insert/delete O(1) | O(n) | Order history, LRU list |
| `// ── 1f.` | **LRU Cache** | get/put O(1) | O(cap) | Product detail pages |
| `// ── 1g.` | **Queue** | enqueue/dequeue O(1) | O(n) | BFS traversal, order scheduling |
| `// ── 1h.` | **Stack** | push/pop O(1) | O(n) | Cart undo, inventory audit [v2] |
| `// ── 1i.` | **Graph** | BFS O(V+E) | O(V+E) | Product similarity |
| `// ── 1j.` | **Segment Tree** | build O(n), query O(log n) | O(n) | Revenue range queries |
| `// ── 2a.` | **Merge Sort** | O(n log n) stable | O(n) | All product/client sorting |
| `// ── 2b.` | **Binary Search** | O(log n) | O(1) | Price range lower/upper bounds |
| `// ── 2c.` | **KMP** | O(n+m) | O(m) | Product & client text search |
| `// ── 2d.` | **Top-K Heap** | O(n log k) | O(k) | Bestsellers, featured products |
| `// ── 2e.` | **Dijkstra** | O((V+E) log V) | O(V) | Delivery route ETA |
| `// ── 2f.` | **BFS/DFS** | O(V+E) | O(V) | Similar products, recommendations |
| `// ── 2g.` | **Cosine Similarity** | O(n·m) | O(n) | Collaborative filtering |
| `// ── 2h.` | **Sliding Window Max** | O(n) | O(k) | Revenue rolling max |
| *(benchmark)* | **Quick Sort** | O(n log n) avg | O(log n) | Benchmark comparison [v2] |
| *(benchmark)* | **Heap Sort** | O(n log n) | O(1) extra | Benchmark comparison [v2] |

**Python Equivalents:**
Trie → `class TrieNode: children = dict()` · Min-Heap → `import heapq` · HashMap → `dict` · Hash Set → `set()` · DLL → `collections.deque` · LRU → `functools.lru_cache` · Graph → `dict` of adjacency lists · Segment Tree → custom `2n` array · Merge Sort → `sorted()` (Timsort) · Binary Search → `bisect.bisect_left()` · Dijkstra → `heapq` + distance dict · BFS → `collections.deque`

---

# File Structure

```
biashara-hub/
├── biashara_hub.html          ← Complete v2 application (open this)
├── initial_biashara_hub.html  ← v1 reference (original)
├── biashara_hub_docs.md       ← System documentation (this file)
└── README.md                  ← GitHub README (this file)
```

The application is intentionally a **single HTML file** to demonstrate that a full-featured, DSA-rich e-commerce platform requires no build toolchain or server infrastructure. All CSS (design system, components, animations) and all JavaScript (DSA implementations, page renderers, state management) are inline.

---

# Coupon Codes

| Code | Discount |
|---|---|
| `BIASHARA10` | 10% off |
| `SAVE20` | 20% off |
| `VIPGOLD` | 25% off |

Coupon codes are stored in a **HashMap** and validated in O(1).

---

# Console DSA Map

Open DevTools → Console to see the complete DSA map printed on startup:

```
🛒 Biashara Hub v2 — DSA Map
HashMap        → Product DB, User DB, Cart, Coupons — O(1) avg
Trie           → Search Autocomplete — O(L+k)
Min/Max Heap   → Reorder Alerts, Top-K, Dijkstra PQ — O(log n)
BST            → Category/Price Index — O(log n)
Stack          → Cart Undo, Inventory History — O(1)
Queue          → Order Processing FIFO — O(1)
DLL            → Order History — O(1) append
LRU Cache      → Product Detail Pages — O(1)
Graph+BFS/DFS  → Similar Products — O(V+E)
Segment Tree   → Revenue Range Queries — O(log n)
Merge Sort     → Product/Client Sorting — O(n log n)
KMP            → Full-Text Search — O(n+m)
Dijkstra       → Delivery Route ETA — O((V+E)logV)
Cosine Sim     → Collaborative Filtering — O(n·m)
Sliding Window → Revenue Trend — O(n)
```

---

# Scalability Roadmap

| Scale Tier | Users | Architecture | Key Changes |
|---|---|---|---|
| **Tier 1: Current** | 0 – 1,000 | Single-Page App | All DSA in-browser; no backend |
| **Tier 2: Growth** | 1,000 – 50,000 | SPA + Node.js / FastAPI backend | PostgreSQL for products; Typesense for search; Redis for sessions |
| **Tier 3: Scale** | 50,000 – 1M | Microservices | Product Service, Order Service, Search Service, ML Recommendation Engine; CDN for assets |
| **Tier 4: Hyperscale** | 1M+ | Distributed / Cloud-native | Kubernetes, Apache Kafka for event streaming, Elasticsearch, Cassandra for orders, ML pipeline |

---

*Biashara Hub — Where Commerce Meets Intelligence.*
*Built with ❤️ and O(log n) efficiency in Nairobi, Kenya.*

