# Lesson 15 — Pairwise testing (practice)

## Exercise 1 — Flower shop, by hand

**Parameters:**
- Size: Small, Medium, Large
- Gift wrap: Yes, No
- Delivery: Standard, Express

**(a) Full combinations:** 3 × 2 × 2 = 12

**(b) Smallest possible pairwise set:** 6 rows
Why: Size has 3 values and Gift wrap has 2, so there are 3 × 2 = 6 Size + Gift wrap pairs. One row can only contain one of those pairs (a row has one size and one gift-wrap choice), so you need at least 6 rows.

**(c) My pairwise set:**

| # | Size | Gift wrap | Delivery |
|---|---|---|---|
| 1 | Small | Yes | Standard |
| 2 | Small | No | Express |
| 3 | Medium | No | Standard |
| 4 | Medium | Yes | Express |
| 5 | Large | Yes | Standard |
| 6 | Large | No | Express |

**Check — every pair and the row that covers it:**

Size + Gift wrap:

| Pair | Covered in row |
|---|---|
| Small + Yes | row 1 |
| Small + No | row 2 |
| Medium + No | row 3 |
| Medium + Yes | row 4 |
| Large + Yes | row 5 |
| Large + No | row 6 |

Size + Delivery :

| Pair | Covered in row |
|---|---|
| Small + Standard| row 1|
| Small + Express | row 2|
| Medium + Standard| row 3|
| Medium + Express| row 4|
| Large + Standard| row 5|
| Large + Express | row 6|

Gift wrap + Delivery :

| Pair | Covered in row |
|---|---|
| Yes + Standard | row 1 |
| Yes + Express | row 4 |
| No + Standard | row 3 |
| No + Express | row 6 |

**Total pairs checked:** 6 (Size + Gift wrap) + 6 (Size + Delivery) + 4 (Gift wrap + Delivery) = 16 pairs — all covered? yes 
If any pair is missing, which one, and how did I fix the set?
At first, Yes + Express and No + Standard were missing, because every size had Standard on the Yes row and Express on the No row, the pattern was too tidy. I fixed it by swapping the delivery options for Medium, so Medium + Yes got Express and Medium + No got Standard.

## Exercise 2 — PICT

**Model file:** docs/notes/exercises/checkout-model.txt

**PICT output:**

| # | Browser | Account | Delivery | Payment |
|---|---|---|---|---|
| 1 | Safari | Returning | D2 | New card |
| 2 | Safari | Returning | D1 | Saved card |
| 3 | Chrome | New | D3 | New card |
| 4 | Chrome | Returning | D2 | Saved card |
| 5 | Chrome | New | D1 | New card |
| 6 | Firefox | New | D2 | New card |
| 7 | Firefox | Returning | D3 | Saved card |
| 8 | Safari | New | D3 | New card |
| 9 | Firefox | Returning | D1 | Saved card |

**(a) Number of rows:** 9
Is this the smallest possible? Yes.
Why: Browser and Delivery both have 3 values, so there are 3 × 3 = 9 Browser + Delivery pairs. One row can only contain one of those pairs, so you need at least 9 rows.

**(b) Constraint check — every row with Account = New:**

| Row | Account | Payment | Allowed? (New must have New card) |
|---|---|---|---|
| 3 | New | New card | Yes |
| 5 | New | New card | Yes |
| 6 | New | New card | Yes |
| 8 | New | New card | Yes |

Any impossible rows? No.

**(c) Spot-check three pairs:**

| Pair | Covered in row |
|---|---|
| Returning + New card | row 1 |
| Returning + Saved card | row 2 |
| Safari + New | row 8 |

**(d) Compared with the table in the lesson:** different rows.
Is that a problem? No. A pairwise set is correct if every pair appears in at least one row, not if it matches another set. There are many valid sets, like the two coffee tables.

## Exercise 3 — Real delivery methods

**Account used:** first@test.com / Test1234
- (a): read the delivery methods
- (c): row 3 first (the account's first order, so it's New), then row 4 (its second order, so it's Returning with a saved card)

**(a) Real delivery methods (from the delivery screen):**

| Placeholder | Real name | Price | Delivery time shown |
|---|---|---|---|
| D1 | One Day Delivery | 0.99¤ | 1 day |
| D2 | Fast Delivery | 0.50¤ | 3 days |
| D3 | Standard Delivery | 0.00¤ | 5 days |

**(b) PICT output with the real names:**

| # | Browser | Account | Delivery | Payment |
|---|---|---|---|---|
| 1 | Safari | Returning | Fast Delivery | New card |
| 2 | Safari | Returning | One Day Delivery | Saved card |
| 3 | Chrome | New | Standard Delivery | New card |
| 4 | Chrome | Returning | Fast Delivery | Saved card |
| 5 | Chrome | New | One Day Delivery | New card |
| 6 | Firefox | New | Fast Delivery | New card |
| 7 | Firefox | Returning | Standard Delivery | Saved card |
| 8 | Safari | New | Standard Delivery | New card |
| 9 | Firefox | Returning | One Day Delivery | Saved card |

Same number of rows as before? Yes, 9.
Any New + Saved card rows? No.

**(c) Two rows I ran as real checkouts** (in Chrome, DevTools open)

### Row 3 — first@test.com · New · Standard Delivery · New card
(First order on this account: no previous orders, no saved card.)

| Check | Expected | Actual | Match? |
|---|---|---|---|
| Delivery price on the Order Summary | 0.00¤ | 0.00¤ | Yes |
| `checkout` request status | 200 | 200 | Yes |
| Order History: delivery method | Standard Delivery | Not shown in the UI. Network: no method field; `eta: "5"` (5 days), and only Standard Delivery takes 5 days | Yes |
| Order History: delivery price | 0.00¤ | Not shown in the UI. Network: `deliveryPrice: 0`, `totalPrice: 1.99` (1.99 + 0.00) | Yes |

### Row 4 — first@test.com · Returning · Fast Delivery · Saved card
(Second order on the same account: it now has one order and the card saved in row 3.)

| Check | Expected | Actual | Match? |
|---|---|---|---|
| Delivery price on the Order Summary | 0.50¤ | 0.50¤ | Yes |
| `checkout` request status | 200 | 200 | Yes |
| Order History: delivery method | Fast Delivery | Not shown in the UI. Network: no method field; `eta: "3"` (3 days), and only Fast Delivery takes 3 days | Yes |
| Order History: delivery price | 0.50¤ | Not shown in the UI. Network: `deliveryPrice: 0.5`, `totalPrice: 2.49` (1.99 + 0.50) | Yes |

**(d) Conclusion:**
Did the delivery method and price in Order History match what I chose? Yes, for both rows.
Is this a pass or a bug? Pass.
Why: For both combinations, the server recorded the delivery price the delivery screen promised
(Claims oracle): Standard 0.00¤ and Fast 0.50¤, and the totals add up. Order History doesn't
show delivery details in the UI, so I checked the order-history response in the Network tab:
deliveryPrice, totalPrice and eta matched. Both orders have paymentId 7, which confirms row 4
used the card saved in row 3.
Not tested: the browser part of the PICT rows (I used Chrome for both), and the other 7 rows.