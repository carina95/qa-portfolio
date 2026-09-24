# Functional test cases — OWASP Juice Shop

Executable test cases for OWASP Juice Shop (v20.2.0), organised by feature.
Environment details: see [../docs/environment.md](../docs/environment.md).
Each case is atomic (verifies one behaviour), and every expected result is
anchored to an oracle.

## Index

| ID | Feature | Title | Priority |
|---|---|---|---|
| TC-REG-001 | Registration | Registration is rejected when the two password fields do not match | High |
| TC-BSK-001 | Basket | A product can be added to the basket from the product list | High |
| TC-SEARCH-001 | Search | Search returns the matching products for a valid term | High |
| TC-SEARCH-002 | Search | Search handles a term with no matches gracefully | Medium |

---

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

---

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

---

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
