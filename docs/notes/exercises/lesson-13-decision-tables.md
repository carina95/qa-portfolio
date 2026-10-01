# Lesson 13 — Decision table testing (practice)

## Exercise 1 — Run the login decision table (both views)

| | R1 | R2 | R3 |
|---|---|---|---|
| C1 — Email registered | Y | Y | N |
| C2 — Password matches | Y | N | — |
| **A1 — Login succeeds (expected)** | YES | no | no |
| UI observed | Logged in | login rejected | login rejected |
| Network status + message | status 200 | status 401 "Invalid email or password."  | status 401 "Invalid email or password." |

**Test data:** registered = `test@test.com` / `Test1234`; unregistered = `nobody@test.com`
**Do R2 and R3 give the identical message?** (yes/no + exact text): Yes, they give the identical message: "Invalid email or password."

## Exercise 2 — Registration button decision table (both views)

(email valid + answer present held constant)

| | R1 | R2 | R3 | R4 |
|---|---|---|---|---|
| C1 — Passwords match | Y | Y | N | N |
| C2 — Length 5–40 valid | Y | N | Y | N |
| **A1 — Button enabled (expected)** | YES | no | no | no |
| Button observed (enabled/disabled) | enabled | disabled | disabled | disabled |

**Example inputs:** R1 `Password12`/`Password12` · R2 `abc`/`abc` · R3 `Password12`/`Password99` · R4 `abc`/`xyz`
**Does only R1 enable the button? Any rule behaving differently?:** Yes, only R1 enables the button

## Exercise 3 — Build your own decision table

**Rule I picked:** A customer can place an order (proceed through checkout).

**Conditions:**
- C1 = user is logged in
- C2 = basket is not empty
- C3 = a delivery address is selected

**Action:** A1 = can place the order

**Full table (2^3 = 8 columns):**

| | R1 | R2 | R3 | R4 | R5 | R6 | R7 | R8 |
|---|---|---|---|---|---|---|---|---|
| C1 — Logged in | Y | Y | Y | Y | N | N | N | N |
| C2 — Basket not empty | Y | Y | N | N | Y | Y | N | N |
| C3 — Address selected | Y | N | Y | N | Y | N | Y | N |
| **A1 — Can place order** | YES | no | no | no | no | no | no | no |

**Simplified table (merged "don't care" —):**

| | R1 | R2 | R3 | R4 |
|---|---|---|---|---|
| C1 — Logged in | Y | Y | Y | N |
| C2 — Basket not empty | Y | Y | N | — |
| C3 — Address selected | Y | N | — | — |
| **A1 — Can place order** | YES | no | no | no |

**What I collapsed and why:**
- Not logged in (C1=N): can't order at all, so basket and address don't matter — merged R5–R8 into one rule.
- Logged in but empty basket (C1=Y, C2=N): can't order an empty basket, so the address doesn't matter — merged R3–R4.
- Result: 8 combinations → 4 rules, so 4 test cases.

**Note:** this table is a design of how checkout *should* work. Whether Juice Shop
actually enforces each rule is a separate thing I'd confirm by running the 4 tests.
Checkout is also a sequence (login → add items → address → delivery → pay), which a
decision table doesn't capture — that's state transition testing 