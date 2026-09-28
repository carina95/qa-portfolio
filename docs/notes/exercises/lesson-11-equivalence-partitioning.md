# Lesson 11 — Equivalence partitioning (practice)

## Exercise 1 — Partitions of the basket quantity (rule: 1–5)

| Partition | Range | Valid/Invalid | Representative |
|---|---|---|---|
| Below minimum | 0 (or less) | Invalid | 0 |
| Valid | 1–5 | Valid | 3 |
| Above maximum | 6+ | Invalid | 7 |

## Exercise 2 — Cases for each partition (basket quantity)

**Precondition (shared):** logged in as `test@test.com` / `Test1234`; basket contains 1 × Apple Juice (1000ml).

### TC-QTY-EP-01 — Below minimum (invalid partition)
- **Input:** try to set the quantity to 0 (press − from 1)
- **Expected:** the quantity should not go below 1. Removing an item is done with the separate trash button, not the − button, so − should just stop at 1.
- **Oracle:** Purpose
- **Observed:** the − button wouldn't go lower than 1, so I couldn't reach 0.

### TC-QTY-EP-02 — Valid partition
- **Input:** set the quantity to 3 (press + from 1 twice)
- **Expected:** the quantity goes up to 3.
- **Oracle:** Purpose, Claims
- **Observed:** it worked, the basket shows 3 × Apple Juice (1000ml).

### TC-QTY-EP-03 — Above maximum (invalid partition)
- **Input:** try to set the quantity to 7 (keep pressing + past 5)
- **Expected:** it should not go above 5. The message "You can order only up to 5 items of this product." should show, and the quantity should stay at 5.
- **Oracle:** Product, Claims
- **Observed:** I couldn't get past 5. The basket stayed at 5 no matter how many times I pressed +.

**Which partition was hardest to reach through the +/− buttons, and what does that tell you?**
The below-minimum one (TC-QTY-EP-01). The UI wouldn't let me get to 0 at all. So to really test that partition I'd have to check it at the API, because the interface blocks it before it ever reaches the server.

## Exercise 3 — Security answer: partitions

**Field:** the "Answer" field on the registration form, with a security question selected.

### What I first thought
I tested `a`, `12345` and empty, and every time the account was not created, so I marked them all as invalid. That turned out to be wrong.

### Why it was wrong (fault masking)
I was reusing the same email (`test@test.com`) for every try. After the first account existed, every next attempt failed because the email was already taken, not because of the answer. So the duplicate-email error was hiding what the answer field actually does. That's fault masking — one error covering up another.

When I used a fresh email each time, `a`, `12345` and `Maria` all created the account fine. So they're all valid, they just have to be non-empty.

### Corrected partitions

| Partition | Example value | Valid/Invalid |
|---|---|---|
| Non-empty answer | `Maria`, `a`, `12345` | Valid |
| Empty | (blank) | Invalid |
| Only spaces | `"   "` | Invalid (still need to test) |

### Note to self
Use a fresh, unique email for every registration test, or restart the container between tries. If I reuse an email, the duplicate-email error masks whatever I'm actually trying to test.

**Oracle(s) used:** Purpose (the answer just needs to exist)

## Exercise 4 — Does the app enforce its stated password rule (5–40)?

**Field:** Password on the registration form (/#/register).
**Stated rule (Claims oracle):** "Password must be 5–40 characters long."

| Partition | Length | Valid/Invalid | Value tested |
|---|---|---|---|
| P1 — too short | 0–4 | Invalid | `abcd` (4 chars) |
| P2 — valid | 5–40 | Valid | `Password12` (10 chars) |
| P3 — too long | 41+ | Invalid | a 45-character password |

| Test | Password | Expected | Observed | Counter |
|---|---|---|---|---|
| P1 | `abcd` | Rejected, length error | Length error showed and the Register button was disabled, so I couldn't create the account | 4/20 |
| P2 | `Password12` | Accepted | Accepted with no error. I could press Register and the account was created, then I was sent to the login page | 10/20 |
| P3 | 45-char password | Rejected, length error | I couldn't create the account. But the counter showed 45/20 and the field still let me type past the limit with no inline message | 45/20 |

### Verdict
- Does the app enforce the 5–40 rule? **Yes**, at submit — all three partitions behaved the way the rule says.
- Which partition reveals a failure? **None** for length enforcement. P1 and P3 are correctly rejected, so that's the app working, not a bug.
- Connection to BUG-001: the length rule is fine on submit, but the counter still shows /20 and lets me type past the limit with no message. That's BUG-001, a separate display bug that exists at the same time as the enforcement working correctly.