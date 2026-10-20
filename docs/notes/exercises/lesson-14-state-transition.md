# Lesson 14 — State transition testing (exercises)

> **Where these results come from:** run by Claude on 2026-10-20 against a
> separate Juice Shop 20.2.0 container, mostly at the API level (requests sent
> directly to the server). Routes were read from the app's frontend code. Items
> marked **(re-check in UI)** were not observed in the browser — confirm them
> yourself with DevTools open before relying on them.

## Exercise 1 — Happy path through the checkout chain

**Precondition:** logged in (fresh account); basket contains 1 × Apple Juice
(1000ml); an address and a card exist; DevTools Network open, Preserve log on.

| # | Transition | URL after `#` | Request fired by the navigation itself? |
|---|---|---|---|
| 1 | S2 → S3 Basket → Choosing address (click Checkout) | /address/select | No — only the fragment changes |
| 2 | S3 → S4 Address → Delivery (Continue) | /delivery-method | No |
| 3 | S4 → S5 Delivery → Payment (Continue) | /payment/shop | No |
| 4 | S5 → S6 Payment → Order summary (Continue) | /order-summary | No |
| 5 | S6 → S7 Summary → Order placed (Place your order and pay) | /order-completion/&lt;id&gt; | **Yes** — the checkout POST |

Each screen also loads its own data (addresses, delivery methods, cards) when
it opens. Those data requests were not observed by me **(re-check in UI)**.

### The order-placing request (transition 5)
- **Method · endpoint · status:** POST · /rest/basket/&lt;basketId&gt;/checkout · **200**
- **Response:** `{"orderConfirmation":"a79e-54d8bb02b846b1dc"}`
- **Basket after the order:** empty (1 item before → 0 after)

### Answer
- Transitions 1–4 produce no request for the navigation itself.
- Why: moving between checkout screens only changes the part of the URL after
  `#`, and the fragment is never sent to the server (Lesson 2). The only
  transition that commits anything on the server is 5, the checkout POST.

## Exercise 2 — Invalid transitions

| # | Invalid transition | UI result | Network / server result | Enforced by |
|---|---|---|---|---|
| 1 | From basket, go straight to /#/order-summary (skip address, delivery, payment) | Route has no frontend guard, so the page opens. What it displays: **(re-check in UI)** | — | Not enforced by the screens |
| 2 | Place the order with no address, delivery or payment chosen | **(re-check in UI)** | POST /rest/basket/&lt;id&gt;/checkout with empty `orderDetails` → **200**, order created and shown in order history (delivered: false, deliveryPrice: 0) | **Not enforced at all** → **BUG-002** |
| 3 | After a successful order, check out again (basket now empty) | **(re-check in UI)** | Second POST …/checkout → **200**, new order created with **0 products** and **totalPrice 0.99** (the delivery fee) | **Not enforced at all** → **BUG-003** |
| 4 | Logged out, open /#/address/select | Page shows **"403 Please login to view this page."** | — | Enforced by the frontend route guard |

## Exercise 3 — State table for the checkout chain

| State \ Event | Checkout | Continue | Back | Place order | Logout | Type URL of a later step |
|---|---|---|---|---|---|---|
| **S2** Basket has items | S3 | invalid | — | **invalid** (but server accepts, BUG-002) | S0 | **invalid** (but allowed, no guard) |
| **S3** Choosing address | invalid | S4 (if address chosen) | S2 | invalid | S0 | invalid (but allowed) |
| **S4** Choosing delivery | invalid | S5 (if method chosen) | S3 | invalid | S0 | invalid (but allowed) |
| **S5** Choosing payment | invalid | S6 (if card chosen) | S4 | invalid | S0 | invalid (but allowed) |
| **S6** Reviewing order | invalid | invalid | S5 | S7 | S0 | — |
| **S7** Order placed (basket empty) | invalid (empty basket) | invalid | S6 | **invalid** (but server accepts, BUG-003) | S0 | — |

### The two riskiest invalid cells
1. **S2 + Place order (skipping steps).** The server accepts an order with no
   address, delivery method or payment. That order can't be delivered or
   correctly charged. Confirmed: BUG-002.
2. **S7 + Place order (empty basket).** The server creates an order with no
   products and still charges the delivery fee. A customer pays for nothing.
   Confirmed: BUG-003.

Both are sequence rules that no single-screen test would catch, and in both
cases the server is the layer that fails to enforce them.
