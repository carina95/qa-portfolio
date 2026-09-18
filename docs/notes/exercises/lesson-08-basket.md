# Lesson 8 — Test design: Basket quantity control (Juice Shop)

**Feature under test:** shopping basket quantity control (+ / − buttons and the
quantity number) on http://localhost:3000
**Version:** OWASP Juice Shop 20.2.0

## Exercise 1 — Test basis

No written specification exists. I am using these test basis sources:
- **UI:** the quantity number and the + / − buttons on each basket line.
- **Comparable products:** other e-commerce baskets (e.g. Amazon).
- **Experience:** a quantity is a positive whole number, cannot be negative,
  and usually has a lower limit (1) and an upper limit (stock).

## Exercise 2 — Test conditions

- C-a: Decrease the quantity when it is 1.
- C-b: Increase the quantity with the + button.
- C-c: Decrease the quantity with the − button.
- C-d: Set a very high quantity (find the upper limit).
- C-e: Submit the basket with the "Checkout" button.
- C-f: Delete the product from the basket.
- C-g: Total price updates when the quantity is modified.

## Exercise 3 — Test cases

### TC-a (from C-a: decrease at quantity 1)
- **Precondition:** Logged in; basket has 1 × Apple Juice (1000ml).
- **Input:** press the − button.
- **Expected:** the app behaves consistently — it either removes the line,
  refuses to go below 1, or shows a defined result — and no error appears.
- **Oracle:** internal consistency (no spec defines this; the app must have
  one repeatable behaviour).
- **Postcondition:** basket is in a valid state; no console error.

### TC-b (from C-b: increase with +)
- **Precondition:** Logged in; basket has 1 × Apple Juice (1000ml).
- **Input:** press the + button once.
- **Expected:** the quantity increases to 2; no error appears.
- **Oracle:** purpose of the + button.
- **Postcondition:** quantity increased by 1; no console error.

### TC-d (from C-d: very high quantity)
- **Precondition:** Logged in; basket has 1 × Apple Juice (1000ml).
- **Input:** keep pressing + until the quantity stops rising, or set a
  deliberately large number (e.g. 50+).
- **Expected:** the app enforces some consistent limit — it either caps at a
  defined maximum with a clear message, or keeps going without breaking;
  either way, no crash and no console error.
- **Oracle:** internal consistency + experience (real baskets have a
  stock/quantity ceiling).
- **Postcondition:** quantity is in a valid state; total price still matches
  quantity × price.

## Exercise 4 — Coverage

- Coverage: 3 of 7 conditions = **42.9%**.
- One condition I did not list: if I log out with items in the basket, do they
  remain after I log back in? (basket persistence across sessions)