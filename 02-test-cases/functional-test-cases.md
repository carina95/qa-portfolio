# Functional Test Suite — OWASP Juice Shop

**Application under test:** OWASP Juice Shop, version 20.2.0 (Docker image `bkimminich/juice-shop`)
**Environment:** see [../docs/environment.md](../docs/environment.md)
**Author:** Carina Plisco

## Overview

This suite contains executable test cases for OWASP Juice Shop, organised by
feature. Each case follows a consistent format (ID, priority, preconditions,
test data, steps with per-step expected results, postconditions), and every
expected result is anchored to a named oracle.

Cases were designed using these techniques:
- **Decision tables** — registration rules
- **Equivalence partitioning** and **boundary value analysis** — field validation, quantity limits, and search input
- **Experience-based testing** — the address form

Some cases **fail** against version 20.2.0 and are linked to the defect reports
in [`../03-bug-reports/`](../03-bug-reports/).

## Index

| ID | Feature | Title | Technique | Priority | Status |
|---|---|---|---|---|---|
| TC-REG-001 | Registration | Rejected when passwords don't match | Decision table | High | Pass |
| TC-REG-002 | Registration | Password field shows one consistent maximum | BVA / consistency | High | **Fail → BUG-001** |
| TC-BSK-001 | Basket | Add a product from the product list | Happy path | High | Pass |
| TC-QTY-EP-01 | Basket | Quantity cannot go below 1 | EP (below min) | Medium | Pass |
| TC-QTY-EP-02 | Basket | Quantity within range (3) accepted | EP (valid) | Medium | Pass |
| TC-QTY-EP-03 | Basket | Quantity cannot exceed 5 | EP / BVA (above max) | Medium | Pass |
| TC-SEARCH-001 | Search | Returns matching products for a valid term | EP (valid) | High | Pass |
| TC-SEARCH-002 | Search | Handles a term with no matches | EP (robustness) | Medium | Pass |
| TC-ADDR-001 | Address | Name rejects a spaces-only value | Experience-based | Medium | **Fail → BUG-004** |


## Registration

### TC-REG-001 — Registration is rejected when the two password fields do not match
**Priority:** High
**Preconditions:**
- Juice Shop running at http://localhost:3000, freshly restarted.
- No user logged in; on the registration form (/#/register).

**Test data:** email `test@test.com`, password `Test1234`, repeat password `Test9999`

| # | Step (action) | Expected result |
|---|---|---|
| 1 | Go to /#/register | The registration form is displayed |
| 2 | Enter `test@test.com` in Email | The email is accepted |
| 3 | Enter `Test1234` in Password | The password is masked |
| 4 | Enter `Test9999` in Repeat Password | The Repeat Password field shows a red underline and the message "Passwords do not match" |
| 5 | Select security question "Your eldest sibling's middle name" | The question is selected |
| 6 | Enter "Maria" in the Answer field | The answer is accepted |
| 7 | Attempt to click Register | The Register button is disabled and cannot be clicked |

**Postconditions:** No account is created; no user is logged in.
**Oracle:** Product (internal consistency — the two password fields must agree); Purpose (a confirm-password field exists to catch typos).

### TC-REG-002 — Password field shows one consistent maximum length
**Priority:** High
**Preconditions:** On the registration form (`/#/register`), no user logged in.
**Test data:** a 30-character password

| # | Step (action) | Expected result |
|---|---|---|
| 1 | Read the password field's hint text and its character counter | Both state the same maximum length |
| 2 | Type a 30-character password | The field enforces its stated maximum consistently; the counter and the hint do not contradict each other |

**Postconditions:** No account created.
**Oracle:** Product (internal consistency — one field must not state two different limits).
**Status:** **Fail → [BUG-001](../03-bug-reports/BUG-001.md)** — the hint says 5–40 but the counter caps at 20, and a 30-character password is accepted while the counter shows 30/20.

## Basket

### TC-BSK-001 — A product can be added to the basket from the product list
**Priority:** High
**Preconditions:** Logged in as `test@test.com` / `Test1234`; on the home page; basket empty.

**Test data:** product Apple Juice (1000ml)

| # | Step (action) | Expected result |
|---|---|---|
| 1 | Click "Add to Basket" on Apple Juice (1000ml) | A confirmation appears: "Placed Apple Juice (1000ml) into basket." |
| 2 | Go to /#/basket | The basket shows 1 × Apple Juice (1000ml) |

**Postconditions:** User remains logged in; basket contains 1 × Apple Juice (1000ml).
**Oracle:** Claims (the confirmation states it was placed); Purpose (the basket holds items for purchase).

### TC-QTY-EP-01 — Basket quantity cannot go below 1 (below-minimum partition)
**Priority:** Medium
**Preconditions:** Logged in as `test@test.com` / `Test1234`; basket contains 1 × Apple Juice (1000ml); on the basket page.

**Test data:** target quantity 0

| # | Step (action) | Expected result |
|---|---|---|
| 1 | At quantity 1, press the − button | The quantity does not go below 1; removal is done with the separate trash button, not − |

**Postconditions:** Basket still contains 1 × Apple Juice (1000ml).
**Oracle:** Purpose (a basket line holds at least 1 item; 0 is handled by removing the line).
**Note:** The UI blocks this below-minimum partition, so it cannot be reached through the buttons; verifying it at the server would require the API.

### TC-QTY-EP-02 — Basket quantity within range is accepted (valid partition)
**Priority:** Medium
**Preconditions:** Logged in as `test@test.com` / `Test1234`; basket contains 1 × Apple Juice (1000ml); on the basket page.

**Test data:** target quantity 3

| # | Step (action) | Expected result |
|---|---|---|
| 1 | Press the + button twice | The quantity increases to 3 |

**Postconditions:** Basket contains 3 × Apple Juice (1000ml).
**Oracle:** Purpose; Claims.

### TC-QTY-EP-03 — Basket quantity cannot exceed 5 (above-maximum partition)
**Priority:** Medium
**Preconditions:** Logged in as `test@test.com` / `Test1234`; basket contains 5 × Apple Juice (1000ml); on the basket page.

**Test data:** target quantity 6

| # | Step (action) | Expected result |
|---|---|---|
| 1 | At quantity 5, press the + button to attempt 6 | The quantity stays at 5; the message "You can order only up to 5 items of this product." is shown |

**Postconditions:** Basket still contains 5 × Apple Juice (1000ml).
**Oracle:** Product (the app's own stated limit); Claims (the limit message).
**Note:** Server-enforced — the attempt sends PUT /api/BasketItems/<id> and the server returns HTTP 400 with the limit message (verified).

## Search

### TC-SEARCH-001 — Search returns the matching products for a valid term
**Priority:** High
**Preconditions:**
- Juice Shop running at http://localhost:3000, freshly restarted.
- No user logged in; on the home page.

**Test data:** search term `apple`

| # | Step (action) | Expected result |
|---|---|---|
| 1 | Click the search icon in the top toolbar | The search input box opens |
| 2 | Type `apple` and press Enter | 3 results are shown: Apple Juice (1000ml), Apple Pomace, Pineapple Juice (1000ml) |

**Postconditions:** Heading reads "Search Results - apple"; the 3 products above are listed.
**Oracle:** Purpose; Claims; User expectations.

### TC-SEARCH-002 — Search handles a term with no matches gracefully (no crash)
**Priority:** Medium
**Preconditions:**
- Juice Shop running at http://localhost:3000, freshly restarted.
- No user logged in; on the home page.

**Test data:** search term `zzzzzz`

| # | Step (action) | Expected result |
|---|---|---|
| 1 | Click the search icon in the top toolbar | The search input box opens |
| 2 | Type `zzzzzz` and press Enter | Heading reads "Search Results - zzzzzz"; the message "No results found. Try adjusting your search to find what you're looking for." is shown; no products are listed |

**Postconditions:** No crash and no error; the "No results" message is displayed.
**Oracle:** Comparable products; Purpose.



## Address

### TC-ADDR-001 — Name field rejects a name made only of spaces
**Priority:** Medium
**Preconditions:** Logged in; on the Add New Address form (Basket → Checkout → Add New Address).
**Test data:** a Name of three spaces `"   "`; all other fields valid.

| # | Step (action) | Expected result |
|---|---|---|
| 1 | Type three spaces in Name, fill the other fields validly | The form treats a spaces-only name the same as an empty one |
| 2 | Click Submit | Submission is rejected, as it is for an empty name; no address is saved |

**Postconditions:** No address saved.
**Oracle:** Product (empty names are already rejected, so spaces-only should be too) and Purpose (a delivery address needs a usable name).
**Status:** **Fail → [BUG-004](../03-bug-reports/BUG-004.md)** — the spaces-only name is accepted and saved (POST 201, `fullName: " "`).



