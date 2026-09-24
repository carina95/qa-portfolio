# Lesson 10 — Executable test cases (practice)

## Exercise 1 — Negative: registration rejects mismatched passwords

**ID:** TC-REG-001
**Title:** Registration is rejected when the two password fields do not match
**Priority:** High
**Preconditions:**
- Juice Shop running at http://localhost:3000, freshly restarted.
- No user logged in; on the registration form.
**Test data:** e-mail: test@test.com, password `Test1234`, repeat password `Test9999`

| # | Step (action) | Expected result |
|---|---|
| 1 | Go to /#/register | The registration form is displayed |
| 2 | Enter test@test.com | The email is accepted |
| 3 | Enter `Test1234` in Password | Password is masked |
| 4 | Enter `Test9999` in Repeat Password | The Repeat Password field is in red underline and displays the message " Passwords do not match" |
| 5 | Chose security question "Your eldest siblings middle name" | The question is succesfully selected |
| 6 | Write "Maria" in Answer field | The answer is accepted |
| 7 | Click Register | The button is unavaible to click |

**Postconditions:** No account created; no user logged in.
**Oracle:** Product, Purpose

## Exercise 2 — Positive: add a product to the basket

**ID:** TC-BSK-001
**Title:** A product can be added to the basket from the product list 
**Priority:** High
**Precondition**: logged in as test@test.com/ Test1234, on the home page, basket empty.
**Test data:** product Apple Juice (1000ml) 

| # | Step (action) | Expected result |
|---|---|
| 1 | Click add to basket on Apple Juice (1000ml) product | Confirmation appears with text shown on page "Placed Apple Juice (1000ml) into basket." 
| 2 | Go to /#/basket page | The basket now shows 1 x Apple Juice (1000ml)

**Postconditions:** User remains logged in, basket contains 1 × Apple Juice (1000ml);
**Oracle:** Claims, Purpose


## Exercise 3 — Rewrite of "test search" (split into 1 or 2 cases — you decide)
"Test search. Search for products and make sure results are correct and the page doesn't break."

The query is implying to search for two different things, so we must split it and do atomic testing. First we see if search returns correct results (positive approach) and then we test if the search handles a no-match / odd input without breaking.

### Case 1 — Positive: search returns correct results

**ID:** TC-SEARCH-001
**Title:** Search returns the matching products for a valid term
**Priority:** High
**Preconditions:**
- Juice Shop running at http://localhost:3000, freshly restarted.
- No user logged in; on the home page.
**Test data:** search term `apple`

| # | Step (action) | Expected result |
|---|---|
| 1 | Click the search icon in the top toolbar | The search input box opens |
| 2 | Type `apple` and press Enter | 3 results: Apple Juice (1000ml), Apple Pomace, Pineapple Juice (1000ml) |

**Postconditions:** Heading text is "Search Results - apple" and shows 3 different products as listed in step 2 expected results
**Oracle:** Purpose, Claims, User expectations

### Case 2 — Robustness: search handles a no-match / odd input without breaking

**ID:** TC-SEARCH-002
**Title:** Search handles a term with no matches gracefully (no crash)
**Priority:** Medium
**Preconditions:**
- Juice Shop running at http://localhost:3000, freshly restarted.
- No user logged in; on the home page.
**Test data:** search term `zzzzzz`

| # | Step (action) | Expected result |
|---|---|
| 1 | Click the search icon in the top toolbar | The search input box opens |
| 2 | Type `zzzzzz` and press Enter | Message that says "No results found. Try adjusting your search to find what you're looking for.". 

**Postconditions:** Heading text is Search Results - zzzzzz . No crash, no error. "No results" message.
**Oracle:** Comparable products, Purpose

## Exercise 4 — Which case is highest priority and why
Highest priority - ordering / add-to-basket (TC-BSK-001). If a user can order products it makes the purpose of the app succesful, even though they presumably can't login or if search handles no-match. If users are able to find the product they need and order it, the app is still producing valuable cost-effective advantage. Frequently people order without loggin in at all, so it does not affect their experience (in the case that they receive an e-mail or some confirmation for the order and are updated on it's status). If this case fails, the app is unusable. 